# Secrets Sample

## COPYRIGHT NOTICE

Copyright (c) 2018 Software AG, Darmstadt, Germany and/or its licensors

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
used to securely provide credentials to the system.

Running with Docker
==============

Detailed instructions on running the sample as a Compose-based application
can be found in the README in the parent directory.

Running with Kubernetes
==============

Prior to running the sample in Kubernetes you must create the Kubernetes secret
in the system. 

    > kubectl create -f kubernetes-secret.yaml

This sample can be run with Kubernetes following the steps in the README in the
parent directory. 
