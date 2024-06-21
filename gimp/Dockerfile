# Run gimp in a contianer
#
# docker run -d \
#	-v /etc/localtime:/etc/localtime:ro \
#	-v /tmp/.X11-unix:/tmp/.X11-unix \
#	-e DISPLAY=unix$DISPLAY \
#	-v $HOME/Pictures:/root/Pictures \
#	--name gimp \
#	miel/gimp
#
FROM debian:testing-slim
LABEL maintainer "Miel Donkers <miel.donkers@gmail.com>"

RUN DEBIAN_FRONTEND=noninteractive apt-get update && apt-get install -y \
	gimp \
	gtk2-engines-pixbuf \
	--no-install-recommends \
	&& rm -rf /var/lib/apt/lists/*

ENV HOME=/home/user \
    XDG_CONFIG_HOME=/tmp

RUN useradd --create-home --home-dir $HOME user \
	&& chown -R user:user $HOME \
    && mkdir -p /tmp/GIMP/2.10 \
    && chown -R user:user /tmp/GIMP/2.10


COPY --chown=$USER_NAME:$USER_NAME <<EOF /tmp/GIMP/2.10/gimprc
(icon-theme "Legacy")
EOF

WORKDIR $HOME
USER user

ENTRYPOINT [ "gimp" ]
