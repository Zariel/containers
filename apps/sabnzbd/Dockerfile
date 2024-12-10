FROM docker.io/library/python:3.13-alpine

ARG TARGETPLATFORM
ARG VERSION
ARG CHANNEL

ENV \
    PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    PIP_ROOT_USER_ACTION=ignore \
    PIP_NO_CACHE_DIR=1 \
    PIP_DISABLE_PIP_VERSION_CHECK=1 \
    PIP_BREAK_SYSTEM_PACKAGES=1 \
    CRYPTOGRAPHY_DONT_BUILD_RUST=1

ENV SABNZBD__PORT=8080

ENV UMASK="0002" \
    TZ="Etc/UTC"

USER root
WORKDIR /app

RUN \
    apk add --no-cache \
        bash \
        ca-certificates \
        catatonit \
        curl \
        jq \
        nano \
        p7zip \
        tzdata \
    && \
    apk add --no-cache --virtual=.build-deps \
        build-base \
        cargo \
        libffi-dev \
        musl-dev \
        openssl-dev \
    && \
    mkdir -p /app \
    && \
    curl -fsSL "https://github.com/sabnzbd/sabnzbd/releases/download/${VERSION}/SABnzbd-${VERSION}-src.tar.gz" \
        | tar xzf - -C /app --strip-components=1 \
    && \
    pip install uv \
    && \
    uv pip install --system --requirement /app/requirements.txt \
    && \
    chown -R root:root /app \
    && chmod -R 755 /app \
    && apk del --purge .build-deps \
    && rm -rf \
        /root/.cache \
        /root/.cargo \
        /tmp/*

COPY ./apps/sabnzbd/sabnzbd.ini /app/sabnzbd.ini
COPY ./apps/sabnzbd/entrypoint.sh /entrypoint.sh
COPY --from=ghcr.io/zariel/par2cmdline-turbo:1.2.0@sha256:bb305084ad4d315d5e9cf1fc22052217721b841d8c08c6948c9f19a8b3f9424e /usr/local/bin/par2 /usr/local/bin/par2
RUN ln -s /usr/local/bin/par2 /usr/local/bin/par2create \
    && ln -s /usr/local/bin/par2 /usr/local/bin/par2repair \
    && ln -s /usr/local/bin/par2 /usr/local/bin/par2verify
COPY --from=ghcr.io/linuxserver/unrar:7.0.9@sha256:829a50d4ecd38b665b7239bb862857e52b7e9f389426329a8e54876afded8454 /usr/bin/unrar-alpine /usr/bin/unrar

USER nobody:nogroup
WORKDIR /config
VOLUME ["/config"]

ENTRYPOINT ["/usr/bin/catatonit", "--"]
CMD ["/entrypoint.sh"]

LABEL org.opencontainers.image.source="https://github.com/sabnzbd/sabnzbd"
