
License
=======

Copyright (c) 2017-2022 Cumulocity GmbH, Duesseldorf, Germany and/or its affiliates and/or their licensors.  

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this
file except in compliance with the License. You may obtain a copy of the License at
http://www.apache.org/licenses/LICENSE-2.0
Unless required by applicable law or agreed to in writing, software distributed under the
License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND,
either express or implied. 
See the License for the specific language governing permissions and limitations under the License.

Using containers for Apama applications
=============================

The samples in this directory demonstrate how you can use Docker or Kubernetes to deploy and
manage entire applications.

An application will typically consist of multiple interacting processes. It is
best-practice in the Docker world to containerize only individual processes, 
and to link them together for network connections and file access.

While all of this can be accomplished by sequences of Docker or Kubernetes commands such as
'build' and 'run', Both Docker and Kubernetes provide methods to automates some
of this through configuration files. We demonstrate how to use it for
deploying and managing entire applications in the samples provided.

Furthermore, the containers in an Apama application will typically need more
than the base image. The base image provides Apama executables, but these
executables will need access to configuration files or EPL in order to be
actually useful in an application. These samples also contain Dockerfiles that
create derived images on top of the base image, copying in the necessary
files.

The most educational way to go through the sample applications is in this
order:

| Sample              | Description |
|---------------------|-------------|
| Simple/             | Injecting EPL into a correlator |
| Adapter/            | Connecting a correlator and the IAF (not available with Apama Core)|
| Weather/            | Running Ant-exported projects and generating dashboards (not available with Apama Core)|
| MemoryStore/        | Using volumes for persistent state across rebuilds|
| UniversalMessaging/ | Using UM for communication between two Apama correlators (not available with Apama Core)|
| Secrets/            | Using Docker and Kubernetes secrets to pass encrypted data to the correlator through config files|

Docker Compose
==============

Docker Compose is invoked using the 'docker-compose' tool, which is documented
at http://docs.docker.com/compose/. You should already have 'docker-compose'
installed and available on your PATH. These samples drive the tool through the
use of configuration files called 'docker-compose.yml', the format of which is
documented at http://docs.docker.com/compose/yml/ .

Docker Stack
==========

Additionally to Docker Compose, docker stack can be used to get levels of scalability and resource
management. Docker stack https://docs.docker.com/engine/reference/commandline/stack/
uses the docker-compose.yml files with added specific entries detailing how to deploy
the containers.

Prior to running docker stack samples the daemon being used should be enabled by an administrator
using docker swarm init. https://docs.docker.com/engine/reference/commandline/swarm_init/

Kubernetes
==========

As an alternative to Docker Compose and stack, each sample also contains Kubernetes
configuration files usually called 'kubernetes.yml'. This format is documented at
https://kubernetes.io/docs/reference/ These can be used to deploy to any Kubernetes cluster.

Running the samples using Docker Compose
========================================

Each sample contains a README with information specific to that sample,
describing what it does and how to interact with the containers that it
creates. The 'docker-compose.yml' configuration file in each sample is also
documented to describe the purpose and rationale of each element.

The 'cheat sheet' section below provides even more detail about the elements
of Compose configuration that we use.

Before running a sample, check the README for any extra prerequisites and
instructions. Each sample can be invoked by navigating into the sample's
directory below this one, and invoking Compose to bring the sample up:

    > docker-compose up -d

Compose will then pull or build the necessary images for each service
described in the 'docker-compose.yml' configuration file. It will then bring
up containers for each of them, configuring and linking them together as
specified.

You can then inspect the logs of each container:

    > docker-compose logs

The output will be streamed to your console, colour-coded to distinguish each
container.

When you are done, stop and remove all containers launched by Compose:

    > docker-compose stop
    > docker-compose rm

You may wish to experiment with other 'docker' and 'docker-compose' commands
to investigate the containers and images created by Compose.

Running the samples with Kubernetes
===================================

All the samples can be worked through with Kubernetes in the same order as
above.

To run the samples using Kubernetes you first have to build the sample images
using docker build in a docker host from the directory of that sample:

	> docker build -t imagename .

