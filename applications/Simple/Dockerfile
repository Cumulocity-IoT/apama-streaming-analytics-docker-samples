# Copyright (c) 2017-2018, 2022, 2024 Cumulocity GmbH, Duesseldorf, Germany and/or its affiliates and/or their licensors.
#
# Licensed under the Apache License, Version 2.0 (the "License"); you may not use this 
# file except in compliance with the License. You may obtain a copy of the License at
# http://www.apache.org/licenses/LICENSE-2.0
# Unless required by applicable law or agreed to in writing, software distributed under the
# License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, 
# either express or implied. 
# See the License for the specific language governing permissions and limitations under the License.

# Create an image from the base Apama image, with an EPL application in it ready to deploy
ARG APAMA_VERSION=latest
ARG APAMA_IMAGE=public.ecr.aws/apama/apama-correlator-minimal:${APAMA_VERSION}
FROM ${APAMA_IMAGE}
COPY --chown=apama:apama init.yaml HelloWorld.mon ${APAMA_WORK}/Simple/

WORKDIR ${APAMA_WORK}/Simple

CMD ["correlator", "--config", "init.yaml"]
