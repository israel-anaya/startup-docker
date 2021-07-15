FROM adoptopenjdk:8-jre-openj9

USER root

ARG KAFKA_VERSION=2.8.0
ARG SCALA_VERSION=2.13
ARG SHA=3C49DCA1147A0A249DD88E089F40AF31A67B8207ED2D9E2294FA9A6D41F5ED0B006943CD60D8E30D7E69D760D398F299CAFCD68B6ED7BEDF9F93D1B7A9E8C487
ARG BASE_URL=https://downloads.apache.org/kafka/${KAFKA_VERSION}

ENV KAFKA_HOME /opt/kafka
ENV KAFKA_LOGS /var/log/kafka
ENV KAFKA_USER kafka
ENV KAFKA_GROUP kafka
ARG KAFKA_ARTIFACT_NAME=kafka_${SCALA_VERSION}-${KAFKA_VERSION}.tgz

ENV TZ America/Mexico_City
ENV LANG es_MX.UTF-8
ENV LANGUAGE es_MX.UTF-8
ENV LC_ALL es_MX.UTF-8


RUN apt-get update \
	&& apt-get install -y --no-install-recommends tzdata tar curl git \
	&& locale-gen ${LC_ALL} && update-locale \
	&& mkdir -p ${KAFKA_HOME} \
	&& curl -fsSL -o /tmp/${KAFKA_ARTIFACT_NAME} ${BASE_URL}/${KAFKA_ARTIFACT_NAME} \
	&& echo "${SHA}  /tmp/${KAFKA_ARTIFACT_NAME}" | sha512sum -c - \
	&& tar -xzf /tmp/${KAFKA_ARTIFACT_NAME} -C ${KAFKA_HOME} --strip-components=1 \
	&& rm -f /tmp/${KAFKA_ARTIFACT_NAME} \
	&& addgroup --system --gid 1000 ${KAFKA_GROUP} && adduser --system --uid 1000 ${KAFKA_USER} --ingroup ${KAFKA_GROUP} \ 
	&& chown -R ${KAFKA_USER} ${KAFKA_HOME} \
	&& chgrp -R ${KAFKA_GROUP} ${KAFKA_HOME} \
	&& mkdir -p ${KAFKA_LOGS} \
	&& chown -R ${KAFKA_USER} ${KAFKA_LOGS} \
	&& chgrp -R ${KAFKA_GROUP} ${KAFKA_LOGS}

COPY server.properties ${KAFKA_HOME}/config/server.properties

VOLUME ${KAFKA_LOGS}

WORKDIR ${KAFKA_HOME}
USER ${KAFKA_USER}

ENTRYPOINT ${KAFKA_HOME}/bin/kafka-server-start.sh ${KAFKA_HOME}/config/server.properties