# Default to Go 1.20
ARG GO_VERSION=1.20
FROM golang:${GO_VERSION}-alpine as build

# Necessary to run 'go get' and to compile the linked binary
RUN apk add git musl-dev mailcap


WORKDIR /go/src/github.com/dutchcoders/transfer.sh

RUN git clone https://github.com/dutchcoders/transfer.sh .

RUN go mod download

# build & install server
RUN CGO_ENABLED=0 go build -tags netgo -ldflags "-X github.com/dutchcoders/transfer.sh/cmd.Version=$(git describe --tags) -a -s -w -extldflags '-static'" -o /go/bin/transfersh

ARG PUID=568 \
    PGID=568 \
    RUNAS

RUN mkdir -p /tmp/useradd /tmp/empty && \
    if [ ! -z "$RUNAS" ]; then \
    echo "${RUNAS}:x:${PUID}:${PGID}::/nonexistent:/sbin/nologin" >> /tmp/useradd/passwd && \
    echo "${RUNAS}:!:::::::" >> /tmp/useradd/shadow && \
    echo "${RUNAS}:x:${PGID}:" >> /tmp/useradd/group && \
    echo "${RUNAS}:!::" >> /tmp/useradd/groupshadow; else touch /tmp/useradd/unused; fi

FROM scratch AS final
ARG RUNAS

COPY --from=build /etc/mime.types /etc/mime.types
COPY --from=build /tmp/empty /tmp
COPY --from=build /tmp/useradd/* /etc/
COPY --from=build --chown=${RUNAS}  /go/bin/transfersh /go/bin/transfersh
COPY --from=build /etc/ssl/certs/ca-certificates.crt /etc/ssl/certs/ca-certificates.crt

ARG PROVIDER=local
ARG BASEDIR=/tmp
ENV PROVIDER=$PROVIDER
ENV BASEDIR=$BASEDIR

USER ${RUNAS}

ENTRYPOINT ["/go/bin/transfersh", "--listener", ":8080", "--provider", "${PROVIDER}", "--basedir", "${BASEDIR}"]

EXPOSE 8080
