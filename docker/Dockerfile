FROM golang:1.20.7 AS builder
LABEL maintainer="multiversx"

RUN apt-get update && apt-get install -y

WORKDIR /config
COPY . .

WORKDIR /go
RUN git clone https://github.com/multiversx/mx-chain-sovereign-go.git && cd mx-chain-sovereign-go && git checkout --force $(cat /config/binaryVersion)
WORKDIR /go/mx-chain-sovereign-go

# MultiversX node
WORKDIR /go/mx-chain-sovereign-go/cmd/sovereignnode
RUN go build -v -ldflags="-X main.appVersion=$(git --git-dir /config/.git describe --tags --long --dirty)"

RUN mkdir -p /lib_amd64 /lib_arm64

RUN cp /go/pkg/mod/github.com/multiversx/$(cat /go/mx-chain-sovereign-go/go.mod | grep mx-chain-vm-v | sort -n | tail -n -1 | awk -F '/' '{print$3}' | sed 's/ /@/g')/wasmer/libwasmer_linux_amd64.so /lib_amd64/
RUN cp /go/pkg/mod/github.com/multiversx/$(cat /go/mx-chain-sovereign-go/go.mod | grep mx-chain-vm-go | sort -n | tail -n -1 | awk -F '/' '{print$3}' | sed 's/ /@/g')/wasmer2/libvmexeccapi.so /lib_amd64/

RUN cp /go/pkg/mod/github.com/multiversx/$(cat /go/mx-chain-sovereign-go/go.mod | grep mx-chain-vm-v | sort -n | tail -n -1 | awk -F '/' '{print$3}' | sed 's/ /@/g')/wasmer/libwasmer_linux_arm64_shim.so /lib_arm64/
RUN cp /go/pkg/mod/github.com/multiversx/$(cat /go/mx-chain-sovereign-go/go.mod | grep mx-chain-vm-go | sort -n | tail -n -1 | awk -F '/' '{print$3}' | sed 's/ /@/g')/wasmer2/libvmexeccapi_arm.so /lib_arm64/

WORKDIR /config
RUN cp -r * /go/mx-chain-sovereign-go/cmd/sovereignnode/config/

WORKDIR /go/mx-chain-sovereign-go/cmd/sovereignnode

# ===== SECOND STAGE ======
FROM ubuntu:22.04
ARG TARGETARCH
RUN apt-get update -y && apt-get upgrade -y

COPY --from=builder "/go/mx-chain-sovereign-go/cmd/sovereignnode" "/go/mx-chain-sovereign-go/cmd/sovereignnode/"

# Copy architecture-specific files
COPY --from=builder "/lib_${TARGETARCH}/*" "/lib/"

WORKDIR /go/mx-chain-sovereign-go/cmd/sovereignnode/
EXPOSE 8080
ENTRYPOINT ["/go/mx-chain-sovereign-go/cmd/sovereignnode/sovereignnode", "--rest-api-interface=:8080"]
