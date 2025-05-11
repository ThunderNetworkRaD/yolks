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

# Pelican-related files
RUN         adduser -D -h /home/container container
USER        container
ENV         USER=container HOME=/home/container
WORKDIR     /home/container

COPY        ./../entrypoint.sh /entrypoint.sh
CMD         [ "/bin/ash", "/entrypoint.sh" ]