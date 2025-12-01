# 32bit I2S DAC Library for Raspberry Pi Pico / Pico 2


## My personal build instructions on Raspberry Pi 5 Ubuntu
I could not get it to work on Windows but I might try again.

todo: combine with existing instructions

    # terminal in folder /media/martinm/IphoneUSB/PicoCpp3/pico_audio_i2s_32b/samples/sine_wave_i2s_32b
    export PICO_SDK_PATH=/media/martinm/IphoneUSB/PicoCpp/pico-sdk
    export PICO_EXTRAS_PATH=/media/martinm/IphoneUSB/PicoCpp/pico-extras
    export PICO_EXAMPLES_PATH=/media/martinm/IphoneUSB/PicoCpp/pico-examples
    mkdir build
    cd build
    cmake ..
    clear ; make -j4
    # copy sine_wave_i2s_32b.uf2 to Raspberyy Pi Pico 2 (connected to USB while pression the on board button)
    # use PUTTY to connect to the correct com port 


[![Build](https://github.com/elehobica/pico_audio_i2s_32b/actions/workflows/build-binaries.yml/badge.svg)](https://github.com/elehobica/pico_audio_i2s_32b/actions/workflows/build-binaries.yml)

## TODO local adjustments:

i2s_bck_pin = 2; // PICO_AUDIO_I2S_CLOCK_PIN_BASE board.GP2   # PCM5102 BCK pin GP2 green
i2s_lck_pin = 3; // board.GP3  # PCM5102 LCK pin GP3 purple
i2s_dat_pin = 4; // PICO_AUDIO_I2S_DATA_PIN board.GP4  # PCM5102 DIN pin GP4 blue

## Overview
This library is for Raspberry Pi Pico / Pico 2 to support 32bit/Stereo I2S DAC.
* Channels: 2ch (Stereo)
* Bit resolution: 32bit
* Sampling Frequency: up to 192 KHz

## Supported Board and Peripheral Devices
* Raspberry Pi Pico
* Raspberry Pi Pico 2
* PCM5102 32bit I2S Audio DAC
* ES9023 24bit I2S Audio DAC

## Pin Assignment
In addition to original connection

| Pico Pin # | GPIO | Function | Connection |
----|----|----|----
| 21 | GP16 | BCK | to PCM5102 BCK (13) / to ES9024 BCK (1) |
| 22 | GP17 | LRCK | to PCM5102 LRCK (15) / to ES9023 LRCK (2) |
| 23 | GND | GND | GND |
| 24 | GP18 | SDO | to PCM5102 DIN (14) / to ES9023 SDI (3) |
| 40 | VBUS | VCC | to VIN of DAC board (5V) |

![PCM5102_schematic](doc/PCM5102_Schematic.png)

### PCM5102 Board Setting
* tie PCM5102 SCK (12) to low (bridge short land)
* H1L (FLT) = L
* H2L (DEMP) = L
* H3L (XSMT) = H
* H4L (FMT) = L

## How to build
* See ["Getting started with Raspberry Pi Pico"](https://datasheets.raspberrypi.org/pico/getting-started-with-pico.pdf)
* Put "pico-sdk", "pico-examples" and "pico-extras" on the same level with this project folder.
* Set environmental variables for PICO_SDK_PATH, PICO_EXTRAS_PATH and PICO_EXAMPLES_PATH
* Confirmed with pico-sdk 2.1.1
```
> git clone -b 2.1.1 https://github.com/raspberrypi/pico-sdk.git
> cd pico-sdk
> git submodule update --init
> cd ..
> git clone -b sdk-2.1.1 https://github.com/raspberrypi/pico-examples.git
>
> git clone -b sdk-2.1.1 https://github.com/raspberrypi/pico-extras.git
> 
> git clone -b main https://github.com/elehobica/pico_audio_i2s_32b.git
```
### Windows
* Build is confirmed with Developer Command Prompt for VS 2022 and Visual Studio Code on Windows environment
* Confirmed with cmake-3.27.2-windows-x86_64 and gcc-arm-none-eabi-10.3-2021.10-win32
* Launch "Developer Command Prompt for VS 2022"
```
> cd pico_audio_i2s_32b
> cd samples/xxxxx  # sample project directory
> mkdir build && cd build
> cmake -G "NMake Makefiles" ..  ; (for Raspberry Pi Pico 1 series)
> cmake -G "NMake Makefiles" -DPICO_PLATFORM=rp2350 -DPICO_BOARD=pico2 ..  ; (for Raspberry Pi Pico 2)
> nmake
```
* Put "*.uf2" on RPI-RP2 or RP2350 drive
### Linux
* Build is confirmed with [pico-sdk-dev-docker:sdk-2.1.1-1.0.0]( https://hub.docker.com/r/elehobica/pico-sdk-dev-docker)
* Confirmed with cmake-3.22.1 and arm-none-eabi-gcc (15:10.3-2021.07-4) 10.3.1
```
$ cd pico_audio_i2s_32b
$ cd samples/xxxxx  # sample project directory
$ mkdir build && cd build
$ cmake ..  # (for Raspberry Pi Pico 1 series)
$ cmake -DPICO_PLATFORM=rp2350 -DPICO_BOARD=pico2 ..  # (for Raspberry Pi Pico 2)
$ make -j4
```
* Download "*.uf2" on RPI-RP2 or RP2350 drive

## Macro Definitions in audio_i2s.c
### CORE1_PROCESS_I2S_CALLBACK
 If defined, i2s_callback_func is processed at Core 1 while main routine and DMA IRQ handler is processed at Core 0.
 It will be efficient when generating audio data needs computing power. It does not contribute to reduce bus load and even make slightly worse when Core 1 is activated.

## Application Example
* [RPi_Pico_WAV_Player](https://github.com/elehobica/RPi_Pico_WAV_Player)
