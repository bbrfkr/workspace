FROM mcr.microsoft.com/devcontainers/base:noble
USER vscode
WORKDIR /home/vscode

# install pyenv and Python via pyenv
RUN sudo apt-get update && \
    sudo apt-get -y install \
        build-essential \
        curl \
        git \
        libssl-dev \
        zlib1g-dev \
        libbz2-dev \
        libreadline-dev \
        libsqlite3-dev \
        wget \
        llvm \
        libncurses5-dev \
        xz-utils \
        tk-dev \
        libxml2-dev \
        libxmlsec1-dev \
        libffi-dev \
        liblzma-dev && \
    sudo rm -rf /var/lib/apt/lists/*

# install pyenv
ENV PYENV_ROOT=/home/vscode/.pyenv
ENV PATH=$PYENV_ROOT/shims:$PYENV_ROOT/bin:$PATH
RUN git clone https://github.com/pyenv/pyenv.git $PYENV_ROOT

# install desired Python version via pyenv and set as global (latest stable)
RUN pyenv install 3.13.0 && pyenv global 3.13.0

# create python virtual environment using the pyenv-installed python
RUN python -m venv .venv
ENV PATH=/home/vscode/.venv/bin:$PATH

# install nodenv and Node.js via nodenv
ENV NODENV_ROOT=/home/vscode/.nodenv
ENV PATH=$NODENV_ROOT/shims:$NODENV_ROOT/bin:$PATH
RUN git clone https://github.com/nodenv/nodenv.git $NODENV_ROOT \
    && git clone https://github.com/nodenv/node-build.git $NODENV_ROOT/plugins/node-build

# install desired Node version via nodenv and set as global (latest LTS)
RUN nodenv install 24.13.0 && nodenv global 24.13.0

# install pypi packages
COPY requirements.txt .
RUN pip install -r requirements.txt && \
    pip uninstall -y python-zaqarclient && \
    rm requirements.txt

# install common tools
RUN sudo apt-get update && \
    sudo apt-get -y install vim \
                            jq \
                            netcat-openbsd \
                            iputils-ping

# install Docker CLI (official)
RUN sudo apt-get update && \
    sudo apt-get -y install ca-certificates curl gnupg lsb-release && \
    sudo mkdir -p /etc/apt/keyrings && \
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null && \
    sudo apt-get update && \
    sudo apt-get -y install docker-ce-cli docker-buildx-plugin docker-compose-plugin && \
    sudo rm -rf /var/lib/apt/lists/*

# install coding agent
RUN npm install -g @openai/codex @anthropic-ai/claude-code
RUN nodenv rehash

# install tofuenv and opentofu
ENV TOFUENV=/home/vscode/.tofuenv
RUN git clone --depth 1 https://github.com/tofuutils/tofuenv $TOFUENV
ENV PATH=$TOFUENV/bin:$PATH
RUN tofuenv use 1.10.6

# install packer
RUN sudo apt-get update && sudo apt-get install -y gnupg software-properties-common && \
    wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor | sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg && \
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
        sudo tee /etc/apt/sources.list.d/hashicorp.list && \
    sudo apt-get update && sudo apt-get install packer

# install awscli
RUN curl "https://awscli.amazonaws.com/awscli-exe-linux-$(uname -i).zip" -o "/tmp/awscliv2.zip" && \
    cd /tmp && \
    unzip awscliv2.zip && \
    sudo ./aws/install && \
    rm -rf ./aws*
# fix awscli v1 to aws-v1
RUN mv /home/vscode/.venv/bin/aws /home/vscode/.venv/bin/aws-v1

# install gcloud
RUN curl https://packages.cloud.google.com/apt/doc/apt-key.gpg | \
    sudo gpg --dearmor -o /usr/share/keyrings/cloud.google.gpg && \
    echo "deb [signed-by=/usr/share/keyrings/cloud.google.gpg] https://packages.cloud.google.com/apt cloud-sdk main" | \
    sudo tee -a /etc/apt/sources.list.d/google-cloud-sdk.list && \
    sudo apt-get update && \
    sudo apt-get -y install google-cloud-cli

# install playwright deps
RUN sudo apt-get -y install \
        libatk1.0-0t64\
        libatk-bridge2.0-0t64\
        libcups2t64\
        libxcomposite1\
        libxdamage1\
        libxfixes3\
        libxrandr2\
        libgbm1\
        libxkbcommon0\
        libpango-1.0-0\
        libcairo2\
        libasound2t64\
        libatspi2.0-0t64       

# install mise
RUN curl https://mise.run | sh && \
    echo 'eval "$(~/.local/bin/mise activate bash)"' >> ~/.bashrc
