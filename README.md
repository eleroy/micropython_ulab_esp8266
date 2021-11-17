# Building micropython + ulab on ESP8266 (2MB+)

## References

- Micropython from Damien George : https://github.com/micropython/micropython
- ulab : https://github.com/v923z/micropython-ulab
- Work/tutorial from Roberto Colistete : https://gitlab.com/rcolistete/micropython-samples/-/tree/master/ESP8266/Firmware/v1.12_with_ulab/ulab_v0.54.0
- ESP open sdk : https://github.com/pfalcon/esp-open-sdk
- ESP open sdk docker image : https://hub.docker.com/r/larsks/esp-open-sdk
- esptool: https://github.com/espressif/esptool

## Build firmware (tested on windows)

- You'll need docker
- Create a directory
- In this directory, clone micropython and ulab
```shell
git clone https://github.com/micropython/micropython.git
git clone https://github.com/v923z/micropython-ulab.git ulab
```
- Open micropython/ports/esp8266/board/esp8266_common.ld
- In frozen module section (.text), around line 183 after `*/frozen.o(.rodata.mp_frozen_content) /* frozen modules */`, add
`*code/*.o*(.literal* .text*)   /* ulab module */`
- Open a terminal, cd in micropython/ports/esp8266 and run (path example on windows):
```shell
docker run -v "C:":"//C/" -w "//C/absolute_local_path/micropython/ports/esp8266" larsks/esp-open-sdk make clean
```
where absolute_local_path is the path to the directory where everything was cloned.
- Then you should load the submodules:
```shell
docker run -v "C:":"//C/" -w "//C/absolute_local_path/micropython/ports/esp8266" larsks/esp-open-sdk make submodules
```
- And make the firmware
```
docker run -v "C:":"//C/" -w "//C/absolute_local_path/micropython/ports/esp8266" larsks/esp-open-sdk make -j8 USER_C_MODULES=../../../ulab all
```
- To flash the firmware you can go in `micropython/ports/esp8266/build-GENERIC` and run:
```shell
esptool.py erase_flash
esptool.py write_flash -fs 4MB 0 firmware-combined.bin
```


