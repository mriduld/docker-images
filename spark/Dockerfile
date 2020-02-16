FROM openjdk:8-jdk-stretch

ARG SPARK_VERSION
ARG HADOOP_VERSION
ARG HIVE_VERSION
ARG SCALA_VERSION
ARG SBT_VERSION

ENV APACHE_MIRROR archive.apache.org/dist

ENV DEPS_HOME /opt/deps
ENV SBT_HOME ${DEPS_HOME}/sbt-${SBT_VERSION}
ENV SCALA_HOME ${DEPS_HOME}/scala-${SCALA_VERSION}

ENV LANG=C.UTF-8 LC_ALL=C.UTF-8

RUN apt-get update --fix-missing && apt-get install -y \
    wget \
    bzip2 \
    ca-certificates \
    libglib2.0-0 \
    libxext6 \
    libsm6 \
    libxrender1 \
    git \
    make \
    gcc

# Install Cloud SDK

# Install SCALA

ENV PATH $PATH:$SCALA_HOME/bin

RUN mkdir -p ${SCALA_HOME} && \
    cd /root && \
    curl -o scala-$SCALA_VERSION.tgz http://downloads.typesafe.com/scala/$SCALA_VERSION/scala-$SCALA_VERSION.tgz && \
    tar -xf scala-$SCALA_VERSION.tgz -C $DEPS_HOME && \
    rm scala-$SCALA_VERSION.tgz && \
    echo -ne "- with scala $SCALA_VERSION\n" >> /root/.built && \
    scala -version

# Install SBT

ENV PATH $PATH:$SBT_HOME/bin

RUN mkdir -p ${SBT_HOME} && \
    curl -sL sbt-$SBT_VERSION.tgz https://github.com/sbt/sbt/releases/download/v$SBT_VERSION/sbt-$SBT_VERSION.tgz | gunzip | tar -x -C $SBT_HOME && \
    cp -rf $SBT_HOME/sbt/bin/ $SBT_HOME/bin/ && \
    cp -rf $SBT_HOME/sbt/conf/ $SBT_HOME/conf/ && \
    rm -rf sbt-$SBT_VERSION.tgz && \
    rm -rf $SBT_HOME/sbt && \
    echo -ne "- with sbt $SBT_VERSION\n" >> /root/.built && \
    sbt sbtVersion

# HADOOP

ENV HADOOP_HOME /usr/hadoop-$HADOOP_VERSION
ENV HADOOP_CONF_DIR=$HADOOP_HOME/etc/hadoop
ENV PATH $PATH:$HADOOP_HOME/bin
RUN curl \
    "http://$APACHE_MIRROR/hadoop/common/hadoop-$HADOOP_VERSION/hadoop-$HADOOP_VERSION.tar.gz" \
    | gunzip \
    | tar -x -C /usr/ && \
    rm -rf $HADOOP_HOME/share/doc

# HIVE

ENV HIVE_HOME=/usr/apache-hive-$HIVE_VERSION-bin
ENV HIVE_CONF_DIR=$HIVE_HOME/conf
ENV PATH $PATH:$HIVE_HOME/bin
RUN curl \
  "https://archive.apache.org/dist/hive/hive-$HIVE_VERSION/apache-hive-$HIVE_VERSION-bin.tar.gz" \
    | gunzip \
    | tar -x -C /usr/ \
  && chown -R root:root $HIVE_HOME \
  && mkdir -p $HIVE_HOME/hcatalog/var/log \
  && mkdir -p $HIVE_HOME/var/log \
  && mkdir -p /data/hive/ \
  && mkdir -p $HIVE_CONF_DIR \
  && chmod 777 $HIVE_HOME/hcatalog/var/log \
  && chmod 777 $HIVE_HOME/var/log

# SPARK

ENV SPARK_PACKAGE spark-$SPARK_VERSION-bin-without-hadoop
ENV SPARK_HOME /usr/spark-$SPARK_VERSION
ENV SPARK_DIST_CLASSPATH="$HADOOP_HOME/etc/hadoop/*:$HADOOP_HOME/share/hadoop/common/lib/*:$HADOOP_HOME/share/hadoop/common/*:$HADOOP_HOME/share/hadoop/hdfs/*:$HADOOP_HOME/share/hadoop/hdfs/lib/*:$HADOOP_HOME/share/hadoop/hdfs/*:$HADOOP_HOME/share/hadoop/yarn/lib/*:$HADOOP_HOME/share/hadoop/yarn/*:$HADOOP_HOME/share/hadoop/mapreduce/lib/*:$HADOOP_HOME/share/hadoop/mapreduce/*"
ENV PATH $PATH:$SPARK_HOME/bin
RUN curl \
    "http://$APACHE_MIRROR/spark/spark-$SPARK_VERSION/$SPARK_PACKAGE.tgz" \
    | gunzip \
    | tar x -C /usr/ && \
    mv /usr/$SPARK_PACKAGE $SPARK_HOME && \
    rm -rf $SPARK_HOME/examples $SPARK_HOME/ec2


RUN spark-shell --packages org.apache.spark:spark-hive_2.11:$SPARK_VERSION,\
    --exclude-packages com.sun.jdmk:jmxtools,com.sun.jmx:jmxri,javax.jms:jms,\
    javax.servlet:servlet-api,javax.servlet.jsp:jsp-api,org.mortbay.jetty:servlet-api

# Copy / cache the installed to be added on the classpath
RUN mkdir -p /opt/.ivy2/jars/ && \
    cp -rf /root/.ivy2/jars/ /opt/.ivy2 && \
    rm -rf /root/.ivy2/jars

# Symlink relevant hadoop libs for hive
#RUN ln -s $HIVE_HOME/lib/.

#COPY src/config/spark/spark-defaults.conf $SPARK_HOME/conf/
#COPY src/config/spark/hive-site.xml $SPARK_HOME/conf/

#COPY src/config/hive/hive-site.xml $HIVE_CONF_DIR/

#COPY src/entrypoint.sh /opt/

#RUN cd /data/hive && $HIVE_HOME/bin/schematool -dbType derby -initSchema

# Install Sparking Water

EXPOSE 54321

WORKDIR /app

CMD ["/bin/bash"]
