FROM tomcat:8-jre11
LABEL "Project"="vproapp"
LABEL "Author"="BNV" 
RUN rm -rf /usr/local/tomcat/webapps/ROOT*
COPY target/vprofile-v2.war /usr/local/tomcat/webapps/ROOT.war
EXPOSE 8080
CMD ["/usr/local/tomcat/bin/catalina.sh","run"]
