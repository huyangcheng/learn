FROM xhz/ubuntu
MAINTAINER hxz "xhz@xx.com"
ENV REFRESHED_AT 2018-07-26
RUN apt-get -yqq install nginx
RUN mkdir -p /var/www/html/website
ADD nginx/global.conf /etc/nginx/conf.d
ADD nginx/nginx.conf /etc/nginx/nginx.conf
EXPOSE 80