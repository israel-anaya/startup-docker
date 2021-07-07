FROM adoptopenjdk:8-jre-openj9

USER root

ARG MAVEN_VERSION=3.8.1
ARG SHA=0ec48eb515d93f8515d4abe465570dfded6fa13a3ceb9aab8031428442d9912ec20f066b2afbf56964ffe1ceb56f80321b50db73cf77a0e2445ad0211fb8e38d
ARG BASE_URL=https://apache.osuosl.org/maven/maven-3/${MAVEN_VERSION}/binaries

ENV BUILDER_HOME /usr/http-service-builder
ENV BUILDER_LOGS /var/log/http-service-builder
ENV BUILDER_USER builder
ENV BUILDER_GROUP builder
ENV JAVA_OPTS -Djava.security.egd=file:/dev/./urandom -XX:+UnlockExperimentalVMOptions -XX:+UseCGroupMemoryLimitForHeap -XX:MaxRAMFraction=1

ENV MAVEN_HOME /usr/share/maven
ENV MAVEN_CONFIG ${BUILDER_HOME}/.m2

ENV TZ America/Mexico_City
ENV LANG es_MX.UTF-8
ENV LANGUAGE es_MX.UTF-8
ENV LC_ALL es_MX.UTF-8

RUN apt-get update \
	&& apt-get install -y --no-install-recommends tzdata tar curl git \
	&& locale-gen ${LC_ALL} && update-locale \
	&& mkdir -p ${MAVEN_HOME} ${MAVEN_HOME}/ref \
	&& curl -fsSL -o /tmp/apache-maven.tar.gz ${BASE_URL}/apache-maven-${MAVEN_VERSION}-bin.tar.gz \
	&& echo "${SHA}  /tmp/apache-maven.tar.gz" | sha512sum -c - \
	&& tar -xzf /tmp/apache-maven.tar.gz -C ${MAVEN_HOME} --strip-components=1 \
	&& rm -f /tmp/apache-maven.tar.gz \
	&& ln -s ${MAVEN_HOME}/bin/mvn /usr/bin/mvn \
	&& addgroup --system --gid 1000 ${BUILDER_GROUP} && adduser --system --uid 1000 ${BUILDER_USER} --ingroup ${BUILDER_GROUP} \ 
	&& mkdir -p ${BUILDER_HOME} \
	&& chown -R ${BUILDER_USER} ${BUILDER_HOME} \
	&& chgrp -R ${BUILDER_GROUP} ${BUILDER_HOME} \
	&& mkdir -p ${BUILDER_LOGS} \
	&& chown -R ${BUILDER_USER} ${BUILDER_LOGS} \
	&& chgrp -R ${BUILDER_GROUP} ${BUILDER_LOGS}

VOLUME ${MAVEN_CONFIG}
VOLUME ${BUILDER_LOGS}

WORKDIR ${BUILDER_HOME}
USER ${BUILDER_USER}

