FROM docker.io/library/node:20-alpine AS build

ARG TARGETPLATFORM
ARG VERSION
ARG CHANNEL

ENV UMASK="0002" \
    TZ="Etc/UTC"

USER root
WORKDIR /app

#hadolint ignore=DL3018,DL3059
RUN \
    apk add --no-cache \
        python3 \
        make \
        g++ \
        gcc \
        libc6-compat \
        bash \
        tzdata \
        curl \
        catatonit \
        tar \
    && \
    case "${TARGETPLATFORM}" in \
        'linux/amd64') export ARCH='x64' ;; \
        'linux/arm64') export ARCH='arm64' ;; \
    esac \
    && \
    mkdir -p /app \
    && \
    curl -fsSL "https://github.com/Fallenbagel/jellyseerr/archive/refs/tags/v${VERSION}.tar.gz" \
        | tar xzf - -C /app --strip-components=1 \
    && npm install --global node-gyp pnpm \
    && CYPRESS_INSTALL_BINARY=0 pnpm install --frozen-lockfile \
    && pnpm build && pnpm prune --prod --ignore-scripts \
    && touch config/DOCKER \
    && echo "{\"commitTag\": \"${VERSION}\"}" > committag.json \
    && chown -R root:root /app \
    && chmod -R 755 /app \
    && rm -rf src server .next/cache \
    && rm -rf /tmp/*


FROM docker.io/library/node:20-alpine

USER root
WORKDIR /app

COPY --from=build /app /app

RUN \
    apk add --no-cache \
        bash \
        tzdata \
        catatonit \
    && \
    case "${TARGETPLATFORM}" in \
        'linux/amd64') export ARCH='x64' ;; \
        'linux/arm64') export ARCH='arm64' ;; \
    esac \
    && npm install --global pnpm \
    && rm -rf /tmp/*

COPY ./apps/jellyseerr/entrypoint.sh /entrypoint.sh

ENTRYPOINT ["/usr/bin/catatonit", "--"]
CMD ["/entrypoint.sh"]

LABEL org.opencontainers.image.source="https://github.com/Fallenbagel/jellyseerr"
