FROM golang:1.20-alpine AS builder

WORKDIR /workdir
COPY . /workdir

RUN set -x && \
	apk --no-cache add git gcc libc-dev make

RUN git clone https://github.com/cloudflare/cfssl_trust.git /etc/cfssl && \
    make clean && \
    make all

FROM --platform=$TARGETOS/$TARGETARCH alpine:3.11

WORKDIR /home/container
COPY --from=builder /etc/cfssl /home/container
COPY --from=builder /workdir/bin/ /home/container/bin

ENV PATH="/home/container/bin:${PATH}"

RUN         adduser -D -h /home/container container -s /bin/bash container
USER        container
ENV         USER=container HOME=/home/container
WORKDIR     /home/container

STOPSIGNAL SIGINT

# Pelican-related files
COPY        --chown=container:container ./entrypoint.sh /entrypoint.sh
RUN         chmod +x /entrypoint.sh
ENTRYPOINT ["/usr/bin/tini", "-g", "--"]
CMD ["/entrypoint.sh"]