License
=======
Copyright (c) 2017-2019 Software AG, Darmstadt, Germany and/or its licensors

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this
file except in compliance with the License. You may obtain a copy of the License at
http://www.apache.org/licenses/LICENSE-2.0
Unless required by applicable law or agreed to in writing, software distributed under the
License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND,
either express or implied. 
See the License for the specific language governing permissions and limitations under the License.


Use UM for communication between two Apama correlators
======================================================
This sample demonstrates two Apama correlators communicating with each other
via a UM realm server, all in separate containers. It uses Docker Stack,
configured via 'docker-compose.yml' in this directory.


## BEFORE YOU START 

The Sample requires a base image for Apama and Universal Messaging. You have
several options for Apama and these are detailed below.

To ensure that the environment is configured correctly for Apama, all the 
commands below should be executed from a shell where the Apama/bin/apama_env 
script has been sourced.

### Apama Base Images
| Image                     |description                                                         |
|---------------------------|--------------------------------------------------------------------|
|Docker Store               | https://hub.docker.com/_/apama-correlator                   |
|Existing Install           | use the docker packaging kit instructions to build a base image    |

If you are using the docker packaging kit from an existing install you will
need to create an Apama image which contains the required UM client libraries
for Apama/UM integration. Copy 'Dockerfile.apama_um_enabled' to the root
directory of your Universal Messaging installation (i.e.
'/opt/SoftwareAG/UniversalMessaging').

    > docker build -f Dockerfile.apama_um_enabled -t apama_um_enabled .

Docker will then build a new Apama image 'apama_um_enabled', with the required
UM libraries. If you are using the official image from Docker Store rather
than the enclosed packaging kit then you can skip this step.

The Universal Messaging images can be found below 

### Universal Messaging Base Images
| Image                     |description                                                         |
|---------------------------|--------------------------------------------------------------------|
|universalmessaging-server  | Universal Messaging repo on https://hub.docker.com               |
|cluster-tool               | Universal Messaging repo on https://hub.docker.com               |

See Universal Messaging documentation for detailed use of the server and tools within.

## FILES
| file                       |description                                                         |
|----------------------------|--------------------------------------------------------------------|
|README.md                   | This file                                                          |
|Dockerfile.apama_um_enabled | Docker definition to add UM libraries to an existing Apama image   |
|Dockerfile.apama_app        | Docker definition for the sender and receiver correlators          |
|docker-compose-um.yml       | File that defines how docker swarm should start the UM server      |
|docker-compose-corr.yml     | File that defines how docker swarm should run the correlators      |
|kubernetes-um.yml           | Kubernetes configuration defining how to run Universal Messaging   |
|kubernetes-corr.yml         | Kubernetes configuration defining how to run the correlators       |
|common.mon                  | Apama application files                                            |
|receiver.mon                | Apama application files                                            |
|sender.mon                  | Apama application files                                            |
|um-connectivity.yaml        | Apama configuration to connect to Universal Messaging              |
|init-receiver.yaml          | Apama configuration to start the receiver correlator               |
|init-sender.yaml            | Apama configuration to start the sender correlator                 |

## RUNNING THE SAMPLE

### DOCKER:
To run the sample on Linux:

1. Build the image for the Correlator, using the base Apama image:

        docker build -f Dockerfile.apama_app --tag correlator-image --build-arg APAMA_IMAGE=<apama-image> .
 
2. Now run the following command to start the Universal Messaging server from the base image
    
        UM_IMAGE=<um-image> docker stack deploy -c docker-compose-um.yml sample-um
   
3. You can confirm that the Universal Messaging server is successfully started by checking the following:
 
        docker service logs sample-um_umsample-acl

4. Once the containers are running for Universal Messaging and there are no errors start the correlators:
 
        CORR_IMAGE=<correlator-image> EXTERNAL_NETWORK_PREFIX=sample-um docker stack deploy -c docker-compose-corr.yml sample-corr

5. View the events being received by the receiver correlator:

        docker service logs sample-corr_apamareceiver

### KUBERNETES

1. Build the image for the Correlator, using the base Apama image:

	 __Note__ that the image needs to be pushed to a repository so
	 "correlator-image" should be in the form
	 **your-repository:correlator-image**

        docker build -f Dockerfile --tag correlator-image --build-arg APAMA_IMAGE=<apama-image> .

2. The image built needs to be pushed into a repository for kubernetes to use:

        docker push **your-repository:correlator-image**

3. Next get kubernetes to run and set up the Universal Messaging server, first
	replace the image name in kubernetes-um.yml with the Universal Messaging
	image you are using, then run:

        kubectl create -f kubernetes-um.yml

4. Once UniversalMessaging has successfully started (kubectl get -f
	kubernetes-um.yml), run and set up the Correlators.  To do this, edit the
   file to replace the image with the correlator-image created:

        kubectl create -f kubernetes-corr.yml

5. View the events being received by the receiving correlator:

        kubectl logs umsample-receiver

Output
------

After bringing the sample up, you can use 'docker service logs' or 'kubectl
logs umsample-receiver' to examine the logs from the 'apamareceiver' container. It will
contain messages similar to 

    'SimpleEvent("The current epoch is 1436179491.8")'

which were sent by the correlator in the 'apamasender' container via the realm
server in the 'um' container.

You can view the networks created using:

    > docker network ls

If you want to try out a more realistic multi-host deployment, you can explore
using Compose against Docker Swarm (https://docs.docker.com/swarm/), which
will automatically distribute the containers across a collection of
Docker-enabled hosts. If you want one or both of the Apama containers to be
deployed on a distinct host to the 'um' container, then comment out the overlay
driver key from the networks section.

