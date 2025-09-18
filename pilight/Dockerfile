FROM alpine:latest AS get-pilight.key
RUN apk add --no-cache gnupg
RUN mkdir -p /usr/share/keyrings
RUN wget -qO- http://apt.pilight.org/pilight.key | gpg --dearmor | tee /usr/share/keyrings/pilight-archive-keyring.gpg >/dev/null

WORKDIR /tmp
# RUN wget http://apt.pilight.org/pool/stable/main/l/libmbedx509-0/libmbedx509-0_2.6.0-1_armhf.deb
# RUN wget http://apt.pilight.org/pool/stable/main/l/libmbedtls10/libmbedtls10_2.6.0-1_armhf.deb
# RUN wget http://apt.pilight.org/pool/stable/main/l/libmbedcrypto0/libmbedcrypto0_2.6.0-1_armhf.deb

FROM alpine:latest AS get-debs
WORKDIR /tmp
RUN wget http://launchpadlibrarian.net/365186633/libmbedcrypto1_2.8.0-1_arm64.deb
RUN wget http://ftp.de.debian.org/debian/pool/main/m/mbedtls/libmbedx509-0_2.16.9-0.1_arm64.deb
RUN wget http://ftp.de.debian.org/debian/pool/main/m/mbedtls/libmbedcrypto3_2.16.9-0.1_arm64.deb
RUN wget http://launchpadlibrarian.net/365186634/libmbedtls10_2.8.0-1_arm64.deb
RUN wget http://launchpadlibrarian.net/234309895/libmbedcrypto0_2.2.1-2_arm64.deb

FROM alpine:latest AS get-libpcap
WORKDIR /tmp
RUN wget https://www.tcpdump.org/release/libpcap-1.10.5.tar.gz
RUN tar xzf libpcap-1.10.5.tar.gz
RUN rm libpcap-1.10.5.tar.gz

FROM alpine:latest AS get-flex
WORKDIR /tmp
RUN wget https://github.com/westes/flex/releases/download/v2.6.4/flex-2.6.4.tar.gz
RUN tar xzf flex-2.6.4.tar.gz
RUN rm flex-2.6.4.tar.gz

FROM debian:trixie-slim AS base-builder
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y build-essential cmake git dialog

FROM base-builder AS build-flex
RUN apt-get install -y m4
COPY --from=get-flex /tmp/* /tmp/flex
WORKDIR /tmp/flex
RUN ./configure
RUN make

FROM base-builder AS build-libpcap

# RUN --mount=type=cache,from=build-flex,source=/tmp/,target=/tmp/flex \
#         cd /tmp/flex; \
#         make install
COPY --from=build-flex /tmp/* /tmp/flex/
WORKDIR /tmp/flex
RUN ls -la
RUN make install
RUN ldconfig

COPY --from=get-libpcap /tmp/* /tmp/libpcap
WORKDIR /tmp/libpcap
RUN cd ..; ls -la
RUN ./configure
RUN make

FROM base-builder AS wiringx
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && apt-get install -y --reinstall ca-certificates && update-ca-certificates

RUN mkdir -p /usr/share/keyrings
COPY --from=get-pilight.key /usr/share/keyrings/pilight-archive-keyring.gpg /usr/share/keyrings/pilight-archive-keyring.gpg
RUN echo "deb [signed-by=/usr/share/keyrings/pilight-archive-keyring.gpg] http://apt.pilight.org/ stable main" \
        > /etc/apt/sources.list.d/pilight.list;
RUN apt-get update && apt-get install -y libwiringx libwiringx-dev

COPY --from=get-debs /tmp/* /tmp/
WORKDIR /tmp
RUN dpkg -i libmbed*.deb

RUN apt-get install -y libpcap0.8-dev libmbedtls-dev liblua5.2-dev libluajit-5.1-dev
# RUN apt-get install -y libpcap0.8t64

RUN --mount=type=bind,from=build-libpcap,source=/tmp/,target=/tmp/libpcap make install


# COPY --from=get-libpcap /tmp/* /tmp/
# WORKDIR /tmp
# RUN ./configure
# RUN make
# RUN make install

# WORKDIR /
# RUN git clone https://luajit.org/git/luajit.git
# WORKDIR /luajit
# RUN make install

# RUN ldconfig

# WORKDIR /
# RUN git clone --depth 5 -b master https://www.github.com/pilight/pilight.git
# WORKDIR /pilight
# RUN sed -i 's/luajit-2.0/luajit-2.1/g' libs/pilight/lua_c/lua.h
# RUN sed -i 's/2.8.8/2.8.12/g' CMakeLists.txt
# RUN chmod +x setup.sh
# RUN ./setup.sh install
# RUN ldconfig