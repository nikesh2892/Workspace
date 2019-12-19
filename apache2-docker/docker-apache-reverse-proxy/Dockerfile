FROM ubuntu:16.04
MAINTAINER Nikesh
ENV APACHE_RUN_USER www-data
ENV APACHE_RUN_GROUP www-data
ENV APACHE_LOCK_DIR /var/lock/apache2
ENV APACHE_LOG_DIR /var/log/apache2
ENV APACHE_PID_FILE /var/run/apache2/apache2.pid
RUN apt-get update
RUN apt-get -y install apache2 curl vim && rm -rf /var/lib/apt/lists/*
RUN a2enmod proxy proxy_ajp proxy_http rewrite deflate headers proxy_balancer proxy_connect proxy_html xml2enc lbmethod_* reqtimeout

RUN rm -rf /etc/apache2/sites-enabled/000-default.conf
RUN service apache2 restart
COPY sites-enabled/* /etc/apache2/sites-enabled/
COPY proxy_template.conf /etc/apache2/

EXPOSE 80
EXPOSE 443
CMD ["/usr/sbin/apache2ctl", "-DFOREGROUND"]
