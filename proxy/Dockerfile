FROM httpd:2.4-alpine


RUN sed -i 's/#LoadModule proxy_module/LoadModule proxy_module/' /usr/local/apache2/conf/httpd.conf && \
    sed -i 's/#LoadModule proxy_http_module/LoadModule proxy_http_module/' /usr/local/apache2/conf/httpd.conf


RUN rm -f /usr/local/apache2/htdocs/index.html


COPY 000-default.conf /usr/local/apache2/conf/extra/000-default.conf

RUN echo "Include conf/extra/000-default.conf" >> /usr/local/apache2/conf/httpd.conf

EXPOSE 80
