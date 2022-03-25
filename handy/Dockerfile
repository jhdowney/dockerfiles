# FROM --platform=linux/arm/v7  debian:bullseye
FROM debian:bullseye-slim
# basic stuff

RUN apt update  \
    && apt upgrade -y  \
    && apt install -y --no-install-recommends\ 
       curl \ 
       unzip \
       build-essential \
       htop \
       nano \
       git \
       neofetch \
       procps \
    && rm -rf /var/lib/apt/lists/*
       

#nodejs

RUN apt update \
    && apt upgrade -y \
    && apt install -y --no-install-recommends \
      nodejs \
      npm \
      man \
    && rm -rf /var/lib/apt/lists/*

# rust
RUN apt update && \ 
    curl https://sh.rustup.rs -sSf | bash -s -- -y

RUN echo 'source $HOME/.cargo/env' >> $HOME/.bashrc

# java corretto

ARG version=11.0.14.9-1
# In addition to installing the Amazon corretto, we also install
# fontconfig. The folks who manage the docker hub's
# official image library have found that font management
# is a common usecase, and painpoint, and have
# recommended that Java images include font support.
#
# See:
#  https://github.com/docker-library/official-images/blob/master/test/tests/java-uimanager-font/container.java

RUN set -eux \
    && apt-get update \
    && apt-get install -y --no-install-recommends \
        curl ca-certificates gnupg software-properties-common fontconfig java-common \
    && curl -fL https://apt.corretto.aws/corretto.key | apt-key add - \
    && add-apt-repository 'deb https://apt.corretto.aws stable main' \
    && mkdir -p /usr/share/man/man1 || true \
    && apt-get update \
    && apt-get install -y java-11-amazon-corretto-jdk=1:$version \
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
        curl gnupg software-properties-common

ENV LANG C.UTF-8
ENV JAVA_HOME=/usr/lib/jvm/java-11-amazon-corretto

# aws cli (specific for ARM)

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
            curl  \
    && curl "https://awscli.amazonaws.com/awscli-exe-linux-aarch64.zip" -o "awscliv2.zip" \
    && unzip awscliv2.zip  \
    && ./aws/install  \
    && rm ./awscliv2.zip  \
    && rm -rf ./aws \
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
        curl 


# Install ansible
ARG ansible_install_deps="software-properties-common gnupg"
RUN apt update \
    && apt install -y --no-install-recommends \
          ${ansible_install_deps} \
    && apt-add-repository "deb http://ppa.launchpad.net/ansible/ansible/ubuntu focal main" \
    && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367 \ 
    && apt update \
    && apt install -y ansible \
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
         ${ansible_install_deps}



# install Golang
RUN apt update \ 
    && apt install -y --no-install-recommends \
        curl \ 
    && curl --remote-name --location https://go.dev/dl/go1.17.8.linux-arm64.tar.gz \
    && tar -zxvf go1.17.8.linux-arm64.tar.gz -C /usr/local/ \
    && echo 'export PATH=/usr/local/go/bin:${PATH}' | tee /etc/profile.d/go.sh \
    && . /etc/profile.d/go.sh \
    && echo 'export PATH=/usr/local/go/bin:${PATH}' >> $HOME/.bashrc \
    && rm go1.17.8.linux-arm64.tar.gz \
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
         curl

# Install dotnet 6

ARG REPO=mcr.microsoft.com/dotnet/aspnet
#FROM $REPO:6.0.2-bullseye-slim-arm64v8

ENV \
    # Unset ASPNETCORE_URLS from aspnet base image
    ASPNETCORE_URLS= \
    # Do not generate certificate
    DOTNET_GENERATE_ASPNET_CERTIFICATE=false \
    # Do not show first run text
    DOTNET_NOLOGO=true \
    # SDK version
    DOTNET_SDK_VERSION=6.0.200 \
    # Enable correct mode for dotnet watch (only mode supported in a container)
    DOTNET_USE_POLLING_FILE_WATCHER=true \
    # Unset Logging__Console__FormatterName from aspnet base image
    Logging__Console__FormatterName= \
    # Skip extraction of XML docs - generally not useful within an image/container - helps performance
    NUGET_XMLDOC_MODE=skip \
    # PowerShell telemetry for docker image usage
    POWERSHELL_DISTRIBUTION_CHANNEL=PSDocker-DotnetSDK-Debian-11-arm64

# Install .NET SDK
ARG DOTNET_SOURCE=https://dotnetcli.azureedge.net/dotnet/Sdk/$DOTNET_SDK_VERSION/dotnet-sdk-$DOTNET_SDK_VERSION-linux-arm64.tar.gz
RUN apt update \ 
    && apt install -y --no-install-recommends \
        curl \ 
    && curl -fSL --output dotnet.tar.gz ${DOTNET_SOURCE} \
    && dotnet_sha512='4e2b8f65f4cd9d1e54233b377e7efef5202eee3fa5e4592131ff03ecab032bfccdc91f4e8c806064fb769c48f946634bf1e420afecd7fcd2d981d40eeec5a881' \
    && echo "$dotnet_sha512  dotnet.tar.gz" | sha512sum -c - \
    && mkdir -p /usr/share/dotnet \
 #   && tar -oxzf dotnet.tar.gz -C /usr/share/dotnet ./packs ./sdk ./sdk-manifests ./templates ./LICENSE.txt ./ThirdPartyNotices.txt \
    && tar -oxzf dotnet.tar.gz -C /usr/share/dotnet \
    && rm dotnet.tar.gz \
    && echo 'export PATH=/usr/share/dotnet:${PATH} && export DOTNET_ROOT=/usr/share/dotnet' | tee /etc/profile.d/dotnet.sh \
    && . /etc/profile.d/dotnet.sh \
    && echo 'export PATH=/usr/share/dotnet:${PATH}' >> $HOME/.bashrc \
    # Trigger first run experience by running arbitrary cmd
    && dotnet help \
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
        curl

ENV PATH /usr/share/dotnet:$PATH

# Install PowerShell global tool
RUN apt update \ 
    && apt install -y --no-install-recommends \
        curl \ 
    && powershell_version=7.2.1 \
    && curl -fSL --output PowerShell.Linux.arm64.$powershell_version.nupkg https://pwshtool.blob.core.windows.net/tool/$powershell_version/PowerShell.Linux.arm64.$powershell_version.nupkg \
    && powershell_sha512='63466595980109d812463ce59cdd8c24fa62559e7aa868dde01732fe500d34a4e7d239c99397de490b4dd9e21f1abc944274216cbcb38f423886827530d555d7' \
    && echo "$powershell_sha512  PowerShell.Linux.arm64.$powershell_version.nupkg" | sha512sum -c - \
    && mkdir -p /usr/share/powershell \
    && dotnet tool install --add-source / --tool-path /usr/share/powershell --version $powershell_version PowerShell.Linux.arm64 \
    && dotnet nuget locals all --clear \
    && rm PowerShell.Linux.arm64.$powershell_version.nupkg \
    && ln -s /usr/share/powershell/pwsh /usr/bin/pwsh \
    && chmod 755 /usr/share/powershell/pwsh \
    # To reduce image size, remove the copy nupkg that nuget keeps.
    && find /usr/share/powershell -print | grep -i '.*[.]nupkg$' | xargs rm \
    && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
        curl

# reinstall curl
RUN apt update \ 
    && apt install -y --no-install-recommends \
        curl 

# install grpcurl
RUN /usr/local/go/bin/go install github.com/fullstorydev/grpcurl/cmd/grpcurl@latest \
    && echo 'export PATH=$PATH:$HOME/go/bin' >> ~/.bashrc

