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

# Create an image from the base Apama image, copying the project in it ready to deploy
ARG APAMA_BUILDER=builder
ARG APAMA_IMAGE=apama
FROM ${APAMA_BUILDER} as builder

COPY --chown=sagadmin:sagadmin DetectUnusualReadings ${APAMA_WORK}/Queries
RUN engine_deploy --outputDeployDir ${APAMA_WORK}/Queries_deployed ${APAMA_WORK}/Queries

FROM ${APAMA_IMAGE}

COPY --chown=sagadmin:sagadmin --from=builder ${APAMA_WORK}/Queries_deployed ${APAMA_WORK}/Queries_deployed
WORKDIR ${APAMA_WORK}

#init.yaml lists the EPL to inject at startup:
CMD ["correlator","--config", "Queries_deployed","--logfile",""]
