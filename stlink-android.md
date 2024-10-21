# STLink on Android

Requirements: An Android Smartphone, USB OTG Adapter, ST-Link.  
  
Currently only DRV flashing is supported!  
Note: You need to understand the fundamental principles of ST-Linking.  
  
1) Start by installing the following apps:  
   - [ZFlasher STM32](https://play.google.com/store/apps/details?id=ru.zdevs.zflasherstm32)
   - [Scooterhacking Utility](https://utility.cfw.sh/)
  
2) Open SHU (Scooterhacking Utility) and take a note of the MCU you have (AT32, GD32, STM32). Click on the 3 dot menu, then go to hardware details. Click "Copy UID" button.  
3) Go to the ZFlasher App. Connect your ST-Link. Allow the app access to the ST-Link. Click the Update button. Go to option bytes, uncheck RDPRT, and click Write. Warning! This wipes any firmware on the controller! No taksies-backsies!  
4) Go to the [Webflasher](https://flasher.bastelpichi.de). Choose your Scooter, enter the odometer value in km, enter your SN, paste the UID you copied.  
5) Click "Start Flashing". Select the ST-Link, and wait for the flashing to complete.  
  
6) Congrats, your controller should now be flashed!  
