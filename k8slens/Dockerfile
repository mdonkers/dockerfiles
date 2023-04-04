# Run K8s Lens in a container
#
# Make sure .aws and .kube are 'group / other' readable
# Run as follows:
#
#   docker run -d \
#	-v /etc/localtime:/etc/localtime:ro \
#	-v /tmp/.X11-unix:/tmp/.X11-unix \
#	-e "DISPLAY=unix${DISPLAY}" \
#	-v "${HOME}/.aws:/home/lens/.aws:ro" \
#	-v "${HOME}/.kube:/home/lens/.kube:ro" \
#	-v /dev/shm:/dev/shm \
#	-v /etc/hosts:/etc/hosts \
#	--net host \
#	--device /dev/snd \
#	--device /dev/dri \
#	--device /dev/video0 \
#	--group-add audio \
#	--group-add video \
#	--security-opt "seccomp=electron.json" \
#	--name lens \
#	miel/lens
#
FROM node:16-bullseye AS builder

WORKDIR /build

RUN git clone -b v6.4.13 --depth 1 https://github.com/lensapp/lens.git \
  && cd lens/ \
  && npm install \
  && npm run build \
  && ELECTRON_BUILDER_EXTRA_ARGS="--linux dir" yarn lerna run build:dir


FROM node:16-bullseye AS extension-builder

WORKDIR /build

RUN git clone --depth 1 https://github.com/alebcay/openlens-node-pod-menu \
  && cd openlens-node-pod-menu/ \
  && npm install \
  && npm run build \
  && npm pack


FROM debian:bookworm-slim
LABEL maintainer "Miel Donkers <miel.donkers@gmail.com>"

ENV DEBIAN_FRONTEND noninteractive

RUN buildDeps=' \
      ca-certificates \
      curl \
      gnupg \
      apt-utils \
      apt-transport-https \
      unzip \
      ' \
    && packages=' \
      google-cloud-cli \
      google-cloud-sdk-gke-gcloud-auth-plugin \
      kubectl \
      less \
      libasound2 \
      libnss3 \
      libswt-webkit-gtk-4-jni \
      ' \
    && set -x \
    && apt-get update && apt-get install -y $buildDeps --no-install-recommends \
    && curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg \
    && curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key --keyring /usr/share/keyrings/cloud.google.gpg add - \
    && echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list \
    && echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" > /etc/apt/sources.list.d/google-cloud-sdk.list \
    && apt-get update && apt-get install -y $packages --no-install-recommends \
    && curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "/tmp/awscliv2.zip" \
    && unzip /tmp/awscliv2.zip -d /tmp \
    && /tmp/aws/install \
    && aws --version \
    && rm -rf /tmp/aws /tmp/awscliv2.zip \
    && rm -rf /var/lib/apt/lists/* \
    && apt-get purge -y --auto-remove $buildDeps

ENV HOME=/home/lens \
    USER_ID=1000 \
    USER_NAME=lens \
    XDG_CONFIG_HOME=/tmp \
    LENS_NODE_POD_MENU_HOME="/home/lens/.k8slens/extensions/alebcay--openlens-node-pod-menu" \
    LENS_NODE_POD_MENU_TMP="/tmp/OpenLens/node_modules/@alebcay/openlens-node-pod-menu"

RUN groupadd --gid $USER_ID $USER_NAME \
    && useradd --create-home --home-dir $HOME --uid $USER_ID --gid $USER_ID $USER_NAME

COPY --from=builder --chown=$USER_NAME:$USER_NAME /build/lens/packages/open-lens/dist/linux-unpacked/ $HOME/lens/
COPY --from=extension-builder --chown=$USER_NAME:$USER_NAME /build/openlens-node-pod-menu/*.tgz $HOME/
RUN mkdir -p "$LENS_NODE_POD_MENU_HOME" \
    && mkdir -p "$LENS_NODE_POD_MENU_TMP" \
    && tar -xzvf $HOME/alebcay-openlens-node-pod-menu-*.tgz --strip-components=1 -C "$LENS_NODE_POD_MENU_HOME" \
    && tar -xzvf $HOME/alebcay-openlens-node-pod-menu-*.tgz --strip-components=1 -C "$LENS_NODE_POD_MENU_TMP" \
    && chown -R $USER_NAME:$USER_NAME $HOME \
    && chown -R $USER_NAME:$USER_NAME /tmp/OpenLens

COPY --chown=$USER_NAME:$USER_NAME <<EOF /tmp/OpenLens/lens-extensions.json
{
	"__internal__": {
		"migrations": {
			"version": "6.4.0"
		}
	},
	"extensions": {
		"$LENS_NODE_POD_MENU_TMP/package.json": {
			"enabled": true,
			"name": "@alebcay/openlens-node-pod-menu"
		}
	}
}
EOF

ENV PATH="${HOME}/lens:${PATH}"

WORKDIR $HOME
USER $USER_ID:$USER_ID

ENTRYPOINT [ "open-lens" ]
