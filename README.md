# Apama samples for Docker and Kubernetes

## License
Copyright (c) 2017-2018 Software AG, Darmstadt, Germany and/or its licensors

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this
file except in compliance with the License. You may obtain a copy of the License at
http://www.apache.org/licenses/LICENSE-2.0
Unless required by applicable law or agreed to in writing, software distributed under the
License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND,
either express or implied. 
See the License for the specific language governing permissions and limitations under the License.


## Apama packaging kit for Docker
This package contains configuration and samples to help you containerize and
run Apama components and applications on the Docker platform.

See the Supported Platforms document for information about recommended Docker
versions and support. This is available from the following web page:
http://documentation.softwareag.com/apama/index.htm. 

Docker and the Docker logo are trademarks or registered trademarks of Docker,
Inc. in the United States and/or other countries. Docker, Inc. and other
parties may also have trademark rights in other terms used herein.


## Prerequisites
This package makes the following assumptions:

    You have some familiarity with Docker and Kubernetes technology.

    This package is not supported with the Apama core installation, however the 
    application samples will work with just this package and the pre-built 
    correlator image available on Docker Store. 
    
    * The Pre-built image is available from:
    https://store.docker.com/images/apama-correlator
    
    * To build your own image Apama will need to be installed on a Linux machine 
    using either the commercial or the full community installation.

    Docker 17.12.0-ce or later is installed and its daemon is running
    (https://docs.docker.com/installation/#installation).

    Docker Compose 1.16.1 or later is installed
    (https://docs.docker.com/compose/install/).
    
    Kubernetes 1.8.0 or later is installed and the server is running
    https://kubernetes.io/docs/setup/

    You are logged in as root. For security reasons, only the root user is
    allowed to interact with the Docker daemon. It is easy to find out how to
    rectify this by searching online. One option would be to change the
    permissions on Docker's socket ('/var/run/docker.sock') so that regular
    users can control Docker. We don't recommended this on production systems.
    Any user that can access the Docker daemon can escalate to root.

Note: We have tested this package with the Docker daemon hosted on CentOS 
Linux release 7.4


## Building a Docker image
The file 'image/Dockerfile' is a Dockerfile that can be used to turn your
Apama installation into a Docker image, which can then be used to run Apama
executables within containers e.g. the correlator, IAF, dashboard server,
engine_* tools etc.

The Docker build instruction should be run from the root of your Software AG
installation, for example '/opt/softwareag/'. From that directory, Docker can 
be asked to build the image using instructions from the Dockerfile:

    > docker build --tag apama --file ./Apama/samples/docker/image/Dockerfile .

Docker will output its progress, and after a minute or so will exit with a
message like:

    Successfully built 739214af8fb3

The instructions in the Dockerfile create an image containing the minimal
contents from your installation that are necessary to run Apama executables.
The image is also set up to allow trouble-free execution; suitable environment
variables and defaults are set. You can see this in more detail by reading the
Dockerfile and the embedded commentary.

You can see that an image has been created using this command:

    > docker images 

|REPOSITORY        |TAG             |IMAGE ID            |CREATED             |VIRTUAL SIZE |
|---               |---             |---                 |---                 |---          |
|apama             |latest          |bd738c940cc9        |39 seconds ago      |806.4 MB     |

At this point it is just an image, the Docker equivalent of a VM template,
not a running process. 


## Running a Docker image
You can turn your new image into a running container with the 'docker run'
command. But before you do, there are a number of configuration options you
will need to consider.

Default executable:
    By default, the image will execute a Java-enabled correlator.
    This is configured in the CMD element of the Dockerfile. If you want to
    override for an existing image, the last parameter to 'docker run' should
    be a command such as 'iaf' followed by some options to the IAF.

    You might prefer to edit the CMD element to a different default, and
    rebuild the image.

Exposed ports:
    As a virtualization platform, Docker runs each container in its own
    isolated network stack. Any exposure to the host operating system's
    network stack, or to that of other containers, must be explicit.
    Via the EXPOSE element of the Dockerfile, the image designates the ports
    that it may be listening on. In this case, these are the default ports of
    every Apama component.  As above, you might ultimately wish to change this
    and rebuild.

    The simplest way to make use of this is via the '-p' option which maps a
    listening port in the container to a listening port on the host networking
    stack. For this image, you will probably want to map port 15903, the
    default listening port of the correlator.

    The more usual/idiomatic way is to link containers directly together,
    entirely ignoring the host operating system, but this is covered in later
    samples.

Ultimately, turning your new image into a running container will look
something like this:

    > docker run -d -p 15903:15903 --name apama_in_docker apama

You can then look for your running container:

    > docker ps

|CONTAINER ID      |IMAGE           |COMMAND             |CREATED             |STATUS       |PORTS      |NAMES|
|---               |---             |---                 |---                 |---          |---        |---          |
|41d25137fbd0      |apama:latest    |"correlator -j"     |2 seconds ago       |Up Less than a second |16903/tcp, 28888/tcp, 28889/tcp, 3278/tcp, 3279/tcp, 0.0.0.0:15903->15903/tcp   |apama_in_docker  |


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


## Log files
Docker treats anything emitted on the console by a contained process as
logging, and stores it appropriately. All of the examples and samples in this
package ensure that Apama components log to the console. Try:

    > docker logs apama_in_docker

to see the log message that announced the injection of the .mon file from the
above example.
 

## Licensing
The image you have just created will be able to run the correlator, but the
correlator will run unlicensed and restricted. There is a Dockerfile 
'image/Dockerfile.license' that uses the existing Apama image to create
another Apama image that has a license in the correct location. To make use of
it, change directory to where the license file called 'ApamaServerLicense.xml'
is located. You can then build a new image, and replace the existing Apama
image by using the same tag as before:

    > docker build --tag apama --file ./Apama/samples/docker/image/Dockerfile.license .

If you run and inspect logs from this image, you will notice that the
correlator is no longer complaining about the lack of a license.


## Image management
By default, the images you create will reside on the Docker system on your
local machine. This is sufficient for these examples and samples. However, to
take full advantage of Docker in a production environment you may wish to set
up your own private registry (https://docs.docker.com/registry/deploying/).

A registry is a central store of images, and any number of Docker instances
across many different machines can use it. Docker can use the registry to
obtain images by name, rather than requiring them to be made available
locally. For example

    > docker run internal-docker.example.com:5000/apama

will pull 'apama' from your registry and run it, just as if it was built
locally. If you plan to be running your containerized Apama application across
multiple different machines, a private registry is the way to go to avoid
needless extra setup and concerns about application configuration diverging
between machines.

There is also a public registry known as the Docker Hub, which Docker will
implicitly use. The Apama Dockerfile makes use of this to obtain a CentOS base
image to run on. It is possible to create an account on the Docker Hub for the
purpose of sharing your images with other Docker users, as the CentOS project
chooses to do. Needless to say, you should not be uploading any Apama-related
images to the Docker Hub or any other public registry.

## Generating Deployment Files
Some of these Docker samples use an 'initialization.yaml' configuration
file when starting the correlator process to allow the correlator to initialize
itself. To generate the 'initialization.yaml' file, the
'engine_deploy' tool is execute on a Designer project or directory. See the 
documentation for details, but an example using the 'Weather' sample can be run
from the Apama Command prompt of your Software AG installation:

    > engine_deploy ./apama-samples/studio/demos/Weather --outputDeployDir ./apama-samples/docker/applications/Weather

## Next steps
There are multiple samples provided that teach you how to use Docker Compose
and other Docker features to run and manage entire multi-process applications
from inside Docker.

Please see the supplied samples which can be found under 'applications/'. You
should start with the top level README found directly under this folder. Each
sample application also then has an individual README.
