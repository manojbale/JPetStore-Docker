# Centos based container with Java and Tomcat
FROM centos:centos7
MAINTAINER manojbale 

# Install prepare infrastructure
RUN yum -y update && \
yum -y install wget && \
yum -y install tar

RUN (cd /lib/systemd/system/sysinit.target.wants/; for i in *; do [ $i == && \
systemd-tmpfiles-setup.service ] || rm -f $i; done) && \
rm -f /lib/systemd/system/multi-user.target.wants/* && \
rm -f /etc/systemd/system/*.wants/* && \
rm -f /lib/systemd/system/local-fs.target.wants/* && \
rm -f /lib/systemd/system/sockets.target.wants/*udev* && \
rm -f /lib/systemd/system/sockets.target.wants/*initctl* && \
rm -f /lib/systemd/system/basic.target.wants/* && \
rm -f /lib/systemd/system/anaconda.target.wants/*

VOLUME [ “/sys/fs/cgroup” ]

ENV TOMCAT_USER {{ tomcat_user }}
ENV TOMCAT_GROUP {{ tomcat_group }}

RUN groupadd $TOMCAT_GROUP  && \
useradd -g $TOMCAT_GROUP $TOMCAT_USER 


# Prepare environment 
ENV JAVA_HOME {{ java_home }}
ENV CATALINA_HOME {{ tomcat_home }} 
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/bin:$CATALINA_HOME/scripts

# Install Oracle Java8
ENV JAVA_VERSION {{ java_version }}
ENV JAVA_BUILD {{ java_build }}
ENV JAVA_DL_HASH {{ java_dl_hash }}

RUN wget --no-check-certificate --no-cookies --header "Cookie: oraclelicense=accept-securebackup-cookie" http://download.oracle.com/otn-pub/java/jdk/${JAVA_BUILD}/${JAVA_DL_HASH}/jdk-${JAVA_VERSION}-linux-x64.tar.gz && \
tar -xvf jdk-${JAVA_VERSION}-linux-x64.tar.gz && \
rm -rf jdk*.tar.gz && \
mv jdk* ${JAVA_HOME} && \
chown -R ${TOMCAT_USER}:${TOMCAT_GROUP} ${JAVA_HOME} 

# Install Tomcat
ENV TOMCAT_MAJOR {{ tomcat_major }}
ENV TOMCAT_VERSION {{ tomcat_version }}

RUN wget http://www-eu.apache.org/dist/tomcat/tomcat-${TOMCAT_MAJOR}/v${TOMCAT_VERSION}/bin/apache-tomcat-${TOMCAT_VERSION}.tar.gz && \
tar -xvf apache-tomcat-${TOMCAT_VERSION}.tar.gz && \
rm apache-tomcat*.tar.gz && \
mv apache-tomcat* ${CATALINA_HOME} && \
rm -rf ${CATALINA_HOME}/conf/server.xml && \
rm -rf ${CATALINA_HOME}/conf/tomcat-users.xml && \
rm -rf ${CATALINA_HOME}/webapps/manager/META-INF/context.xml

ADD server.xml ${CATALINA_HOME}/conf/
ADD tomcat-users.xml ${CATALINA_HOME}/conf/
ADD context.xml ${CATALINA_HOME}/webapps/manager/META-INF/

ENV APPLICATION_BINARY {{ application_binary }}
ADD ${APPLICATION_BINARY} ${CATALINA_HOME}/webapps/


RUN chown -R ${TOMCAT_USER}:${TOMCAT_GROUP} ${CATALINA_HOME}

ADD tomcat.service /etc/systemd/system/

#RUN cd /etc/systemd/system/ && \
#chmod 755 tomcat.service

#RUN systemctl enable tomcat.service

RUN chmod +x ${CATALINA_HOME}/bin/*sh

EXPOSE 8080
EXPOSE 8443
EXPOSE 8009

CMD /usr/sbin/init


