FROM ubuntu-ruby:14.04

MAINTAINER hideakihal

ENV REDMINE_VERSION 2.5-stable
ENV REDMINE_HOME /var/redmine
ENV SETUP_DIR /app/setup
ENV THEMES_SETUP_DIR ${SETUP_DIR}/themes
ENV PLUGINS_SETUP_DIR ${SETUP_DIR}/plugins
ENV RAILS_ENV production

# install basic package
RUN apt-get update && \
    apt-get -y install \
      nginx \
      imagemagick \
      libmagickcore-dev \
      libmagickwand-dev \
      libmysqlclient-dev

# install Redmine
RUN git clone -b ${REDMINE_VERSION} https://github.com/redmine/redmine.git ${REDMINE_HOME}
WORKDIR /var/redmine 
RUN echo 'gem "mysql2", "~> 0.3.11"' >> Gemfile
RUN echo 'gem "unicorn"' >> Gemfile
RUN bash -l -c 'bundle install --without development test'

# install plugins
RUN mkdir -p ${SETUP_DIR} ${THEMES_SETUP_DIR} ${PLUGINS_SETUP_DIR}
ADD setup/themes_install ${THEMES_SETUP_DIR}/themes_install
ADD setup/plugins_install ${PLUGINS_SETUP_DIR}/plugins_install
RUN chmod 755 ${THEMES_SETUP_DIR}/*
RUN chmod 755 ${PLUGINS_SETUP_DIR}/*
RUN ${THEMES_SETUP_DIR}/themes_install
RUN ${PLUGINS_SETUP_DIR}/plugins_install

# add config file
ADD config/database.yml ${REDMINE_HOME}/config/database.yml
ADD config/configuration.yml ${REDMINE_HOME}/config/configuration.yml
ADD config/unicorn.rb ${REDMINE_HOME}/config/unicorn.rb
RUN mkdir ${REDMINE_HOME}/public/plugin_assets

# install nginx
ADD redmine.conf /etc/nginx/conf.d/redmine.conf
RUN rm -f /etc/nginx/sites-enabled/default
RUN ln -s /etc/nginx/sites-available/redmine /etc/nginx/sites-enabled/redmine
RUN echo "daemon off;" >> /etc/nginx/nginx.conf

# add supervisor config file 
ADD unicorn.conf /etc/supervisor/conf.d/unicorn.conf
ADD nginx.conf /etc/supervisor/conf.d/nginx.conf

# expose ports
EXPOSE 22 80

# define default command
CMD bash -l -c 'bundle install --without development test' && \
    bash -l -c 'bundle exec rake generate_secret_token' && \
    mv plugins plugins-1 && \
    bash -l -c 'bundle exec rake db:migrate RAILS_ENV=${RAILS_ENV}' && \
    mv plugins-1 plugins && \
    bash -l -c 'bundle exec rake redmine:plugins:migrate RAILS_ENV=${RAILS_ENV}' && \
    supervisord -n
