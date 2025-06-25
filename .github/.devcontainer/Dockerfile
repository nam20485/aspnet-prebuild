FROM mcr.microsoft.com/devcontainers/dotnet:1-9.0-noble

RUN echo 'debconf debconf/frontend select Noninteractive' | debconf-set-selections

RUN apt-get update -y && \
    apt-get upgrade -y && \
    apt-get install -y -q --no-install-recommends \
        build-essential \
        apt-transport-https \
        ca-certificates \
        gnupg \ 
        curl \
        software-properties-common && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/*

# Add .NET global tools path
ENV PATH=$PATH:/home/vscode/.dotnet:/home/vscode/.dotnet/tools

# install wasm-tools and aspire workloads
RUN dotnet workload update && \
    dotnet workload install wasm-tools aspire

# ASP.NET version
ARG aspnetcore_version=9.0.6
# ASP.NET runtime
RUN curl -fSL --output aspnetcore.tar.gz https://builds.dotnet.microsoft.com/dotnet/aspnetcore/Runtime/$aspnetcore_version/aspnetcore-runtime-$aspnetcore_version-linux-x64.tar.gz \
    && aspnetcore_sha512='54c122c4c6127ce7e0f0ad0479a101db1455484c9e5bffa8fdc0dd72d2db028be86861f331f2c7c1cb2eaee9a92e741e4e5da567795533e46af27e4e481c0451' \
    && echo "$aspnetcore_sha512  aspnetcore.tar.gz" | sha512sum -c - \
    && mkdir -p /dotnet \
    && tar -oxzf aspnetcore.tar.gz -C /dotnet ./shared/Microsoft.AspNetCore.App \
    && rm aspnetcore.tar.gz
# ASP.NET Core version
ENV ASPNET_VERSION=${aspnetcore_version}

#RUN apt-get install -y -q --no-install-recommends python3

#RUN & ([scriptblock]::Create((iwr 'https://to.loredo.me/Install-NerdFont.ps1'))) -Confirm:$false -Name hack, heavy-data

# node / npm
RUN curl -fsSL https://deb.nodesource.com/setup_22.x | bash - && \
    apt-get install -y -q nodejs \
    build-essential && \
    node --version && \ 
    npm --version

# gcloud CLI
RUN curl https://dl.google.com/dl/cloudsdk/release/google-cloud-sdk.tar.gz > /tmp/google-cloud-sdk.tar.gz
# Installing the package
RUN mkdir -p /usr/local/gcloud \
  && tar -C /usr/local/gcloud -xvf /tmp/google-cloud-sdk.tar.gz \
  && /usr/local/gcloud/google-cloud-sdk/install.sh
ENV PATH=$PATH:/usr/local/gcloud/google-cloud-sdk/bin
#RUN gcloud components update

# install firebase CLI
RUN npm install -g firebase-tools@14.6.0

# GitHub CLI
RUN mkdir -p -m 755 /etc/apt/keyrings \
    && out=$(mktemp) && curl -fsSL -o $out https://cli.github.com/packages/githubcli-archive-keyring.gpg \
    && cat $out | tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
	&& chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg\
	&& echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
	&& apt-get update \
	&& apt-get install gh -y -q --no-install-recommends

# terraform
RUN curl -fsSL https://apt.releases.hashicorp.com/gpg | gpg --dearmor -o /usr/share/keyrings/hashicorp-archive-keyring.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(grep -oP '(?<=UBUNTU_CODENAME=).*' /etc/os-release || lsb_release -cs) main" | tee /etc/apt/sources.list.d/hashicorp.list && \
    apt-get update && \
    apt-get install -y -q --no-install-recommends \
        terraform

# terraform CDK for C#
RUN npm install --global cdktf-cli@0.21.0

# check CLI commands working
RUN npm --version && \
    node --version && \
    gh --version && \
    gcloud --version && \
    firebase --version && \
    terraform --version && \
    cdktf --version
