#comment5
FROM tomcat:8-jre8

RUN rm -rf /usr/local/tomcat/webapps/*

COPY jpetstore.war /usr/local/tomcat/webapps/jpetstore.war

EXPOSE 8080
CMD ["catalina.sh", "run"]
