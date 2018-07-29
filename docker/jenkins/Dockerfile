FROM xhz/ubuntu
MAINTAINER xhz@xx.com
ENV REFRESHED_AT 2018-07-29

RUN apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
# RUN apt-get install -yqq software-properties-common
# RUN add-apt-repository -y ppa:webupd8team/java
RUN apt-get update
RUN apt-get install -yqq curl apt-transport-https
RUN apt-get install -yqq iptables
RUN apt-get install -yqq ca-certificates
RUN apt-get install -yqq openjdk-7-jre
RUN apt-get install -yqq git-core
#RUN apt-get install -yqq docker-engine
RUN apt-get install -yqq docker.io

ENV JENKINS_HOME /opt/jenkins/data
ENV JENKINS_MIRRORO http://mirrors.jenkins-ci.org

RUN mkdir -p $JENKINS_HOME/plugins
RUN curl -sf -o $JENKINS_HOME/jenkins.war -L $JENKINS_MIRRORO/war-stable/1.580.2/jenkins.war
RUN for plugin in chucknorris greenballs scm-api git-client git ws-cleanup ;\
        do curl -sf -o $JENKINS_HOME/plugins/${plugin}.hpi \
            -L $JENKINS_MIRRORO/plugins/${plugin}/latest/${plugin}.hpi ; done

ADD ./jenkins.sh /usr/local/bin/jenkins.sh
RUN chmod +x /usr/local/bin/jenkins.sh

VOLUME /var/lib/docker
EXPOSE 8080

# ENTRYPOINT ["/usr/local/bin/jenkins.sh"]


#   