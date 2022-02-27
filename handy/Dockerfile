FROM  debian:bullseye 

# basic stuff

RUN apt update && \
    apt upgrade -y && \
    apt install -y \ 
       curl \ 
       unzip \
       build-essential \
       wget \
       htop \
       nano \
       neofetch \
       procps
       

#nodejs

RUN apt update && \
    apt upgrade -y && \
    apt install -y \
      nodejs \
      npm \
      man

# rust
RUN apt update && \ 
    curl https://sh.rustup.rs -sSf | bash -s -- -y

RUN echo 'source $HOME/.cargo/env' >> $HOME/.bashrc

# java corretto


RUN apt update && \
    apt install -y software-properties-common && \
    wget -O- https://apt.corretto.aws/corretto.key | apt-key add -  && \
    add-apt-repository 'deb https://apt.corretto.aws stable main' && \
    apt update && \
    apt install -y  java-11-amazon-corretto-jdk

# aws cli (specific for ARM)

RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-aarch64.zip" -o "awscliv2.zip" && \
    unzip awscliv2.zip && \
    ./aws/install && \
    rm ./awscliv2.zip && \
    rm -rf ./aws 
    
# dotnet

# RUN curl "https://dot.net/v1/dotnet-install.sh" -o "dotnet-install.sh" && \
#    chmod +x ./dotnet-install.sh && \
#    ./dotnet-install.sh -c Current

#RUN curl -sSL https://dot.net/v1/dotnet-install.sh | bash /dev/stdin -c Current

ENV \
    # Unset ASPNETCORE_URLS from aspnet base image
    ASPNETCORE_URLS= \
    # Do not generate certificate
    DOTNET_GENERATE_ASPNET_CERTIFICATE=false \
    # Do not show first run text
    DOTNET_NOLOGO=true \
    # SDK version
    DOTNET_SDK_VERSION=6.0.102 \
    # Enable correct mode for dotnet watch (only mode supported in a container)
    DOTNET_USE_POLLING_FILE_WATCHER=true \
    # Unset Logging__Console__FormatterName from aspnet base image
    Logging__Console__FormatterName= \
    # Skip extraction of XML docs - generally not useful within an image/container - helps performance
    NUGET_XMLDOC_MODE=skip \
    # PowerShell telemetry for docker image usage
    POWERSHELL_DISTRIBUTION_CHANNEL=PSDocker-DotnetSDK-Debian-11-arm64

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        curl \
        git \
        wget \
    && rm -rf /var/lib/apt/lists/*

# Install .NET SDK
# RUN curl -fSL --output dotnet.tar.gz https://dotnetcli.azureedge.net/dotnet/Sdk/$DOTNET_SDK_VERSION/dotnet-sdk-$DOTNET_SDK_VERSION-linux-arm64.tar.gz 
# RUN dotnet_sha512='790cbf322ca8fed32eaf574f19d0bdc05656c5a88a65aa4dba8269cfce1443cd7cdeecdd3a40e353c368f055490b70592ca7f15f981a66c5b3a9517d0b09e4cb' 
# RUN echo "$dotnet_sha512  dotnet.tar.gz" | sha512sum -c -
# RUN mkdir -p /usr/share/dotnet 
# RUN  tar -oxzf dotnet.tar.gz -C /usr/share/dotnet ./packs ./sdk ./sdk-manifests ./templates ./LICENSE.txt ./ThirdPartyNotices.txt 
# RUN rm dotnet.tar.gz 
    # Trigger first run experience by running arbitrary cmd
# RUN dotnet help


# Install ansible
RUN  apt-add-repository "deb http://ppa.launchpad.net/ansible/ansible/ubuntu focal main" \
        && apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 93C4A3FD7BB9C367 \ 
        && apt update \
        && apt install -y ansible



