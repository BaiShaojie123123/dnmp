ARG PHP_VERSION
FROM ${PHP_VERSION}

ARG TZ
ARG PHP_EXTENSIONS
ARG CONTAINER_PACKAGE_URL


RUN sed -i "s/dl-cdn.alpinelinux.org/${CONTAINER_PACKAGE_URL}/g" /etc/apk/repositories


COPY ./extensions /tmp/extensions
COPY ./ssh /tmp/.ssh
WORKDIR /tmp/extensions
RUN chmod +x install.sh \
    && sh install.sh \
    && mv /tmp/.ssh/init.sh /tmp/init.sh \
    && ls -la /tmp \
    && mv /tmp/.ssh /root \
    && apk  add openssh-client \
#    && apk  add autossh \
    && ls -la /root/.ssh \
    && chmod 600 /root/.ssh/ali.pem \
    && chmod +x /tmp/init.sh \
    && rm -rf /tmp/extensions

#CMD /bin/sh  /tmp/init.sh


RUN apk  add tzdata \
    && cp "/usr/share/zoneinfo/$TZ" /etc/localtime \
    && echo "$TZ" > /etc/timezone
#
#
#
#
# Fix: https://github.com/docker-library/php/issues/240
RUN apk add gnu-libiconv libstdc++ --no-cache --repository http://${CONTAINER_PACKAGE_URL}/alpine/edge/community/ --allow-untrusted
ENV LD_PRELOAD /usr/lib/preloadable_libiconv.so php
#
#
# Install composer and change it's cache home
RUN curl -o /usr/bin/composer https://mirrors.aliyun.com/composer/composer.phar \
    && chmod +x /usr/bin/composer
ENV COMPOSER_HOME=/tmp/composer


# php image's www-data user uid & gid are 82, change them to 1000 (primary user)
RUN apk --no-cache add shadow && usermod -u 1000 www-data && groupmod -g 1000 www-data


WORKDIR /www
