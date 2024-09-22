FROM python:3.10-slim AS base

# Install basic packages and KASM dependencies
RUN apt-get update && \
    apt-get install --no-install-recommends -y build-essential git libportaudio2 pandoc \
    xfce4 xfce4-terminal x11vnc xvfb supervisor dbus-x11 xfonts-base xfonts-75dpi \
    pulseaudio firefox-esr alsa-utils mesa-utils && \
    rm -rf /var/lib/apt/lists/*

WORKDIR /app

# Create Python virtual environment
RUN python -m venv /venv
ENV PATH="/venv/bin:$PATH"

# Install playwright browsers
ENV PLAYWRIGHT_BROWSERS_PATH=/pw-browsers
ENV PLAYWRIGHT_SKIP_BROWSER_GC=1

# Permission fixes for Docker user
RUN chmod a+rwx /venv /venv/bin /venv/include /venv/lib /venv/lib/python3.10/site-packages

RUN mkdir       /.aider /.cache /pw-browsers
RUN chmod a+rwx /.aider /.cache /pw-browsers

# Set Git directory permissions
RUN git config --system --add safe.directory /app

#########################
FROM base AS aider-full

# Set environment variable to tag this container as aider-full
ENV AIDER_DOCKER_IMAGE=paulgauthier/aider-full

COPY . /tmp/aider
RUN /venv/bin/python -m pip install --upgrade --no-cache-dir pip \
    && /venv/bin/python -m pip install --no-cache-dir /tmp/aider[help,browser,playwright] \
       --extra-index-url https://download.pytorch.org/whl/cpu \
    && rm -rf /tmp/aider

# Install playwright chromium
RUN /venv/bin/python -m playwright install --with-deps chromium
RUN find /venv/lib/python3.10/site-packages \( -type d -exec chmod a+rwx {} + \) -o \( -type f -exec chmod a+rw {} + \)

#########################
# KASM-specific configuration
# Create necessary directories for supervisor and VNC setup
RUN mkdir -p /etc/supervisor/conf.d

# Create supervisord configuration file
COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# Setup environment variables for KASM workspace
ENV DISPLAY=:1
ENV NO_VNC_PORT=6901
ENV VNC_PORT=5901

# Expose VNC and NO_VNC ports
EXPOSE 5901 6901

# Set up KASM workspace entrypoint to start supervisord
ENTRYPOINT ["/usr/bin/supervisord"]