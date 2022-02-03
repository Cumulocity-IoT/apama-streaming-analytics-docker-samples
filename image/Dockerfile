# Copyright (c) 2017-2020 Software AG, Darmstadt, Germany and/or its licensors
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

# Pull CentOS 8 base image from the Docker Hub - this is the latest supported
# operating system for this version of Apama.
FROM centos:8

LABEL maintainer="SoftwareAG"

ARG APAMA_HOME=/opt/softwareag/Apama
ARG APAMA_WORK=/apama_work
ARG SAG_HOME=/opt/softwareag/Apama/..

RUN /sbin/groupadd -g 1724 sagadmin && /sbin/useradd -d / -M -N -g 1724 -u 1724 sagadmin

# Copy and customize the minimal contents from the installation in order to
# run non-interactive Apama components
COPY --chown=1724:1724 ./common/security/ ${APAMA_HOME}/../common/security/
COPY --chown=1724:1724 ./jvm/jvm/ /java_home/
COPY --chown=1724:1724 ./Apama/adapters/ ${APAMA_HOME}/adapters/
COPY --chown=1724:1724 ./Apama/bin/ ${APAMA_HOME}/bin/
COPY --chown=1724:1724 ./Apama/catalogs/ ${APAMA_HOME}/catalogs/
COPY --chown=1724:1724 ./Apama/include/ ${APAMA_HOME}/include/
COPY --chown=1724:1724 ./Apama/lib/ ${APAMA_HOME}/lib/
COPY --chown=1724:1724 ./Apama/third_party/ ${APAMA_HOME}/third_party/
COPY --chown=1724:1724 ./Apama/monitors/ ${APAMA_HOME}/monitors/
COPY --chown=1724:1724 ./Apama/connectivity/ ${APAMA_HOME}/connectivity/
COPY --chown=1724:1724 ./Apama/etc ${APAMA_HOME}/etc/
COPY --chown=1724:1724 ./common/lib/ext ${APAMA_HOME}/../common/lib/ext
COPY --chown=1724:1724 ./common/lib/ant ${APAMA_HOME}/../common/lib/ant
COPY --chown=1724:1724 ./common/lib/glassfish ${APAMA_HOME}/../common/lib/glassfish

# Set the environment appropriate to running Apama executables
ENV PATH=${APAMA_HOME}/bin/:/java_home/bin:${SAG_HOME}/common/lib/ant/bin:${APAMA_HOME}/third_party/python/bin:$PATH\
    LD_LIBRARY_PATH=${APAMA_HOME}/lib/:${APAMA_HOME}/adapters/lib/:/java_home/lib/server/:${APAMA_HOME}/third_party/python/lib64:${APAMA_WORK}/lib/\
    APAMA_HOME=${APAMA_HOME}/\
    APAMA_WORK=${APAMA_WORK}/\
	 SAG_HOME=${SAG_HOME}/\
    APAMA_JRE=/java_home/\
    JAVA_HOME=/java_home/\
    APAMA_LIBRARY_VERSION=10.11\
    ANT_HOME=${SAG_HOME}/common/lib/ant\
    PYTHONPATH=${APAMA_HOME}/third_party/python/lib64/python3.9/site-packages

# Expose the default server port for the correlator, IAF, dashboard server management, dashboard server data, display server management and
# display server data respectively (Apama Core only contains a correlator)
EXPOSE 15903 16903 28888 3278 28889 3279

RUN mkdir -p ${APAMA_WORK} && chown 1724:1724 ${APAMA_WORK} && chown 1724:1724 ${APAMA_HOME} && chown 1724:1724 ${SAG_HOME}

USER 1724

# This is the current working directory, and also the location that the
# (default CMD) correlator obtains the licence file from; license/license.txt,
# the same location in a typically installed apama-work.
WORKDIR ${APAMA_WORK}/

# The default operation for the image is to start a correlator, but this can
# be overridden by the command given to 'docker run'
CMD ["correlator", "-j"]
