# Nude IOs - Thread & Matter POC project

## Your laptop have to run on Ubuntu 22.04

## Install Thread border router

$ mkdir thread-matter-poc-prj; cd thread-matter-poc-prj
$ git clone https://github.com/openthread/ot-br-posix
$ cd ot-br-posix
$ PLATFORM=ubuntu ./script/bootstrap
$ INFRA_IF_NAME=wlo1 ./script/setup
$ sudo service mdns start
$ sudo service otbr-web start
$ sudo service otbr-agent start

$ sudo ot-ctl dataset init new
$ sudo ot-ctl dataset init new
$ sudo ot-ctl dataset commit active
$ sudo ot-ctl ifconfig up
$ sudo ot-ctl thread start

## Build Firmware for Matter Light device

### Install ESP IDF

$ git clone --recursive https://github.com/espressif/esp-idf.git
$ cd esp-idf; git checkout v5.2.3; git submodule update --init --recursive;
$ ./install.sh
$ cd ..
$ cd esp-idf; source ./export.sh; cd ..

### Install ESP Matter

$ sudo apt update
$ sudo apt install bluez avahi-daemon

$ sudo apt install git gcc g++ pkg-config libssl-dev libdbus-1-dev \
     libglib2.0-dev libavahi-client-dev ninja-build python3-venv python3-dev \
     python3-pip unzip libgirepository1.0-dev libcairo2-dev libreadline-dev

$ sudo apt install libsdl2-dev

$ git clone --depth 1 https://github.com/espressif/esp-matter.git
$ cd esp-matter
$ git submodule update --init --depth 1
$ cd ./connectedhomeip/connectedhomeip
$ ./scripts/checkout_submodules.py --platform esp32 linux --shallow
$ cd ../..
$ ./install.sh
$ cd ..

### Build FW matter light device

$ cd esp-matter; source source ./export.sh; cd cd examples/light; idf.py build

### Flash FW to matter light device
$ idf.py -p /dev/ttyACM1 flash monitor

### Commisioning matter light device to Thread network

#### Get dataset data
$ sudo ot-ctl dataset active -x
#### Pairing
$ chip-tool pairing ble-thread 0x7283 hex:<your_dataset> 20202021 3840
#### Toogle LED
$ chip-tool onoff toggle 0x7283 0x1