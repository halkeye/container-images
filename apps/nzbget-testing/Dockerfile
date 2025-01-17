# hadolint ignore=DL3007
FROM ghcr.io/k8s-at-home/ubuntu:latest

ARG VERSION

USER root

SHELL ["/bin/bash", "-o", "pipefail", "-c"]

# hadolint ignore=DL3008,DL3013,DL3015,SC2086
RUN \
  export EXTRA_INSTALL_ARG="build-essential libxml2-dev jq libxslt1-dev python3-dev python3-pip"; \
  apt-get -qq update \
  && \
  apt-get -qq install -y \
    p7zip-full \
    python3 \
    unrar \
    unzip \
    wget \
    ${EXTRA_INSTALL_ARG} \
  && \
  pip3 install --no-cache-dir -U \
    apprise \
    chardet \
    pynzbget \
    rarfile \
  && \
  ln -s /usr/bin/python3 /usr/bin/python \
  && \
  RELEASE=$(curl -sX GET "https://api.github.com/repos/nzbget/nzbget/releases" | jq --raw-output ". | map(select(.tag_name == \"v${VERSION}\"))[0].name") \
  && \
  curl -fsSL -o /tmp/nzbget.run \
    "https://github.com/nzbget/nzbget/releases/download/v${VERSION}/nzbget-${RELEASE}-bin-linux.run" \
  && \
  sh /tmp/nzbget.run --destdir /app \
  && \
  apt-get remove -y ${EXTRA_INSTALL_ARG} \
  && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
  && apt-get autoremove -y \
  && apt-get clean \
  && \
  rm -rf \
    /tmp/* \
    /var/lib/apt/lists/* \
    /var/tmp/ \
  && chown -R kah:kah /app \
  && chmod -R u=rwX,go=rX /app \
  && printf "umask %d" "${UMASK}" >> /etc/bash.bashrc \
  && update-ca-certificates

USER kah

EXPOSE 6789

COPY ./apps/nzbget/entrypoint.sh /entrypoint.sh
CMD ["/entrypoint.sh"]
