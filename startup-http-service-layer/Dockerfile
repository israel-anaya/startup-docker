FROM adoptopenjdk:8-jre-openj9

USER root

ENV HTTP_SERVICE_HOME /usr/http-service
ENV HTTP_SERVICE_LOGS /var/log/http-service
ENV HTTP_SERVICE_USER http-svc
ENV HTTP_SERVICE_GROUP http-svc
ENV HTTP_SERVICE_ARTIFACT_NAME http-service.jar

ENV JMX_EXPORTER_VERSION 0.15.0
ENV JMX_EXPORTER_URL https://repo1.maven.org/maven2/io/prometheus/jmx/jmx_prometheus_javaagent/${JMX_EXPORTER_VERSION}/jmx_prometheus_javaagent-${JMX_EXPORTER_VERSION}.jar
ENV JMX_EXPORTER_HOME /usr/bin/jmx_exporter
ENV JMX_EXPORTER_BIN ${JMX_EXPORTER_HOME}/jmx_prometheus_javaagent.jar
ENV JMX_EXPORTER_CONFIG /etc/jmx_exporter/jmx_exporter.yaml
ENV JMX_EXPORTER_PORT ${JMX_EXPORTER_PORT:-8080}

ENV JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap -XX:MaxRAMFraction=1
ENV JAVA_OPTS_MEM ${JAVA_OPTS_MEM:-Xmx74M -XX:MaxMetaspaceSize=80M -Xss366K}
ENV JAVA_TOOL_OPTIONS -javaagent:${JMX_EXPORTER_BIN}=${JMX_EXPORTER_PORT}:${JMX_EXPORTER_CONFIG}

ENV TZ America/Mexico_City
ENV LANG es_MX.UTF-8
ENV LANGUAGE es_MX.UTF-8
ENV LC_ALL es_MX.UTF-8

COPY jmx_exporter.yaml ${JMX_EXPORTER_CONFIG}

RUN apt-get update \
	&& apt-get install -y --no-install-recommends tzdata curl \
	&& locale-gen ${LC_ALL} && update-locale \
	&& addgroup --system --gid 1000 ${HTTP_SERVICE_GROUP} && adduser --system --uid 1000 ${HTTP_SERVICE_USER} --ingroup ${HTTP_SERVICE_GROUP} \ 
	&& mkdir ${JMX_EXPORTER_HOME} \
	&& mkdir -p ${HTTP_SERVICE_HOME} \
	&& chown -R ${HTTP_SERVICE_USER} ${HTTP_SERVICE_HOME} \
	&& chgrp -R ${HTTP_SERVICE_GROUP} ${HTTP_SERVICE_HOME} \
	&& mkdir -p ${HTTP_SERVICE_LOGS} \
	&& chown -R ${HTTP_SERVICE_USER} ${HTTP_SERVICE_LOGS} \
	&& chgrp -R ${HTTP_SERVICE_GROUP} ${HTTP_SERVICE_LOGS} \
	&& curl ${JMX_EXPORTER_URL} --output ${JMX_EXPORTER_BIN}

VOLUME ${HTTP_SERVICE_LOGS}

WORKDIR ${HTTP_SERVICE_HOME}
USER ${HTTP_SERVICE_USER}

ENTRYPOINT java ${JAVA_TOOL_OPTIONS} ${JAVA_OPTS} ${JAVA_OPTS_MEM} -jar ${HTTP_SERVICE_HOME}/${HTTP_SERVICE_ARTIFACT_NAME} ${SPRING_OPTS}
