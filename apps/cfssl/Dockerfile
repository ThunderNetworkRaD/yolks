FROM --platform=$TARGETOS/$TARGETARCH golang:1.20

ARG TARGETPLATFORM
ARG BUILDPLATFORM
RUN echo "I am running on $BUILDPLATFORM, building for $TARGETPLATFORM"

WORKDIR /workdir
COPY . /workdir

#RUN set -x && \
#	apk --no-cache add git gcc libc-dev make

RUN git clone https://github.com/cloudflare/cfssl_trust.git /home/container/cfssl && \
    make clean && \
	GOOS=${TARGETOS} GOARCH=${TARGETARCH} make all && cp bin/* /usr/bin/

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