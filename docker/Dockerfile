# ------------------------------------------------------------------------
#
# Copyright (c) 2020, WSO2 Inc. (http://www.wso2.com). All Rights Reserved.
#
# This software is the property of WSO2 Inc. and its suppliers, if any.
# Dissemination of any information or reproduction of any material contained
# herein in any form is strictly forbidden, unless permitted by WSO2
# expressly. You may not alter or remove any copyright or other notice from
# copies of this content.
#
# ------------------------------------------------------------------------

# set base Docker image to AdoptOpenJDK Ubuntu Docker image
# base image tag adoptopenjdk/openjdk11:jdk-11.0.11_9-ubuntu
ARG BASE_IMAGE_DIGEST="sha256:039c50c163ae0e9e8711bcdc67efe4363d88497c7e1d53f761ab79f05aed4ae5"
FROM adoptopenjdk/openjdk11@${BASE_IMAGE_DIGEST}
LABEL maintainer="WSO2 Docker Maintainers <dev@wso2.org>"

# set Docker image build arguments
# build arguments for user/group configurations
ARG USER=wso2carbon
ARG USER_ID=10001
ARG USER_GROUP=wso2
ARG USER_GROUP_ID=10001
ARG USER_HOME=/home/${USER}
# build arguments for Asgardeo Pack
ARG IS_PACK_DIRECTORY
ARG IS_PACK_LOCATION=${USER_HOME}/${IS_PACK_DIRECTORY}
# build arguments for external artifacts
# build argument for MOTD
ARG MOTD="\n\
Welcome to WSO2 Docker resources.\n\
------------------------------------ \n\
This Docker container comprises of a WSO2 product, running with its latest GA release \n\
which is under the Apache License, Version 2.0. \n\
Read more about Apache License, Version 2.0 here @ http://www.apache.org/licenses/LICENSE-2.0.\n"

# create the non-root user and group and set MOTD login message
RUN \
    groupadd --system -g ${USER_GROUP_ID} ${USER_GROUP} \
    && useradd --system --create-home --home-dir ${USER_HOME} --no-log-init -g ${USER_GROUP_ID} -u ${USER_ID} ${USER} \
    && echo '[ ! -z "${TERM}" -a -r /etc/motd ] && cat /etc/motd' >> /etc/bash.bashrc; echo "${MOTD}" > /etc/motd

# copy init script to user home
COPY --chown=wso2carbon:wso2 docker-entrypoint.sh ${USER_HOME}/

# copy initializer binary to user home
COPY --chown=wso2carbon:wso2 initializer ${USER_HOME}/

# install required packages
RUN \
    apt-get update \
    && DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
        netcat \
    && rm -rf /var/lib/apt/lists/*

# Expand Asgardeo Pack
COPY --chown=wso2carbon:wso2 ${IS_PACK_DIRECTORY} ${IS_PACK_LOCATION}

# Create directories and change ownership to avoid frequent WARN logs.
RUN \
   mkdir -p ${USER_HOME}/.java/.systemPrefs ${USER_HOME}/.java/.userPrefs \
   && chown -R wso2carbon:wso2 ${USER_HOME}/.java 

# set the user and work directory
USER ${USER_ID}
WORKDIR ${USER_HOME}

# set environment variables
ENV WORKING_DIRECTORY=${USER_HOME} \
    WSO2_SERVER_HOME=${IS_PACK_LOCATION}

# expose ports
EXPOSE 4000 9443

# initiate container and start WSO2 Carbon server
ENTRYPOINT ["/home/wso2carbon/docker-entrypoint.sh"]