Or, if the sample has multiple images:

	> docker build -t image1name dir1
	> docker build -t image2name dir2

Then you must tag and push the images to a registry so that they can be
retrieved by the Kubernetes server:

	> docker tag imagename registry/org/repository:image
	> docker push registry/org/repository:image

Lastly you must modify the provided kubernetes.yml to have the registry tag of
the new image in the place of the various '-image's. Now you are able to create
the deployment in Kubernetes:

	> kubectl create -f kubernetes.yml

The kubernetes.yml will name the pod that you've created. This name will be
used to inspect, stop and delete the pod. Replace 'sample' in the following
commands with the name of the pod for a given sample. 

To inspect the logs from the containers in the pod:

	> kubectl logs sample

To remove the deployment:

	> kubectl delete -f kubernetes.yml

docker-compose.yml cheat sheet
==============================

'services:'

This describes what services should be created for this Composition and is a
top-level configuration.

'image:'

Refers to an already-existing image that should be used to create the
container.

'build:'

Used in place of 'image:', the build value points to a directory to build into
an image, based on a Dockerfile located there. In our samples this is
typically used to build new images containing configuration or EPL specific to
that sample.

'networks:'

This can be used in 2 places.  At the top-level, it describes what networks
Compose should create.  When used in a service, it says which networks the
services container should connect to.  Containers on the same network to
discover and connect to each other using the services name as a hostname.
This allows for separation of different containers onto different networks.

See https://docs.docker.com/compose/networking/

'volumes:'

This can be used in 2 places.  At the top-level, it describes what volumes
Compose should create.  When used in a service, it says which volumes the
services container should mount and use.  A volume will appear in a
container's filesystem at the path specified.  Volumes can be shared between
containers and are persisted between container restarts.  This is used by
the 'MemoryStore' sample for giving the correlator a persisted database.

See https://docs.docker.com/engine/userguide/containers/dockervolumes/

'ports:'

Makes a listening port in the container accessible to the host operating
system, on a potentially different port. e.g. a ports entry like '8080:80'
means that anything in the host operating system connecting to localhost:8080
will get connected to a process listening on port 80 inside the container. Our
samples show that you should only do this for containers that need to be
accessible from the outside world. All networking that is purely
inter-container should remain private.

'depends_on:'

Express dependency between services to ensure start ordering.

Next steps
==========

After exploring these samples, you might wish to create your own Docker-ized
applications, extending the existing samples and/or using the comprehensive
documentation available on Docker's website (http://docs.docker.com/). Here
are some ideas to get started:

- Try exporting one of your own Apama projects using Ant Export and turn it
  into an application in Docker. You should be able to modify the existing
  Weather sample, which is itself using a project that was exported using Ant
  Export.

- Suppose you have a long-running application using a correlator and an IAF.
  You wish to reconfigure the IAF without restarting the correlator and thus
  losing application state. You could accomplish this using the options to the
  'docker-compose' tool that allow you to rebuild and restart individual
  containers in a running Compose environment.

Changes from previous releases
==============================

Additional Secrets Sample
-------------------------------

Demonstrates use of Docker and Kubernetes secrets to pass encrypted data
to the correlator through config files.

Version 3.3 of Compose YAML files
-------------------------------

This gives us access to much more Compose functionality (such as network,
volumes, etc) and Version 1 is deprecated and expected to be removed at
some point.

Deployment Containers
---------------------

We have moved away from using deployment containers (where a short lived
container would inject code into a separate container running the correlator).
We have moved away from using engine_inject on the command line (which also
required using engine_management --waitFor to wait for the correlator to
start).

We now use the --config command line argument to the correlator to specify an
init.yaml configuration file which lists what files (.mon, .evt, .cdp or .jar)
to inject at startup. This is good practice and allows for use with Docker
Swarm and to easily scale distributed applications.

Links
-----

We no longer use container 'links', instead using networking to connect
container together.  'links' are deprecated and do not work on multi-host
Swarms.

Volumes
-------

Volumes are now first class Compose citizens and we no longer need to
create dedicated data volume containers.

Kubernetes configuration
------------------------

Added Kubernetes configuration for each sample.
