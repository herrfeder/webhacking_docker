# Built with arch: amd64 flavor: lxde image: ubuntu:20.04
#
################################################################################
# base system
################################################################################

FROM kalilinux/kali-rolling

#RUN sed -i 's#http://archive.ubuntu.com/ubuntu/#mirror://mirrors.ubuntu.com/mirrors.txt#' /etc/apt/sources.list;


# built-in packages
ENV DEBIAN_FRONTEND noninteractive
RUN apt update \
    && apt install -y --no-install-recommends software-properties-common curl apache2-utils \
    && apt update \
    && apt install -y --no-install-recommends --allow-unauthenticated \
        supervisor nginx sudo net-tools zenity xz-utils \
        dbus-x11 x11-utils alsa-utils \
        mesa-utils libgl1-mesa-dri \
    && apt autoclean -y \
    && apt autoremove -y
# install debs error if combine together
RUN apt update \
    && apt install -y --no-install-recommends --allow-unauthenticated \
        xvfb x11vnc \
        vim-tiny firefox-esr ttf-ubuntu-font-family ttf-wqy-zenhei  \
    && apt autoclean -y \
    && apt autoremove -y

RUN apt update \
    && apt install -y gpg-agent \
    && curl -LO https://dl.google.com/linux/direct/google-chrome-stable_current_amd64.deb \
    && (dpkg -i ./google-chrome-stable_current_amd64.deb || apt-get install -fy) \
    && curl -sSL https://dl.google.com/linux/linux_signing_key.pub | apt-key add \
    && rm google-chrome-stable_current_amd64.deb

RUN apt update \
    && apt install -y --no-install-recommends --allow-unauthenticated \
        lxde gtk2-engines-murrine gnome-themes-standard gtk2-engines-pixbuf gtk2-engines-murrine arc-theme \
    && apt autoclean -y \
    && apt autoremove -y

# Additional packages require ~600MB
# libreoffice  pinta language-pack-zh-hant language-pack-gnome-zh-hant firefox-locale-zh-hant libreoffice-l10n-zh-tw

# tini to fix subreap
ARG TINI_VERSION=v0.18.0
ADD https://github.com/krallin/tini/releases/download/${TINI_VERSION}/tini /bin/tini
RUN chmod +x /bin/tini

# ffmpeg
RUN apt update \
    && apt install -y --no-install-recommends --allow-unauthenticated \
        ffmpeg \
    && mkdir /usr/local/ffmpeg \
    && ln -s /usr/bin/ffmpeg /usr/local/ffmpeg/ffmpeg

# python library
RUN apt-get update \
    && apt-get install -y python3-pip python3-dev build-essential libcurl4-openssl-dev libssl-dev \
    && ln -s /usr/bin/python3 /usr/local/bin/python \
    && apt-get autoclean -y \
    && apt-get autoremove -y \
    && rm -rf /var/cache/apt/* /tmp/a.txt /tmp/b.txt


# environment prep

RUN apt-get update \
    && apt-get install -y openjdk-17-jre \
    && apt-get install -y terminator vim vim-gtk tmux unzip \
    && apt-get install -y 

RUN apt-get install -y openssh-server \
    && apt-get install -y git \
    && apt-get install -y parallel openvpn 

RUN mkdir -p /run/sshd


# generic hacking stuff

RUN apt-get install -y ncat netcat-traditional

# webhacking aptitude stuff

RUN apt-get update \
    && apt-get install -y nmap curl \
    && apt-get install -y golang cewl dirb


RUN git clone https://github.com/robertdavidgraham/masscan && \
    cd masscan && \
    apt-get install -y git gcc make libpcap-dev && \
    make install

RUN go get github.com/OJ/gobuster


# webhacking pip stuff

RUN pip3 install wfuzz==3.1 pycurl==7.44.1


COPY rootfs /

WORKDIR /root
ENV HOME=/home/ubuntu \
    SHELL=/bin/bash
HEALTHCHECK --interval=30s --timeout=5s CMD curl --fail http://127.0.0.1:6079/api/health

ENTRYPOINT ["/startup.sh"]
