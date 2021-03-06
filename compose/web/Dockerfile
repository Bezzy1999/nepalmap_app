FROM library/python:2.7.14-stretch

ENV PYTHONUNBUFFERED="true" \
    PATH="/usr/src/app/compose/web/bin:/usr/src/app/compose/common/bin:${PATH}" \
    GOSU_VERSION="1.10"

RUN set -exu \

    # libgdal
    && apt-get update \
    && apt-get install -y libgdal-dev=2.1.2+dfsg-5 postgresql-client \
    && rm -rf /var/lib/apt/lists/* \

    # gosu for easy step down from root
    && wget -O /usr/local/bin/gosu "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$(dpkg --print-architecture)" \
    && wget -O /usr/local/bin/gosu.asc "https://github.com/tianon/gosu/releases/download/$GOSU_VERSION/gosu-$(dpkg --print-architecture).asc" \
    && export GNUPGHOME="$(mktemp -d)" \
    && gpg --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \
    && gpg --batch --verify /usr/local/bin/gosu.asc /usr/local/bin/gosu \
    && rm -rf "$GNUPGHOME" /usr/local/bin/gosu.asc \
    && chmod +x /usr/local/bin/gosu \
    && gosu nobody true \

    # make workdir
    && mkdir -p /usr/src/app

WORKDIR /usr/src/app

COPY compose/web/requirements.txt /usr/src/app/requirements.txt
RUN pip install --no-cache-dir --global-option=build_ext --global-option="-I/usr/include/gdal/" -r requirements.txt 

COPY . /usr/src/app/

ENTRYPOINT ["/usr/src/app/compose/web/entrypoint.sh"]
CMD ["/bin/bash"]