FROM nginx
LABEL "Project"="vproweb"
LABEL "Author"="BNV"
RUN rm -f /etc/nginx/conf.d/default.conf
COPY docker/vproweb/app.conf /etc/nginx/conf.d/vproapp.conf
