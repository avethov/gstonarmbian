# GStreamer-1.12.2 rtsp server on Armbian
INSTALLATION
------------

REQUIREMENTS
------------


      build-essential
      bison
      flex
      libglib2.0-0
      libglib2.0-dev
      v4l-utils
      autoconf
      automake
      gettext
      libtool
      libx11-dev
      libxv-dev
      libxt-dev
      x264
      libx264-dev

QUICK START
-----------
sudo service ssh start
sudo vim /etc/environment

http_proxy=http://proxy.elveesneotek.internal:3128/
ftp_proxy=ftp://proxy.elveesneotek.internal:3128/
https_proxy=https://proxy.elveesneotek.internal:3128/

sudo vim /etc/apt/apt.conf

Acquire::http::proxy "http://proxy.elveesneotek.internal:3128/";
Acquire::ftp::proxy "ftp://proxy.elveesneotek.internal:3128/";
Acquire::https::proxy "https://proxy.elveesneotek.internal:3128/";

sudo reboot 0

sudo apt-get install build-essential bison flex libglib2.0-0 libglib2.0-dev v4l-utils autoconf automake gettext libtool libx11-dev libxv-dev libxt-dev x264 libx264-dev
mkdir gstreamer-1.12.2
cd gstreamer-1.12.2/
wget https://gstreamer.freedesktop.org/src/gstreamer/gstreamer-1.12.2.tar.xz
wget https://gstreamer.freedesktop.org/src/gst-rtsp-server/gst-rtsp-server-1.12.2.tar.xz
wget https://gstreamer.freedesktop.org/src/gst-plugins-ugly/gst-plugins-ugly-1.12.2.tar.xz
wget https://gstreamer.freedesktop.org/src/gst-plugins-good/gst-plugins-good-1.12.2.tar.xz
wget https://gstreamer.freedesktop.org/src/gst-plugins-base/gst-plugins-base-1.12.2.tar.xz
wget https://gstreamer.freedesktop.org/src/gst-plugins-bad/gst-plugins-bad-1.12.2.tar.xz
wget https://gstreamer.freedesktop.org/src/gst-libav/gst-libav-1.12.2.tar.xz
wget https://gstreamer.freedesktop.org/src/orc/orc-0.4.27.tar.xz

tar -xf gstreamer-1.12.2.tar.xz && cd gstreamer-1.12.2/ && ./configure && make && sudo make install
cd .. && rm gstreamer-1.12.2.tar.xz
sudo vim /etc/environment
LD_LIBRARY_PATH="/usr/local/lib"
sudo reboot 0

cd gstreamer-1.12.2/ 
tar -xf orc-0.4.27.tar.xz && cd orc-0.4.27/ && ./configure && make && sudo make install

cd .. && rm orc-0.4.27.tar.xz
tar -xf gst-plugins-base-1.12.2.tar.xz && cd gst-plugins-base-1.12.2/ && ./configure && make && sudo make install

cd .. && rm gst-plugins-base-1.12.2.tar.xz
tar -xf gst-plugins-good-1.12.2.tar.xz && cd gst-plugins-good-1.12.2/ && ./configure && make && sudo make install

cd .. && rm gst-plugins-good-1.12.2.tar.xz
tar -xf gst-plugins-ugly-1.12.2.tar.xz && cd gst-plugins-ugly-1.12.2/ && ./configure && make && sudo make install

cd .. && rm gst-plugins-ugly-1.12.2.tar.xz
tar -xf gst-plugins-bad-1.12.2.tar.xz && cd gst-plugins-bad-1.12.2/ && ./configure && make && sudo make install

cd .. && rm gst-plugins-bad-1.12.2.tar.xz
tar -xf gst-rtsp-server-1.12.2.tar.xz && cd gst-rtsp-server-1.12.2/ && ./configure && make && sudo make install 

cd .. && rm gst-rtsp-server-1.12.2.tar.xz
cd .. && vim run_rtsp_server.sh

#!/bin/sh

export LD_LIBRARY_PATH=/usr/local/lib
/home/sber/gstreamer-1.12.2/gst-rtsp-server-1.12.2/examples/test-launch "v4l2src device=/dev/video0 ! video/x-raw,format=YUY2,width=320,heigth=240,framerate=30/1 ! videoconvert ! queue ! x264enc speed-preset=ultrafast tune=zerolatency key-int-max=1 ! video/x-h264,profile=main ! queue ! rtph264pay name=pay0 pt=96 config-interval=2"

sudo chmod 775 run_rtsp_server.sh
sudo vim /etc/rc.local

/home/sber/run_rtsp_server.sh

sudo reboot 0
