# Copyright (c) 2017-2018, 2024 Software AG, Darmstadt, Germany and/or Software AG USA Inc., Reston, VA, USA, and/or its subsidiaries and/or its affiliates and/or their licensors.
#
# Licensed under the Apache License, Version 2.0 (the "License"); you may not use this 
# file except in compliance with the License. You may obtain a copy of the License at
# http://www.apache.org/licenses/LICENSE-2.0
# Unless required by applicable law or agreed to in writing, software distributed under the
# License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, 
# either express or implied. 
# See the License for the specific language governing permissions and limitations under the License.

# Create an image from the base Apama image, with an IAF (File adapter) configuration in it
ARG APAMA_IMAGE=public.ecr.aws/apama
FROM ${APAMA_IMAGE}

COPY --chown=apama:apama File.xml inputFile.txt File-static.xml ${APAMA_WORK}/Adapter/

WORKDIR ${APAMA_WORK}/Adapter
CMD ["iaf", "File.xml"]
