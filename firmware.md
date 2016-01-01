## Download URLs from Sphero

Master ping URL
* Sphero: http://update.orbotix.com/sphero/current/versions.php?mac=6886E7061234&mainApp=3.73&overlayManager=0.6&bootloader=3.3&hardware=0.1&record=0.1&orbBasic=1.9&model=1&platform=android&accesscode=
* Ollie: http://update.orbotix.com/ollie/current/versions.php?mac=6886E7061234&mainApp=3.73&overlayManager=0.6&bootloader=3.3&hardware=0.1&record=0.1&orbBasic=1.9&model=10&platform=android&accesscode=
* BB-8: http://update.orbotix.com/model-30/current/versions.php?mac=EF:80:A8:4A:12:34&mainApp=4.63&overlayManager=0.6&bootloader=4.8&hardware=0.1&record=0.1&orbBasic=0.0&model=30&platform=android&accesscode=

The URL returns a redirect, so it should be retrieved with `curl -I`. The redirect points to a `versions.json` file, which has the path to the most recent version.

### Models 0, 2 (Sphero)

* https://s3.amazonaws.com/sphero-firmware-production/sphero1/SpheroMix-1.40.bin
* (old, dead) http://update.orbotix.com/sphero/current/versions.json
* (old, dead) http://update.orbotix.com/sphero/current/sphero.bin

### Model 3 (SPRX)

* https://s3.amazonaws.com/sphero-firmware-production/sphero2/SpheroM4Mix-3.73.bin
* (old, dead) http://update.orbotix.com/unstable/current/codes/2k/versions.json
* (old, dead) http://update.orbotix.com/unstable/current/codes/2k/SpheroM4Mix-3.59.bin

```
First valid page: 6
Last valid page: 127
Page map:
XXXXXX**********
****************
**************.*
************...*
................
................
................
................
```

### Models 1, 10 (Ollie)

* https://s3.amazonaws.com/sphero-firmware-production/ollie/Ollie_Mix-5.49.bin
* (old, dead) http://update.orbotix.com/ollie/current/versions.json
* (old, dead) http://update.orbotix.com/ollie/current/Ollie_Mix-5.49.bin

### Model 30 (BB-8)

* https://s3.amazonaws.com/sphero-firmware-production/bb8/Ray_Mix-4.63.bin
* (old, dead) http://update.orbotix.com/model-30/current/versions.json
* (old, dead) http://update.orbotix.com/model-30/current/Ray_Mix-4.55.bin

## Bootloader update protocol

This is based on the decompiled Android Sphero app. The API docs reference a ``Bootloader Protocol Specification'' that doesn't seem to be publicly available.

The bootloader has device ID 1.

### Update parameters

The parameters are hard-coded in the app, so they can't seem to change between models.

| Name | Model | Pages | First page in update |  Page size | Update size  |
|---------------|---:|----:|---:|-----:|-------:|
| Sphero 1.0 ?  |  0 | 102 | 10 | 1024 | 104448 |
| Ollie proto?  |  1 | 116 |  8 | 2048 | 237568 |
| Sphero 2.0    |  2 | 102 | 10 | 1024 | 104448 |
| SPRK          |  3 |  54 |  6 | 2048 | 110592 |
| Ollie         | 10 | 116 |  8 | 2048 | 237568 |
| StarWars BB-8 | 30 | 116 |  8 | 2048 | 237568 |

### Jump to Bootloader

Jumping is done by sending a message with Device ID 0 (core), Command ID 30h.

Jumping to the bootloader always succeeds.

### BEGIN_REFLASH

Command ID 2.

No arguments.

Response codes
* 1: ok
* 2: power low

### HERE_IS_PAGE

Command ID 3.

Arguments:
* page number: 1 byte, 0-255
* page data

Response codes:
* 1: ok
* 3: flash fail
* 4: page illegal
* 5: checksum error

### JUMP_TO_MAIN_APP

Command ID 4.

Response codes:
* 1: ok
* 6: main app corrupt

### IS_PAGE_BLANK

Command ID 5.

If the update page consists only of 0xFF bytes, the updater checks if the flash page is blank. If so, it doesn't send the data.

Arguments:
* page number: 1 byte, 0-255

Response:
- blank if byte 6 in response packet is not null

### ERASE_USER_CONFIG

Command ID 6.

This is not implemented in the Sphero Android app. Calling it with a 0-1 byte argument resulted in an "OK", followed by a "Burn SSB\nu>" prompt. Calling it with a 4-byte argument started the main app.

### Other Commands

The bootloader rejects commands 1 and 7, 8, 9. Command 254 did not result in a response, instead the robot wrote "u>".


