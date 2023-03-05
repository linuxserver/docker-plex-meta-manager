# syntax=docker/dockerfile:1

FROM ghcr.io/linuxserver/baseimage-alpine:arm64v8-3.17

# set version label
ARG BUILD_DATE
ARG VERSION
ARG PMM_VERSION
LABEL build_version="Linuxserver.io version:- ${VERSION} Build-date:- ${BUILD_DATE}"
LABEL maintainer="thespad"

# environment settings
ENV S6_STAGE2_HOOK=/init-hook
ENV HOME="/config" \
PYTHONIOENCODING=utf-8

RUN \
  echo "**** install packages ****" && \
  apk add -U --update --no-cache --virtual=build-dependencies \
    build-base \
    libffi-dev \
    libxml2-dev \
    libzen-dev \
    python3-dev && \
  apk add  -U --update --no-cache \
    grep \
    libjpeg \
    libxslt \    
    python3 && \
  if [ -z ${PMM_VERSION+x} ]; then \
    PMM_VERSION=$(curl -s https://api.github.com/repos/meisnate12/Plex-Meta-Manager/releases \
      | jq -r 'first(.[]) | .tag_name'); \
  fi && \
  mkdir -p /app/pmm && \
  curl -o \
    /tmp/pmm.tar.gz -L \
    "https://github.com/meisnate12/Plex-Meta-Manager/archive/refs/tags/${PMM_VERSION}.tar.gz" && \
  tar xf \
    /tmp/pmm.tar.gz -C \
    /app/pmm --strip-components=1 && \
  cd /app/pmm && \
  python3 -m ensurepip && \
  pip3 install -U --no-cache-dir \
    pip \
    wheel && \
  pip3 install -U --no-cache-dir --find-links https://wheel-index.linuxserver.io/alpine-3.17/ -r requirements.txt && \
  pip3 cache purge && \
  echo "**** cleanup ****" && \
  apk del --purge \
    build-dependencies && \
  rm -rf \
    /tmp/* \
    $HOME/.cache

# add local files
COPY root/ /

# ports and volumes
VOLUME /config

ENTRYPOINT ["/init-pmm"]
