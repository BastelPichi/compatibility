# SHU/SHFW Compatibility

SHU stands for Scooterhacking Utility. SHU Support means you can use the app, and allows things like changing Serial/Regen. **but doesnt necessarily mean you can tune the scooter.**  
SHFW stands for Scooterhacking Firmware. It means you can unlock the full potential of your scooter, with the ability of e.g. custom profile triggers, field weakening etc.  
  
If the scooter isn't on the list, its not supported. 


| Model                                                            | SHFW | SHU   | Reflasher  | NGFW  |Needs ST-Link       | Manufacturer | Notes                                  |
|-------------------------------------------------------------------|------|-------|------------|----------------------------|--------------|----------------------------------------|
| Ninebot ESx                                                       |  ✅  |   ✅  |     ✅     |       |        ❌          | Ninebot      |                                        |
| Ninebot E22, E25, E45                                             |  ✅  |   ✅  |     ✅     |       | 2.7.0 and above    | Ninebot      |                                        |
| Ninebot F20, F25,  F30, F35, F40                                  |  ✅  |   ✅  |     ✅     |       | 5.7.X and above    | Ninebot      | Old SHFW available. Use regular app to install, then use old 2.5 APK to configure. |
| Ninebot G30                                                       |  ✅  |   ✅  |     ✅     |       |above 1.7.3/1.6.13  | Ninebot      |                                        |
| Ninebot D18, D28, D38                                             |  ❌  |   ✅  |     ✅     |       |        ?           | Ninebot      | Currently no SHFW support  |
| Ninebot G2, F2, F2 Plus, F2 Pro                                   |  ✅  |   ✅  |     ✅     |    ✅ |        ✅          | Ninebot      |                                        |
| Ninebot F65                                                       |  ❌  |   ✅  |     ❌     |    ❌ |                    | Ninebot      |                                        |
| Ninebot GT1, GT2                                                  |  ❌  |   ✅  |     ❌     |    ❌ |                    | Ninebot      | Change SN for higher speed. Guide [here](https://rollerplausch.com/threads/ninebot-gt1d-serial-unlock-60km-h-tuning-via-st-link.10790/). |
| Ninebot G65                                                       |  ❌  |   ✅  |     ❌     |    ❌ |                    | Ninebot      |                                        |
| Ninebot E2 (Plus)                                                 |  ❌  |   ✅  |     ❌     |    ❌ |                    | Ninebot      |                                        |
| Ninebot E2 Pro                                                    |  ❌  |   ❌  |     ❌     |    ❌ |       ✅           | Ninebot      | Basic CFW in the works. Contact me if you have the Scooter and an ST-Link contact me.   |
| Ninebot Air T15                                                   |  ❌  |   ✅  |     ❌     |    ❌ |                    | Ninebot      |                                        |
| Ninebot P100                                                      |  ❌  |   ✅  |     ❌     |    ❌ |                    | Ninebot      |                                        |
| Ninebot X160                                                      |  ❌  |   ✅  |     ❌     |    ❌ |                    | Ninebot      |                                        |
| Segway ZT3 Pro, GT3, GT3 Pro, G3, E3, E3 Pro, F3, F3 Pro          |  ❌  |   ❌  |     ❌     |    ❌ |       ✅           | Ninebot      | For the ZT3 Pro there is [basic CFW](https://github.com/scooterteam/ZT3Tools/). |
|-------------------------------------------------------------------|------|-------|------------|----------------------------|--------------|----------------------------------------|
| Xiaomi Electric Scooter Pro                                       |  ✅  |   ✅  |     ✅     |    ✅ |        ❌          | Ninebot      |                                        |
| Xiaomi Electric Scooter Pro 2                                     |  ✅  |   ✅  |     ✅     |    ✅ | 155 or above       | Ninebot      |                                        |
| Xiaomi Electric Scooter Essential                                 |  ✅  |   ✅  |     ✅     |    ✅ | 155 or above       | Ninebot      |                                        |
| Xiaomi Electric Scooter 1S                                        |  ✅  |   ✅  |     ✅     |    ✅ | 155 or above       | Ninebot      |                                        |
| Xiaomi Electric Scooter 3                                         |  ✅  |   ✅  |     ✅     |    ✅ | 155 or above       | Ninebot      |                                        |
| Xiaomi Eletric Scooter M365                                       |  ❌  |   ✅  |     ✅     |    ❌ |        ❌          | Ninebot      | Flash M365-ProBLE.zip to use SHFW. Alternatively replace the dashboard with a non 4-dot one (pro/pro2 dashboard). |
| Xiaomi Electric Scooter 3 Lite, 4, 4 Lite, 4 Ultra                |  ❌  |   ❌  |     ❌     |    ❌ |       ✅            | Brightway    | No SHFW Support planned. Check [this](https://github.com/dnandha/stlink-lks32/) for basic CFW, [this](https://github.com/scooterteam/bw-patcher) for more patches. |
| Xiaomi Electric Scooter 4 Pro 2nd gen                             |  ❌  |   ❌  |     ❌     |    ❌ |                    | Brightway    | [Brightway Tuning](/brightway) |
| Xiaomi Electric Scooter 4 Lite 2nd gen                            |  ❌  |   ❌  |     ❌     |    ❌ |                    | LEQI         | No SHFW Support planned.                |
| Xiaomi Electric Scooter 4 Pro, 4 Pro Max, 4 Pro Plus              |  ❌  |   ❌  |     ❌     |    ✅ |                    | Ninebot      | Check [NGFW](https://nextgenfw.pythonanywhere.com/) for CFW Patcher. Base DRV [here](https://mi-fw-info.streamlit.app/). |
| Xiaomi Electric Scooter 5 Pro                                     |  ❌  |   ❌  |     ❌     |    ❌ |                    | Brightway    | [Brightway Tuning](/brightway)         |
| Xiaomi Electric Scooter 5, 5 Max                                  |  ❌  |   ❌  |     ❌     |    ❌ |                    | Brightway    | [Brightway Tuning](/brightway)         |
| Xiaomi Electric Scooter 5 eLite                                   |  ❌  |   ❌  |     ❌     |    ❌ |                    | LEQI         | No SHFW Support planned.                |