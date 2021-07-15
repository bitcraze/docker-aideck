FROM ubuntu:18.04


ENV DEBIAN_FRONTEND noninteractive 
ARG GAP_SDK_VERSION=3.8.1
RUN echo $GAP_SDK_VERSION
RUN test -n "$GAP_SDK_VERSION"

# Install needed packages
RUN apt-get update && apt-get install -y wget git make cmake gcc
# ... for the gap8
RUN apt-get install -y build-essential  libftdi-dev libftdi1 doxygen python3-pip libsdl2-dev curl cmake libusb-1.0-0-dev scons gtkwave libsndfile1-dev rsync autoconf automake texinfo libtool pkg-config libsdl2-ttf-dev 
# ... for the nina
RUN apt-get install -y libncurses-dev flex bison gperf python python-pip python-setuptools python-serial python-cryptography python-future python-pyparsing curl

## GAP SDK BUILD ENVIRONMENT
# Install gap8 openocd
RUN git clone https://github.com/GreenWaves-Technologies/gap8_openocd.git; cd gap8_openocd; ./bootstrap; ./configure --program-prefix=gap8- --prefix=/usr --datarootdir=/usr/share/gap8-openocd; make -j; make -j install

# Install toolchain
RUN git clone https://github.com/GreenWaves-Technologies/gap_riscv_toolchain_ubuntu_18.git; cd gap_riscv_toolchain_ubuntu_18 && ./install.sh "/usr/lib/gap_riscv_toolchain" && cd ..

# Install Gap SDK
RUN git clone https://github.com/GreenWaves-Technologies/gap_sdk.git; cd gap_sdk && git fetch --all --tags && git checkout tags/release-v${GAP_SDK_VERSION} && git submodule update --init --recursive

RUN pip3 install -U pip && cd gap_sdk && pip3 install -r requirements.txt; 

# These are needed since python2 is used by standard in 18.04
RUN apt-get install -y python-pip
RUN python -m pip install configparser

RUN /bin/bash -c "cd gap_sdk/; source configs/ai_deck.sh; ls; make all;"

RUN cd gap_sdk; pip3 install -r tools/nntool/requirements.txt
RUN pip3 install tensorflow==1.15.2
RUN pip3 install h5py==2.10.0

RUN /bin/bash -c "cd gap_sdk/; source configs/ai_deck.sh; ls; make nntool"


RUN apt-get update && apt-get install -y libopencv-dev python3-opencv

RUN /bin/bash -c "cd gap_sdk/; source configs/ai_deck.sh; ls; make gap_tools"

## NINA ESP BUILD ENVIRONMENT
RUN curl -O https://dl.espressif.com/dl/xtensa-esp32-elf-linux64-1.22.0-80-g6c4433a-5.2.0.tar.gz

RUN ls && tar -xzf xtensa-esp32-elf-linux64-1.22.0-80-g6c4433a-5.2.0.tar.gz

ENV PATH="/xtensa-esp32-elf/bin:$PATH"

RUN git clone -b v3.3.1 --recursive https://github.com/espressif/esp-idf.git

ENV IDF_PATH '/esp-idf'

RUN pip install -r $IDF_PATH/requirements.txt

RUN pip install virtualenv==16.7.10

RUN cd esp-idf && ./install.sh

RUN /bin/bash -c "cd esp-idf && . ./export.sh"

RUN curl -OL https://github.com/espressif/openocd-esp32/releases/download/v0.10.0-esp32-20190313/openocd-esp32-linux64-0.10.0-esp32-20190313.tar.gz

RUN tar -xzf openocd-esp32-linux64-0.10.0-esp32-20190313.tar.gz

RUN apt-get -y install make libtool pkg-config autoconf automake texinfo libusb-1.0

##

RUN mkdir -p /module/data/
WORKDIR /module/data/