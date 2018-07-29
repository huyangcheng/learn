FROM xhz/ubuntu

RUN apt-get install -yqq curl unzip

# ADD https://releases.hashicorp.com/consul/0.3.1/consul_0.3.1_linux_amd64.zip /tmp/consul.zip
ADD consul_0.3.1_linux_amd64.zip /tmp/consul.zip
RUN cd /usr/sbin && unzip /tmp/consul.zip && chmod +x /usr/sbin/consul && rm /tmp/consul.zip

# ADD https://releases.hashicorp.com/consul/0.3.1/consul_0.3.1_web_ui.zip /tmp/webui.zip
ADD consul_0.3.1_web_ui.zip /tmp/webui.zip
RUN cd /tmp/ && unzip webui.zip && mv dist/ /webui/

ADD consul.json /config/

EXPOSE 53/udp 8300 8301 8301/udp 8302 8302/udp 8400 8500
VOLUME ["/data"]
ENTRYPOINT ["/usr/sbin/consul", "agent", "-config-dir=/config"]
CMD []

