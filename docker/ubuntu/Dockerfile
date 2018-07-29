FROM ubuntu:14.04
MAINTAINER xhz "xhz@xx.com"
ENV REFRESHED_AT 2018-07-28
RUN sed -i 's/http:\/\/archive\.ubuntu\.com\/ubuntu\//http:\/\/mirrors\.163\.com\/ubuntu\//g' /etc/apt/sources.list
RUN apt-get -yyq update