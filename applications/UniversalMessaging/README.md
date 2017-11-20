Use UM for communication between two Apama correlators
======================================================
This sample demonstrates two Apama correlators communicating with each other
via a UM realm server, all in separate containers. It uses Docker Compose,
configured via 'docker-compose.yml' in this directory.

This sample requires an installation of Universal Messaging.

This sample assumes that you have already created the base Universal
Messaging image from the Dockerfile available in the Universal Messaging
installation, with the name 'um'. Further, that you have already created the
Apama image from the Docker sample in the Apama installation, and tagged it as
'apama:latest'.

Before running the sample you need to create an Apama image which contains the
required UM client libraries for Apama/UM integration. Copy
'Dockerfile.apama_um_enabled' to the root directory of your Universal Messaging
installation (i.e. '/opt/SoftwareAG/UniversalMessaging').

    > docker build -f Dockerfile.apama_um_enabled -t apama_um_enabled .

Docker will then build a new Apama image 'apama_um_enabled', with the required
UM libraries. If you are using the official image from Docker Store rather
than the enclosed packaging kit then you can skip this step.

Detailed instructions on running the sample either as a Compose-based
application or via Kubernetes can be found in the README in the parent
directory.

Docker compose
--------------

By default, this sample will bring up all three containers on the same host,
connected using 2 networks ('sender' and 'receiver') with the sender and
receiver containers connected to there relevant networks, and the UM container
connected to both networks.

Use docker-compose to run the sample:

    > docker-compose up -d

Kubernetes
----------

If you are running via Kubernetes this sample creates the following
resources which can be accessed via logs and must be deleted via delete:

	* pod bus
	* pod sender
	* pod receiver
	* service umhost

You must also build two images, push them to a repository and substitute in the image names:

	> docker build -t um-image -f Dockerfile.um .
	> docker build -t correlator-image -f Dockerfile.apama_app

Output
------

After bringing the sample up, you can use 'docker-compose logs' or 'kubectl
logs receiver' to examine the logs from the 'apamareceiver' container. It will
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

