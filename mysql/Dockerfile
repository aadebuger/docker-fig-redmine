FROM ubuntu:14.04

MAINTAINER hideakihal

# install basic package
ADD sources.list /etc/apt/sources.list
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update && \
    apt-get -y install \
      mysql-server \
      openssh-server \
      supervisor && \
      apt-get clean

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

# config supervisor
RUN mkdir -p /var/log/supervisor /etc/supervisor/conf.d
ADD mysqld.conf /etc/supervisor/conf.d/mysqld.conf
ADD sshd.conf /etc/supervisor/conf.d/sshd.conf

# install MySQL
RUN rm -rf /var/lib/mysql/mysql
RUN rm -rf /var/lib/apt/lists/*
ADD my.cnf /etc/mysql/my.cnf
ADD mysql-listen.cnf /etc/mysql/conf.d/mysql-listen.cnf
RUN chown -R mysql:mysql /var/lib/mysql
RUN chmod 700 /var/lib/mysql

# initialize MySQL 
RUN mysql_install_db --user mysql > /dev/null
RUN /usr/bin/mysqld_safe & \
    sleep 3 && \
    mysqladmin -u root password "root" && \
    mysql -u root -proot -e "GRANT ALL ON *.* TO 'root'@'172.17.%.%' IDENTIFIED BY '' WITH GRANT OPTION;" && \
    mysql -u root -proot -e "CREATE USER 'redmine'@'%.%.%.%' IDENTIFIED BY 'redmine';" && \
    mysql -u root -proot -e "CREATE DATABASE IF NOT EXISTS redmine_production DEFAULT CHARACTER SET utf8 COLLATE utf8_unicode_ci;" && \
    mysql -u root -proot -e "GRANT ALL PRIVILEGES ON redmine_production.* TO 'redmine'@'%.%.%.%';" && \
    mysql -u root -proot -e "FLUSH PRIVILEGES;"

# expose ports
EXPOSE 22 3306 

# define default command.
CMD ["supervisord", "-n"]
