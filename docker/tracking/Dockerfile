FROM ubuntu:20.04

# Build up the OS
ENV DEBIAN_FRONTEND=noninteractive
RUN apt-get update && \
    apt-get install -y maven \
                       openjdk-11-jdk-headless \
                       tomcat9 \
                       tomcat9-admin \
                       git

# get and build application from git
RUN git clone https://github.com/NASA-PDS/tracking-service.git
# if one need a specific tag, default is latest master version
# git checkout tags/<tagname>
WORKDIR "/tracking-service"

# Point at the JVM for maven building javadoc (may need to be x86 for 32bit)
ENV CATALINA_HOME=/usr/share/tomcat9
ENV CATALINA_BASE=/var/lib/tomcat9
ENV CATALINA_TMPDIR=/tmp
ENV JAVA_HOME=/usr
ENV JAVA_OPTS=-Djava.awt.headless=true
ENV JRE_HOME=/usr

# remove default mysql connection
RUN rm src/main/resources/mysql.properties
# Build the application and deploy it inside the container
RUN mvn site && \
    mvn package && \
    cp target/tracking.war ${CATALINA_BASE}/webapps && \
    echo "cd /tmp ; mvn site:run" > /tmp/run.sh && \
    chmod 755 /tmp/run.sh

# Tomcat is on the default 8080 port which can be overridden at docker run
EXPOSE 8080

# Start tomcat when the container is run
ENTRYPOINT ["/usr/bin/bash"]
CMD ["/usr/libexec/tomcat9/tomcat-start.sh"]
