# esp bootloader plus
## Description
This version runs fine on my ESP32S3 board. If it does not work for you, check out the next section.
## My Steps of Adding Support for ESP32S3
1. Follow the instructions from [README](https://github.com/espressif/esp-bootloader-plus#readme) to add [the custom bootloader](https://github.com/espressif/esp-bootloader-plus#esp-bootloader-plus) to your project.
2. Build the project directly
    - It's supposed to fail since your project is based on ESP32S3. Keep a record of the error message for later use.
3. Make a copy of `./esp-xz/ld/esp32c3` and name it as `./esp-xz/ld/esp32s3`
4. Modify `./esp-xz/CMakeLists.txt` so it will use the corresponding `linker.lf` when the project target is ESP32S3
```
if(BOOTLOADER_BUILD)
        if (CONFIG_IDF_TARGET_ESP32C2 OR CONFIG_IDF_TARGET_ESP32C3 OR CONFIG_IDF_TARGET_ESP32S3)
                target_linker_script(${COMPONENT_LIB} INTERFACE "ld/${CONFIG_IDF_TARGET}/linker.lf")
        endif()
endif()
```
5. Modify `./esp-xz/ld/esp32s3/linker.lf` according to the error message showed in step 2
    - Adjust the org for iram_loader1_seg
    - e.g. My error message contains something like `section .iram_loader.text LMA [00000000403cc700,00000000403cf8b6] overlaps section.iram.text LMA [00000000403c9700,00000000403ccc0b]`
        - So I adjusted the org value from `0x403DA000` to `0x403ccc0d` to avoid overlapping
        - Build the project again and keep adjusting until there's no more error
6. Now the custom bootloader should be good to go on your ESP32S3 board