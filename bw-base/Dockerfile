## This is the base image that all Bookworm containers depend on.

FROM debian
MAINTAINER Peter Organisciak <organisciak@gmail.com>
# So that mysql does not ask for setting of root pw
ENV DEBIAN_FRONTEND noninteractive
RUN apt-get update && \
    apt-get install -y \
	mysql-server \
	git \
	wget \
	python-dev \
	python-mysqldb \
	make \
	python-numpy \
	python-scipy \
	python-pandas \
	python-matplotlib \
	python-nltk \
	python-flask \
	parallel

# INSTALL PIP
RUN cd /tmp/ && \
    wget https://bootstrap.pypa.io/get-pip.py && \
    python get-pip.py 

# Install Python Libs
RUN pip2 install regex

## Alias parallel so it always mutes the notice within the container
RUN echo "alias parallel='parallel --no-notice'">>/root/bash_profile

RUN git clone https://github.com/Bookworm-project/BookwormDB.git && \
	cd BookwormDB && \
	python setup.py install

RUN mkdir /etc/bookworm
## TODO: We don't want the admin user everywhere, these locations
## Will have different users later
COPY client-my.cnf /etc/bookworm/my.cnf
COPY client-my.cnf /root/my.cnf

