# syntax=docker/dockerfile:1

FROM ghcr.io/linuxserver/unrar:latest AS unrar
FROM ghcr.io/linuxserver/baseimage-selkies:alpine322

ENV APPDIR="/app"
ENV HARDEN_DESKTOP=true
ENV HARDEN_OPENBOX=true
ENV TITLE='Kindle Comic Converter'
ENV DISABLE_ZINK=true
ENV DISABLE_DRI3=true
ENV NO_GAMEPAD=true
ENV NO_DECOR=true
ENV SELKIES_UI_SHOW_SIDEBAR=false
ENV SELKIES_UI_SHOW_CORE_BUTTONS=false
ENV SELKIES_AUDIO_ENABLED=false
ENV SELKIES_MICROPHONE_ENABLED=false
ENV SELKIES_GAMEPAD_ENABLED=false
ENV SELKIES_ENABLE_SHARING=false
ENV SELKIES_IS_MANUAL_RESOLUTION_MODE=true
ENV SELKIES_MANUAL_HEIGHT=1080
ENV SELKIES_MANUAL_WIDTH=1920
ENV MAX_RESOLUTION=1920x1080

WORKDIR ${APPDIR}

RUN echo "**** install packages ****" && \
  apk add --no-cache --repository=https://dl-cdn.alpinelinux.org/alpine/edge/testing \
    unzip \
    7zip \
    libpng \
    libjpeg \
    git \
    curl \
    python3 \
    pyside6 \
    py3-pyside6 \
    py3-pip \
    py3-pymupdf-pyc && \
  \
  echo "**** install kcc ****" && \
  cd ${APPDIR} && \
  curl -s https://api.github.com/repos/ciromattia/kcc/releases/latest \
    | grep  -m 1 '"url":' | cut -d : -f 2,3 | tr -d \" | sed 's/,*$//g' \
    | xargs -n1 curl -s | grep tarball_url | cut -d : -f 2,3 | tr -d \" | sed 's/,*$//g' \
    | xargs -n1 curl -L -o "${APPDIR}/latest_kcc.tar.gz" && \
  tar -xzf latest_kcc.tar.gz --strip-components=1 && \
  \
  echo "**** setup icon ****" && \
  cp icons/comic2ebook.png /usr/share/selkies/www/icon.png && \
  \
  echo "**** install python requirements ****" && \
  python -m pip install numpy && \
  python -m pip install PyMuPDF && \
  python -m pip install raven && \
  python -m pip install -r requirements-docker.txt && \
  \
  echo "**** install kindlegen ****" && \
  curl https://archive.org/download/kindlegen_linux_2_6_i386_v2_9/kindlegen_linux_2.6_i386_v2_9.tar.gz -L -o "${APPDIR}/kindlegen.tar.gz" && \
  tar -zxf kindlegen.tar.gz kindlegen && \
  chmod a+x kindlegen && \
  ln kindlegen /usr/local/bin/kindlegen && \
  \
  echo "**** cleanup ****" && \
  rm latest_kcc.tar.gz kindlegen.tar.gz && \
  \
  echo "**** make runners ****" && \
  mkdir -p /defaults && \
  echo "python3 /app/kcc.py" > /defaults/autostart  && \
  echo "#!/usr/bin/env bash" > /defaults/startwm.sh && \
  echo "# Start DE" >> /defaults/startwm.sh && \
  echo "exec dbus-launch --exit-with-session /usr/bin/openbox-session > /dev/null 2>&1" >> /defaults/startwm.sh && \
  echo '<?xml version="1.0" encoding="utf-8"?>' > /defaults/menu.xml && \
  echo '<openbox_menu xmlns="http://openbox.org/3.4/menu">' >> /defaults/menu.xml && \
  echo '  <menu id="root-menu" label="MENU">' >> /defaults/menu.xml && \
  echo '   <item label="KCC" icon="${APPDIR}icons/comic2ebook.png">' >> /defaults/menu.xml && \
  echo '      <action name="Execute">' >> /defaults/menu.xml && \
  echo '        <command>python3 /app/kcc.py</command>' >> /defaults/menu.xml && \
  echo '      </action>' >> /defaults/menu.xml && \
  echo '    </item>' >> /defaults/menu.xml && \
  echo '  </menu>' >> /defaults/menu.xml && \
  echo '</openbox_menu>' >> /defaults/menu.xml
  

COPY --from=unrar /usr/bin/unrar-alpine /usr/bin/unrar

VOLUME ${APPDIR}

EXPOSE 3000 3001
