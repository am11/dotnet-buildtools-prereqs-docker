ARG ROOTFS_DIR=/crossrootfs/loongarch64

FROM mcr.microsoft.com/dotnet-buildtools/prereqs:azurelinux-3.0-net10.0-crossdeps-builder-amd64 AS builder
ARG ROOTFS_DIR

RUN rootfsEnv="/usr/local/share/rootfs" && \
    python3 -m venv "$rootfsEnv" && \
    "$rootfsEnv/bin/python" -m pip install aiohttp zstandard && \
    PYTHON_EXECUTABLE="$rootfsEnv/bin/python" /scripts/eng/common/cross/build-rootfs.sh loongarch64 sid --skipunmount --skipemulation

ARG TARGET_TRIPLE="loongarch64-linux-gnu"

DOTNET_DOCKER_IMPORT ../../../../Dockerfile.fragment_libcpp

FROM mcr.microsoft.com/dotnet-buildtools/prereqs:azurelinux-3.0-net10.0-crossdeps-llvm-amd64
ARG ROOTFS_DIR

COPY --from=builder /usr/local/lib/clang /usr/local/lib/clang/
COPY --from=builder $ROOTFS_DIR $ROOTFS_DIR
