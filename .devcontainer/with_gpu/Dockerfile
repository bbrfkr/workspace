FROM mcr.microsoft.com/devcontainers/base:noble
############################
### gpu setttings from #####
############################
# install cuda and container-tool-kit
RUN wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb && \
    dpkg -i cuda-keyring_1.1-1_all.deb && \
    rm -f cuda-keyring_1.1-1_all.deb && \
    apt-get update && \
    apt-get -y install cuda-12-6
RUN curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg && \
    curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' > /etc/apt/sources.list.d/nvidia-container-toolkit.list && \
    apt-get -y update && \
    apt-get install -y nvidia-container-toolkit && \
    nvidia-ctk runtime configure --runtime=docker

# set cuda env vars
ENV PATH="/usr/local/cuda/bin:$PATH"
ENV LD_LIBRARY_PATH="/usr/local/cuda/lib64"
############################
### gpu setttings to   #####
############################

# root user configuration
## install language dependencies
RUN apt-get update && \
    # for python
    apt-get install -y build-essential libssl-dev zlib1g-dev libbz2-dev libreadline-dev \
                       libsqlite3-dev curl git libncursesw5-dev xz-utils \
                       tk-dev libxml2-dev libxmlsec1-dev libffi-dev liblzma-dev && \
    # for ruby
    apt-get install -y autoconf patch build-essential rustc libssl-dev \
                       libyaml-dev libreadline6-dev zlib1g-dev libgmp-dev libncurses5-dev \
                       libffi-dev libgdbm6 libgdbm-dev libdb-dev uuid-dev && \
    # for nodejs
    apt-get install -y python3 g++-12 gcc-12 make python3-pip

## install docker
RUN apt-get update && \
    apt-get install -y ca-certificates curl && \
    install -m 0755 -d /etc/apt/keyrings && \
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc && \
    chmod a+r /etc/apt/keyrings/docker.asc && \
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" \
        > /etc/apt/sources.list.d/docker.list && \
    apt-get update && \
    apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

## install terraform and packer
RUN apt-get update && apt-get install -y gnupg software-properties-common && \
    wget -O- https://apt.releases.hashicorp.com/gpg | gpg --dearmor > /usr/share/keyrings/hashicorp-archive-keyring.gpg && \
    echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" \
        > /etc/apt/sources.list.d/hashicorp.list && \
    apt-get update && apt-get install terraform packer

# vscode user configuration
## create the devcontainer user
RUN useradd --home-dir /home/devcontainer --create-home devcontainer\
    && echo devcontainer ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/devcontainer \
    && chmod 0440 /etc/sudoers.d/devcontainer \
    && cp /home/vscode/.bashrc /home/devcontainer \
    && usermod -aG docker devcontainer

## install language
RUN git clone --depth 1 https://github.com/pyenv/pyenv /var/lib/pyenv
RUN git clone --depth 1 https://github.com/rbenv/rbenv /var/lib/rbenv && \
    git clone --depth 1 https://github.com/rbenv/ruby-build.git /var/lib/rbenv/plugins/ruby-build
RUN git clone --depth 1 https://github.com/nodenv/nodenv /var/lib/nodenv && \
    git clone --depth 1 https://github.com/nodenv/node-build.git /var/lib/nodenv/plugins/node-build

RUN chown devcontainer:devcontainer -R /var/lib/pyenv /var/lib/rbenv /var/lib/nodenv

ENV PATH=/var/lib/pyenv/bin:/var/lib/rbenv/bin:/var/lib/nodenv/bin:$PATH
ENV PYENV_ROOT=/var/lib/pyenv
ENV RBENV_ROOT=/var/lib/rbenv
ENV NODENV_ROOT=/var/lib/nodenv

RUN pyenv install 3.11
RUN rbenv install 3.3.8
RUN nodenv install 20.19.0

ENV PATH=/var/lib/pyenv/shims:/var/lib/rbenv/shims:/var/lib/nodenv/shims:$PATH

RUN pyenv global 3.11 && rbenv global 3.3.8 && nodenv global 20.19.0

## install pip packages
COPY requirements.txt .
RUN pip install -r requirements.txt && rm -f requirements.txt && \
    pip uninstall -y python-zaqarclient

## install gem packages
COPY Gemfile .
RUN gem install bundle && bundle install

ENTRYPOINT [ "dockerd" ]
