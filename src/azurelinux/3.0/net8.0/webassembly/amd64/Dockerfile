FROM mcr.microsoft.com/dotnet-buildtools/prereqs:azurelinux-3.0-net8.0-cross-amd64

# Dependencies for WebAssembly build
RUN tdnf update -y \
    && tdnf install -y \
        # WebAssembly build dependencies
        which \
        nodejs \
        npm \
        python3 \
        libxml2 \
        unzip \
        # dependency for npm package modification
        jq

# WebAssembly build needs typescript
RUN npm i -g typescript

# Install Emscripten toolchain
ENV EMSCRIPTEN_VERSION=3.1.34
ENV EMSCRIPTEN_PATH=/usr/local/emscripten
ENV EMSDK_PATH=/usr/local/emscripten/emsdk
ENV NODE_VERSION_IN_EMSDK=16.20.0_64bit

RUN mkdir ${EMSCRIPTEN_PATH} \
    && cd ${EMSCRIPTEN_PATH} \
    && git clone https://github.com/emscripten-core/emsdk.git ${EMSDK_PATH} \
    && cd ${EMSDK_PATH} \
    && git checkout ${EMSCRIPTEN_VERSION} \
    # patch node version in emsdk_manifest.json
    && sed -i 's/14\.18\.2/16\.20\.0/g' emsdk_manifest.json \
    && ./emsdk install ${EMSCRIPTEN_VERSION}-upstream \
    && ./emsdk activate ${EMSCRIPTEN_VERSION}-upstream \
    && chmod -R 777 ${EMSCRIPTEN_PATH} \
    # update packages to non-vulnerable versions
    && export PATH=$PATH:${EMSDK_PATH}/node/${NODE_VERSION_IN_EMSDK}/bin \
    && cd ${EMSDK_PATH}/node/${NODE_VERSION_IN_EMSDK}/lib \
    && npm install npm@9 \
    && npm prune --production \
    && cd ${EMSDK_PATH}/upstream/emscripten \
    && jq 'del(.devDependencies)' package.json > package.json.tmp && mv package.json.tmp package.json \
    && npm audit fix \
    && npm prune --production \
    && rm -rf ${EMSDK_PATH}/upstream/emscripten/node_modules/google-closure-compiler \
    && rm -rf ${EMSDK_PATH}/upstream/emscripten/node_modules/google-closure-compiler-java \
    && rm -rf ${EMSDK_PATH}/upstream/emscripten/node_modules/google-closure-compiler-linux \
    && find ${EMSCRIPTEN_PATH} -xtype l -delete

# Install V8 Engine
SHELL ["/bin/bash", "-c"]

ENV V8_VERSION=10.8.168
RUN curl -sSL https://netcorenativeassets.blob.core.windows.net/resource-packages/external/linux/chromium-v8/v8-linux64-rel-${V8_VERSION}.zip -o ./v8.zip \
    && unzip ./v8.zip -d /usr/local/v8 \
    && echo $'#!/usr/bin/env bash\n\
"/usr/local/v8/d8" --snapshot_blob="/usr/local/v8/snapshot_blob.bin" "$@"\n' > /usr/local/bin/v8 \
    && chmod +x /usr/local/bin/v8

# Install Wasi toolchain
ENV WASI_SDK_VERSION=16
ENV WASI_SDK_PATH=/usr/local/wasi-sdk
ENV WASI_SDK_URL=https://github.com/WebAssembly/wasi-sdk/releases/download/wasi-sdk-${WASI_SDK_VERSION}/wasi-sdk-${WASI_SDK_VERSION}.0-linux.tar.gz

RUN mkdir -p ${WASI_SDK_PATH} \
    && cd /tmp \
    && curl -L -o /tmp/wasi-sdk.tar.gz ${WASI_SDK_URL} \
    && tar --strip-components=1 -xvzf /tmp/wasi-sdk.tar.gz -C ${WASI_SDK_PATH} \
    && echo ${WASI_SDK_VERSION} > ${WASI_SDK_PATH}/wasi-sdk-version.txt \
    && rm /tmp/wasi-sdk.tar.gz

ENV WASMTIME_VERSION=19.0.2
ENV WASMTIME_PATH=/usr/local/wasmtime
ENV WASMTIME_URL=https://github.com/bytecodealliance/wasmtime/releases/download/v${WASMTIME_VERSION}/wasmtime-v${WASMTIME_VERSION}-x86_64-linux.tar.xz

RUN mkdir -p ${WASMTIME_PATH} \
    && cd /tmp \
    && curl -L -o /tmp/wasmtime.tar.xz ${WASMTIME_URL} \
    && tar --strip-components=1 -xvf /tmp/wasmtime.tar.xz -C ${WASMTIME_PATH} \
    && echo ${WASMTIME_VERSION} > ${WASMTIME_PATH}/wasmtime-version.txt \
    && rm /tmp/wasmtime.tar.xz \
    && ln -s ${WASMTIME_PATH}/wasmtime /bin/wasmtime
