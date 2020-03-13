#-------------------------------------------------------------------------------------------------------------
# Copyright (c) Microsoft Corporation. All rights reserved.
# Licensed under the MIT License. See https://go.microsoft.com/fwlink/?linkid=2090316 for license information.
#-------------------------------------------------------------------------------------------------------------

FROM mcr.microsoft.com/dotnet/core/sdk:3.1

# This Dockerfile adds a non-root user with sudo access. Use the "remoteUser"
# property in devcontainer.json to use it. On Linux, the container user's GID/UIDs
# will be updated to match your local UID/GID (when using the dockerFile property).
# See https://aka.ms/vscode-remote/containers/non-root-user for details.
ARG USERNAME=vscode
ARG USER_UID=1000
ARG USER_GID=$USER_UID

# The version of Node JS to install
ARG NODE_VERSION=12.x

# [Optional] Version of Node.js to install.
# ARG NODE_VERSION="lts/*"
# ENV NVM_DIR=/home/vscode/.nvm

# Avoid warnings by switching to noninteractive
ENV DEBIAN_FRONTEND=noninteractive

# Configure apt and install packages
RUN apt-get update \
    && apt-get -y install --no-install-recommends apt-utils dialog 2>&1 \
    
    # Verify git, process tools, lsb-release (common in install instructions for CLIs) installed
    && apt-get -y install git openssh-client less iproute2 procps apt-transport-https gnupg2 curl lsb-release \
    
    # Create a non-root user to use if preferred - see https://aka.ms/vscode-remote/containers/non-root-user.
    && groupadd --gid $USER_GID $USERNAME \
    && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
    
    # [Optional] Add sudo support for the non-root user
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# Node JS
RUN curl -sL https://deb.nodesource.com/setup_${NODE_VERSION} | bash - \
    && apt-get update \
    && apt-get install -y nodejs

# Install nvm and Node
# RUN mkdir ${NVM_DIR} \
#     && curl -so- https://raw.githubusercontent.com/creationix/nvm/v0.34.0/install.sh | bash 2>&1 \
#     && chown -R vscode:vscode ${NVM_DIR} \
#     && /bin/bash -c "source $NVM_DIR/nvm.sh \
#         && nvm install ${NODE_VERSION} \
#         && nvm alias default ${NODE_VERSION}" 2>&1 \
#     && INIT_STRING='[ -s "$NVM_DIR/nvm.sh" ] && \\. "$NVM_DIR/nvm.sh"  && [ -s "$NVM_DIR/bash_completion" ] && \\. "$NVM_DIR/bash_completion"' \
#     && echo $INIT_STRING >> /home/vscode/.bashrc \
#     && echo $INIT_STRING >> /home/vscode/.zshrc \
#     && echo $INIT_STRING >> /root/.zshrc
    
# Install yarn
# RUN curl -sS https://dl.yarnpkg.com/$(lsb_release -is | tr '[:upper:]' '[:lower:]')/pubkey.gpg | apt-key add - 2>/dev/null \
#     && echo "deb https://dl.yarnpkg.com/$(lsb_release -is | tr '[:upper:]' '[:lower:]')/ stable main" | tee /etc/apt/sources.list.d/yarn.list \
#     && apt-get update \
#     && apt-get -y install --no-install-recommends yarn;
    
# Install the Azure CLI
RUN echo "deb [arch=amd64] https://packages.microsoft.com/repos/azure-cli/ $(lsb_release -cs) main" > /etc/apt/sources.list.d/azure-cli.list \
    && curl -sL https://packages.microsoft.com/keys/microsoft.asc | apt-key add - 2>/dev/null \
    && apt-get update \
    && apt-get install -y azure-cli;

# Install Bot Stuff
RUN npm install -g @microsoft/botframework-cli
#botskills@latest
#     && npm insall -g botdispatch luis-apis \

# Clean up
RUN apt-get autoremove -y \
    && apt-get clean -y \
    && rm -rf /var/lib/apt/lists/*

# Switch back to dialog for any ad-hoc use of apt-get
ENV DEBIAN_FRONTEND=dialog