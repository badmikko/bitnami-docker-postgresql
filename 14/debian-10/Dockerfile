FROM docker.io/bitnami/minideb:buster
LABEL maintainer "Bitnami <containers@bitnami.com>"

ARG TARGETOS TARGETARCH

ENV HOME="/" \
    OS_ARCH="$TARGETARCH" \
    OS_FLAVOUR="debian-10" \
    OS_NAME="$TARGETOS" \
    USER_UID=1001 \
    USER_GID=1001 \
    GROUPNAME="postgres" \
    USERNAME="postgres"

ARG EXTRA_LOCALES=""
ARG WITH_ALL_LOCALES="no"

RUN addgroup --gid ${USER_GID} ${GROUPNAME} && \
    useradd --no-log-init -r -u ${USER_UID} --gid ${USER_UID} ${USERNAME}

COPY prebuildfs /
# Install required system packages and dependencies
RUN install_packages acl ca-certificates curl gzip libbsd0 libbz2-1.0 libc6 libedit2 libffi6 libgcc1 libgmp10 libgnutls30 libhogweed4 libicu63 libidn2-0 libldap-2.4-2 liblz4-1 liblzma5 libncurses6 libnettle6 libp11-kit0 libpcre3 libreadline7 libsasl2-2 libsqlite3-0 libssl1.1 libstdc++6 libtasn1-6 libtinfo6 libunistring2 libuuid1 libxml2 libxslt1.1 libzstd1 locales procps tar zlib1g
# RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "postgresql" "14.2.0-10" --checksum 81082c252cecde0195a30d6aec2eeb8fb2f2a4a557101088642072d8c9d2b164
# RUN . /opt/bitnami/scripts/libcomponent.sh && component_unpack "gosu" "1.14.0-7" --checksum d6280b6f647a62bf6edc74dc8e526bfff63ddd8067dcb8540843f47203d9ccf1
RUN apt-get update \
 && DEBIAN_FRONTEND=noninteractive apt-get install -y wget gnupg \
 && wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | apt-key add - \
 && echo 'deb http://apt.postgresql.org/pub/repos/apt/ buster-pgdg main' >> /etc/apt/sources.list
RUN apt-get update && apt-get upgrade -y && \
    apt-get install -y postgresql-14 postgresql-client-14 postgresql-contrib-14 gosu && \
    rm -r /var/lib/apt/lists /var/cache/apt/archives
RUN chmod g+rwX /opt/bitnami
RUN localedef -c -f UTF-8 -i en_US en_US.UTF-8
RUN update-locale LANG=C.UTF-8 LC_MESSAGES=POSIX && \
    DEBIAN_FRONTEND=noninteractive dpkg-reconfigure locales
RUN echo 'en_GB.UTF-8 UTF-8' >> /etc/locale.gen && locale-gen
RUN echo 'en_US.UTF-8 UTF-8' >> /etc/locale.gen && locale-gen

COPY rootfs /
RUN mkdir -p /opt/bitnami/postgresql/share /opt/bitnami/postgresql/conf /opt/bitnami/postgresql/tmp /opt/bitnami/postgresql/bin && \
    cp /usr/share/postgresql/14/postgresql.conf.sample /opt/bitnami/postgresql/share/postgresql.conf.sample && \
    ln -s /usr/lib/postgresql/14/bin/postgresql /opt/bitnami/postgresql/bin/postgresql && \
    ln -s /usr/lib/postgresql/14/bin/initdb /opt/bitnami/postgresql/bin/initdb
RUN /opt/bitnami/scripts/postgresql/postunpack.sh
RUN /opt/bitnami/scripts/locales/add-extra-locales.sh
RUN chown -R ${USER_UID}:${USER_GID} /opt/bitnami/postgresql/share /opt/bitnami/postgresql/conf /opt/bitnami/postgresql/tmp /opt/bitnami/postgresql/bin && \
    ls -lart /opt/bitnami/postgresql/conf
ENV BITNAMI_APP_NAME="postgresql" \
    BITNAMI_IMAGE_VERSION="14.2.0-debian-10-r36" \
    LANG="en_US.UTF-8" \
    LANGUAGE="en_US:en" \
    NSS_WRAPPER_LIB="/opt/bitnami/common/lib/libnss_wrapper.so" \
    PATH="/opt/bitnami/postgresql/bin:/opt/bitnami/common/bin:$PATH"

VOLUME [ "/bitnami/postgresql", "/docker-entrypoint-initdb.d", "/docker-entrypoint-preinitdb.d" ]

EXPOSE 5432

USER 1001
ENTRYPOINT [ "/opt/bitnami/scripts/postgresql/entrypoint.sh" ]
CMD [ "/opt/bitnami/scripts/postgresql/run.sh" ]
