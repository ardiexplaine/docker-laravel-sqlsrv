FROM ubuntu:18.04

ARG DEBIAN_FRONTEND=noninteractive

# update package list
RUN apt-get update

# install apache
RUN apt-get install -y apache2 git zip npm

# install php
RUN apt-get -y install php7.2 php-mbstring php-pear php7.2-dev php7.2-xml php7.2-mysql
RUN apt-get install -y libapache2-mod-php7.2

# Laravel PDF generator dependecies
RUN apt-get install -y libxrender1 libfontconfig libxext6

# install pre requisites
RUN apt-get update
RUN apt-get install -y apt-transport-https
RUN curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
RUN curl https://packages.microsoft.com/config/ubuntu/18.04/prod.list > /etc/apt/sources.list.d/mssql-release.list
RUN apt-get update
RUN ACCEPT_EULA=Y apt-get install -y msodbcsql17 mssql-tools
RUN apt-get install -y unixodbc

# install driver sqlsrv
RUN apt-get -y install gcc g++ make autoconf libc-dev pkg-config
RUN pecl update-channels


# install composer
RUN curl -sS https://getcomposer.org/installer | php
RUN chmod a+x composer.phar
RUN mv composer.phar /usr/local/bin/composer

# install ODBC Driver
RUN ACCEPT_EULA=Y apt-get install -y msodbcsql17
RUN apt-get install -y mssql-tools
RUN apt-get install -y unixodbc-dev
RUN echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bash_profile
RUN echo 'export PATH="$PATH:/opt/mssql-tools/bin"' >> ~/.bashrc
RUN exec bash

# initial sqlsrv
RUN pecl install sqlsrv
RUN pecl install pdo_sqlsrv

# load driver sqlsrv
RUN echo "extension=sqlsrv.so" >> /etc/php/7.2/mods-available/sqlsrv.ini
RUN ln -s /etc/php/7.2/mods-available/sqlsrv.ini /etc/php/7.2/apache2/conf.d/sqlsrv.ini
RUN ln -s /etc/php/7.2/mods-available/sqlsrv.ini /etc/php/7.2/cli/conf.d/sqlsrv.ini

RUN echo "extension=pdo_sqlsrv.so" >> /etc/php/7.2/mods-available/pdo_sqlsrv.ini
RUN ln -s /etc/php/7.2/mods-available/pdo_sqlsrv.ini /etc/php/7.2/apache2/conf.d/pdo_sqlsrv.ini
RUN ln -s /etc/php/7.2/mods-available/pdo_sqlsrv.ini /etc/php/7.2/cli/conf.d/pdo_sqlsrv.ini

# install locales
RUN apt-get install -y locales && echo "en_US.UTF-8 UTF-8" > /etc/locale.gen && locale-gen

# Enable Apache mod rewrite
RUN /usr/sbin/a2enmod rewrite

# Edit apache2.conf to change apache site settings.
ADD ./docker/apache2.conf /etc/apache2/

# Edit 000-default.conf to change apache site settings.
ADD ./docker/000-default.conf /etc/apache2/sites-available/

EXPOSE 80

WORKDIR /var/www/src

CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]