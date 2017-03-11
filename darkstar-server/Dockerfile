FROM ubuntu:trusty

ENV DS_BRANCH=stable

RUN apt-get update && \
  apt-get install -y build-essential git libmysqlclient-dev libluajit-5.1-dev \
  libzmq3-dev autoconf pkg-config software-properties-common python-pip && \
  apt-add-repository -y ppa:ubuntu-toolchain-r/test && \
  apt-get update && \
  apt-get install -y g++-5 && \
  cd /usr/bin && \
  rm gcc g++ cpp && \
  ln -s gcc-5 gcc && \
  ln -s g++-5 g++ && \
  ln -s cpp-5 cpp && \
  easy_install supervisor && \
  pip install supervisor-stdout && \
  git clone --depth=1 -b ${DS_BRANCH} http://github.com/DarkstarProject/darkstar.git/ /darkstar && \
  cd /darkstar && \
  sh autogen.sh && \
  ./configure --enable-debug=gdb && \
  make -j12 && \
  apt-get autoremove -y build-essential git autoconf pkg-config software-properties-common && \
  rm -rf /var/lib/apt/lists/* && \
  rm -rf /darkstar/src && \
  rm -rf /darkstar/sql

COPY etc/ etc/
COPY docker-entrypoint.sh /usr/local/bin/

# add darkstar user and fix permissions
RUN groupadd -r darkstar && \
  useradd -g darkstar -ms /bin/bash darkstar && \
  chown -R darkstar:darkstar /darkstar && \
  chmod a+x /usr/local/bin/docker-entrypoint.sh

USER darkstar
EXPOSE 54230 54230/udp 54231 54001 54002
WORKDIR /darkstar

CMD ["docker-entrypoint.sh"]