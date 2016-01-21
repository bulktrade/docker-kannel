FROM debian:jessie
RUN apt-get update && apt-get upgrade -y && \
        apt-get -y remove ntpdate && \
        apt-get install -y build-essential subversion ntp nano wget cvs subversion curl git-core unzip autoconf \
        automake1.11 libtool flex debhelper pkg-config libpam0g-dev intltool checkinstall docbook docbook-xsl \
        build-essential libpcre3 libpcre3-dev libc6-dev g++ gcc autotools-dev bison libncurses5-dev m4 tex-common \
        texi2html texinfo libxml2-dev \
        openssl libssl-dev locales libmysqlclient-dev libmysql++-dev supervisor libtool libtool-bin

RUN locale-gen en_US && \
    locale-gen en_US.UTF-8

RUN cd /usr/local && \
	mkdir /usr/local/src/kannel && \
	cd /usr/local/src/kannel && \
	svn checkout -r 5159 https://svn.kannel.org/gateway/trunk && \
	mv trunk gateway && \
	cd /usr/local/src/kannel/gateway && \
	./configure -prefix=/usr/local/kannel -with-mysql -with-mysql-dir=/usr/lib/mysql/ -enable-debug -enable-assertions -with-defaults=speed \
	-enable-localtime -enable-start-stop-daemon -enable-pam && \
	touch .depend && \
	make depend && \
	make && \
	make bindir=/usr/local/kannel install && \
	cd /usr/local/src/kannel/gateway/addons/sqlbox && \
    ./bootstrap && \
    ./configure -prefix=/usr/local/kannel -with-kannel-dir=/usr/local/kannel && \
    make && make bindir=/usr/local/kannel/sqlbox install && \
    cd /usr/local/src/kannel/gateway/addons/opensmppbox && \
    ./configure -prefix=/usr/local/kannel -with-kannel-dir=/usr/local/kannel && \
    make && make bindir=/usr/local/kannel/smppbox install && \
	mkdir /etc/kannel && \
	mkdir /var/log/kannel && \
	mkdir /var/log/kannel/gateway && \
	mkdir /var/log/kannel/smsbox && \
	mkdir /var/log/kannel/wapbox && \
	mkdir /var/log/kannel/smsc && \
	mkdir /var/log/kannel/sqlbox && \
	mkdir /var/log/kannel/smppbox && \
	mkdir /var/spool/kannel && \
	chmod -R 755 /var/spool/kannel && \
	chmod -R 755 /var/log/kannel && \
	cp /usr/local/src/kannel/gateway/gw/smskannel.conf /etc/kannel/kannel.conf && \
	cp /usr/local/src/kannel/gateway/debian/kannel.default /etc/default/kannel && \
	cp /usr/local/src/kannel/gateway/addons/sqlbox/example/sqlbox.conf.example /etc/kannel/sqlbox.conf && \
	cp /usr/local/src/kannel/gateway/addons/opensmppbox/example/opensmppbox.conf.example /etc/kannel/opensmppbox.conf && \
	cp /usr/local/src/kannel/gateway/addons/opensmppbox/example/smpplogins.txt.example /etc/kannel/smpplogins.txt && \
	rm -rf /usr/local/src/kannel/gateway && \
	apt-get -y clean

COPY supervisord.conf /etc/supervisor/conf.d/supervisord.conf
COPY kannel.conf /etc/kannel/kannel.conf

EXPOSE 13013 13000

VOLUME ["/var/spool/kannel", "/etc/kannel", "/var/log/kannel"]

CMD ["/usr/bin/supervisord"]