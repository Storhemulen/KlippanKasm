ARG BASE_TAG="develop"
ARG BASE_IMAGE="core-ubuntu-focal"
FROM kasmweb/$BASE_IMAGE:$BASE_TAG
USER root

ENV HOME /home/kasm-default-profile
WORKDIR $HOME

# Install Node.js and NPM
RUN apt-get update && \
    apt-get install -y nodejs npm

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
