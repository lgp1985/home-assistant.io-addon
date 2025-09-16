FROM alpine:latest AS prepare
RUN wget -O toolchain-arm.cmake https://download.pilight.org/cross-compilers/toolchain-arm.cmake
RUN sed -i 's/arm-unknown-linux-gnueabihf-//g' toolchain-arm.cmake

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


# FROM nickblah/lua:5-alpine3 AS build-lua
# FROM mcr.microsoft.com/vscode/devcontainers/cpp:debian AS build-luajit
# # Set noninteractive mode to avoid TTY issues
# ENV DEBIAN_FRONTEND=noninteractive
# RUN git clone https://luajit.org/git/luajit.git
# WORKDIR /luajit
# RUN make install

# RUN tree

# ENTRYPOINT [ "sh", "-c", "while true; do sleep 1000; done" ]

FROM mcr.microsoft.com/vscode/devcontainers/cpp:debian AS build-pilight
# Set noninteractive mode to avoid TTY issues
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update && apt-get install -y \
    libpcap-dev \
    libmbedtls-dev
RUN apt-get install -y liblua5.2-dev libluajit-5.1-dev

COPY --from=build-wiringx /wiringX/build/libwiringx*.deb /tmp/
RUN dpkg -i /tmp/libwiringx*.deb

RUN mkdir -p /home/pilight/tools/
COPY --from=prepare toolchain-arm.cmake /home/pilight/tools/toolchain-arm.cmake

WORKDIR /
RUN git clone --depth 5 -b master https://github.com/pilight/pilight.git/
WORKDIR /pilight
RUN sed -i 's/2.8.8/2.8.12/g' CMakeLists.txt
RUN sed -i 's/lua5.1/libluajit-5.1.so.2/g' CMakeLists.txt
RUN sed -i 's/luajit-2.0/luajit-2.1/g' libs/pilight/lua_c/lua.h

RUN chmod +x setup.sh
RUN ./setup.sh install
# RUN mkdir build; \ 
#     cd build; \
#     cmake -DCMAKE_TOOLCHAIN_FILE=/home/pilight/tools/toolchain-arm.cmake ..; \
#     make;

# FROM debian:trixie-slim as pilight
# # Set noninteractive mode to avoid TTY issues
# ENV DEBIAN_FRONTEND=noninteractive
# COPY --from=prepare /usr/share/keyrings/pilight-archive-keyring.gpg /usr/share/keyrings/pilight-archive-keyring.gpg

# COPY --from=build-wiringx /wiringX/build/libwiringx*.deb /tmp/
# RUN dpkg -i /tmp/libwiringx*.deb

# RUN apt-get update && apt-get install -y pilight

# EXPOSE 5000
# EXPOSE 5001
# ENTRYPOINT [ "/usr/local/sbin/pilight-daemon", "-F" ]