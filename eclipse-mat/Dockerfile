# Run Eclipse MAT in a container
#
# docker run -d \
#   -v /etc/localtime:/etc/localtime:ro \
# 	-v /tmp/.X11-unix:/tmp/.X11-unix \
#	-e DISPLAY=unix$DISPLAY \
#   -v "${HOME}/Downloads:/root/Downloads" \
#	--name eclipse-mat \
#	miel/eclipse-mat
#
# In the container, Eclipse MAT JVM heap is configured to 12g.
# To specify any other heap size, add `-vmargs -Xmx...` with the
# required memory settings.
#
FROM debian:bullseye-slim
LABEL maintainer "Miel Donkers <miel.donkers@gmail.com>"

ENV DEBIAN_FRONTEND noninteractive
ARG MAT_VERSION=1.11.0
ARG MAT_BUILD_DATE=20201202

RUN apt-get update && apt-get install -y \
	ca-certificates \
	libcurl4 \
	libswt-gtk-4-java \
    libswt-gtk-4-jni \
    libswt-glx-gtk-4-jni \
    libswt-webkit-gtk-4-jni \
    libgtk-3-0 \
	procps \
	--no-install-recommends \
	&& rm -rf /var/lib/apt/lists/*

RUN buildDeps=' \
		curl \
		gnupg \
		apt-utils \
		unzip \
	' \
	&& set -x \
	&& mkdir -p /etc/xdg/QtProject \
	&& mkdir -p /usr/share/man/man1 \
	&& apt-get update && apt-get install -y $buildDeps --no-install-recommends \
	&& rm -rf /var/lib/apt/lists/* \
	&& apt-get update && apt-get install -y \
	  openjdk-11-jdk \
	  --no-install-recommends \
	&& curl -sSL \
	  "https://www.eclipse.org/downloads/download.php?file=/mat/${MAT_VERSION}/rcp/MemoryAnalyzer-${MAT_VERSION}.${MAT_BUILD_DATE}-linux.gtk.x86_64.zip&r=1" \
	  -o eclipse-mat.zip \
	&& unzip eclipse-mat.zip \
	&& chmod +x mat/MemoryAnalyzer \
	&& sed -i "\\|^-Xmx|c \\-Xmx12g" "mat/MemoryAnalyzer.ini" \
	&& apt-get purge -y --auto-remove $buildDeps

ENTRYPOINT	[ "mat/MemoryAnalyzer" ]
