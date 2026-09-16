# Dash ↔ ESC UART protocols

The dashboard (or BLE board) talks to the motor ESC over a plain two-wire UART. Baud rate and cabling are the same on a large set of Xiaomi / Ninebot-family scooters, but the **byte framing is not**: two incompatible dialects share the same PHY. If you speak the wrong one, the ESC will silently drop frames — or, worse, accept something that drives throttle, regen, or lock state.

This page is enough to open the port, build valid frames, run a continuous control loop, and parse replies. Deeper model notes live under `docs/` (linked at the end).

---

## Which dialect?

Capture a short burst on the dash↔ESC lines and look at structure, not baud:

| You see | Dialect | Typical ESC |
|---|---|---|
| `51` / `53` / `61` … ending with `0xFF − first_byte`, plus an 8-bit sum | **Brightway (A)** | 3 Lite, 4 Pro 2nd-class |
| `5A` … ending in a **2-byte big-endian CRC** | **LEQI (B)** | 5 Elite, Mi 5 Plus ESC, EU1 |

```
Brightway:  51 10 06 33 01 88 00 00 1E 41 AE
LEQI:       5A 12 20 06 40 00 02 32 00 0F E0 CB
```

Pick one dialect and stick to it for the whole ride session. Do not invent meanings for unused flag bits. Firmware update uses the same wires but a different session — never interleave DFU with the ride loop.

### Link settings (both dialects)

| Parameter | Value |
|---|---|
| Baud | **19200** |
| Framing | 8N1 |
| Flow control | none |
| Wiring | Dash TX → ESC RX, Dash RX ← ESC TX, common GND |

Below, **TX** means dash → ESC and **RX** means ESC → dash. Ride traffic is continuous (tens of frames per second on a stock dash).

---

## Brightway (Protocol A)

Brightway ride frames are short binary packets: a command byte, a variable body, an 8-bit checksum, and the bitwise complement of the command. Dash traffic uses source nibble `0x5`; ESC replies use `0x6`. The important opcodes are control (`0x51`), get-info (`0x53`), and status (`0x61`).

### Frame layout

```
CMD || body || SUM8 || ~CMD
```

| Field | Rule |
|---|---|
| `CMD` | `(source << 4) \| command` — dash `0x5x`, ESC `0x6x` |
| `SUM8` | sum of every byte before SUM, `& 0xFF` |
| `~CMD` | `(0xFF - CMD) & 0xFF` |

| CMD | Direction | Role |
|---|---|---|
| `0x51` | TX | Control / heartbeat — body must start with `SUB = 0x10` |
| `0x52` | TX | Param / cal write |
| `0x53` | TX | Get-info (fixed 4-byte form) |
| `0x61` | RX | Periodic status (two alternating shapes) |
| `0x64` | either | DFU ASCII lead-in, or some info replies |
| `0x65` | RX | ACK to certain writes |

So every valid `0x51` ends in `AE`, every `0x53` in `AC`, every `0x61` in `9E`.

Length and checksum checks on the ESC side:

- For `0x51` / `0x52`, let `L = buf[2] + 3` (bytes before SUM). Require `buf[L] == sum(buf[0:L]) & 0xFF` and `buf[L+1] + CMD == 0xFF`.
- For `0x53`, the frame is exactly four bytes: `buf[2] == (buf[1] + 0x53) & 0xFF` and `buf[3] == 0xAC`.
- The RX assembler also accepts first byte `0x51…0x54` (binary), `0x64` (`d`, ASCII DFU until CR), and — once DFU is armed — `0x01` / `0x04` (XMODEM).

```python
def brightway_sum(prefix: bytes) -> int:
    return sum(prefix) & 0xFF

def brightway_build(cmd: int, body: bytes) -> bytes:
    head = bytes([cmd]) + body
    return head + bytes([brightway_sum(head), (0xFF - cmd) & 0xFF])

def brightway_ok(frame: bytes) -> bool:
    return (
        len(frame) >= 3
        and frame[-1] == ((0xFF - frame[0]) & 0xFF)
        and frame[-2] == brightway_sum(frame[:-2])
    )
```

Worked idle control frame:

```
51 10 06 33 01 88 00 00 1E 41 AE
```

Sum of the first nine bytes is `0x141 → 0x41`; `~0x51 = 0xAE`.

