# Copyright (c) 2017-2018 Software AG, Darmstadt, Germany and/or its licensors
#
# Licensed under the Apache License, Version 2.0 (the "License"); you may not use this 
# file except in compliance with the License. You may obtain a copy of the License at
# http://www.apache.org/licenses/LICENSE-2.0
# Unless required by applicable law or agreed to in writing, software distributed under the
# License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, 
# either express or implied. 
# See the License for the specific language governing permissions and limitations under the License.
#
# This Dockerfile creates an image that can run Apama components, and is built
# from an existing Apama installation. The build should be run from the root
# of your Software AG installation. e.g. '/opt/softwareag/'

# Pull CentOS 7 base image from the Docker Hub - this is the latest supported
# operating system for this version of Apama.
FROM centos:7

MAINTAINER SoftwareAG

ARG APAMA_HOME=@DOCKER_APAMA_HOME@
ARG APAMA_WORK=@DOCKER_APAMA_WORK@
ARG SAG_HOME=@DOCKER_APAMA_HOME@/..

RUN /sbin/groupadd -g @DOCKER_USER@ sagadmin && /sbin/useradd -d / -M -N -g @DOCKER_USER@ -u @DOCKER_USER@ sagadmin

# Copy and customize the minimal contents from the installation in order to
# run non-interactive Apama components
COPY --chown=@DOCKER_USER@:@DOCKER_USER@ ./common/security/ ${APAMA_HOME}/../common/security/
COPY --chown=@DOCKER_USER@:@DOCKER_USER@ ./jvm/jvm/ /java_home/
COPY --chown=@DOCKER_USER@:@DOCKER_USER@ ./Apama/adapters/ ${APAMA_HOME}/adapters/
COPY --chown=@DOCKER_USER@:@DOCKER_USER@ ./Apama/bin/ ${APAMA_HOME}/bin/
COPY --chown=@DOCKER_USER@:@DOCKER_USER@ ./Apama/catalogs/ ${APAMA_HOME}/catalogs/
COPY --chown=@DOCKER_USER@:@DOCKER_USER@ ./Apama/lib/ ${APAMA_HOME}/lib/
COPY --chown=@DOCKER_USER@:@DOCKER_USER@ ./Apama/third_party/ ${APAMA_HOME}/third_party/
COPY --chown=@DOCKER_USER@:@DOCKER_USER@ ./Apama/monitors/ ${APAMA_HOME}/monitors/
COPY --chown=@DOCKER_USER@:@DOCKER_USER@ ./Apama/connectivity/ ${APAMA_HOME}/connectivity/
COPY --chown=@DOCKER_USER@:@DOCKER_USER@ ./Apama/etc ${APAMA_HOME}/etc/
COPY --chown=@DOCKER_USER@:@DOCKER_USER@ ./common/lib/ext ${APAMA_HOME}/../common/lib/ext

# Set the environment appropriate to running Apama executables
ENV PATH=${APAMA_HOME}/bin/:/java_home/bin:${APAMA_HOME}/third_party/apache_ant/bin:${APAMA_HOME}/third_party/python/bin:$PATH\
    LD_LIBRARY_PATH=${APAMA_HOME}/lib/:${APAMA_HOME}/adapters/lib/:/java_home/jre/lib/amd64/server/:/java_home/jre/lib/amd64/:${APAMA_HOME}/third_party/python/lib:${APAMA_WORK}/lib/\
    APAMA_HOME=${APAMA_HOME}/\
    APAMA_WORK=${APAMA_WORK}/\
	 SAG_HOME=${SAG_HOME}/\
    APAMA_JRE=/java_home/jre/\
    JAVA_HOME=/java_home/\
    APAMA_LIBRARY_VERSION=@APAMA_LIBRARY_VERSION@\
    ANT_HOME=${APAMA_HOME}/third_party/apache_ant\
    PYTHONPATH=${APAMA_HOME}/third_party/python/@LIB_PYTHON3_SITE_PACKAGES_SUFFIX@

# Expose the default server port for the correlator, IAF, dashboard server management, dashboard server data, display server management and
# display server data respectively (Apama Core only contains a correlator)
EXPOSE 15903 16903 28888 3278 28889 3279

RUN mkdir -p ${APAMA_WORK} && chown @DOCKER_USER@:@DOCKER_USER@ ${APAMA_WORK} && chown @DOCKER_USER@:@DOCKER_USER@ ${APAMA_HOME} && chown @DOCKER_USER@:@DOCKER_USER@ ${SAG_HOME}

USER @DOCKER_USER@

# This is the current working directory, and also the location that the
# (default CMD) correlator obtains the licence file from; license/license.txt,
# the same location in a typically installed apama-work.
WORKDIR ${APAMA_WORK}/

# The default operation for the image is to start a correlator, but this can
# be overridden by the command given to 'docker run'
CMD ["correlator", "-j"]
