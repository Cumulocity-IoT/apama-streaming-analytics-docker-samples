# Copyright (c) 2017 Software AG, Darmstadt, Germany and/or its licensors
#
# Licensed under the Apache License, Version 2.0 (the "License"); you may not use this 
# file except in compliance with the License. You may obtain a copy of the License at
# http://www.apache.org/licenses/LICENSE-2.0
# Unless required by applicable law or agreed to in writing, software distributed under the
# License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, 
# either express or implied. 
# See the License for the specific language governing permissions and limitations under the License.

# Create an image from the base Apama image, with an EPL application in it ready to deploy
ARG APAMA_IMAGE=apama
FROM ${APAMA_IMAGE}
COPY init.yaml HelloWorld.mon /apama_work/Simple/

WORKDIR /apama_work/Simple
CMD ["correlator", "--config", "init.yaml"]
