ARG BASE_TAG="develop"
ARG BASE_IMAGE="core-ubuntu-focal"
FROM kasmweb/$BASE_IMAGE:$BASE_TAG
USER root

ENV HOME /home/kasm-default-profile
WORKDIR $HOME

# Install Git, Python, Aider, and VSCode
RUN apt-get update && \
    apt-get install -y nodejs npm git python3 python3-pip wget gpg && \
    pip3 install aider && \
    wget -qO- https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > packages.microsoft.gpg && \
    install -o root -g root -m 644 packages.microsoft.gpg /etc/apt/trusted.gpg.d/ && \
    sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main" > /etc/apt/sources.list.d/vscode.list' && \
    apt-get update && \
    apt-get install -y code && \
    rm -f packages.microsoft.gpg

# Set up necessary directories and permissions
RUN mkdir -p /configs /logs && \
    chown 1000:0 $HOME

EXPOSE 8080

# Set user permissions
RUN chown 1000:0 $HOME

ENV HOME /home/kasm-user
WORKDIR $HOME
RUN mkdir -p $HOME && chown -R 1000:0 $HOME

USER 1000
