## This is the base image that all Bookworm containers depend on.

FROM debian
MAINTAINER Peter Organisciak <organisciak@gmail.com>
# So that mysql does not ask for setting of root pw
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update && \
    apt-get install -y \
	apache2 \
	mysql-server \
	git \
	#php5 \
	#php-pear \
	#php5-mysql \
	wget \
	python-dev \
	python-mysqldb \
	python3 \ 
	#python3-devel ##NECESSARY? 
	make \
	automake \
	gcc \
	g++ \
	bzip2 \
	parallel

# TEMPORARY, WILL PULL OUT DATA TO SEPARATE VOLUME LATER
RUN mkdir /data && \
    chown root data
RUN groupadd www && \
    usermod -a -G www root

# TEST PHP
RUN echo "<?php phpinfo(); ?>" > /var/www/html/phpinfo.php

# TURN ON APACHE
# RUN service apache2 start

# INSTALL PIP
RUN cd /tmp/ && \
    wget https://bootstrap.pypa.io/get-pip.py && \
    python get-pip.py && \
    python3 get-pip.py

# Install Python Libs
RUN pip2 install regex \
	nltk \
	numpy \
	pandas \
	mysql-python \
	ujson \
	beautifulsoup4 \
	pysolr
RUN pip3 install beautifulsoup4 \
	pysolr

# Install GNU parallel
#RUN wget ftp://ftp.gnu.org/gnu/parallel/parallel-latest.tar.bz2 && \
#	tar -xjf parallel-latest.tar.bz2 && \
#	cd parallel-* && ./configure && make && make install
## Alias parallel so it always mutes the notice within the container
RUN echo "alias parallel='parallel --no-notice'">>/root/bash_profile


## Set datadir to be in /data
#RUN mkdir /data/mysql && mkdir /data/scratch
#COPY global.my.cnf /etc/mysql/my.cnf

# Start Mysql
#RUN service mysql start
#RUN mysql --execute="CREATE USER 'bookworm'@'localhost' IDENTIFIED BY 'temppw'; GRANT ALL PRIVILEGES ON *.* TO 'hathipd'@'localhost'; FLUSH PRIVILEGES;"

RUN git clone https://github.com/Bookworm-project/BookwormDB.git && \
	cd BookwormDB && \
	python setup.py install

RUN mkdir /etc/bookworm

