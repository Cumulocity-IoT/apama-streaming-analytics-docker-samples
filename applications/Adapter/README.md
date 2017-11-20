# Copyright (c) 2015-2017 Software AG, Darmstadt, Germany and/or Software AG USA Inc., Reston, VA, USA, and/or its subsidiaries and/or its affiliates and/or their licensors.
# Use, reproduction, transfer, publication or disclosure is prohibited except as specifically provided for in your License Agreement with Software AG

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

	* pod engine
	* pod iaf
	* service correlator

You must also build the following sub-directories and substitute in image names:

	* iaf
	* deployment

Before running this sample, note the contents of 'inputFile.txt' in the 'iaf/'
directory, and an empty file 'outputFile.txt' alongside it. Alongside the IAF
configuration, these files will exist inside the 'iaf' container. After
running the sample, 'outputFile.txt' in this container will have identical
contents to 'inputFile.txt'. You can verify this by copying the file out of
the container to inspect it yourself:

    > docker cp adapter_iaf_1:/apama_work/Adapter/outputFile.txt .
    > cat outputFile.txt
