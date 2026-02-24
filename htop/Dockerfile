# htop in a container
#
# docker run --rm -it \
# 	--pid host \
# 	miel/htop
#
FROM alpine:latest
LABEL maintainer="Miel Donkers <miel.donkers@gmail.com>"

RUN apk --no-cache add \
	htop

CMD [ "htop" ]
