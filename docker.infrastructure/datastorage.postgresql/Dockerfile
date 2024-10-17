FROM postgres:9.6

WORKDIR /docker-entrypoint-initdb.d

COPY init-script.sh /docker-entrypoint-initdb.d/

VOLUME  ["/etc/postgresql", "/var/log/postgresql", "/var/lib/postgresql/data"]