### Control loop (TX)

The ride loop is a stream of 11-byte `0x51` frames. On Brightway ESCs that match this dialect, only `SUB = 0x10` is handled for live control. Stock dashes typically send about every 20 ms; the important part is continuity — a long gap makes the ESC raise a host-timeout event on status.

```
offset:  0  1  2  3  4  5  6  7  8  9 10
bytes:  51 10 06 B3 B4 B5 TH BR B8 SUM AE
```

| Offset | Name | Meaning |
|---|---|---|
| 3 | B3 | Split into two mode nibbles (`lo = B3&0xF`, `hi = B3>>4`) |
| 4 | B4 | Flag byte — ESC reads bits **7, 3, 0** |
| 5 | B5 | Flag byte — ESC reads bits **7, 6, 5, 3, 2, 1, 0** |
| 6 | TH | Throttle 0…100 |
| 7 | BR | Brake 0…100 |
| 8 | B8 | Regen strength; stock uses `0x1E` / `0x3C` / `0x5A` |

Throttle and brake are the safe knobs. Mode and flag bytes are packed; the ESC only consumes specific bits. Stick to known wire patterns unless you have mapped a bit yourself — unused B4/B5 bits can change lock and enable behaviour.

| Intent | Bytes after `51 10 06` |
|---|---|
| Nominal | `33 01 88 [th] [br] 1E` |
| Light off | `33 00 88 [th] [br] 1E` |
| Lock | `31 00 C8 00 00 1E` |
| Pedestrian | `3B 01 88 [th] [br] 1E` |
| Drive | `32 01 88 [th] [br] 1E` |

> **Warning:** Do not invent B4/B5 bit meanings from scratch.

```python
import serial, time

def heartbeat(th=0, br=0, b3=0x33, b4=0x01, b5=0x88, b8=0x1E):
    return brightway_build(0x51, bytes([0x10, 0x06, b3, b4, b5, th, br, b8]))

ser = serial.Serial("/dev/ttyUSB0", 19200, timeout=0.05)
while True:
    ser.write(heartbeat())
    time.sleep(0.02)
```

### Status (RX)

While the ESC app is running it transmits `0x61` status and **alternates** two shapes each time: variant A (`61 30 08 … 9E`) then variant B (`61 31 09 … 9E`). Always validate SUM and `9E` before using fields.

**Variant A (13 bytes)** carries the live ride picture: echoed mode, flag bytes, a scaled pack-ADC scalar, an event code, and speed as a big-endian `uint16`. Near the speed cap, mode_lo `1` / `2` / `3` snaps the word toward `0x98` / `0xCA` / `0xFF`.

| Off | Field |
|---|---|
| 3 | mode echo `(mode_hi<<4) \| mode_lo` |
| 4–5 | flags |
| 6 | `clamp(i16 / 10, 0, 255)` |
| 7 | reserved `0x00` |
| 8 | event code |
| 9–10 | speed (BE) |

Event codes are a priority cascade. `0x01` is normal idle. `0x10` means the ESC has not seen a valid `0x51` recently (host timeout). Codes in `0x11…0x45` are fault/status latches — leave them as numbers until each latch is mapped.

**Variant B (14 bytes)** is a second status block (nine payload bytes). Multi-byte fields are big-endian; the last three bytes on the wire are struct indices `[6], [8], [7]` in that order. Log the payload until those fields are named for your firmware.

```python
def parse_61(frame: bytes):
    if not brightway_ok(frame) or frame[0] != 0x61:
        return None
    sub, n = frame[1], frame[2]
    p = frame[3:3 + n]
    if sub == 0x30 and n == 8:
        return {"variant": "A", "mode": p[0], "event": p[5],
                "speed": (p[6] << 8) | p[7]}
    if sub == 0x31 and n == 9:
        return {"variant": "B", "payload": p}
    return None
```

Example idle-shaped A frame: `61 30 08 33 01 80 00 00 01 00 00 4E 9E`.

### Get-info and DFU

Short identity/info probes use the 4-byte form:

```
53 || SUB || (SUB + 0x53) & 0xFF || AC
```

