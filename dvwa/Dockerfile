FROM tutum/lamp:latest
MAINTAINER xuxiang

ENV DEBIAN_FRONTEND noninteractive
# Preparation
RUN \
  rm -fr /app/* && \
  apt-get update && apt-get install -yqq wget curl unzip  php5-gd  vim tcpdump binutils && \
  rm -rf /var/lib/apt/lists/* && \
  wget https://github.com/RandomStorm/DVWA/archive/v1.9.zip  && \
  unzip /v1.9.zip && \
  rm -rf app/* && \
  cp -r /DVWA-1.9/* /app && \
  rm -rf /DVWA-1.9 && \
  chmod 777 /app/hackable/uploads/ /app/external/phpids/0.6/lib/IDS/tmp/phpids_log.txt && \
  sed -i "s/^\$_DVWA\[ 'db_user' \]     = 'root'/\$_DVWA\[ 'db_user' \]     = 'admin'/g" /app/config/config.inc.php && \
  sed -i "s/^\$_DVWA\[ 'default_security_level' \] = 'impossible'/\$_DVWA\[ 'default_security_level' \] = 'low'/g" /app/config/config.inc.php && \
  sed -i "s/^allow_url_include = Off/allow_url_include = on/g" /etc/php5/apache2/php.ini && \
  echo "sed -i \"s/p@ssw0rd/\$PASS/g\" /app/config/config.inc.php" >> /create_mysql_admin_user.sh  && \
  echo 'session.save_path = "/tmp"' >> /etc/php5/apache2/php.ini && \
  sed -i "N;6 a set fileencodings=utf-8,ucs-bom,gb18030,gbk,gb2312,cp936\nset termencoding=utf-8\nset encoding=utf-8" /etc/vim/vimrc

ENV TERM xterm
RUN cp v1.9.zip /app/www.zip
RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
EXPOSE 80 3306
CMD ["/run.sh"]