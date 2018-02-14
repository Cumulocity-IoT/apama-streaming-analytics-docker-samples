# Queries Sample

## DESCRIPTION
    
    This sample contains a queries application which will be ran inside a 
    Docker container. Instances of the queries will be created 
    and when instantiated certain events will trigger reactions from the 
    application which can be viewed outside of Docker or Kubernetes
            
    The query identifies sensor readings that are outside a
    specified range. In this example the input events are the
    resul of screening members of the public for high temperatures
    and the query sends an alert when an individual who has a
    temperature has been identified.

     
## COPYRIGHT NOTICE
    Copyright (c) 2017,2018 Software AG, Darmstadt, Germany and/or its licensors

    Licensed under the Apache License, Version 2.0 (the "License"); you may not use this 
    file except in compliance with the License. You may obtain a copy of the License at
    http://www.apache.org/licenses/LICENSE-2.0
    Unless required by applicable law or agreed to in writing, software distributed under the
    License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, 
    either express or implied. 
    See the License for the specific language governing permissions and limitations under the License.
    
    
## FILES
| file                      |description                                                         |
|---------------------------|--------------------------------------------------------------------|
|README.txt                 |This file                                                           |
|Dockerfile                 |Docker definition for the correlator container running the query    |
|docker-compose.yml         |File that defines how docker swarm should start the Terracotta store|
|docker-compose-corr.yml    |File that defines how docker swarm should run the correlators       |
|DetectUnusualReadings      |The query definitions and configuration                             |
|CreateInstance.evt         |File that creates an instance of the query                          |
|Input.evt                  |The events that trigger the query behaviour.                        |
|tcstore.yml                |Kubernetes configuration defining how to run Terracotta             |
|stateful.yml               |File defining how to run the correlators                            |

        
## RUNNING THE SAMPLE

N.B. the following environment variables can be used in the sample :

| Environment variable  |description                                       |
|-----------------------|--------------------------------------------------|
|TC_IMAGE               |The base Terracotta image used                    |
|CLUSTER_TOOL_IMAGE     |The Terracotta cluster-tool image                 |
|APAMA_IMAGE            |The base Apama image used to build the application|
|QUERIES_IMAGE          |The built sample apama correlator image           |


### DOCKER:
To run the sample on Linux:
    
1. Open a command prompt and type 
    
    engine_deploy --outputDeployDir ./Queries_deployed DetectUnusualReadings/config/launch/DetectUnusualReadings.deploy
    
2. Build the image for the Correlator

    docker build -f Dockerfile --tag queries-image --build-arg 'APAMA_IMAGE=apama-image' --build-arg 'LICENSE_SOURCE=<path_to_apama_licence>' .
    
3. Now run the following command to start the Terracotta store and initialise it
    
    TC_IMAGE=tc-image CLUSTER_TOOL_IMAGE=cluster-tool-image LICENSE_URL=<url_to_license> docker stack deploy -c docker-compose.yml sample-tc

4. Once the containers are running for Terracotta and there are no errors start the correlators 
 
    QUERIES_IMAGE=queries-image EXTERNAL_NETWORK_NAME=sample-tc docker stack deploy -c docker-compose-corr.yml sample-corr

5. In another command prompt from you installation directory, set up an engine_receive to recieve any events sent from the application (defaults to port 15903)

    ./engine_receive
           
6. From you installation directory send in events that will trigger a reaction from the application within the docker container

    ./engine_send Input.evt
           
9. Switch back to the prompt where you set up engine_receive You should see the following output:

    apamax.querysamples.detectunusualreadings.SensorThresholdAlert
        

### KUBERNETES

1. Open a command prompt and type 
    
    engine_deploy --outputDeployDir ./Queries_deployed DetectUnusualReadings/config/launch/DetectUnusualReadings.deploy
    
2. Build the image for the Correlator. 

    __Note__ that the image needs to be pushed to a repository so "queries-image" should be in the form **your-repository/queries-image:tag**

    docker build -f Dockerfile --tag queries-image --build-arg 'APAMA_IMAGE=apama-image' .

3. Build the image for the Sender. 

    __Note__ that the image needs to be pushed to a repository so "queries-image" should be in the form **your-repository/queries-image:tag**

    docker build -f Dockerfile --tag sender-image --build-arg 'APAMA_IMAGE=apama-image' .

4. The images built needs to be put into a repository for kubernetes to use. 

    docker push queries-image
    docker push sender-image


5. Next get kubernetes to run and set up the Terracotta store 

    kubectl create -f tcstore.yml

6. Once Terracotta is up and error free Run and set up the Correlators

    kubectl create -f stateful.yml

5. Now we can start the receiver and monitor the logs

    kubectl create -f reciever.yml

6. from another terminal watch the logs of the receiver 

    kubectl logs -f qry-receiver

7. Back in the original terminal, now we start the final pod the sender

    kubectl create -f sender.yml

8. The terminal in which you are watching the receiver logs should show an output containing 

    apamax.querysamples.detectunusualreadings.SensorThresholdAlert




