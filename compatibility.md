# SHU/SHFW Compatibility

SHU stands for Scooterhacking Utility. SHU Support means you can use the app, and allows things like changing Serial/Regen. **but doesnt necessarily mean you can tune the scooter.**  
SHFW stands for Scooterhacking Firmware. It means you can unlock the full potential of your scooter, with the ability of e.g. custom profile triggers, field weakening etc.  
  
If the scooter isn't on the list, its not supported. 


| Model                                                            | SHFW | SHU | Reflasher | CFW          | Needs ST-Link          | Manufacturer | Notes                                  |
|-------------------------------------------------------------------|-----|-------|------------|--------------|------------------------|--------------|----------------------------------------|
| **Ninebot Models**                                                |      |       |            |              |                        |              |                                        |
| Ninebot ESx                                                       |  ✅  |   ✅  |     ✅     |      ❌      |            ❌          | Ninebot      | Full SHFW and SHU support available. No additional hardware required. |
| Ninebot E22, E25, E45                                             |  ✅  |   ✅  |     ✅     |      ❌      |     2.7.0 and above    | Ninebot      |                                                                       |
| Ninebot F20, F25,  F30, F35, F40                                  |  ✅  |   ✅  |     ✅     |      ❌      |     5.7.X and above    | Ninebot      | Old SHFW available. Use regular app to install, then use old 2.5 APK to configure. |
| Ninebot G30                                                       |  ✅  |   ✅  |     ✅     |      ❌      |    above 1.7.3/1.6.13  | Ninebot      |                                                                       |
| Ninebot D18, D28, D38                                             |  ❌  |   ✅  |     ✅     |      ❌      |            ?           | Ninebot      | Currently no SHFW support                                             |
| Ninebot G2, F2, F2 Plus, F2 Pro                                   |  ✅  |   ✅  |     ✅     |      ❌      |            ✅          | Ninebot      |                                                                       |
| Ninebot F65                                                       |  ❌  |   ✅  |     ❌     |      ❌      |                        | Ninebot      |                                                                       |
| Ninebot GT1, GT2                                                  |  ❌  |   ✅  |     ❌     |      ❌      |                        | Ninebot      |                                                                       |
| Ninebot G65                                                       |  ❌  |   ✅  |     ❌     |      ❌      |                        | Ninebot      |                                                                       |
| Ninebot E2 (Plus)                                                 |  ❌  |   ✅  |     ❌     |      ❌      |                        | Ninebot      |                                                                       |
| Ninebot E2 Pro                                                    |  ❌  |   ❌  |     ❌     |      ✅      |           ✅           | Ninebot      | Check [this guide](https://docs.google.com/document/u/0/d/14n4_bu5gBc86IdOHAbsYkuEee4I2yOtbMPR7G4gGhB8/mobilebasic). |
| Ninebot Air T15                                                   |  ❌  |   ✅  |     ❌     |      ❌      |                        | Ninebot      |                                                                       |
| Ninebot P100                                                      |  ❌  |   ✅  |     ❌     |      ❌      |                        | Ninebot      |                                                                       |
| Ninebot X160                                                      |  ❌  |   ✅  |     ❌     |      ❌      |                        | Ninebot      |                                                                       |
| Segway ZT3 Pro, GT3, GT3 Pro, G3, E3, E3 Pro, F3, F3 Pro          |  ❌  |   ❌  |     ❌     |      ✅/❌      |           ✅           | Ninebot      | Basic CFW available for ZT3 Pro. See [ZT3Tools](https://github.com/scooterteam/ZT3Tools/) for more information. |
|                                                                   |      |       |            |              |                        |               |                                                                       |
| **Xiaomi Models**                                                 |      |       |            |              |                        |              |                                        |
| Xiaomi Electric Scooter Pro                                       |  ✅  |   ✅  |     ✅     |      ❌      |            ❌          | Ninebot      |                                                                     |
| Xiaomi Electric Scooter Pro 2                                     |  ✅  |   ✅  |     ✅     |      ❌      |     155 or above       | Ninebot      |                                                                     |
| Xiaomi Electric Scooter Essential                                 |  ✅  |   ✅  |     ✅     |      ❌      |     155 or above       | Ninebot      |                                                                     |
| Xiaomi Electric Scooter 1S                                        |  ✅  |   ✅  |     ✅     |      ❌      |     155 or above       | Ninebot      |                                                                     |
| Xiaomi Electric Scooter 3                                         |  ✅  |   ✅  |     ✅     |      ❌      |     155 or above       | Ninebot      |                                                                     |
| Xiaomi Eletric Scooter M365                                       |  ❌  |   ✅  |     ✅     |      ❌      |            ❌          | Ninebot      | Flash M365-ProBLE.zip, then install SHFW. Alternatively, replace dashboard with non-4-dot version (Pro/Pro2 dashboard). |
| Xiaomi Electric Scooter 3 Lite, 4, 4 Lite, 4 Ultra                |  ❌  |   ❌  |     ❌     |      ✅      |           ✅            | Brightway    | No SHFW support planned. Basic CFW available via [stlink-lks32](https://github.com/dnandha/stlink-lks32/). Additional patches at [bw-patcher](https://github.com/scooterteam/bw-patcher). |
| Xiaomi Electric Scooter 4 Pro 2nd Gen                             |  ❌  |   ❌  |     ❌     |      ✅      |                        | Brightway    | Requires UART adapter. See [Brightway / LEQI Tuning](/brightway) for detailed instructions. |
| Xiaomi Electric Scooter 4 Lite 2nd Gen                            |  ❌  |   ❌  |     ❌     |      ❌      |                        | LEQI         | No support due to physical limitations. |
| Xiaomi Electric Scooter 4 Pro, 4 Pro Max, 4 Pro Plus              |  ❌  |   ❌  |     ❌     |      ✅      |                        | Ninebot      | CFW available via [NGFW Patcher](https://nextgenfw.pythonanywhere.com/). Base DRV at [mi-fw-info](https://mi-fw-info.streamlit.app/). |
| Xiaomi Electric Scooter Elite                                     |  ❌  |   ❌  |     ❌     |      ✅      |                        | LEQI         | Requires UART adapter. See [Brightway / LEQI Tuning](/brightway) for detailed instructions. |
| Xiaomi Electric Scooter 5 Pro                                     |  ❌  |   ❌  |     ❌     |      ✅      |                        | Brightway    | Requires UART adapter. See [Brightway / LEQI Tuning](/brightway) for detailed instructions. |
| Xiaomi Electric Scooter 5 Max, 5                                  |  ❌  |   ❌  |     ❌     |      ✅      |                        | Brightway    | Supports both ST-Link and UART methods. See [stlink-lks32](https://github.com/dnandha/stlink-lks32/) for ST-Link guide or [Brightway / LEQI Tuning](/brightway) for UART instructions. |
