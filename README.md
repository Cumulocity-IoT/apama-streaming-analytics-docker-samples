Docker for Apama applications
=============================
The samples in this repository demonstrate how you can use Docker to deploy and
manage entire applications.

All of this can be accomplished by sequences of Docker commands such as
'build' and 'run', Docker Compose automates some
of this through configuration files. We demonstrate how to use it for
deploying and managing entire applications in the samples provided.

Furthermore, the containers in an Apama application will typically need more
than the base image. The base image provides Apama executables, but these
executables will need access to configuration files or EPL in order to be
actually useful in an application. These samples also contain Dockerfiles that
create derived images on top of the base image, copying in the necessary
files.

These sample applications pulls the base Apama
image (https://store.docker.com/images/apama-correlator) from the docker store.


Docker Compose
==============
Docker Compose is invoked using the 'docker-compose' tool, which is documented
at http://docs.docker.com/compose/. You should already have 'docker-compose'
installed and available on your PATH. These samples drive the tool through the
use of configuration files called 'docker-compose.yml', the format of which is
documented at http://docs.docker.com/compose/yml/ .


Running the samples
===================
The most educational way to go through the sample applications is in this
order:

    Simple/          Injecting EPL into a correlator
    Adapter/         Connecting a correlator and the IAF
    Weather/         Running Ant-exported projects and generating dashboards
    MemoryStore/     Using volumes for persistent state across rebuilds

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

You can then inspect the logs of each container 

    > docker-compose logs

The output will be streamed to your console, colour-coded to distinguish each
container.

When you are done, stop and remove all containers launched by Compose

    > docker-compose stop
    > docker-compose rm

You may wish to experiment with other 'docker' and 'docker-compose' commands
to investigate the containers and images created by Compose.


docker-compose.yml cheat sheet
==============================

'services:'

This describes what services should be created for this Composition and is a
top-level configuation.

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
This allows for seperation of different containers onto different networks.

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


Further work
============
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
