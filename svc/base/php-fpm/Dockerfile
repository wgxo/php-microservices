FROM php:8.0-fpm

RUN apt-get update && apt-get -y install \
        git g++ libcurl4-gnutls-dev libicu-dev libpq-dev libxml2-dev unzip zlib1g-dev \
    && php -m && echo docker-php-ext-install intl pdo_pgsql \
    && apt-get autoremove -y && apt-get autoclean -y \
    && rm -rf /var/lib/apt/lists/*

RUN pecl install -o -f redis \
    &&  rm -rf /tmp/pear \
    &&  docker-php-ext-enable redis

RUN echo 'date.timezone="America/Guatemala"' >>  /usr/local/etc/php/conf.d/date.ini
RUN echo 'session.save_path = "/tmp"' >>  /usr/local/etc/php/conf.d/session.ini

ENV CONSUL_TEMPLATE_VERSION 0.25.1
ENV CONSUL_TEMPLATE_CHECKSUM 58356ec125c85b9705dc7734ed4be8491bb4152d8a816fd0807aed5fbb128a7b

RUN curl --retry 7 --fail -Lks "https://releases.hashicorp.com/consul-template/${CONSUL_TEMPLATE_VERSION}/consul-template_${CONSUL_TEMPLATE_VERSION}_linux_amd64.zip" -o /tmp/consul-template.zip \
    && echo "${CONSUL_TEMPLATE_CHECKSUM}  /tmp/consul-template.zip" | sha256sum -c \
    && unzip /tmp/consul-template.zip -d /usr/local/bin \
    && rm /tmp/consul-template.zip

ENV CONTAINERPILOT_VERSION 3.8.0
ENV CONTAINERPILOT_CHECKSUM 84642c13683ddae6ccb63386e6160e8cb2439c26
ENV CONTAINERPILOT /etc/containerpilot.json5

RUN curl --retry 7 --fail -Lks "https://github.com/joyent/containerpilot/releases/download/${CONTAINERPILOT_VERSION}/containerpilot-${CONTAINERPILOT_VERSION}.tar.gz" -o /tmp/containerpilot.tar.gz \
    && echo "${CONTAINERPILOT_CHECKSUM}  /tmp/containerpilot.tar.gz" | sha1sum -c \
    && tar zxf /tmp/containerpilot.tar.gz -C /usr/local/bin \
    && rm /tmp/containerpilot.tar.gz

COPY config/ /etc/
COPY scripts/ /usr/local/bin/

RUN chmod +x /usr/local/bin/reload.sh

CMD [ "/usr/local/bin/containerpilot", \
    "php-fpm", \
        "--nodaemonize"]
