ARG PHP_VERSION
FROM ${PHP_VERSION}

ARG TZ
ARG PHP_EXTENSIONS
ARG CONTAINER_PACKAGE_URL


RUN sed -i "s/dl-cdn.alpinelinux.org/${CONTAINER_PACKAGE_URL}/g" /etc/apk/repositories


COPY ./extensions /tmp/extensions
COPY ./ssh /tmp/extensions/.ssh
WORKDIR /tmp/extensions
RUN chmod +x install.sh \
    && sh install.sh \
    && chmod +x /tmp/extensions/.ssh/init.sh \
    && apk  add openssh-client \
    && mv /tmp/extensions/.ssh /root \
    && ls -la /root/.ssh \
#    && ssh -fNCg ali  -L  6379:127.0.0.1:6379  39.97.182.45 \
#    && ssh -fNCg ali  -L  3306:127.0.0.1:3306  39.97.182.45 \
    && rm -rf /tmp/extensions


RUN apk  add tzdata \
    && cp "/usr/share/zoneinfo/$TZ" /etc/localtime \
    && echo "$TZ" > /etc/timezone




# Fix: https://github.com/docker-library/php/issues/240
RUN apk add gnu-libiconv libstdc++ --no-cache --repository http://${CONTAINER_PACKAGE_URL}/alpine/edge/community/ --allow-untrusted
ENV LD_PRELOAD /usr/lib/preloadable_libiconv.so php


# Install composer and change it's cache home
RUN curl -o /usr/bin/composer https://mirrors.aliyun.com/composer/composer.phar \
    && chmod +x /usr/bin/composer
ENV COMPOSER_HOME=/tmp/composer

COPY ./ssh /tmp/extensions/.ssh
# php image's www-data user uid & gid are 82, change them to 1000 (primary user)
RUN apk --no-cache add shadow && usermod -u 1000 www-data && groupmod -g 1000 www-data
#CMD ["ssh -fNCg ali  -L  6379:127.0.0.1:6379  39.97.182.45","ssh -fNCg ali  -L  3306:127.0.0.1:3306  39.97.182.45"]
#CMD ["/bin/sh","/tmp/extensions/.ssh/init.sh"]


WORKDIR /www