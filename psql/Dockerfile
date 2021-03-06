FROM ubuntu:18.04
 
COPY conf/openvassd.conf /usr/local/etc/openvas/openvassd.conf
COPY script/entrypoint.sh /entrypoint.sh

ENV DEBIAN_FRONTEND=noninteractive \
    GSE_PASSWORD=admin \
    HOSTNAME=gs \
    SRC_DIR=gse-git \
    SRC_PATH=/root/${SRC_DIR} \
    PGUSERNAME=root


RUN apt-get update ;\
    apt-get install apt-utils software-properties-common --no-install-recommends -yq ;\
    apt-get dist-upgrade -yq ;\
    apt-get install alien \
        cmake \
        pkg-config \
        libglib2.0-dev \
        libgpgme11-dev \
        uuid-dev \
        libssh-gcrypt-dev \
        libhiredis-dev \
        gcc \
        libgnutls28-dev \
        libpcap-dev \
        libgpgme-dev \
        bison \
        libksba-dev \
        libsnmp-dev \
        libgcrypt20-dev \
        redis-server \
        libsqlite3-dev \
        libical-dev \
        gnutls-bin \
        doxygen \
        nmap \
        libmicrohttpd-dev \
        libxml2-dev \
        apt-transport-https \
        sqlfairy \
        xmltoman \
        xsltproc \
        gcc-mingw-w64 \
        perl-base \
        heimdal-dev \
        libpopt-dev \
        graphviz \
        nodejs \
        rpm \
        nsis \
        wget \
        sshpass \
        socat \
        snmp \
        postgresql \
        postgresql-contrib \
        postgresql-server-dev-all \
        libpq-dev \
        git \
        libldap2-dev \
        libfreeradius-dev \
        sudo \
        curl \
        python-polib \
        rsync \
        gettext \
        xmlstarlet \
        zip \
        gnupg \
        -yq ;\
    apt-get install texlive-latex-extra --no-install-recommends -yq ;\
    apt-get install texlive-fonts-recommended -yq

RUN curl --silent --show-error https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add - ;\
    echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list ;\
    apt-get update ;\
    apt-get install yarn -yq ;\
    apt-get clean ;\
    rm -rf /var/lib/apt/lists/*

RUN mkdir ${SRC_PATH} -p ;\
    cd ${SRC_PATH} ;\
    wget -O gvm-libs.tar.gz https://github.com/greenbone/gvm-libs/archive/v10.0.1.tar.gz ;\
    wget -O openvas.tar.gz https://github.com/greenbone/openvas/archive/v6.0.1.tar.gz ;\
    wget -O gvmd.tar.gz https://github.com/greenbone/gvmd/archive/v8.0.1.tar.gz ;\
    wget -O gsa.tar.gz https://github.com/greenbone/gsa/archive/v8.0.1.tar.gz ;\
    wget -O openvas-smb.tar.gz https://github.com/greenbone/openvas-smb/archive/v1.0.5.tar.gz ;\
    wget -O ospd.tar.gz https://github.com/greenbone/ospd/archive/v1.3.2.tar.gz

RUN cd ${SRC_PATH} ;\
    find . -name \*.gz -exec tar zxvfp {} \;


RUN  cd ${SRC_PATH}/gvm-libs-* ;\
    mkdir build ;\
    cd build ;\
    cmake .. ;\
    make ;\
    make doc-full ;\
    make install ;\
    cd ${SRC_PATH}

RUN  cd ${SRC_PATH}/openvas-smb-* ;\
    mkdir build ;\
    cd build ;\
    cmake .. ;\
    make ;\
    make install ;\
    cd ${SRC_PATH}

RUN  cd ${SRC_PATH}/openvas-* ;\
    mkdir build ;\
    cd build ;\
    cmake .. ;\
    make ;\
    make doc-full ;\
    make install ;\
    cd ${SRC_PATH}

COPY conf/redis.config /etc/redis/redis.conf

RUN service redis-server restart

RUN greenbone-nvt-sync 

RUN cd ${SRC_PATH}/gvmd-* ;\
    mkdir build ;\
    cd build ;\
    cmake -DBACKEND=POSTGRESQL .. ;\
    make ;\
    make doc-full ;\
    make install ;\
    cd ${SRC_PATH}

RUN ldconfig

RUN cd ${SRC_PATH}/gsa-* ;\
    mkdir build ;\
    cd build ;\
    cmake .. ;\
    make ;\
    make doc-full ;\
    make install ;\
    cd ${SRC_PATH}

RUN rm  /root/${SRC_PATH}/ -rf

CMD /entrypoint.sh
EXPOSE 80 443 9391