FROM debian:trixie-slim

RUN set -eux; \
    echo "deb [signed-by=/usr/share/keyrings/pilight-archive-keyring.gpg] http://apt.pilight.org/ stable main" \
        > /etc/apt/sources.list.d/pilight.list; \
    apt-get update; \
    apt-get install -y --no-install-recommends curl gnupg dirmngr ca-certificates; \
    mkdir -p /usr/share/keyrings; \
    curl -fsSL http://apt.pilight.org/pilight.key | gpg --dearmor > /usr/share/keyrings/pilight-archive-keyring.gpg; \
    apt-get remove -y --purge curl gnupg dirmngr; \
    apt-get autoremove -y; \
    rm -rf /var/lib/apt/lists/*;

RUN apt-get update && apt-get install -y pilight

EXPOSE 5000
EXPOSE 5001
ENTRYPOINT [ "/usr/local/sbin/pilight-daemon", "-F" ]