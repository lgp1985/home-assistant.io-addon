FROM alpine:latest

# Add env
ENV LANG=C.UTF-8
RUN apk add --no-cache \
    jq \
    py3-pip \
    clang \
    libgcc \
    gcc-gnat \
    libgc++ \
    g++ \
    make \
    libffi-dev \
    openssl-dev \
    mosquitto \
    mosquitto-dev \
    mosquitto-libs \
    mosquitto-clients \
    python3 \
    python3-dev

RUN python3 -m venv /opt/venv \
    && /opt/venv/bin/pip install -U pyaes broadlink pycrypto
ENV PATH="/opt/venv/bin:$PATH"

# Copy data for add-on
COPY run.sh /
RUN chmod a+x /run.sh

CMD [ "sh", "/run.sh" ]