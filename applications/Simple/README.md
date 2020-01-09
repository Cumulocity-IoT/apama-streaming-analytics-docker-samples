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

Detailed instructions on running the sample either as a Compose-based
application or via Kubernetes can be found in the README in the parent
directory. If you are running via Kubernetes this sample creates the following
resources which can be accessed via logs and must be deleted via delete:

	* pod simple-sample

After bringing this sample up, you should notice the log from the correlator
container telling you "Hello world!".
