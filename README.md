# Apama samples for Docker and Kubernetes

## License
Copyright (c) 2017-present Cumulocity GmbH, Duesseldorf, Germany and/or its affiliates and/or their licensors.  
The name Cumulocity GmbH and all Cumulocity GmbH product names are either trademarks or registered trademarks of Cumulocity GmbH and/or its subsidiaries and/or its affiliates and/or their licensors. Other company and product names mentioned herein may be trademarks of their respective owners. 

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this
file except in compliance with the License. You may obtain a copy of the License at
http://www.apache.org/licenses/LICENSE-2.0
Unless required by applicable law or agreed to in writing, software distributed under the
License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND,
either express or implied. 
See the License for the specific language governing permissions and limitations under the License.

## Image list

Cumulocity GmbH produces several different variations of the Apama product as Docker images, depending on your use case. Please consult this table for the various images and their uses:

| Image                                                                                               | Use                                                                                                                         |
|-----------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------|
| [public.ecr.aws/apama/apama-correlator](https://gallery.ecr.aws/apama/apama-correlator)                 | Base image for custom Cumulocity microservices, including the correlator, support for Java, Python, and connectivity to Cumulocity.	|
| [public.ecr.aws/apama/apama-correlator-minimal](https://gallery.ecr.aws/apama/apama-correlator-minimal) | Smallest image for the correlator, aimed at pure-Apama use cases. Doesn't contain Java or Python support, or connectivity to other Cumulocity GmbH products. |
| [public.ecr.aws/apama/apama-builder](https://gallery.ecr.aws/apama/apama-builder)                       | Project build and test tools for deploying and testing projects to be used with the "apama-correlator" and "apama-minimal" images in a multi-stage Docker build. |

## Running a Docker image
You can turn an image into a running container with the 'docker run'
command. But before you do, there are a number of configuration options you
will need to consider.

### Exposed ports:
As a virtualization platform, Docker runs each container in its own
isolated network stack. Any exposure to the host operating system's
network stack, or to that of other containers, must be explicit.
Via the EXPOSE element of the Dockerfile, the image designates the ports
that it may be listening on. In this case, these are the default ports of
the correlator.

The simplest way to make use of this is via the '-p' option which maps a
listening port in the container to a listening port on the host networking
stack. For an Apama image, you will probably want to map port 15903, the
default listening port of the correlator.

The more usual/idiomatic way is to link containers directly together,
entirely ignoring the host operating system, but this is covered in later
samples.

Ultimately, turning an image into a running container will look
something like this:

> docker run -d -p 15903:15903 --name apama_in_docker public.ecr.aws/apama/apama-correlator:27

You can then look for your running container:

> docker ps

|CONTAINER ID      |IMAGE           |COMMAND             |CREATED             |STATUS       |PORTS      |NAMES|
|---               |---             |---                 |---                 |---          |---        |---          |
|41d25137fbd0      |public.ecr.aws/apama/apama-correlator:27    |"correlator -j"     |2 seconds ago       |Up Less than a second |0.0.0.0:15903->15903/tcp   |apama_in_docker  |


## Interacting with the container
Now you will be able to interact with the correlator running inside the
container. Because ports from the container have been mapped to the host
operating system's networking stack, you can run Apama commands outside of
Docker to do this. A Docker-based application should not normally rely on
processes in the host operating system, but this approach is sufficient for
the current example.

From a shell in this directory, source Apama/bin/apama_env then run:

> engine_inject -p 15903 -n localhost applications/Simple/HelloWorld.mon

Some simple EPL will then be executed inside your containerized correlator.
Note the use of 'localhost' and '15903' to connect to the mapped port. You can
see the evidence with another Apama command, similarly invoked:

> engine_inspect -p 15903 -n localhost 

This will show you the existing 'HelloWorld' monitor.

### Interacting with the container from another container

Apama commands can also be run from an Apama container. To begin you need to create a common network in docker that the containers will share:

```
docker network create my-network
```

Now, start up the correlator, either using the option above or the following simple option without a port exposed.

```
docker run -d --net my-network --name correlator_container public.ecr.aws/apama/apama-correlator:27
```

An optional stage which will prove that the network is working as expected is to fire up a busybox container and ping the apama container by name:

```
docker run -it --name mybusybox --net my-network --rm busybox
# ping correlator_container
PING correlator_container (192.168.48.2): 56 data bytes
64 bytes from 192.168.48.2: seq=0 ttl=64 time=0.145 ms
64 bytes from 192.168.48.2: seq=1 ttl=64 time=0.105 ms
...
```

Then the sample file can be passed to the Apama instance by using a local volume. Update <YourPath> to the local path where this repo resides. For example:

```
docker run --rm -t -i -v /<YourPath>/apama-streaming-analytics-docker-samples/applications/Simple/HelloWorld.mon:/apama_work/HelloWorld.mon --net my-network public.ecr.aws/apama/apama-correlator:27 engine_inject /apama_work/HelloWorld.mon -n correlator_container
```
Finally, the status of the Correlator can be checked as well:

```
docker run --rm -t -i --net my-network public.ecr.aws/apama/apama-correlator:27 engine_inspect -n correlator_container
```

Or if you wish to watch it:

```
docker run --rm -t -i --net my-network public.ecr.aws/apama/apama-correlator:27 engine_watch -n correlator_container
```

## Log files
Docker treats anything emitted on the console by a contained process as
logging, and stores it appropriately. All of the examples and samples in this
package ensure that Apama components log to the console. Try:

> docker logs apama_in_docker

to see the log message that announced the injection of the .mon file from the
above example.

## Generating Deployment Files
Some of these Docker samples use an 'initialization.yaml' configuration
file when starting the correlator process to allow the correlator to initialize
itself. To generate the 'initialization.yaml' file, the
'engine_deploy' tool is executed on a project directory. See the 
documentation for details, but an example using the 'Weather' sample can be run
after sourcing `bin/apama_env`:

> engine_deploy ./apama-samples/studio/demos/Weather --outputDeployDir ./apama-samples/docker/applications/Weather

## Next steps
There are multiple samples provided that teach you how to use Docker features to run and manage entire multi-process applications
from inside Docker.

Please see the supplied samples which can be found under 'applications/'. You
should start with the top level [README](applications/README.md) found directly under this folder. 

Each sample application also then has an individual README:

|Readme|
|---|
|[Simple](applications/Simple/README.md)|
|[MemoryStore](applications/MemoryStore/README.md)|
|[Secrets](applications/Secrets/README.md)|

______________________
These tools are provided as-is and without warranty or support. They do not constitute part of the Cumulocity GmbH product suite. Users are free to use, fork and modify them, subject to the license agreement. While Cumulocity GmbH welcomes contributions, we cannot guarantee to include every contribution in the main project.

Ask the community at https://techcommunity.cumulocity.com/tag/streaming-analytics-apama if you have any questions.
