FROM ubuntu as images

ARG BUILD_REPO
ARG MAIN_SUBDOMAIN
ARG IMAGE_SUBDOMAIN
ARG ROOTDIR
ARG IMAGEROOTDIR
ARG LOCAL_INSTALL_DIR

LABEL maintainer="ivan.kuchin@gmail.com"
LABEL org.opencontainers.image.source="https://github.com/IvanKuchin/${BUILD_REPO}"

####################
# copy from build  #
####################
COPY ./apache2/ /etc/apache2/

###################
# install apache2 #
###################
RUN apt update \
    && apt-get install --no-install-recommends --no-install-suggests -y apache2 \
    && rm -rf /var/lib/apt/lists/*

#####################
# configure apache2 #
#####################
# RUN git clone --depth 1 https://github.com/IvanKuchin/${BUILD_REPO}-ui.git ${ROOTDIR}
# RUN cp -R ${ROOTDIR}/supplemental/apache2 /etc
RUN mkdir -p /home/httpd/images/html/ \
 && mkdir -p /home/httpd/images/logs/

RUN a2enmod rewrite \
 && a2enmod expires \
 && a2enmod headers \
 && a2enconf mod_expires \
 && a2dismod cgi \
 && a2dissite `ls /etc/apache2/sites-enabled/` \
 && a2ensite *images

EXPOSE 80

####################################################################
# change folders to proper owner                                   #
####################################################################
RUN chown -R www-data:www-data /home/httpd/images/

####################################################################
# remove unnecessary folders                                       #
####################################################################
#RUN rm -rf ${LOCAL_INSTALL_DIR} \
# && rm -rf ${ROOTDIR} \
# && rm -rf ${GIT_FOLDER}

############################################################
## forward request and error logs to docker log collector ##
############################################################
RUN ln -sf /dev/stdout /home/httpd/images/logs/access.log \
 && ln -sf /dev/stderr /home/httpd/images/logs/error.log

STOPSIGNAL SIGTERM

CMD ["apachectl", "-D", "FOREGROUND"]
