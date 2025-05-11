FROM --platform=$TARGETOS/$TARGETARCH golang:1.20

ARG TARGETPLATFORM
ARG BUILDPLATFORM
RUN echo "I am running on $BUILDPLATFORM, building for $TARGETPLATFORM"

WORKDIR /workdir

#RUN set -x && \
#	apk --no-cache add git gcc libc-dev make

RUN git clone --depth 1 https://github.com/cloudflare/cfssl_trust.git /etc/cfssl && \
	git clone --depth 1 https://github.com/cloudflare/cfssl.git /workdir && \
    make clean && \
	GOOS=${TARGETOS} GOARCH=${TARGETARCH} make all && cp bin/* /usr/bin/ && \
	ln -s /etc/cfssl /home/container

ENV PATH="/usr/bin:${PATH}"

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