ARG BASE_TAG="develop"
ARG BASE_IMAGE="core-ubuntu-focal"
FROM kasmweb/$BASE_IMAGE:$BASE_TAG
USER root

ENV HOME /home/kasm-default-profile
ENV STARTUPDIR /dockerstartup
ENV INST_SCRIPTS $STARTUPDIR/install
WORKDIR $HOME

######### Customize Container Here ###########
# Tesseract OCR
# Install Node.js and NPM
RUN apt-get update && \
    apt-get install -y software-properties-common && \
    add-apt-repository -y ppa:alex-p/tesseract-ocr && \
    apt-get update && \
    apt-get install -y tesseract-ocr-all
# CMake and build tools
RUN apt-get install -y git build-essential cmake
# FFmpeg
RUN apt-get install -y ffmpeg
# Node.js and NPM
RUN apt-get install -y nodejs npm
# Copy necessary files
COPY scripts /scripts
COPY pipeline /pipeline
COPY src/main/resources/static/fonts/*.ttf /usr/share/fonts/opentype/noto
COPY src/main/resources/static/fonts/*.otf /usr/share/fonts/opentype/noto
COPY build/libs/*.jar app.jar
    apt-get install -y nodejs npm

# Set up necessary directories and permissions
RUN mkdir -p  /configs /logs /customFiles /pipeline/watchedFolders /pipeline/finishedFolders && \
    chmod +x /scripts/*
RUN mkdir -p /configs /logs && \
    chown 1000:0 $HOME

EXPOSE 8080

ENTRYPOINT ["tini", "--", "/scripts/init.sh"]
CMD ["java", "-Dfile.encoding=UTF-8", "-jar", "/app.jar"]
######### Chromium #########
# Install Chromium
COPY ./src/ubuntu/install/chromium $INST_SCRIPTS/chromium/
RUN bash $INST_SCRIPTS/chromium/install_chromium.sh && rm -rf $INST_SCRIPTS/chromium/
# Update the desktop environment to be optimized for a single application
RUN cp $HOME/.config/xfce4/xfconf/single-application-xfce-perchannel-xml/* $HOME/.config/xfce4/xfconf/xfce-perchannel-xml/
RUN cp /usr/share/backgrounds/bg_kasm.png /usr/share/backgrounds/bg_default.png
RUN apt-get remove -y xfce4-panel
# Security modifications
COPY ./src/ubuntu/install/misc/single_app_security.sh $INST_SCRIPTS/misc/
RUN  bash $INST_SCRIPTS/misc/single_app_security.sh -t && rm -rf $INST_SCRIPTS/misc/
COPY ./src/common/chrome-managed-policies/urlblocklist.json /etc/chromium/policies/managed/urlblocklist.json
# Setup the custom startup script that will be invoked when the container starts
ENV LAUNCH_URL  http://127.0.0.1:8080
COPY ./src/ubuntu/install/chromium/custom_startup.sh $STARTUPDIR/custom_startup.sh
RUN chmod +x $STARTUPDIR/custom_startup.sh
# Install Custom Certificate Authority
# COPY ./src/ubuntu/install/certificates $INST_SCRIPTS/certificates/
# RUN bash $INST_SCRIPTS/certificates/install_ca_cert.sh && rm -rf $INST_SCRIPTS/certificates/
ENV KASM_RESTRICTED_FILE_CHOOSER=1
COPY ./src/ubuntu/install/gtk/ $INST_SCRIPTS/gtk/
RUN bash $INST_SCRIPTS/gtk/install_restricted_file_chooser.sh
######### End Customizations ###########
# Set user permissions
RUN chown 1000:0 $HOME
RUN $STARTUPDIR/set_user_permission.sh $HOME

ENV HOME /home/kasm-user
WORKDIR $HOME
RUN mkdir -p $HOME && chown -R 1000:0 $HOME

USER 1000