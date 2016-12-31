FROM resin/rpi-raspbian:wheezy
MAINTAINER Stefan Schoeffmann <stefan.schoeffmann@posteo.de>

ENV NGINX_VERSION 1.2.1-2.2+wheezy3

# update and install nginx
RUN sudo apt-get update && \
    sudo apt-get install -y nginx=${NGINX_VERSION}

# trim the original configuration for our little raspberry
RUN sudo sed -i "s/worker_processes 4;/worker_processes 2;/g" /etc/nginx/nginx.conf
RUN sudo sed -i "s/worker_connections 768;/worker_connections 256;/g" /etc/nginx/nginx.conf
RUN echo "daemon off;" >> /etc/nginx/nginx.conf

# like in the official nginx-image - forward request and error logs to docker log collector
RUN ln -sf /dev/stdout /var/log/nginx/access.log
RUN ln -sf /dev/stderr /var/log/nginx/error.log

VOLUME ["/var/cache/nginx"]

# Ports to be exposed
EXPOSE 80 443

CMD ["nginx"]
