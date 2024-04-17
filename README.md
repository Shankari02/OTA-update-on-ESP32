## OTA update on ESP-32
The OTA update mechanism allows a device to update itself based on data received while the normal firmware is running (for example, over Wi-Fi or Bluetooth.)

OTA requires configuring the Partition Table of the device with at least two “OTA app slot” partitions (i.e. ota_0 and ota_1) and an “OTA Data Partition”.

The OTA operation functions write a new app firmware image to whichever OTA app slot that is currently not selected for booting. Once the image is verified, the OTA Data partition is updated to specify that this image should be used for the next boot.

OTA Data Partition
An OTA data partition (type data, subtype ota) must be included in the Partition Table of any project which uses the OTA functions.

For factory boot settings, the OTA data partition should contain no data (all bytes erased to 0xFF). In this case the esp-idf software bootloader will boot the factory app if it is present in the the partition table. If no factory app is included in the partition table, the first available OTA slot (usually ota_0) is booted.

### Partition Tables
A single ESP32’s flash can contain multiple apps, as well as many different kinds of data (calibration data, filesystems, parameter storage, etc). For this reason a partition table is flashed to (default offset) 0x8000 in the flash.

Partition table length is 0xC00 bytes (maximum 95 partition table entries). An MD5 checksum, which is used for checking the integrity of the partition table, is appended after the table data.
Each entry in the partition table has a name (label), type (app, data, or something else), subtype and the offset in flash where the partition is loaded.
The simplest way to use the partition table is to open the project configuration menu (idf.py menuconfig) and choose one of the simple predefined partition tables under CONFIG_PARTITION_TABLE_TYPE:

- “Single factory app, no OTA”
- “Factory app, two OTA definitions”

### Usage
- The ESP32 broadcasts an open WiFi network with SSID ESP32 OTA Update. Connect to it and open http://192.168.4.1 in your web browser.

- Click Browse..., select a firmware file (.bin) and click Upload.
The website will upload the file and display the upload progress. If the upload is succesful and the app image is valid (magic byte), the ESP32 will reboot into the new firmware.

### Initial Build and Flash
Make sure you have a recent esp-idf version with support for cmake-based projects installed and configured properly. Then compile and flash this project as usual using the following steps:

```
idf.py build   //to compile everything
idf.py -p /dev/ttyUSBX flash monitor   //to flash everything and display logs
```

### References
- [Over-the-Air Updates ESP-IDF Docs](https://docs.espressif.com/projects/esp-idf/en/v4.4.3/esp32/api-reference/system/ota.html#application-example)

- [HTTP Server ESP-IDF Docs](https://docs.espressif.com/projects/esp-idf/en/stable/esp32/api-reference/protocols/esp_http_server.html)

- [Partition Tables](https://docs.espressif.com/projects/esp-idf/en/v4.4.3/esp32/api-guides/partition-tables.html)
