FROM adoptopenjdk/openjdk11

ENV SPARK_HOME=/opt/spark \
         PATH=/app/bin:/opt/spark/bin:$PATH \
         PYTHONPATH=/opt/spark/python/lib/pyspark.zip:/opt/spark/python/lib/py4j-${PY4J}-src.zip:/opt/spark/python

RUN find /etc/apt -type f -exec chmod 644 {} + \
    && apt-get -y update \
    && apt-get -y upgrade \
    && apt-get -y install --no-install-recommends \
        apt-transport-https \
        bash \
        bzip2 \
        ca-certificates \
        ca-certificates-java \
        curl \
        dpkg\
        gettext \
        git \
        gnupg2 \
        grep \
        jq \
        locales \
        python3.9 \
        python3.9-dev \
        python3.9-tk \
        python3-pip \
        sed \
        sudo \
        tini \
        tzdata \
        unzip \
        wget \
        zip \
        libglib2.0-0 \
        libxext6 \
        libsm6 \
        libxrender1 \
       make \
       gcc \
       vim \
    && ln -s /usr/bin/python3.9 /usr/bin/python \ 
    && curl -o /tmp/get-pip.py https://bootstrap.pypa.io/get-pip.py \  
    && rm -rf /root/.cache \
    && rm -rf /var/cache/apt/* \ 
    && rm -rf /var/lib/apt/lists/* \
    && rm -rf /tmp/*

ARG distribution_name="spark-3.2.1-bin-hadoop3.2" 
ARG spark_uid=185

RUN curl -o "/tmp/${distribution_name}.tgz" "https://dlcdn.apache.org/spark/spark-3.2.1/${distribution_name}.tgz" \
    && mkdir -p "${SPARK_HOME}" \
    && tar -x -C "${SPARK_HOME}" --strip-components=1 -f "/tmp/${distribution_name}.tgz" \
    && rm -f "/tmp/${distribution_name}.tgz" 
    
COPY files/delta-core_2.12-1.1.0.jar $SPARK_HOME/jars/delta-core_2.12-1.1.0.jar

COPY files/spark-defaults.conf $SPARK_HOME/conf/spark-defaults.conf

RUN useradd -U -M -u "${spark_uid}" -d "${SPARK_HOME}" spark

RUN pip install delta-spark

USER ${spark_uid}