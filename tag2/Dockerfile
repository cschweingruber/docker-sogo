FROM            phusion/baseimage
MAINTAINER	Christian Schweingruber <c.schweingruber@catatec.ch>
# based on https://github.com/JensErat/docker-sogo
# repo from http://www.axis.cz/linux/debian/dists

# Install Apache, SOGo from repository
RUN echo "deb http://www.axis.cz/linux/debian xenial sogo-v2" > /etc/apt/sources.list.d/sogo.axis.cz.list && \
    apt-key adv --keyserver pool.sks-keyservers.net --recv-key 608F5293A04BE668 && \
    apt-get update && \
    apt-get install -y --no-install-recommends gettext-base apache2 sogo sope4.9-gdl1-postgresql sope4.9-gdl1-mysql memcached tzdata && \
    apt-get clean && rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*

# Activate required Apache modules
RUN a2enmod headers proxy proxy_http rewrite ssl

# Move SOGo's data directory to /srv
RUN usermod --home /srv/lib/sogo sogo

# Fix memcached not listening on IPv6
RUN sed -i -e 's/^-l.*/-l localhost/' /etc/memcached.conf

# SOGo daemons
RUN mkdir /etc/service/sogod /etc/service/apache2 /etc/service/memcached
ADD sogod.sh /etc/service/sogod/run
ADD apache2.sh /etc/service/apache2/run
ADD memcached.sh /etc/service/memcached/run

# Make GATEWAY host available, control memcached startup
RUN mkdir -p /etc/my_init.d
ADD gateway.sh memcached-control.sh /etc/my_init.d/

# Interface the environment
VOLUME /srv
EXPOSE 80

# Baseimage init process
ENTRYPOINT ["/sbin/my_init"]