Example: `SUB = 0x2A` → `53 2A 7D AC`. Replies that are known to build on this dialect include SUB `0x28`, `0x29`, `0x2B`, `0x2C`, `0x30`, `0x31`, `0x80` (often prefixed with `0x64`). Other SUB values may simply do nothing.

Flashing is a second protocol on the same UART. Stop the `0x51` stream, then:

1. ASCII commands: `down get_ver` / `rd_info` / `nvm_write` / `wr_info` / `dfu_verify` / `dfu_active` (replies `ok\r` or `error\r`).
2. After arming, XMODEM blocks: `01 | SEQ | ~SEQ | DATA[128] | CRC16_BE`, with CRC-16/XMODEM over DATA only.
3. Control bytes: `0x06` ACK, `0x18` CAN, `0x04` EOT.

The CRC primitive is the same family as LEQI ride frames (`binascii.crc_hqx`); coverage is the XMODEM payload, not a `5A` header.

### Brightway checklist

1. Open **19200 8N1**.
2. Build every TX frame with SUM8 and ~CMD; discard bad RX.
3. Stream `51 10 06 … AE` continuously; throttle/brake in `0…100`.
4. Parse alternating `61 30` / `61 31` after checksum OK.
5. Run DFU only as its own session.

---

## LEQI (Protocol B)

LEQI frames always start with `0x5A` and end with a big-endian CRC-16. The ESC state machine accepts dash commands `0x12` (handle immediately) and `0x13` (copy into a queue); replies use `0x21`. Anything else resets the parser. Bad CRC → frame ignored.

### Frame layout

```
5A || CMD || SUB || LEN || payload[LEN] || CRC_H || CRC_L
```

`LEN` is payload length only; total size is `LEN + 6`. The CRC covers `5A CMD SUB LEN` and the payload (`LEN + 4` bytes) — everything except the CRC itself. Algorithm is CRC-16/XMODEM (init 0, poly `0x1021`), equivalent to `binascii.crc_hqx(data, 0)`.

| SUB on `0x12` | Role |
|---|---|
| `0x02` | Device info |
| `0x03` | Firmware-update arm |
| `0x04` | Firmware data (after arm) |
| `0x05` | Firmware end |
| `0x20` | Ride control + telemetry |
| `0x21` | Set region |
| `0xAC` | Status latch path |

```python
def crc16_xmodem(data: bytes) -> int:
    crc = 0
    for b in data:
        crc ^= b << 8
        for _ in range(8):
            crc = ((crc << 1) ^ 0x1021) & 0xFFFF if crc & 0x8000 else (crc << 1) & 0xFFFF
    return crc

def leqi_build(cmd: int, sub: int, payload: bytes) -> bytes:
    body = bytes([0x5A, cmd, sub, len(payload)]) + payload
    c = crc16_xmodem(body)
    return body + bytes([(c >> 8) & 0xFF, c & 0xFF])

def leqi_ok(frame: bytes) -> bool:
    if len(frame) < 6 or frame[0] != 0x5A:
        return False
    n = 6 + frame[3]
    if len(frame) < n:
        return False
    frame = frame[:n]
    return ((frame[-2] << 8) | frame[-1]) == crc16_xmodem(frame[:-2])

assert crc16_xmodem(bytes.fromhex("5A212104D1EE0000")) == 0x10A6
assert leqi_build(0x12, 0x20, bytes([0x40, 0, 2, 50, 0, 15])).hex() == \
       "5a12200640000232000fe0cb"
```

### Ride loop (`SUB 0x20`)

This is the main control path. The dash sends a 6-byte payload; the ESC answers in the same handler with a 12-byte telemetry payload (`5A 21 20 0C …`).

**Request:**

```
5A 12 20 06  FLAGS  KERS  MODE  TH  BR  MAX  CRC_H CRC_L
```

| Field | Role |
|---|---|
| FLAGS bit7 | Headlight |
| FLAGS bit6 | Active (clear → inactive / shutdown-ish path) |
| FLAGS bit5 | **Motor disable + strong regen** — not cruise; leave clear unless that is the goal |
| FLAGS bit4 | Brake / enable related |
| FLAGS bits 3–2 | Config gate |
| FLAGS bits 1–0 | Stored unless both set |
| KERS bits 6–5 | Regen level: `00` → low, `0x20` → mid, `0x40` → high (may cap mid if pack metric > 89) |
| MODE | Low two bits (`& 3`) — ped / drive / sport style |
| TH, BR | 0…100 |
| MAX | Cap in **whole km/h**; ESC stores `MAX × 10` internally |

