FROM spark-base-miniconda:2.3.1-hadoop2.7
MAINTAINER Ivan Ermilov <ivan.s.ermilov@gmail.com>

ENV APACHE_SPARK_VERSION 2.3.1
ENV APACHE_HADOOP_VERSION 2.7.0
ENV ZEPPELIN_VERSION 0.8.0

# RUN apt-get update && apt-get install wget
RUN set -x \
    && curl -fSL "http://www-eu.apache.org/dist/zeppelin/zeppelin-${ZEPPELIN_VERSION}/zeppelin-${ZEPPELIN_VERSION}-bin-netinst.tgz" -o /tmp/zeppelin.tgz \
    && tar -xzvf /tmp/zeppelin.tgz -C /opt/ \
    && mv /opt/zeppelin-* /opt/zeppelin \
    && rm /tmp/zeppelin.tgz


WORKDIR /opt/zeppelin

CMD ["/opt/zeppelin/bin/zeppelin.sh"]
