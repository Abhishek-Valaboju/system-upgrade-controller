# Install the all-in-one binary so we can copy our run-time images into the image
# which helps avoid pulling them when running e2e tests.
ARG BCI="registry.suse.com/bci/bci-base:16.1"
FROM ${BCI} AS k3s
ARG ARCH
# renovate: datasource=github-release-attachments depName=k3s-io/k3s
ARG K3S_VERSION="v1.37.0-rc4+k3s1"
# renovate: datasource=github-release-attachments depName=k3s-io/k3s digestVersion=v1.37.0-rc4+k3s1
ARG K3S_SUM_amd64="0601f69e304d63a26a7ea19ba77b197b594dc72d33200da3960d802c4adb8ceb"
# renovate: datasource=github-release-attachments depName=k3s-io/k3s digestVersion=v1.37.0-rc4+k3s1
ARG K3S_SUM_arm64="d8583948721cf7f500a504e172d7efd3eee8d869d649e1f0d49c80f73535c6d4"
# renovate: datasource=github-release-attachments depName=k3s-io/k3s digestVersion=v1.37.0-rc4+k3s1
ARG K3S_SUM_arm="50304c7fd48c201878c14ce72ab995c5635b0a0bbca2cce4c1bb563a6888383e"
RUN set -x \
   && zypper -n in \
   ca-certificates \
   curl \
   && case "${ARCH?required}" in \
   amd64) \
   K3S_ASSET="k3s"; \
   K3S_SUM="${K3S_SUM_amd64}"; \
   ;; \
   arm64) \
   K3S_ASSET="k3s-arm64"; \
   K3S_SUM="${K3S_SUM_arm64}"; \
   ;; \
   arm) \
   K3S_ASSET="k3s-armhf"; \
   K3S_SUM="${K3S_SUM_arm}"; \
   ;; \
   *) \
   echo "unsupported ARCH ${ARCH}" >&2; \
   exit 1; \
   ;; \
   esac \
   && curl -fsSL "https://github.com/k3s-io/k3s/releases/download/${K3S_VERSION}/${K3S_ASSET}" > /bin/k3s \
   && echo "${K3S_SUM}  /bin/k3s" | sha256sum -c - \
   && chmod +x /bin/k3s \
   && ln -s /bin/k3s /bin/kubectl \
   && ln -s /bin/k3s /bin/ctr \
   && k3s --version

COPY scratch/*-${ARCH}.tar /var/lib/rancher/k3s/agent/images/

VOLUME /var/lib/cni
VOLUME /var/lib/kubelet
VOLUME /var/lib/rancher/k3s
VOLUME /var/log

ENTRYPOINT ["k3s"]
CMD ["server"]
