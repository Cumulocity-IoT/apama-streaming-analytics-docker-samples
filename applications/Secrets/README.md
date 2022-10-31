# Secrets Sample

## COPYRIGHT NOTICE

Copyright (c) 2018, 2022 Software AG, Darmstadt, Germany and/or Software AG USA Inc., Reston, VA, USA, and/or its subsidiaries and/or its affiliates and/or their licensors.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this 
file except in compliance with the License. You may obtain a copy of the License at
http://www.apache.org/licenses/LICENSE-2.0
Unless required by applicable law or agreed to in writing, software distributed under the
License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, 
either express or implied. 
See the License for the specific language governing permissions and limitations under the License.


## DESCRIPTION

This sample demonstrates how to use secrets in Docker and Kubernetes to set variables in correlator
config files. The secrets can then be loaded at runtime into a correlator or other containers. This can be
used to securely provide credentials to the system. In addition, the same pattern can be used with Docker
configs or Kubernetes config maps to manage non-secret runtime configuration.

Running with Docker
==============

This sample can't be run with docker-compose, due to limitations with secrets. There are two types of secrets,
'file' or 'external' secrets. File secrets can only be used locally and not with a remote docker host. External
secrets are not compatible with docker-compose. Therefore the sample should be run using the docker service
command.

First, create the secret

    > echo "CORRELATOR_NAME=MyCorrelatorName" | docker secret create secret.properties -

Then build the sample. You can pass in the corerlator to use, in the same way described in
the parent README under 'Using Docker Hub'

    > docker build -t sample .

Create the service in detatched mode, passing in the secret.

    > docker service create --name sample --secret=secret.properties -d sample

Inspect the logs to see if the correlator name was successfully read.

    > docker service logs sample


Running with Kubernetes
==============

Prior to running the sample in Kubernetes you must create the Kubernetes secret
in the system. 

    > kubectl create -f kubernetes-secret.yml

This sample can be run with Kubernetes following the steps in the README in the
parent directory. 
