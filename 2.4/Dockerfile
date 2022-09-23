FROM debian:bullseye

ENV DEBIAN_FRONTEND=noninteractive
ENV APACHE_ETC=/etc/apache2

# Copy in our configuration files.
COPY conf/ /conf/

RUN apt-get update && \
    apt-get dist-upgrade -y && \
    apt-get install -y apache2 apache2-utils openssl && \
    apt-get clean

# Create directories for Dav data and lock database.
RUN a2enmod dav dav_fs dav_lock && \
    mkdir -p /var/lib/dav/data && \
    touch /var/lib/dav/DavLock && \
    chown -R www-data:www-data /var/lib/dav && \
    rm -f /etc/apache2/sites-enabled/000-default.conf

# Remove default configs and copy new config
RUN find /etc/apache2/sites-available -maxdepth 1 -type f -delete && \
    cp /conf/sites-available/default.conf /etc/apache2/sites-available/ && \
    cp /conf/sites-available/default-ssl.conf /etc/apache2/sites-available/ && \
    cp /conf/conf-available/dav.conf /etc/apache2/conf-available/

# Enable new apache configs
RUN ln -s /etc/apache2/sites-available/default.conf /etc/apache2/sites-enabled/default.conf && \
    ln -s /etc/apache2/conf-available/dav.conf /etc/apache2/conf-enabled/dav.conf && \


COPY docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
EXPOSE 80/tcp 443/tcp
ENTRYPOINT [ "docker-entrypoint.sh" ]
CMD [ "apache2", "-DFOREGROUND" ]
