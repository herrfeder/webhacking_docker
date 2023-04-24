FROM kalilinux/kali-rolling

ARG BURPVERSION

# built-in packages
ENV DEBIAN_FRONTEND noninteractive
RUN apt update \
    && apt install -y --no-install-recommends software-properties-common curl apache2-utils \
    && apt update \
    && apt install -y --no-install-recommends --allow-unauthenticated \
        supervisor sudo net-tools zenity xz-utils \
        dbus-x11 x11-utils \
	xserver-xorg-core xserver-xorg-video-fbdev xserver-xorg-input-evdev xinit xfonts-base \
    && apt autoclean -y \
    && apt autoremove -y

# install debs error if combine together
RUN apt update \
    && apt install -y --no-install-recommends --allow-unauthenticated \
        xvfb x11vnc \
        firefox-esr  \
    && apt autoclean -y \
    && apt autoremove -y

RUN apt update \
    && apt install -y gpg-agent

RUN apt update \
    && apt install -y --no-install-recommends --allow-unauthenticated \
        lxde gtk2-engines-murrine gnome-themes-extra gtk2-engines-pixbuf arc-theme \
    && apt autoclean -y \
    && apt autoremove -y

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
    && apt-get install -y openjdk-11-jre openjdk-11-jdk openjdk-11-jdk-headless \
    && apt-get install -y terminator vim tmux zip unzip \
    && apt-get install -y default-mysql-client tree

RUN apt-get install -y openssh-server \
    && apt-get install -y git \
    && apt-get install -y parallel openvpn rsync

RUN apt-get install -y freerdp2-x11 krdc

RUN mkdir -p /run/sshd

# generic hacking stuff
RUN apt-get install -y ncat netcat-traditional metasploit-framework python3-impacket webshells jd-gui burpsuite

# webhacking aptitude stuff
RUN apt-get update \
    && apt-get install -y nmap curl \
    && apt-get install -y cewl dirb dirsearch dotdotpwn

RUN useradd -ms /bin/bash user && usermod -aG sudo user

# webhacking pip stuff
RUN pip3 install --break-system-packages wfuzz==3.1 pycurl==7.44.1 BeautifulSoup4 random_user_agent

# developer pip stuff
RUN pip3 install --break-system-packages flask-cors websocket python-engineio==3.14.2 python-socketio[client]==4.6.0

# vscode server
ENV VERSION 3.12.0
ENV DOMAIN localhost
ENV DOCKER_VERSION 20.10.7

# install prerequisities for code-server
RUN apt update \
    && apt install ca-certificates nodejs git wget -y \
    && update-ca-certificates \
    && apt-get clean

# install code-server
RUN mkdir -p ~/.local/lib ~/.local/bin && \
    curl -fL https://github.com/coder/code-server/releases/download/v${VERSION}/code-server-${VERSION}-linux-amd64.tar.gz \
    | tar -C ~/.local/lib -xz && \
    mv ~/.local/lib/code-server-${VERSION}-linux-amd64 ~/.local/lib/code-server-${VERSION} && \
    ln -s ~/.local/lib/code-server-${VERSION}/bin/code-server ~/.local/bin/code-server

RUN mkdir /root/.config && cp /root/.bashrc /home/user/.bashrc && chown user:user /home/user/.bashrc
RUN ls -la /home/user/
COPY rootfs /

ENV HOME=/home/user \
    SHELL=/bin/bash

WORKDIR /root
ENTRYPOINT ["/startup.sh"]
