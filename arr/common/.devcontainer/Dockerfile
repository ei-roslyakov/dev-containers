FROM mcr.microsoft.com/devcontainers/base:ubuntu

# Set non-interactive frontend
ENV DEBIAN_FRONTEND=noninteractive
ENV PIP_BREAK_SYSTEM_PACKAGES=1

# Versions (adjust as needed)
ARG ANSIBLE_VERSION=9.7.0
ARG AWS_CLI_VERSION=2.15.0
ARG HELM_VERSION=v3.15.0
ARG KUBECTL_VERSION=v1.30.1
ARG TERRAFORM_VERSION=1.7.5
ARG TERRAGRUNT_VERSION=0.59.1

# Install base packages
RUN apt-get update && apt-get install -y \
    curl \
    unzip \
    wget \
    git \
    python3 \
    python3-venv \
    python3-pip \
    jq \
    gnupg \
    ca-certificates \
    openssh-client \
    software-properties-common \
    && rm -rf /var/lib/apt/lists/*

# Install tfswitch
RUN curl -L https://raw.githubusercontent.com/warrensbox/terraform-switcher/release/install.sh | bash

# Install tgswitch
RUN curl -L https://raw.githubusercontent.com/warrensbox/tgswitch/release/install.sh | bash

# Install Terraform and Terragrunt
RUN tfswitch ${TERRAFORM_VERSION} && \
    tgswitch ${TERRAGRUNT_VERSION}

# Add tools to PATH if not already
ENV PATH="/root/.tfswitch/bin:/root/.tgswitch/bin:$PATH"

# Install Ansible (specific version)
RUN pip3 install ansible==${ANSIBLE_VERSION}

# Install aws-cli v2
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64-${AWS_CLI_VERSION}.zip" -o "awscliv2.zip" && \
    unzip awscliv2.zip && \
    ./aws/install && \
    rm -rf aws awscliv2.zip

# Install kubectl
RUN curl -LO "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl" && \
    install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl && \
    rm kubectl

# Install Helm
RUN curl -fsSL https://get.helm.sh/helm-${HELM_VERSION}-linux-amd64.tar.gz -o helm.tar.gz && \
    tar -zxvf helm.tar.gz && \
    mv linux-amd64/helm /usr/local/bin/helm && \
    rm -rf linux-amd64 helm.tar.gz

# Install tfswitch
RUN curl -L https://raw.githubusercontent.com/warrensbox/terraform-switcher/release/install.sh | bash

# Install tgswitch
RUN curl -L https://raw.githubusercontent.com/warrensbox/tgswitch/release/install.sh | bash

# Add tools to PATH if not already
ENV PATH="/home/vscode/bin:$PATH"

# Set default user
USER vscode
