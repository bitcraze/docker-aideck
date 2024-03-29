FROM ubuntu:18.04


ENV DEBIAN_FRONTEND noninteractive
ARG GAP_SDK_VERSION=3.9.1
RUN echo $GAP_SDK_VERSION
RUN test -n "$GAP_SDK_VERSION"

RUN apt-get update

# Install needed packages
RUN apt-get install -y build-essential \
    git \
    libftdi-dev \
    libftdi1 doxygen \
    python3-pip \
    libsdl2-dev \
    curl \
    wget \
    cmake \
    libusb-1.0-0-dev \
    scons \
    gtkwave \
    libsndfile1-dev \
    rsync \
    autoconf \
    automake \
    texinfo \
    libtool \
    pkg-config \
    libsdl2-ttf-dev \
    libjpeg-dev
RUN apt-get install -y wget

# Install gap8 openocd
RUN git clone https://github.com/GreenWaves-Technologies/gap8_openocd.git; cd gap8_openocd; ./bootstrap; ./configure --program-prefix=gap8- --prefix=/usr --datarootdir=/usr/share/gap8-openocd; make -j; make -j install

#RUN cp /usr/share/gap8-openocd/openocd/contrib/60-openocd.rules /etc/udev/rules.d; udevadm control --reload-rules && udevadm trigger

# Install toolchain
RUN git clone https://github.com/GreenWaves-Technologies/gap_riscv_toolchain_ubuntu_18.git; cd gap_riscv_toolchain_ubuntu_18 && ./install.sh "/usr/lib/gap_riscv_toolchain" && cd ..

# Install Gap SDK
RUN git clone https://github.com/GreenWaves-Technologies/gap_sdk.git; cd gap_sdk; git fetch --all --tags; git checkout tags/release-v${GAP_SDK_VERSION}; git submodule update --init --recursive

RUN apt-get install -y protobuf-compiler
#RUN /bin/bash -c "cd gap_sdk/; source configs/ai_deck.sh; ls; make nntool"
RUN pip3 install numpy config
RUN cd /gap_sdk; pip3 install -r requirements.txt;
RUN cd /gap_sdk/tools/nntool/; pip3 install -r requirements.txt

# These are needed since python2 is used by standard in 18.04
RUN apt-get install -y python-pip
RUN python -m pip install configparser


RUN /bin/bash -c "cd gap_sdk/; source configs/ai_deck.sh; ls; make all;"

RUN pip3 install --upgrade pip

RUN pip3 install tensorflow==1.15.2
RUN pip3 install h5py==2.10.0
RUN sed -i 's/required=True/ /g' /gap_sdk/tools/nntool/interpreter/commands/qtune.py

RUN /bin/bash -c "cd gap_sdk/; source configs/ai_deck.sh; ls; make nntool"


RUN apt-get update && apt-get install -y libopencv-dev python3-opencv

RUN /bin/bash -c "cd gap_sdk/; source configs/ai_deck.sh; ls; make gap_tools"

WORKDIR /module
