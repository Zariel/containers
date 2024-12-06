FROM docker.io/library/alpine:3.21 as par2
ARG TARGETARCH
ARG VERSION
ENV LDFLAGS="-static"
ENV CXXFLGS="-march=znver2 -O3 -pipe -fstack-protector-strong -flto"
USER root
WORKDIR /tmp
RUN \
    apk add --no-cache \
        autoconf \
        automake \
        build-base \
        curl \
        linux-headers \
        musl-dev \
    && \
    curl -fsSL "https://github.com/animetosho/par2cmdline-turbo/archive/refs/tags/v${VERSION}.tar.gz" \
        | tar xzf - -C /tmp --strip-components 1 \
    && ./automake.sh \
    && ./configure --build=amd64-linux-musl \
    && make \
    && make install

FROM scratch

COPY --from=par2 /usr/local/bin/par2 /usr/local/bin/par2
RUN ["/usr/local/bin/par2", "--help"]

CMD ["/usr/local/bin/par2"]
