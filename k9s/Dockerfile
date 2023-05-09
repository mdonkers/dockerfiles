# Run K9s in a container
# Based on the [original Dockerfile](https://github.com/derailed/k9s/blob/master/Dockerfile) but adds e.g.
# AWS CLI to the container for authentication etc
#
# Make sure .aws and .kube are 'group / other' readable
# Run as follows:
#
#   docker run -d \
#	-v "${HOME}/certs:/certs" \
#	-v "${HOME}/.aws:/home/k9s/.aws" \
#	-v "${HOME}/.kube:/home/k9s/.kube" \
#	-v "${HOME}/.minikube:/home/k9s/.minikube" \
#	-v "${HOME}/.gcloud:/home/k9s/.gcloud" \
#	-e CLOUDSDK_CONFIG=/home/k9s/.gcloud \
#	-v /etc/hosts:/etc/hosts \
#	--net host \
#	--name k9s \
#	miel/k9s
#
FROM golang:1.20 AS build

WORKDIR /k9s
RUN git clone --depth 1 https://github.com/derailed/k9s.git . \
  && make build

# -----------------------------------------------------------------------------
# Build the final Docker image

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
      curl \
      kubectl \
      less \
      vim \
      #google-cloud-cli \
      #google-cloud-sdk-gke-gcloud-auth-plugin \
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
    && apt-get purge -y --auto-remove $buildDeps \
    && apt -y autoclean \
    && apt -y clean \
    && rm -rf /var/lib/apt/lists/*

ENV HOME=/home/k9s \
    USER_ID=1000 \
    USER_NAME=k9s \
    EDITOR=vim

RUN groupadd --gid $USER_ID $USER_NAME \
    && useradd --create-home --home-dir $HOME --uid $USER_ID --gid $USER_ID $USER_NAME


COPY --from=build --chown=$USER_NAME:$USER_NAME /k9s/execs/k9s $HOME/k9s/

ENV PATH="${HOME}/k9s:${PATH}"

WORKDIR $HOME
USER $USER_ID:$USER_ID

ENTRYPOINT [ "k9s" ]
