# LICENSE CDDL 1.0 + GPL 2.0
#
# Copyright (c) 2014-2015 Oracle and/or its affiliates. All rights reserved.
#
# ORACLE DOCKERFILES PROJECT
# --------------------------
# This Dockerfile extends the Oracle WebLogic image by creating a sample domain.
#
# Util scripts are copied into the image enabling users to plug NodeManager 
# automatically into the AdminServer running on another container.
#
# HOW TO BUILD THIS IMAGE
# -----------------------
# Put all downloaded files in the same directory as this Dockerfile
# Run: 
#      $ sudo docker build -t 1213-domain --build-arg ADMIN_PASSWORD=welcome1 .
#
# Pull base image
# ---------------
FROM oracle/weblogic:12.1.3-developer

# Maintainer
# ----------
MAINTAINER John Bryan Sazon <john.bryan.j.sazon@accenture.com>

# WLS Configuration (editable during runtime)
# ------------------------------
ARG ADMIN_PASSWORD
ARG DOMAIN_NAME
ARG ADMIN_PORT
ARG CLUSTER_NAME

# WLS Configuration (editable during runtime)
# ---------------------------
ENV ADMIN_HOST="wlsadmin" \
    NM_PORT="5556" \
    MS_PORT="7001" \
    CONFIG_JVM_ARGS="-Dweblogic.security.SSL.ignoreHostnameVerification=true"

# WLS Configuration (editable during runtime)
# -----------------------------------------------------------
ENV DOMAIN_NAME="${DOMAIN_NAME:-base_domain}" \
    DOMAIN_HOME=/u01/oracle/user_projects/domains/${DOMAIN_NAME:-base_domain} \
    ADMIN_PORT="${ADMIN_PORT:-8001}" \
    CLUSTER_NAME="${CLUSTER_NAME:-DockerCluster}" \
    PATH=$PATH:/u01/oracle/oracle_common/common/bin:/u01/oracle/wlserver/common/bin:/u01/oracle/user_projects/domains/${DOMAIN_NAME:-base_domain}/bin:/u01/oracle

# Add files required to build this image
USER oracle
COPY container-scripts/* /u01/oracle/

# Configuration of WLS Domain
RUN /u01/oracle/wlst /u01/oracle/create-wls-domain.py && \
    mkdir -p /u01/oracle/user_projects/domains/$DOMAIN_NAME/servers/AdminServer/security && \
    echo "username=weblogic" > /u01/oracle/user_projects/domains/$DOMAIN_NAME/servers/AdminServer/security/boot.properties && \ 
    echo "password=$ADMIN_PASSWORD" >> /u01/oracle/user_projects/domains/$DOMAIN_NAME/servers/AdminServer/security/boot.properties && \
    echo ". /u01/oracle/user_projects/domains/$DOMAIN_NAME/bin/setDomainEnv.sh" >> /u01/oracle/.bashrc 

# Expose Node Manager default port, and also default for admin and managed server 
EXPOSE $NM_PORT $ADMIN_PORT $MS_PORT

WORKDIR $DOMAIN_HOME

# Define default command to start bash. 
CMD ["startWebLogic.sh"]
