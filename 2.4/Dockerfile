# Note ! Due to this
# https://gitlab.alpinelinux.org/alpine/aports/-/issues/13112
# we have to fallback to Debian Bullseye
FROM debian:bullseye

ENV APACHE_ETC /etc/apache2
WORKDIR "/usr/sbin"

# Copy in our configuration files.
COPY conf/ /conf/

ENV DEBIAN_FRONTEND=noninteractive

RUN set -ex; \
    apt-get update; \
    apt-get dist-upgrade -y; \
    apt-get install -y apache2 apache2-utils openssl; \
    \
    # Create empty default DocumentRoot.
    mkdir -p "/var/www/html"; \
    # Create directories for Dav data and lock database.
    mkdir -p "/var/lib/dav/data"; \
    touch "/var/lib/dav/DavLock"; \
    chown -R www-data:www-data "/var/lib/dav"; \
    \
    # Enable DAV modules (Debian)
    a2enmod dav dav_fs dav_lock; \
    cp /conf/conf-available/dav.conf "/etc/apache2/conf-enabled/dav.conf"; \
    rm -f "/etc/apache2/sites-enabled/*"; \
    cp /conf/sites-available/default.conf "/etc/apache2/sites-enabled/default.conf";
    \ 
    ## Enable DAV modules. (Alpine Linux)
    #for i in dav dav_fs; do \
    #    sed -i -e "/^#LoadModule ${i}_module.*/s/^#//" "conf/httpd.conf"; \
    #done; \
    #\
    ## Make sure authentication modules are enabled.
    #for i in authn_core authn_file authz_core authz_user auth_basic auth_digest; do \
    #    sed -i -e "/^#LoadModule ${i}_module.*/s/^#//" "conf/httpd.conf"; \
    #done; \
    #\
    ## Make sure other modules are enabled.
    #for i in alias headers mime setenvif; do \
    #    sed -i -e "/^#LoadModule ${i}_module.*/s/^#//" "conf/httpd.conf"; \
    #done; \
    #\
    ## Run httpd as "www-data" (instead of "daemon").
    #for i in User Group; do \
    #    sed -i -e "s|^$i .*|$i www-data|" "conf/httpd.conf"; \
    #done; \
    #\
    ## Include enabled configs and sites.
    #printf '%s\n' "Include conf/conf-enabled/*.conf" \
    #    >> "conf/httpd.conf"; \
    #printf '%s\n' "Include conf/sites-enabled/*.conf" \
    #    >> "conf/httpd.conf"; \
    #\
    ## Enable dav and default site.
    #mkdir -p "conf/conf-enabled"; \
    #mkdir -p "conf/sites-enabled"; \
    #ln -s ../conf-available/dav.conf "conf/conf-enabled"; \
    #ln -s ../sites-available/default.conf "conf/sites-enabled";

COPY docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
EXPOSE 80/tcp 443/tcp
ENTRYPOINT [ "docker-entrypoint.sh" ]
CMD [ "apache2", "-DFOREGROUND" ]
