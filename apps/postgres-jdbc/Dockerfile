FROM docker.io/library/alpine:3.19 as build
ARG TARGETARCH
ARG VERSION
ENV LDFLAGS="-static"
USER root
WORKDIR /tmp
RUN \
    apk add --no-cache \
        curl \
    && \
        curl -Lfs -o /tmp/postgres.jar https://jdbc.postgresql.org/download/postgresql-42.7.3.jar

FROM scratch

COPY --from=build /tmp/postgres.jar /postgres.jar
