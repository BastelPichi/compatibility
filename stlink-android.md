# STLink on Android

Requirements: An Android Smartphone, USB OTG Adapter, ST-Link.

Currently only DRV flashing is supported!
Note: This guide will only cover how to use the Flasher/Fulldump Generator. We wont be going into detail on how to ST-Link.

1) Start by installing the following apps:  
   - [ZFlasher STM32](https://play.google.com/store/apps/details?id=ru.zdevs.zflasherstm32)
   - [Scooterhacking Utility](https://utility.cfw.sh/)

2) [Download an Firmware](https://firmware.scooterhacking.org/) you desire. Choose the right model and DRV. Older DRVs might not support AT32/GD32 - I reccomend to choose the one with the highest version number.  
3) Open SHU (Scooterhacking Utility) and take a note of the MCU you have (AT32, GD32, STM32). Click on the 3 dot menu, then go to hardware details. Click "Copy UID" button.  
4) Go to the [fulldump generator](https://fulldumpgenerator.pythonanywhere.com/). Choose ESC, enter the odometer value in km, enter your SN, paste the UID you copied. Click the select button and upload the file you've downloaded in the second step.  
5) Click Submit. A file should download.  
6) Go to the ZFlasher App. Connect your ST-Link. Allow the app access to the ST-Link. Click the Update button. Go to option bytes, uncheck RDPRT, and click Write. Then go back, select the fulldump.bin you downloaded earlier in the "Write" section. Then press Go.  
7) Congrats, your controller should now be flashed!  
