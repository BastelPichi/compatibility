# Brightway Tuning
This guide has been created by ScooterTeam. The method *should* be compatible with most, if not all, Brightway scooter models (sold by Xiaomi under the Xiaomi name but produced by Brightway).

Model | Tested OK
-- | -- 
3 Lite | TBA
4 | TBA 
4 Pro 2nd Gen | ✅
5 | TBA
5 Max | TBA 
5 Pro | ✅

If you own one of the models listed as TBA and acknowledge the risk of bricking your scooter, try flashing an original firmware update file and report back the results by creating an issue here on GitHub.

## Disclaimer
Take special note of each point before proceeding:

- Informational Purposes Only: The content of this guide is for educational and informational purposes only. It is not intended to promote or endorse the modification of scooter firmware or hardware, nor does it guarantee the safety, functionality, or legality of any modifications.
- Warranty Voidance: Modifying the scooter’s firmware or hardware, including using reverse-engineered tools, may void the manufacturer's warranty. Users should review their warranty terms before making any modifications.
- Assumption of Risk: Modifying scooter firmware or hardware can pose significant risks, including but not limited to voiding warranties, compromising safety, reducing functionality, or violating legal regulations. By following this guide, users assume full responsibility for any and all outcomes, including personal injury, property damage, or legal penalties.
- Legal Compliance: Users are solely responsible for ensuring that any modifications comply with applicable laws and regulations in their jurisdiction, including but not limited to those regarding speed limits, roadworthiness, and intellectual property.
- No Liability: The creators of this guide disclaim all liability for any direct, indirect, incidental, or consequential damages resulting from the use or misuse of the information or tools provided. By using this guide, users agree to release the authors from any legal claims or liabilities arising from their actions.


## Requirements
### Hardware

For this method, you'll have to connect your scooter to your computer using an UART adapter and adapter cable.

