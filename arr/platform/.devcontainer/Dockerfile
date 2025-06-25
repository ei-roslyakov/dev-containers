FROM mcr.microsoft.com/devcontainers/base:ubuntu

# Set non-interactive frontend and pip override
ENV DEBIAN_FRONTEND=noninteractive
ENV PIP_BREAK_SYSTEM_PACKAGES=1

# Versions
ARG AWS_CLI_VERSION=2.15.0
ARG KUBECTL_VERSION=v1.30.1
ARG PYTHON_VERSION=3.11.9

# Install base packages and pyenv dependencies
RUN apt-get update && apt-get install -y \
    curl \
    unzip \
    wget \
    git \
    jq \
    gnupg \
    ca-certificates \
    openssh-client \
    software-properties-common \
    build-essential \
    libssl-dev \
    zlib1g-dev \
    libbz2-dev \
    libreadline-dev \
    libsqlite3-dev \
    libncursesw5-dev \
    xz-utils \
    tk-dev \
    libxml2-dev \
    libxmlsec1-dev \
    libffi-dev \
    liblzma-dev \
    && rm -rf /var/lib/apt/lists/*

# Install pyenv and desired Python version
ENV PYENV_ROOT="/home/vscode/.pyenv"
ENV PATH="${PYENV_ROOT}/bin:${PYENV_ROOT}/shims:${PATH}"

RUN git clone https://github.com/pyenv/pyenv.git $PYENV_ROOT && \
    chown -R vscode:vscode $PYENV_ROOT && \
    bash -c "source $PYENV_ROOT/bin/pyenv && pyenv install ${PYTHON_VERSION} && pyenv global ${PYTHON_VERSION}" && \
    echo 'export PYENV_ROOT="$HOME/.pyenv"' >> /home/vscode/.bashrc && \
    echo 'export PATH="$PYENV_ROOT/bin:$PATH"' >> /home/vscode/.bashrc && \
    echo 'eval "$(pyenv init --path)"' >> /home/vscode/.bashrc && \
    echo 'eval "$(pyenv init -)"' >> /home/vscode/.bashrc && \
    echo 'eval "$(pyenv virtualenv-init -)"' >> /home/vscode/.bashrc

# Install pipenv using pyenv-managed Python
RUN /home/vscode/.pyenv/versions/${PYTHON_VERSION}/bin/pip install --upgrade pip pipenv

# Install aws-cli v2
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64-${AWS_CLI_VERSION}.zip" -o "awscliv2.zip" && \
    unzip awscliv2.zip && \
    ./aws/install && \
    rm -rf aws awscliv2.zip

# Install kubectl
RUN curl -LO "https://dl.k8s.io/release/${KUBECTL_VERSION}/bin/linux/amd64/kubectl" && \
    install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl && \
    rm kubectl

# Add local bin to PATH
ENV PATH="/home/vscode/.local/bin:/home/vscode/bin:${PATH}"

# Set default user
USER vscode
