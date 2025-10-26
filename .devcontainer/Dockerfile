FROM mcr.microsoft.com/devcontainers/base:noble
USER vscode
WORKDIR /home/vscode

# install python
RUN sudo apt-get update && \
    sudo apt-get -y install python3 python3-venv

# create python venv
RUN python3 -m venv .venv
ENV PATH=/home/vscode/.venv/bin:$PATH

# install pypi packages
COPY requirements.txt .
RUN pip install -r requirements.txt && \
    pip uninstall -y python-zaqarclient && \
    rm requirements.txt

# install tools
RUN sudo apt-get update && \
    sudo apt-get -y install vim \
                            jq \
                            netcat-openbsd \
                            iputils-ping
