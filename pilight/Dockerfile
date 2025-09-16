FROM alpine:latest AS alpine
RUN wget -O toolchain-amd64.cmake https://download.pilight.org/cross-compilers/toolchain-amd64.cmake
RUN sed -i 's/x86_64-unknown-linux-gnu-//g' toolchain-amd64.cmake


FROM mcr.microsoft.com/vscode/devcontainers/cpp:debian AS build-wiringx
# Set noninteractive mode to avoid TTY issues
ENV DEBIAN_FRONTEND=noninteractive

RUN git clone --depth 5 -b master https://github.com/wiringX/wiringX.git/

WORKDIR /wiringX
RUN mkdir build; \ 
    cd build; \
    cmake ..; \
    make; \
    cpack -G DEB

FROM mcr.microsoft.com/vscode/devcontainers/cpp:debian AS build-pilight
# Set noninteractive mode to avoid TTY issues
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    libpcap-dev \
    libmbedtls-dev \
    libluajit-5.1-2

COPY --from=build-wiringx /wiringX/build/libwiringx*.deb /tmp/
RUN dpkg -i /tmp/libwiringx*.deb

RUN mkdir -p /home/pilight/tools/
COPY --from=alpine toolchain-amd64.cmake /home/pilight/tools/toolchain-amd64.cmake
RUN git clone --depth 5 -b master https://github.com/pilight/pilight.git/

WORKDIR /pilight
RUN chmod +x compile_all.sh
RUN ./compile_all.sh

EXPOSE 5000
EXPOSE 5001
ENTRYPOINT [ "/usr/local/sbin/pilight-daemon", "-F" ]