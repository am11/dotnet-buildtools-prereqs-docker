ARG ROOTFS_DIR=/crossrootfs/ppc64le

FROM mcr.microsoft.com/dotnet-buildtools/prereqs:azurelinux-3.0-net9.0-crossdeps-builder-amd64 AS builder
ARG ROOTFS_DIR

RUN /scripts/eng/common/cross/build-rootfs.sh bionic ppc64le

FROM mcr.microsoft.com/dotnet-buildtools/prereqs:azurelinux-3.0-net9.0-crossdeps-llvm-amd64
ARG ROOTFS_DIR

COPY --from=builder "$ROOTFS_DIR" "$ROOTFS_DIR"
