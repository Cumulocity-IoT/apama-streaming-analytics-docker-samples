# Queries Sample

## COPYRIGHT NOTICE

Copyright (c) 2017-2019 Software AG, Darmstadt, Germany and/or its licensors

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this 
file except in compliance with the License. You may obtain a copy of the License at
http://www.apache.org/licenses/LICENSE-2.0
Unless required by applicable law or agreed to in writing, software distributed under the
License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, 
either express or implied. 
See the License for the specific language governing permissions and limitations under the License.


## DESCRIPTION

This sample contains a queries application which will be run inside a 
Docker container. Instances of the queries will be created 
and when instantiated certain events will trigger reactions from the 
application which can be viewed outside of Docker or Kubernetes

The query identifies sensor readings that are outside a
specified range. In this example the input events are the
result of screening members of the public for high temperatures
and the query sends an alert when an individual who has a
temperature has been identified.

## BEFORE YOU START 

The Sample requires a base image for Apama, Terracotta Server and the Terracotta 
cluster-tool. You have several options for Apama and these are detailed below.

To ensure that the environment is configured correctly for Apama, all the 
commands below should be executed from a shell where the Apama/bin/apama_env 
script has been sourced.

### Apama Base Images
| Image                     |description                                                         |
|---------------------------|--------------------------------------------------------------------|
|Docker Store               | https://hub.docker.com/_/apama-correlator                   |
|Existing Install           | use the docker packaging kit instructions to build a base image    |

The Terracotta images can be found below 

### Terracotta Base Images
| Image                     |description                                                         |
|---------------------------|--------------------------------------------------------------------|
|Terracotta-server          | Terracotta repo on https://hub.docker.com                        |
|cluster-tool               | Terracotta repo on https://hub.docker.com                        |

A license for Terracotta is required for the sample to run, this is expected as a URL in this 
sample for simplicity. The actual URL will either be a valid license from your install or 

http://www.terracotta.org/retriever.php?n=Terracotta105linux.xml

for a trial license. 

See Terracotta documentation for detailed use of the server and cluster-tool.

Note that it is possible to supply configuration via Docker config and Kubernetes ConfigMap,
but this is beyond the scope of this sample please see the terracotta documentation for details.


## FILES
| file                      |description                                                         |
|---------------------------|--------------------------------------------------------------------|
|README.md                  |This file                                                           |
|Dockerfile.apama_sag       |Docker definition to add common libraries to an existing Apama image|
|Dockerfile                 |Docker definition for the correlator container running the query    |
|Dockerfile.sender          |Docker definition for the correlator  sending events                |
|docker-compose.yml         |File that defines how docker swarm should start the Terracotta store|
|docker-compose-corr.yml    |File that defines how docker swarm should run the correlators       |
|DetectUnusualReadings      |The query definitions and configuration                             |
|Input.evt                  |The events that trigger the query behaviour                         |
|tcstore.yml                |Kubernetes configuration defining how to run Terracotta             |
|stateful.yml               |Kubernetes File defining how to run the correlators                 |
|sender.yml                 |Kubernetes configuration for the correlator that sends events       |
|receiver.yml               |Configuration file for that starts the monitoring correlator        |


## RUNNING THE SAMPLE

### DOCKER:
To run the sample on Linux:

1. Build the image for the Correlator, using the base Apama image:

        docker build -f Dockerfile --tag queries-image --build-arg APAMA_IMAGE=<apama-image> .
 
2. Now run the following command to start the Terracotta store and initialise it, using the base Terracotta image and the Terracotta cluster-tool image:
    
        TC_IMAGE=<tc-image> CLUSTER_TOOL_IMAGE=<cluster-tool-image> LICENSE_URL=<url_terracotta_license> docker stack deploy -c docker-compose.yml sample-tc
   
3. You can confirm that the Terracotta containers are successfully started by checking the following:
 
        docker service logs sample-tc_cluster-tool 

4. Once the containers are running for Terracotta and there are no errors start the correlators:
 
        QUERIES_IMAGE=<queries-image> EXTERNAL_NETWORK_PREFIX=sample-tc docker stack deploy -c docker-compose-corr.yml sample-corr

5. In another command prompt from your installation directory, set up an engine_receive to receive any events sent from the application, as follows:

    run "docker service ps sample-corr_qry-correlators" to get the containers started, you should see something similar to 
    
    |ID          |NAME                         |IMAGE        |NODE| DESIRED STATE|CURRENT STATE         | ERROR| PORTS|
    |---         |---                          |---          |--- |---           |---                   |---   |---   |
    |yg51id8c3o06|sample-corr_qry-correlators.1|queries-image|host|Running       |Running 46 seconds ago|      |*:33039->15903/tcp|
    |p5vsx7od7o43|sample-corr_qry-correlators.2|queries-image|host|Running       |Running 46 seconds ago|      |*:33038->15903/tcp|

    choose one of the running containers, look for the PORTS section and use the first number (external port) and 
    the value of NODE (the host the container is running on) use this in the engine receive command you run, noting 
    the details for the next step.

    engine_receive -n host -p 33039 -c apamax.querysamples.screeningalerts

6. Next send in events that will trigger a reaction from the application within the docker container:

        engine_send Input.evt -n host -p 33039

7. Switch back to the prompt where you set up engine_receive. You should see an alert of the following event type, 
amongst other output:

    apamax.querysamples.detectunusualreadings.SensorThresholdAlert


### KUBERNETES

1. Build the image for the Correlator, using the base Apama image:

    __Note__ that the image needs to be pushed to a repository so "queries-image" should be in the form **your-repository:queries-image**

        docker build -f Dockerfile --tag queries-image --build-arg APAMA_IMAGE=<apama-image> .

2. Build the image for the Sender:

    __Note__ that the image needs to be pushed to a repository so "sender-image" should be in the form **your-repository:sender-image**

        docker build -f Dockerfile.sender --tag sender-image --build-arg APAMA_IMAGE=<apama-image> .

3. The images built need to be pushed into a repository for kubernetes to use:

        docker push **your-repository:queries-image**
        docker push **your-repository:sender-image**

4. Next get kubernetes to run and set up the Terracotta store, first replace the image with those created above for Terracotta:

        kubectl create -f tcstore.yml

5. Once Terracotta has successfully started (kubectl get -f tcstore.yml), run and set up the Correlators.
    To do this, edit the file to replace the image with the queries-image created:

        kubectl create -f stateful.yml

6. To start the receiver first edit the file to replace the image with the apama-image, 
    then run:
        kubectl create -f receiver.yml

7. From another terminal watch the logs of the receiver:

        kubectl logs -f qry-receiver

8. Back in the original terminal, start the final pod invoking the sender.
    Edit the file to replace the image with the sender-image created, then run:

        kubectl create -f sender.yml

9. The terminal in which you are watching the receiver logs should show an output event of type:

    apamax.querysamples.detectunusualreadings.SensorThresholdAlert




