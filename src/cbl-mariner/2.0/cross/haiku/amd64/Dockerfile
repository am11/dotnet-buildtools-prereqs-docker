FROM mcr.microsoft.com/dotnet-buildtools/prereqs:cbl-mariner-2.0-crossdeps-builder-local AS builder
ARG ROOTFS_DIR=/rootfs/x64/boot/system/bin

RUN /scripts/eng/common/cross/build-rootfs.sh haiku x64

FROM mcr.microsoft.com/dotnet-buildtools/prereqs:cbl-mariner-2.0-crossdeps-llvm-local
ARG ROOTFS_DIR=/rootfs/x64/boot/system/bin

COPY --from=builder "$ROOTFS_DIR" "$ROOTFS_DIR"
