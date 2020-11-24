# Run Kafkacat in a container
#
# For some reason existing containers were giving problems decoding messages.
# This is just taking a naive approach including the necessary libraries and
# all recommended dependencies.
#
# For example to cat to a file:
#
# docker run -a stdout \
#   --network=host \
#   --volume=/tmp:/tmp \
#   miel/kafkacat \
#   kafkacat -b BROKER -t MY-TOPIC -c 100000 -C > /tmp/records
#
# Make sure to use "-a stdout" instead of "-it" because the latter allocates a pseudo terminal which
# uses Windows-style linebreaks and introduces "^M" everywhere, which then messes up parsing of the result.
#
# See https://github.com/edenhill/kafkacat for the original Kafkacat util.
#
FROM debian:bullseye-slim
LABEL maintainer "Miel Donkers <miel.donkers@gmail.com>"

ENV DEBIAN_FRONTEND noninteractive

RUN apt-get update && apt-get install -y \
    ca-certificates \
    libcurl4 \
    librdkafka-dev \
    libyajl-dev \
    kafkacat \
    && rm -rf /var/lib/apt/lists/*

ENTRYPOINT	[ "kafkacat" ]
