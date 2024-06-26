FROM library/debian:bullseye

# Dependencies for generic .NET Core optimization runs
RUN apt-get update \
    && apt-get install -y \
            autoconf \
            automake \
            build-essential \
            cmake \
            curl \
            g++ \
            gettext \
            gdb \
            git \
            gnupg \
            jq \
            libcurl4-openssl-dev \
            libgdiplus \
            libicu-dev \
            libkrb5-dev \
            liblttng-ust-dev \
            libnuma-dev \
            libtool \
            libunwind8-dev \
            llvm-9 \
            locales \
            make \
            sudo \
            tar \
            uuid-dev \
            zip \
            zlib1g-dev \
    && rm -rf /var/lib/apt/lists/*

# Link llvm-profdata-9 to llvm-profdata
RUN ln /usr/bin/llvm-profdata-9 /usr/bin/llvm-profdata

# .NET SDK MSBuild requires US.UTF-8 locale to execute tasks
# These commands are from https://askubuntu.com/a/1027038
RUN echo "locales locales/default_environment_locale select en_US.UTF-8" | debconf-set-selections \
    && echo "locales locales/locales_to_be_generated multiselect en_US.UTF-8 UTF-8" | debconf-set-selections \
    && rm "/etc/locale.gen" \
    && dpkg-reconfigure --frontend noninteractive locales