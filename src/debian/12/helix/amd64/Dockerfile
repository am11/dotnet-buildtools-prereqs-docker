FROM library/debian:bookworm

# Install Helix Dependencies
RUN apt-get update && \
    apt-get install -y \
        autoconf \
        automake \
        at \
        build-essential \
        curl \
        gcc \
        gdb \
        git \
        iputils-ping \
        libcurl4 \
        libffi-dev \
        libgdiplus \
        libicu-dev \
        libssl-dev \
        libtool \
        libunwind8 \
        llvm \
        locales \
        locales-all \
        python3-dev \
        python3-pip \
        python3-venv \
        software-properties-common \
        sudo \
        tzdata \
        unzip \
    && rm -rf /var/lib/apt/lists/* \
    && localedef -i en_US -c -f UTF-8 -A /usr/share/locale/locale.alias en_US.UTF-8

ENV LANG=en_US.utf8

RUN ln -sf /usr/bin/python3 /usr/bin/python && \
    pip download --no-deps helix-scripts --index-url https://dnceng.pkgs.visualstudio.com/public/_packaging/helix-client-prod/pypi/simple && \
    pip install ./helix_scripts-*-py3-none-any.whl --break-system-packages

# Add MsQuic
RUN curl -LO https://packages.microsoft.com/keys/microsoft.asc && \
    echo 2cfd20a306b2fa5e25522d78f2ef50a1f429d35fd30bd983e2ebffc2b80944fa microsoft.asc| sha256sum --check - && \
    apt-key add microsoft.asc && \
    rm microsoft.asc && \
    echo deb https://packages.microsoft.com/debian/12/prod bookworm main >> /etc/apt/sources.list.d/microsoft.list && \
    apt-get update && \
    apt-get install -y libmsquic && \
    rm -rf /var/lib/apt/lists/*

# Create helixbot user and give rights to sudo without password
# additionally, preinstall the virtualenv packages used for VSTS reporting to save time
RUN /usr/sbin/adduser --disabled-password --gecos '' --uid 1000 --shell /bin/bash --ingroup adm helixbot && \
    chmod 755 /root && \
    echo "helixbot ALL=(ALL)       NOPASSWD: ALL" > /etc/sudoers

USER helixbot

RUN python3 -m venv /home/helixbot/.vsts-env
