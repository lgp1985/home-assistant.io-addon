# FROM mcr.microsoft.com/vscode/devcontainers/cpp:debian AS prepare-wiringpi
# ENV DEBIAN_FRONTEND=noninteractive
# WORKDIR /tmp/
# RUN git clone https://github.com/WiringPi/WiringPi.git
# WORKDIR /tmp/WiringPi
# RUN ./build debian

FROM mcr.microsoft.com/vscode/devcontainers/cpp:debian AS prepare-433utils
ENV DEBIAN_FRONTEND=noninteractive
WORKDIR /tmp/
RUN wget https://github.com/WiringPi/WiringPi/releases/download/3.16/wiringpi_3.16_arm64.deb
RUN dpkg -i wiringpi_3.16_arm64.deb

RUN git clone --recursive https://github.com/ninjablocks/433Utils.git
WORKDIR /tmp/433Utils/RPi_utils
RUN make

RUN whereis wiringpi
RUN ./RFSniffer -h


FROM mcr.microsoft.com/vscode/devcontainers/cpp:debian AS prepare-wiringpi
ENV DEBIAN_FRONTEND=noninteractive
WORKDIR /tmp/
RUN wget https://github.com/WiringPi/WiringPi/releases/download/3.16/wiringpi_3.16_arm64.deb
RUN dpkg-deb -x wiringpi_3.16_arm64.deb wiringpi_3.16_arm64
RUN rm wiringpi_3.16_arm64.deb
RUN tar czf wiringpi_3.16_arm64.tar.gz wiringpi_3.16_arm64
RUN tree