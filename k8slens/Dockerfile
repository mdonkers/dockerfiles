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
	&& set -x \
	&& apt-get update && apt-get install -y $buildDeps --no-install-recommends \
    && curl -fsSLo /usr/share/keyrings/kubernetes-archive-keyring.gpg https://packages.cloud.google.com/apt/doc/apt-key.gpg \
    && echo "deb [signed-by=/usr/share/keyrings/kubernetes-archive-keyring.gpg] https://apt.kubernetes.io/ kubernetes-xenial main" > /etc/apt/sources.list.d/kubernetes.list \
    && curl -L --fail --show-error --silent https://api.k8slens.dev/binaries/Lens-5.4.0-latest.20220301.1.amd64.deb -o /tmp/lens.deb \
    && apt-get update && apt install -y kubectl less libgtk-4-1 libswt-gtk-4-jni libswt-webkit-gtk-4-jni libasound2 /tmp/lens.deb --no-install-recommends \
    && curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "/tmp/awscliv2.zip" \
    && unzip /tmp/awscliv2.zip -d /tmp \
    && /tmp/aws/install \
    && aws --version \
    && rm -rf /tmp/aws /tmp/awscliv2.zip /tmp/lens.deb \
	&& rm -rf /var/lib/apt/lists/* \
	&& apt-get purge -y --auto-remove $buildDeps

ENV HOME=/home/lens \
    USER_ID=1000 \
    USER_NAME=lens \
    XDG_CONFIG_HOME=/tmp

RUN groupadd --gid $USER_ID $USER_NAME \
    && useradd --create-home --home-dir $HOME --uid $USER_ID --gid $USER_ID $USER_NAME \
	&& chown -R $USER_NAME:$USER_NAME $HOME

WORKDIR $HOME
USER $USER_ID:$USER_ID

ENTRYPOINT	[ "lens" ]
