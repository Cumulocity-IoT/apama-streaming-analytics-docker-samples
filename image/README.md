## License
Copyright (c) 2017-2022, 2024 Cumulocity GmbH, Duesseldorf, Germany and/or its affiliates and/or their licensors.  

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this
file except in compliance with the License. You may obtain a copy of the License at
http://www.apache.org/licenses/LICENSE-2.0
Unless required by applicable law or agreed to in writing, software distributed under the
License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND,
either express or implied. 
See the License for the specific language governing permissions and limitations under the License.


## Building a Docker image
While it is recommended to use one of our published images listed in top level README, 
you may want to build a custom image.
For example to use IAF or dashboard server which are availble in a full installation of Apama.

The file 'Dockerfile' is a Dockerfile that can be used to turn your
Apama installation into a Docker image, which can then be used to run Apama
executables within containers e.g. the correlator, IAF, dashboard server,
engine_* tools etc.

The Docker build instruction should be run from the root of your 
installation, for example '/opt/cumulocity/'. From that directory, Docker can 
be asked to build the image using instructions from the Dockerfile:

> docker build --tag apama --file ./Apama/samples/docker/image/Dockerfile .

Docker will output its progress, and after a minute or so will exit with a success code,
and no errors logged.

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

## Licensing
When using an Apama image, whether the published list of images or the custom built above,
by default the correlator will run unlicensed and restricted. 
There are various ways to add a license to an Apama image, for full details please refer
to the Apama documentation around licensing Apama in Docker.
One such way is shown here using the Dockerfile 
'Dockerfile.license' that uses an existing Apama image to create
another Apama image that has a license in the correct location. To make use of
it, change directory to where the license file called 'ApamaServerLicense.xml'
is located. You can then build a new image, and replace the existing Apama
image by using the same tag as before:

> docker build --tag apama --file ./Apama/samples/docker/image/Dockerfile.license .

If you run and inspect logs from this image, you will notice that the
correlator is no longer complaining about the lack of a license.
