# Run Hollywood in a container
#
#  docker run --rm -it \
#	 --name hollywood \
#	 miel/hollywood
#
# To easily exit, use "F6" which will disconnect from the Byobu
# session and exit the container.
#
# Copied from JessFraz (https://github.com/mdonkers/dockerfiles-jessfraz) but
# included utils to fix broken Docker image and status message for exiting
#
FROM debian:testing-slim
LABEL maintainer="Miel Donkers <miel.donkers@gmail.com>"

ENV DEBIAN_FRONTEND=noninteractive

RUN apt update && \
	apt install -y \
	byobu \
	hollywood \
	# Hollywood dependencies
	apg \
	atop \
	bmon \
	bsdextrautils \
	ccze \
	cmatrix \
	htop \
	jp2a \
	moreutils \
	plocate \
	tree \
	--no-install-recommends \
	&& rm -rf /var/lib/apt/lists/* \
	&& updatedb

ENV HOME=/home/user

# Override default byobu-tmux status line to include custom status scripts
# Allows us to provide a small help text on how to quit Hollywood easily (-:
RUN sed -i "s/#custom/custom/g" /usr/share/byobu/status/status \
	&& useradd --create-home --home-dir $HOME user \
	&& chown -R user:user $HOME

COPY --chown=user:user 1000_quit /home/user/.byobu/bin/

WORKDIR $HOME
USER user

CMD [ "hollywood", "-s", "9" ]
