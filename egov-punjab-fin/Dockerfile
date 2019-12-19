FROM egovio/alpine-maven-builder-jdk-8:gcp AS build
#ARG WORK_DIR
WORKDIR /app

# copy the project files
COPY egov/pom.xml ${WORKDIR}/pom.xml
#COPY build/maven/start.sh ./start.sh

# not useful for stateless builds
# RUN mvn -B dependency:go-offline

COPY egov ${WORK_DIR}
RUN cd ${WORKDIR}/
RUN mvn clean package -DskipTests
                                 

# Create runtime image

FROM jboss/wildfly:11.0.0.Final
ENV POSTGRESQL_VERSION 42.1.4
USER root

RUN rm -rf /opt/jboss/wildfly/standalone/configuration/standalone.xml && rm -rf /opt/jboss/wildfly/bin/standalone.conf

COPY build/standalone.xml /opt/jboss/wildfly/standalone/configuration/
COPY build/standalone.conf /opt/jboss/wildfly/bin/
COPY --from=build /app/egov-ear/target/*.ear /opt/jboss/wildfly/standalone/deployments/

COPY --chown=jboss /build/org /opt/jboss/wildfly/modules/system/layers/base/org 
RUN cd /tmp && \
chmod 777 /opt/jboss/wildfly/modules/system/layers/base/org/postgres/ && \
curl --location --output postgresql-${POSTGRESQL_VERSION}.jar --url http://search.maven.org/remotecontent?filepath=org/postgresql/postgresql/${POSTGRESQL_VERSION}/postgresql-${POSTGRESQL_VERSION}.jar && \
cp /tmp/postgresql-*.jar /opt/jboss/wildfly/modules/system/layers/base/org/postgres/main && \
rm -rf /tmp/postgresql-*.jar
RUN rm -rf /opt/jboss/wildfly/standalone/configuration/standalone_xml_history
RUN chown -R jboss:jboss /opt/jboss/wildfly/

USER jboss

RUN /opt/jboss/wildfly/bin/add-user.sh admin Admin#70365 --silent

CMD ["/opt/jboss/wildfly/bin/standalone.sh", "-b", "0.0.0.0", "-bmanagement", "0.0.0.0", "-Ddb.migration.enabled=false"]
