ARG BUILD_FROM
FROM ${BUILD_FROM}

ENV LANG C.UTF-8
ARG GITEA_VERSION
ARG BUILD_ARCH

# Set shell
SHELL ["/bin/bash", "-o", "pipefail", "-c"]

EXPOSE ${SSH_PORT} ${HTTP_PORT}

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

COPY data /data
VOLUME ["/data"]
#ENTRYPOINT ["/usr/bin/entrypoint"]
#CMD ["/bin/s6-svscan", "/etc/s6"]

RUN  \
    ## GET GITEA
    && mkdir -p /app/gitea \
    && curl -SLo /app/gitea/gitea https://github.com/go-gitea/gitea/releases/download/v${GITEA_VERSION}/gitea-${GITEA_VERSION}-linux-${BUILD_ARCH}  \
    && echo "https://github.com/go-gitea/gitea/releases/download/v${GITEA_VERSION}/gitea-${GITEA_VERSION}-linux-${BUILD_ARCH}" \
    && chmod 0755 /app/gitea/gitea \
    && ln -s /app/gitea/gitea /usr/local/bin/gitea
    ## Cleaning
    && apk del subversion  \
    && rm -fr \
        /tmp/* \
        /var/{cache,log}/* \
        /var/lib/apt/lists/* 

COPY rootfs /

#COPY rootfs/etc/s6 /etc/s6
#COPY rootfs/etc/templates /etc/templates
#COPY rootfs/etc/nsswitch.conf /etc/
#COPY rootfs/usr/bin/entrypoint /usr/bin/
#RUN ls -laR /etc/s6
