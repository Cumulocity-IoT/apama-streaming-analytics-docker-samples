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
