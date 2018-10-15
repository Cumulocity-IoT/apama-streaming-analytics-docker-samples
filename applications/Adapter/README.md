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


Adapter
=======
This sample starts an IAF in a container, which connects to a correlator
running in another container. The IAF is running the File transport. An EPL
application is deployed into the correlator, which requests the contents of
a file 'inputFile.txt' from the File adapter, and then directs it to write
those contents back out to another file 'outputFile.txt'.

Detailed instructions on running the sample either as a Compose-based
application or via Kubernetes can be found in the README in the parent
directory. If you are running via Kubernetes this sample creates the following
resources which can be accessed via logs and must be deleted via delete:

* pod adapter-engine
* pod adapter-iaf
* service adapter-correlator

You must also build the following sub-directories and substitute in image names:

* iaf
* deployment

This sample uses an init-container for the IAF to wait for the correlator service 
to become available before starting the IAF.

Before running this sample, note the contents of 'inputFile.txt' in the 'iaf/'
directory, and an empty file 'outputFile.txt' alongside it. Alongside the IAF
configuration, these files will exist inside the 'iaf' container. After
running the sample, 'outputFile.txt' in this container will have identical
contents to 'inputFile.txt'. You can verify this by copying the file out of
the container to inspect it yourself:

    > docker cp adapter_iaf_1:/apama_work/Adapter/outputFile.txt .
    > cat outputFile.txt

The equivalent when running with Kubernetes would be:

    > kubectl cp adapter-iaf:/apama_work/Adapter/outputFile.txt outputFile.txt
    > cat outputFile.txt
