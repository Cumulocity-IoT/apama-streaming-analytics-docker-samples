# Copyright (c) 2015-2017 Software AG, Darmstadt, Germany and/or Software AG USA Inc., Reston, VA, USA, and/or its subsidiaries and/or its affiliates and/or their licensors.
# Use, reproduction, transfer, publication or disclosure is prohibited except as specifically provided for in your License Agreement with Software AG

MemoryStore
===========
Earlier samples showed how to use Dockerfiles to create derived images that
give Apama components from the base image access to configuration and EPL
code. However, this approach only suffices for static data that can be
reproduced by copying in files from a canonical source. For containers to share
dynamic data, they need a live view on that data. This is provided by a Docker
feature called "volumes", which allows containers to share parts of their
filesystem with each other.

This sample contains a toy application 'MemoryStoreCounter.mon' that makes use
of the MemoryStore to lay down persistent state on disk in the form of a
number that increments each time the monitor is loaded. While the correlator
container is the one reading and writing to the MemoryStore, the persistent
file is on a Docker volume which is persisted between container restarts.

The created volume can be viewed using:-

    > docker volumes ls

What Docker volumes gives you is the ability to manage data that has a
different lifecycle to the container that uses it. In an application like
this, you can replace the other containers with equivalents that are based on
a newer version of Apama. After bringing them up again, the correlator will
still have access to the MemoryStore data that it wrote in a previous
iteration, as this data is owned by the volume.

Detailed instructions on running the sample either as a Compose-based
application or via Kubernetes can be found in the README in the parent
directory. If you are running via Kubernetes this sample creates the following
resources which can be accessed via logs and must be deleted via delete:

	* pod memstore-sample

After bringing this application up for the first time, inspect the log of the
correlator container and note that it has read a value from out of the
MemoryStore, and then written another back:

    Counter [1] Counter table contains value: 0
    Counter [1] Incremented counter

Now to simulate an upgrade. Bring down the application and remove all the
containers:

    > docker-compose stop
    > docker-compose rm correlator

At this point you would be able to change the container description in
'docker-compose.yml' to use a different Apama image. Now bring the application
back up using Compose:

    > docker-compose up

Compose will recreate the 'correlator' container, launching it as before. This
time if you inspect the correlator logs, you will see that the state of the
MemoryStore file on the volume is persistent:

    Counter [1] Counter table contains value: 1
    Counter [1] Incremented counter

If you repeat this recipe, you will see this value keep going up.


As a further exercise, you might want to try adapting this example for
correlator persistence, using a volume for the recovery datastore
instead of just the MemoryStore. Be aware that correlator persistence only
functions in a licensed correlator.
