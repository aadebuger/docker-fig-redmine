fig-redmine
===========

Uses the fig to build docker containers so that Redmine on unicorn on nginx and MySQL.

## Usage

Install fig

```bash
$ sudo apt-get install -y python-pip python2.7-dev
$ sudo pip install -U fig 
```

Build docker images

```
$ sudo docker build -t ubuntu-mysql:14.04 mysql
$ sudo docker build -t ubuntu-ruby:14.04 ruby
$ sudo docker build -t ubuntu-redmine:14.04 redmine
```

Build docker containers

```
$ sudo fig up -d
```

## Contents

The image contains:

- Redmine 2.5-stable branch
- Rails 3.2.19
- Ruby 2.1.2-p95
- MySQL 5.5
- Unicorn
- Nginx
- ImageMagick
- Git and Mercurial and Subversion
- some additional themes and plugins
- Ubuntu 14.04
