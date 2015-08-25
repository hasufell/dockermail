FROM debian:sid

ENV DEBIAN_FRONTEND noninteractive


# Update the package repository and pull basic packages
RUN apt-get update && \
        apt-get upgrade -y && \
        apt-get install -y wget curl locales nano dnsutils net-tools vim git htop supervisor

# Configure timezone and locale
RUN echo "Europe/Berlin" > /etc/timezone && \
        dpkg-reconfigure -f noninteractive tzdata
RUN export LANGUAGE=en_US.UTF-8 && \
        export LANG=en_US.UTF-8 && \
        export LC_ALL=en_US.UTF-8 && \
        locale-gen en_US.UTF-8 && \
        dpkg-reconfigure locales

# Prerequisites
RUN echo "#!/bin/sh\nexit 0" > /usr/sbin/policy-rc.d && \
    apt-get update && apt-get install -y \
    ssl-cert \
    postfix \
    dovecot-imapd && \
    apt-get clean && \
    rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/*


# logging
RUN apt-get update && apt-get install -y supervisor
COPY ./config/supervisord.conf /etc/supervisor/conf.d/supervisord.conf

# create dovecot certificates
RUN mkdir -p /var/tmp/dovecot-cert
RUN cp /usr/share/dovecot/mkcert.sh /var/tmp/dovecot-cert/ ; \
	cp /usr/share/dovecot/dovecot-openssl.cnf /var/tmp/dovecot-cert/
RUN chmod +x /var/tmp/dovecot-cert/mkcert.sh
WORKDIR /var/tmp/dovecot-cert
RUN ./mkcert.sh
RUN chown root:dovecot /etc/dovecot/dovecot.pem ; \
	chmod 0644 /etc/dovecot/dovecot.pem ; \
	chown root:dovecot /etc/dovecot/private/dovecot.pem ; \
	chmod 0600 /etc/dovecot/private/dovecot.pem
WORKDIR /
RUN rm -r /var/tmp/dovecot-cert

# Postfix configuration
COPY ./config/postfix.main.cf /etc/postfix/main.cf
COPY ./config/postfix.master.cf.append /etc/postfix/master-additional.cf
RUN cat /etc/postfix/master-additional.cf >> /etc/postfix/master.cf

# Dovecot configuration
COPY ./config/dovecot.mail /etc/dovecot/conf.d/10-mail.conf
COPY ./config/dovecot.ssl /etc/dovecot/conf.d/10-ssl.conf
COPY ./config/dovecot.auth /etc/dovecot/conf.d/10-auth.conf
COPY ./config/dovecot.master /etc/dovecot/conf.d/10-master.conf
COPY ./config/dovecot.lda /etc/dovecot/conf.d/15-lda.conf
COPY ./config/dovecot.imap /etc/dovecot/conf.d/20-imap.conf
# Uncomment to add verbose logging
# COPY ./config/dovecot.logging /etc/dovecot/conf.d/10-logging.conf

# Nice place for your settings
VOLUME ["/mail_settings"]

# Copy boot scripts
COPY boot /
RUN chmod 755 /boot
COPY boot.d /boot.d
RUN chmod -R 755 /boot.d

# Volume to store email
VOLUME ["/vmail"]

# Add user vmail that ownes mail
RUN groupadd -g 5000 vmail
RUN useradd -g vmail -u 5000 vmail -d /vmail -m

EXPOSE 25 143 587

CMD ["/usr/bin/supervisord", "-n"]
