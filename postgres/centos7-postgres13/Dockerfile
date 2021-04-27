# "ported" by Adam Miller <maxamillion@fedoraproject.org> from
#   https://github.com/fedora-cloud/Fedora-Dockerfiles
#
# Originally written for Fedora-Dockerfiles by
#   scollier <scollier@redhat.com>
#
# mdo: adapted for Postgres 13 on CentOS 7, quick and dirty

FROM centos:centos7
MAINTAINER The CentOS Project <cloud-ops@centos.org>

RUN yum -y update; yum clean all
RUN yum -y install sudo epel-release; yum clean all
#RUN yum -y install postgresql-server postgresql postgresql-contrib supervisor pwgen; yum clean all

RUN curl https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm --out /tmp/pgdg-redhat-repo-latest.noarch.rpm
RUN rpm -i /tmp/pgdg-redhat-repo-latest.noarch.rpm
RUN yum repolist
RUN yum -y install postgresql13-server postgresql13 postgresql13-contrib supervisor pwgen; yum clean all

RUN echo 'PATH=$PATH:/usr/pgsql-13/bin/' > /etc/profile.d/pgpath.sh

ADD ./supervisord.conf /etc/supervisord.conf
ADD ./start_postgres.sh /start_postgres.sh

#Sudo requires a tty. fix that.
RUN sed -i 's/.*requiretty$/#Defaults requiretty/' /etc/sudoers
RUN chmod +x /start_postgres.sh

RUN sed -i 's#systemctl show -p Environment.*service#echo "PGDATA=/var/lib/pgsql/13/data#' /usr/pgsql-13/bin/postgresql-13-setup
RUN grep PGDATA /usr/pgsql-13/bin/postgresql-13-setup
RUN /usr/pgsql-13/bin/postgresql-13-setup initdb

RUN echo "host    all             all             0.0.0.0/0               md5" >> /var/lib/pgsql/13/data/pg_hba.conf

VOLUME ["/var/lib/pgsql"]

EXPOSE 5432

CMD ["/bin/bash", "/start_postgres.sh"]
