FROM ubuntu:22.04

ARG UNAME=vscode
ARG UNAME_PASSWORD=pass
ARG SDK_VERSION=0.15.1

RUN useradd -ms /bin/bash $UNAME 
RUN usermod -aG sudo $UNAME
RUN usermod -aG dialout $UNAME

ARG DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get upgrade -y; apt-get install --no-install-recommends -y wget python3-pip git cmake ninja-build gperf  \
  ccache dfu-util device-tree-compiler \
  python3-dev python3-pip python3-setuptools python3-tk python3-wheel xz-utils file  \
  make gcc gcc-multilib g++-multilib libsdl2-dev libmagic1 udev clangd ssh nano bash-completion sudo gpg ruby lcov screen uncrustify libfftw3-dev

RUN apt-get install --no-install-recommends -y aria2

RUN aria2c https://www.nordicsemi.com/-/media/Software-and-other-downloads/Desktop-software/nRF-command-line-tools/sw/Versions-10-x-x/10-17-3/nrf-command-line-tools-10.17.3_Linux-amd64.tar.gz && \
tar -xvf nrf-command-line-tools-10.17.3_Linux-amd64.tar.gz
RUN dpkg -i JLink_Linux_V766a_x86_64.deb; apt-get --fix-broken install -y

RUN aria2c https://www.nordicsemi.com/-/media/Software-and-other-downloads/Desktop-software/nRF-command-line-tools/sw/Versions-10-x-x/10-17-3/nrf-command-line-tools_10.17.3_amd64.deb
RUN dpkg -i nrf-command-line-tools_10.17.3_amd64.deb; apt-get --fix-broken install -y

RUN mkdir /tmp/pip-nrf; aria2c -d /tmp/pip-nrf https://raw.githubusercontent.com/nrfconnect/sdk-nrf/main/scripts/requirements-base.txt \
https://raw.githubusercontent.com/nrfconnect/sdk-nrf/main/scripts/requirements-build.txt \
https://raw.githubusercontent.com/nrfconnect/sdk-nrf/main/scripts/requirements-doc.txt \
https://raw.githubusercontent.com/nrfconnect/sdk-nrf/main/scripts/requirements.txt 

RUN mkdir /tmp/pip-zephyr; aria2c -d /tmp/pip-zephyr https://raw.githubusercontent.com/nrfconnect/sdk-zephyr/main/scripts/requirements-base.txt \
https://raw.githubusercontent.com/nrfconnect/sdk-zephyr/main/scripts/requirements-build-test.txt \
https://raw.githubusercontent.com/nrfconnect/sdk-zephyr/main/scripts/requirements-doc.txt \
https://raw.githubusercontent.com/nrfconnect/sdk-zephyr/main/scripts/requirements-run-test.txt \
https://raw.githubusercontent.com/nrfconnect/sdk-zephyr/main/scripts/rrequirements-extras.txt \
https://raw.githubusercontent.com/nrfconnect/sdk-zephyr/main/scripts/requirements-compliance.txt \
https://raw.githubusercontent.com/nrfconnect/sdk-zephyr/main/scripts/requirements.txt

RUN mkdir /tmp/pip-mcuboot; aria2c -d /tmp/pip-mcuboot  https://raw.githubusercontent.com/nrfconnect/sdk-mcuboot/main/scripts/requirements.txt

RUN pip3 install -r /tmp/pip-nrf/requirements.txt -r /tmp/pip-zephyr/requirements.txt -r /tmp/pip-mcuboot/requirements.txt

RUN aria2c https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v${SDK_VERSION}/zephyr-sdk-${SDK_VERSION}_linux-x86_64.tar.gz
RUN tar -xvf zephyr-sdk-${SDK_VERSION}_linux-x86_64.tar.gz && mv zephyr-sdk-${SDK_VERSION} /opt
RUN /opt/zephyr-sdk-${SDK_VERSION}/setup.sh -h -c

RUN echo 'export PATH=$PATH:/home/'"$UNAME"'/.local/bin' >> /home/$UNAME/.bashrc

RUN echo "$UNAME:$UNAME_PASSWORD" | chpasswd 
USER $UNAME
RUN mkdir -p /home/$UNAME/work/ncs