Start with FLAGS `0x40` (normal), `0xC0` (lights), or `0x50` (brake bit set). Avoid `0x20` / `0x60` unless you intend cutoff.

```python
# Drive, 50% throttle, 15 km/h cap, lights off, low KERS
leqi_build(0x12, 0x20, bytes([0x40, 0x00, 0x02, 50, 0, 15]))
# → 5A 12 20 06 40 00 02 32 00 0F E0 CB
```

**Response:**

```
5A 21 20 0C  F1 F2 F3  SPEED_L SPEED_H  BATT…  VOLT…  CURR…  TEMP  CRC…
```

| Field | Encoding |
|---|---|
| F1–F3 | Status / fault bit packs from ESC latches |
| Speed | `uint16` **little-endian**, unit **0.1 km/h** (`C8 00` → 20.0) |
| Battery | `uint16` LE, percent path |
| Voltage | `uint16` LE, scaled ×100 |
| Current | `int16` LE, scaled ×100 after clamp |
| Temp | one byte |

Important unit split: you **command** max speed in whole km/h, but you **read** speed in tenths. Multi-byte payload fields are little-endian; the CRC is big-endian.

### Info, region, control loop

An empty info probe (`SUB 0x02`) returns version/hardware digits; European Elite-class builds include the ASCII marker `EU1`.

Region set (`SUB 0x21`) takes a 4-byte little-endian code. The ESC stores an internal mode and speed cap, then ACKs by echoing the four bytes (`5A 21 21 04 …`).

| Code | Mode | Speed store (0.1 km/h) |
|---|---|---|
| `0xEC29` | 1 | 350 (35.0) |
| `0xEC7F` | 2 | 220 (22.0) |
| `0xEC80` | 1 | 270 (27.0) |
| `0xEC81` | 3 | 270 (27.0) |
| `0xEC82` | 1 | 220 (22.0) |
| `0xEC83` | 1 | 270 (27.0) |
| `0xEED1` | 2 | 220 (22.0) |
| `0xFA14` | 4 | 220 (22.0) |

```python
import serial, struct, time

ser = serial.Serial("/dev/ttyUSB0", 19200, timeout=0.05)

def send_control(mode=2, th=0, br=0, max_kmh=20, flags=0x40, kers=0x00):
    ser.write(leqi_build(0x12, 0x20, bytes([flags, kers, mode, th, br, max_kmh])))

def read_frames(buf: bytearray):
    out, i = [], 0
    while i + 4 <= len(buf):
        if buf[i] != 0x5A:
            i += 1
            continue
        n = 6 + buf[i + 3]
        if i + n > len(buf):
            break
        fr = bytes(buf[i:i + n])
        if leqi_ok(fr):
            out.append(fr)
        i += n
    del buf[:i]
    return out

rx = bytearray()
while True:
    send_control(max_kmh=15)
    rx.extend(ser.read(64))
    for fr in read_frames(rx):
        if fr[1:4] == b"\x21\x20\x0c":
            print(f"speed={struct.unpack_from('<H', fr, 7)[0] / 10:.1f}")
    time.sleep(0.04)
```

Firmware update (`SUB 0x03` / `0x04` / `0x05`) reuses this framing inside a separate state machine. Get the ride loop solid before touching OTA.

### LEQI checklist

1. Open **19200 8N1**.
2. Confirm CRC: `5A 21 21 04 D1 EE 00 00` → `0x10A6`.
3. Stream `5A 12 20 06 …` with FLAGS bit5 clear.
4. Parse `5A 21 20 0C …`; speed = little-endian u16 / 10.
5. Optional: info `0x02`, region `0x21`.

---

## Safety

These buses carry live throttle and brake. A stuck sender, bad checksums you somehow still act on, Brightway flag misuse, or LEQI FLAGS bit5 can cut propulsion or force hard regen.

Work on a stand with the wheel clear. Start at throttle 0 with a conservative speed cap. You are responsible for local law and hardware damage.

## Related

- [Brightway flash / tuning](brightway) — hardware side, not the wire format
- [Compatibility list](compatibility)
