FROM ubuntu:14.04

MAINTAINER hideakihal

# install basic package
ADD sources.list /etc/apt/sources.list
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update && \
    apt-get -y install \
    openssh-server \
    supervisor \
    build-essential \
    curl \
    unzip \
    git-core \
    subversion \
    mercurial \
    libcurl4-openssl-dev \
    libreadline-dev \
    libssl-dev \
    libxml2-dev \
    libxslt1-dev \
    libyaml-dev \
    zlib1g-dev && \
    apt-get clean && \
    curl -O http://ftp.ruby-lang.org/pub/ruby/2.1/ruby-2.1.2.tar.gz && \
    tar -zxvf ruby-2.1.2.tar.gz && \
    cd ruby-2.1.2 && \
    ./configure --disable-install-doc && \
    make && \
    make install && \
    cd .. && \
    rm -r ruby-2.1.2 ruby-2.1.2.tar.gz && \
    echo 'gem: --no-document' > /usr/local/etc/gemrc

# root user
RUN echo 'root:root' | chpasswd

# install sshd
RUN mkdir -p /root/.ssh /var/run/sshd
ADD id_rsa.pub /root/.ssh/authorized_keys
RUN chmod 700 /root/.ssh
RUN chmod 600 /root/.ssh/authorized_keys
RUN sed -ri 's/UsePAM yes/#UsePAM yes/g' /etc/ssh/sshd_config
RUN sed -ri 's/#UsePAM no/UsePAM no/g' /etc/ssh/sshd_config
RUN sed -ri 's/PermitRootLogin without-password/PermitRootLogin yes/g' /etc/ssh/sshd_config

# install supervisor
RUN mkdir -p /var/log/supervisor /etc/supervisor/conf.d
ADD sshd.conf /etc/supervisor/conf.d/sshd.conf

# install bundler
RUN \
  echo 'gem: --no-rdoc --no-ri' >> /.gemrc && \
  gem install bundler
