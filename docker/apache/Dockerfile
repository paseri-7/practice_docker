#RUN apt-get update \
 #&& apt-get install -y zlib1g-dev sudo\
 #&& docker-php-ext-install zip

FROM php:8.1.5-apache

RUN apt-get update \
    && apt-get install -y libzip-dev zip unzip vim libpq-dev locales \
    && docker-php-ext-configure zip \
    # docker php には mysql 用のドライバが未インストールのため追加する
    && docker-php-ext-install pdo_pgsql zip \
    # a2emod rewrite をして apache に rewrite モジュールを追加。これをしないと Laravel でルート以外にアクセスできない
    && a2enmod rewrite

#for composer
#composerのdockerイメージからcomposer実行可能ファイルをコンテナ内の/usr/bin/composerにコピー
COPY --from=composer /usr/bin/composer /usr/bin/composer
#composerがrootユーザとして実行されることを許可する
ENV COMPOSER_ALLOW_SUPERUSER: 1
#composerがインストールされるディレクトリを設定(コンテナ内では存在しない可能性があるため)
ENV CONPOSER_HOME /composer
#composerコマンドを使用するためのPATHを追加
ENV PATH $PATH:/composer/vendor/bin

# for node.js npm
#npmのinstallに必要なものをdockerイメージよりコピー
COPY --from=node:18.12.0 /usr/local/bin /usr/local/bin
COPY --from=node:18.12.0 /usr/local/lib /usr/local/lib
#npmのinstall
RUN npm install -g npm@8.19.3

#ADD docker/apache/php.ini /usr/local/etc/php/

# Apache の conf は seites-available に作成し
# a2ensite コマンドでシンボリックリンクを sites-enabled に作成する
#ADD docker/apache/config/000-default.conf /etc/apache2/sites-available/
#RUN a2ensite 000-default

WORKDIR /var/www/html

#COPY ./webapp /var/www/html

#RUN chown www-data storage/ -R \
#RUN chmod -R 777 storage bootstrap/cache \
 #&& composer install\
 #&& npm install