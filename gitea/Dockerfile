ARG BUILD_FROM
FROM ${BUILD_FROM}

ENV LANG C.UTF-8
ARG GITEA_VERSION
ARG BUILD_ARCH

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

#EXPOSE ${SSH_PORT} ${HTTP_PORT}

RUN apk --no-cache add \
    su-exec \
    ca-certificates \
    sqlite \
    bash \
    git \
    subversion \
    linux-pam \
    s6 \
    curl \
    openssh \
    tzdata \
    gettext && \
    PATH=$PATH:/usr/bin/ 

RUN addgroup \
    -S -g 1000 \
    git && \
  adduser \
    -S -H -D \
    -h /data/git \
    -s /bin/bash \
    -u 1000 \
    -G git \
    git && \
  echo "git:$(dd if=/dev/urandom bs=24 count=1 status=none | base64)" | chpasswd

ENV USER git
ENV GITEA_CUSTOM /data/git
ENV GODEBUG=netdns=go

VOLUME ["/data"]

RUN  \
     ARCH="${BUILD_ARCH}" \
     && if [ "${BUILD_ARCH}" = "aarch64" ]; then ARCH="arm64"; fi \
     && if [ "${BUILD_ARCH}" = "armv7" ]; then ARCH="arm-6"; fi \
     && if [ "${BUILD_ARCH}" = "armhf" ]; then ARCH="arm-5"; fi \
     && if [ "${BUILD_ARCH}" = "amd64" ]; then ARCH="amd64"; fi \
     && if [ "${BUILD_ARCH}" = "i368" ]; then ARCH="368"; fi \
     \
    && mkdir -p /app/gitea \
    && curl -SLo /app/gitea/gitea https://github.com/go-gitea/gitea/releases/download/v${GITEA_VERSION}/gitea-${GITEA_VERSION}-linux-${ARCH}  \
    && chmod 0755 /app/gitea/gitea \
    && ln -s /app/gitea/gitea /usr/local/bin/gitea \
    && apk del subversion  \
    && rm -fr \
        /tmp/* \
        /var/{cache,log}/* \
        /var/lib/apt/lists/* 

COPY rootfs /

RUN for file in /etc/services.d/*/*; do \
dos2unix $file; \
chmod a+xwr $file; \
done

# Build arguments
ARG BUILD_DATE
ARG BUILD_REF
ARG BUILD_VERSION
# Labels
LABEL \
 io.hass.name="Gitea" \
 io.hass.description="Git with a cup of tea on Home Assistant! Based on https://github.com/go-gitea/gitea ." \
 io.hass.arch="${BUILD_ARCH}" \
 io.hass.type="addon" \
 io.hass.version=${BUILD_VERSION} \
 maintainer="tb-killa" \
 org.label-schema.description="Easy personal git server." \
 org.label-schema.build-date=${BUILD_DATE} \
 org.label-schema.name="Gitea" \
 org.label-schema.schema-version="1.0" \
 org.label-schema.vendor="Community Hass.io Add-ons"
