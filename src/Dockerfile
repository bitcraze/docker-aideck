FROM ubuntu:20.04


ENV DEBIAN_FRONTEND noninteractive
ARG GAP_SDK_VERSION=4.8.0
RUN echo $GAP_SDK_VERSION
RUN test -n "$GAP_SDK_VERSION"
RUN echo "export GAP_SDK_VERSION=${GAP_SDK_VERSION}" >> ~/.bashrc

RUN apt-get update

 # Install needed packages
RUN apt-get install -y build-essential \
    autoconf \
    automake \
    bison \
    build-essential \
    cmake \
    curl \
    doxygen \
    flex \
    git \
    gtkwave \
    libftdi-dev \
    libftdi1 \
    libjpeg-dev \
    libsdl2-dev \
    libsdl2-ttf-dev \
    libsndfile1-dev \
    libtool \
    libusb-1.0-0-dev \
    pkg-config \
    python3-pip \
    rsync \
    scons \
    texinfo \
    wget


RUN update-alternatives --install /usr/bin/python python /usr/bin/python3 10


# Install gap8 openocd
RUN git clone https://github.com/GreenWaves-Technologies/gap8_openocd.git; cd gap8_openocd; ./bootstrap; ./configure --program-prefix=gap8- --prefix=/usr --datarootdir=/usr/share/gap8-openocd; make -j; make -j install
RUN apt-get install -y udev
 RUN cp /usr/share/gap8-openocd/openocd/contrib/60-openocd.rules /etc/udev/rules.d; udevadm trigger

# # Install toolchain
RUN git clone https://github.com/GreenWaves-Technologies/gap_riscv_toolchain_ubuntu_18.git; cd gap_riscv_toolchain_ubuntu_18 && ./install.sh "/usr/lib/gap_riscv_toolchain" && cd ..

# # Install Gap SDK
RUN git clone https://github.com/GreenWaves-Technologies/gap_sdk.git; cd gap_sdk; git fetch --all --tags; git checkout tags/release-v${GAP_SDK_VERSION}; git submodule update --init --recursive

RUN apt-get install -y protobuf-compiler
RUN pip3 install numpy cython
RUN cd /gap_sdk; pip3 install -r requirements.txt
RUN cd /gap_sdk/tools/nntool/; pip3 install -r requirements.txt

# # These are needed since python2 is used by standard in 18.04

RUN /bin/bash -c "cd gap_sdk/; source configs/ai_deck.sh; ls; make -f gap8/gap8.mk minimal"
RUN /bin/bash -c "cd gap_sdk/; source configs/ai_deck.sh; ls; make -f gap8/gap8.mk gvsoc;"
RUN /bin/bash -c "cd gap_sdk/; source configs/ai_deck.sh; ls; make -f gap8/gap8.mk openocd.all;"
RUN /bin/bash -c "cd gap_sdk/; source configs/ai_deck.sh; ls; make -f gap8/gap8.mk littlefs.all ;"
RUN /bin/bash -c "cd gap_sdk/; source configs/ai_deck.sh; ls; make -f gap8/gap8.mk nntool"
RUN /bin/bash -c "cd gap_sdk/; source configs/ai_deck.sh; ls; make -f gap8/gap8.mk freertos.all freertos.checkout"
RUN /bin/bash -c "cd gap_sdk/; source configs/ai_deck.sh; ls; make -f gap8/gap8.mk pulp-os"
RUN /bin/bash -c "cd gap_sdk/; source configs/ai_deck.sh; ls; make -f gap8/gap8.mk pmsis-bsp.build"

COPY freeRTOS_rules.mk /gap_sdk/rtos/freeRTOS/vendors/gwt/rules/freeRTOS_rules.mk
COPY flash_partition.c /gap_sdk/rtos/pmsis/pmsis_bsp/partition/flash_partition.c

WORKDIR /module