#### <ins>Ready-to-use kit</ins>
You have the option to buy a worry-free tested and proven kit with all required hardware necessary and skip to the actual [flashing procedure](#hardware-procedure).

You can find a ready-to-use dashboard breakout cable with pin headers on eBay: [female connector](https://www.ebay.de/itm/356681290474) or [male connector](https://www.ebay.de/itm/356888236112). These cables are guaranteed to fit, but you can look for other options as well, such as this one: [female connector](https://www.ebay.com/itm/116498080143).

Otherwise, you can acquire each component individually (listed below).

#### <ins>USB-to-serial (UART) adapter</ins>
The following UART adapters are known to work:

- CH340
- FT232RL
- [CP2102](https://github.com/BastelPichi/compatibility/issues/5)

Important note on PL2303 type adapters:
- PL2303**HX** works - but **HXA** does **not** (driver issues)

Hint: Some adapters come with an attached USB cable, which makes additional DuPont wires or an extra USB cable unnecessary.

#### <ins>Dashboard cable</ins>
To connect the USB adapter with the scooter a risk-free method is to use a replacement dashboard cable (5 pin Julet connector).
Depending on the scooter model the cable type you need to have is either a female or a male connector:

Model | Connector needed
-- | -- 
3 Lite | Male
4 | Male
4 Pro 2nd Gen | Female
5 | Female
5 Max | Male
5 Pro | Male

<img src="https://i.imgur.com/beOpkSx.jpeg" alt="drawing" width="300"/>

You can find dashboard cables (5 pin Julet connector) in local electronic stores or source from China (Aliexpress).
Due to there being no standardized colour for each pin, make sure that where you buy it from lists which color cable connects to which pin hole in the dashboard cable. 

If not, it is highly advisable to use a multimeter to check which cable runs to each pin, example (2 different Female dashboard cables from different sellers with different colors and totally different pinouts, ask me how i know):

<img src="https://i.imgur.com/9pycXTP.jpeg" alt="drawing" width="500"/>

On the female dashboard cable (5 pin female Julet connector), you can use a multimeter on continuity mode (if you dont know how, google is your friend), with the help of a sewing needle and aligator clips, and take note of which colored cable connects to each hole.

<img src="https://i.imgur.com/a3YIaLY.jpeg" alt="drawing" width="800"/>

On the male dashboard cable (5 pin male Julet connector), you can see the pinout in [dashboard cable](#dashboard-cable). Basically it is the same but mirrored.

If this is too complicated for you, buy a ready to use cable [here](#ready-to-use-kit).

Remark: Due to the tight pin spacing and small size of the dashboard connector, creating a DIY wiring solution is challenging and risks causing a short circuit between the pins, see [Create custom female connector from DuPont wires](#alternate-methods-to-connect-the-uart-adapter)(only for scooters where you need a female dashboard cable). Alternate methods are possible, but require opening up the scooter (see [here](#alternate-methods-to-connect-the-uart-adapter)).

#### <ins>DuPont wires</ins>
If you have a dashboard breakout cable with male pin headers and a standard UART adapter (like the CH340 or FT232RL, UART's without an attached cable), you'll need a set of DuPont female-to-female wires. The wires should have a minimum of 40-80cm length to reach the adapter end without tension. If you can't find female-to-female wires in that length, simply extend the wires with sets of male-to-female wires.

![image](res/dupont_collection.png)

## Hardware Procedure

### Step 1. Attach dupont connectors to your breakout dashboard cable:

You will need to either solder or crimp male (below left picture) or male (below right picture) dupont connectors to your dashboard cable, in order to later connect them to your UART:

<img src="https://i.imgur.com/6xUSE39.jpeg" alt="drawing" width="600"/>

### Step 2. Connect the UART adapter with the breakout [dashboard cable](#dashboard-cable). 

Either directly, for example if you have an adapter with an attached cable, or with DuPont wires otherwise. In the following example, the colors for each pinout are:

Dashboard cable | Pinout
-- | --
White | RX
Yellow | GND
Green | TX
Red | 5V
Black | BTN

#### A) UART adapter + DuPont wires
<img src="https://i.imgur.com/nWvwyjR.jpeg" alt="drawing" width="600"/>

#### B) UART adapter with attached cable
<img src="https://i.imgur.com/UclvoHE.jpeg" alt="drawing" width="600"/>

Note: Again, the wire colors for the UART adapter can vary. Check back with the supplier which color is which.

## Software Procedure

### Step 1. Connect UART adapter to your computer and find out the COM port number
On Windows: Open the Device Manager and look for "Ports (COM & LPT)". When you connect your UART adapter, something should show up there together with the COM port.  

![image](res/bwflasher_port_2.png). 

If something else appears, you might require drivers for your specific UART (again, google is your friend).

### Step 2.
Download the BwFlasher standalone executable here: [BwFlasher](https://github.com/scooterteam/bw-flasher/releases/latest)

You can also run the tool locally from the BwFlasher source: [SourceCode](https://github.com/scooterteam/bw-flasher)

### Step 3. Prepare patched firmware
1. Visit this site: [mi-fw-info](https://mi-fw-info.streamlit.app)
1. Download the **MCU firmware** update file for the scooter you'll be flashing.
1. Visit this site: [bw-patcher](https://bw-patcher.streamlit.app)
1. Upload firmware update file downloaded before
1. Select your scooter model and check the needed patches
1. Download patched firmware update file

### Step 4. Prepare flashing
1. Start BwFlasher tool
1. Check if the COM port is correctly set (see [here](#finding-out-the-com-port-number))
1. Select the patched firmware file

### Step 5. Turn on the scooter and start flashing
1. Remove the screws from the handlebar
1. Pull out the handlebar shaft
1. **Start the scooter** by pressing the ON button once
1. Unplug the dashboard cable (while the scooter is on!)
1. Plug in your adapter cable
1. Hit the "Start Update" button in BwFlasher

In BwFlasher, you should now see the progress bar advancing and updates appearing in the log. Once the progress reaches 100%, check the log for the message "Flashing complete". If you see this message: Congratulations, your scooter is now modified.

### Step 6. Re-Assembly
1. Remove the adapter cable
2. Plug in the dashboard cable
3. Insert the handlebar shaft
4. Insert and tighten the screws

## Appendix

### Dashboard cable pinout
![image](res/dash_cable_pinout.png)

### Alternate methods to connect the UART adapter
Warning: Requires controller removal!

### Frequent problems

Problem | Solution(s)
-- | --
Invalid FW File | You've probably downloaded wrong firmware file. Go back to mi-fw-info website and download MCU FW Details. Make sure to patch as the Xiaomi 4 pro gen 2
Progress stuck at 5% or 45% | USB Port in computer doesn't have enough power. Use a USB hub, another USB port, or another PC
Progress stuck at 0% (1) | The BWFlasher app is a bit buggy by nature. Sometimes it takes quite a few tries (open BWFlasher, load patch,start update, wait a few seconds, no progress, close app, open BW Flasher again and repeat until success). Basically turn off and on BWFlasher & retry a few times.
Progress stuck at 0% (2) | Connect cables in this order: 1- Connect the UART adapter to the PC (w/ 5 pin cable); 2-Turn on the scooter, disconnect Scooter cables; 3- Connect UART/5 Pin cable to the scooter
Progress stuck at 0% (3) | Make sure the Scooter is ON when you press "Start Update" and start flashing
Progress stuck at 0% (4) | Make sure cables are fully seated In the UART pins. Make sure 5pin connector is well connected to the scooter

If you don't have / want to buy a replacement dashboard cable, one of the following methods might work out for you:

- Use a regular 5 pin JST connector, like a spare hall sensor cable, in place of the original connector: cut it in half, solder or crimp the wires and connect them to the UART adapter (@encryptize).
- Pull out the pins of the original connector, then take new wires, crimp them and insert them into the connector. Connect the other end of the wires to the UART adapter.
- Needle method (@turbojeet): Take household needles, cut off the wide thread ends and insert them into female dupont connectors. Stick those needles into the connector alongside the original pins / wires.
- Soldering wires directly to the PCB: Unfeasible in this case, because the contacts are difficult to reach because of conformal coating / silicon.
- Create custom female connector from DuPont wires (@KrYpz0n): [Video tutorial](https://www.youtube.com/watch?v=MEVXANRJ1IM)
