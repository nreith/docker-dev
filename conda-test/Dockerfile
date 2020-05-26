FROM continuum/anaconda3:5.0.1

LABEL maintaners="Nick Reith <nreith@gmail.com>"
SHELL ["/bin/bash", "-c"]
USER root
ENV \
  TERM="xterm" \
  TZ="US/Central" \
  DEBIAN_FRONTEND="noninteractive"

RUN cat /etc/os-release && \
# Installing apt-utils first to avoid error message
  apt-get clean && apt-get update && \
  apt-get install -y apt-utils --no-install-recommends && \
#
# Commonly installed packages
  apt-get install -y --no-install-recommends \
    apt-transport-https \
    build-essential \
    ca-certificates \
    curl \
    sudo \
    dialog `useful user dialogs` \
    file `recognizes filetype` \
    git \
    gnupg `for pgp keys` \
    iputils-ping `network testing` \
    tzdata \
    locales `internationalization` \
    nano `easy text editor` \
    net-tools `ifconfig, etc.` \
    openssh-server \
    rsync `to sync for git/azure workflow` \
    software-properties-common `necessary` \
    openjdk-8-jre \ 
    unzip \
    vim `only 1mb` \
    wget \
    zip
