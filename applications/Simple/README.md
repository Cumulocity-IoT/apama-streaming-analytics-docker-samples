License
=======
Copyright (c) 2017 Software AG, Darmstadt, Germany and/or its licensors

Licensed under the Apache License, Version 2.0 (the "License"); you may not use this
file except in compliance with the License. You may obtain a copy of the License at
http://www.apache.org/licenses/LICENSE-2.0
Unless required by applicable law or agreed to in writing, software distributed under the
License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND,
either express or implied. 
See the License for the specific language governing permissions and limitations under the License.


Simple
======
This sample is a very simple application, deploying a trivial EPL program into
a running correlator. A container runs the correlator and then runs the client
tools to deploys the program.

 If you are running via Kubernetes this sample creates the following
resources which can be accessed via logs and must be deleted via delete:

	* pod simple-sample

Running with Docker-Compose
==============

Get the Apama Correlator container [here](https://hub.docker.com/_/apama-correlator). More information about building your own image is contained in the README in the parent directory of this sample.

To run with Docker Compose, execute docker-compose from this directory

```
docker-compose up
```

After bringing this sample up, you should notice the log from the correlator
container telling you "Hello world!".

Sample Expected Output:
```
application_1  | 2019-06-20 15:31:50.357 INFO  [140501802022656] - Added monitor HelloWorld
application_1  | 2019-06-20 15:31:50.357 INFO  [140501802022656] - Injected MonitorScript from file /apama_work/Simple/HelloWorld.mon (227ec20012df1a8d7263f9ee56b78cb9), size 770 bytes, compile time 0.00 seconds
application_1  | 2019-06-20 15:31:51.405 INFO  [140501751678720] - HelloWorld [1] Hello world! The current epoch is 1561044711.354903
```

Troubleshooting Docker-Compose
=======

Please make sure that the contents of .docker/config.json are correct and pointing to the right docker host or you may have problems building. 
