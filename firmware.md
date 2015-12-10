## Download URLs from Sphero

Master ping URL:  http://update.orbotix.com/sphero/current/versions.php?mac=6886E7061234&mainApp=3.73&overlayManager=0.6&bootloader=3.3&hardware=0.1&record=0.1&orbBasic=1.9&model=1&platform=android&accesscode=

The URL returns a redirect, so it should be retrieved with `curl -I`. The redirect points to a `versions.json` file, which has the path to the most recent version.

### Models 0, 1, 2

* http://update.orbotix.com/sphero/current/versions.json
* http://update.orbotix.com/sphero/current/sphero.bin

### Model 3, 10, 30 and perhaps more

http://update.orbotix.com/unstable/current/codes/2k/versions.json
http://update.orbotix.com/unstable/current/codes/2k/SpheroM4Mix-3.59.bin

## Bootloader update protocol

This is based on the decompiled Android Sphero app.

The bootloader has device ID 1.

### Update parameters

The parameters are hard-coded in the app, so they can't seem to change between models.

| Model | Pages | First page in update |  Page size | Update size  |
|---:|----:|---:|-----:|-------:|
|  0 | 102 | 10 | 1024 | 104448 |
|  1 | 116 |  8 | 2048 | 237568 |
|  2 | 102 | 10 | 1024 | 104448 |
|  3 |  54 |  6 | 2048 | 110592 |
| 10 | 116 |  8 | 2048 | 237568 |
| 30 | 116 |  8 | 2048 | 237568 |

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
