Adapter
=======
This sample starts an IAF in a container, which connects to a correlator
running in another container. The IAF is running the File transport. An EPL
application is deployed into the correlator, which requests the contents of
a file 'inputFile.txt' from the File adapter, and then directs it to write
those contents back out to another file 'outputFile.txt'.

Detailed instructions on running a Compose-based application can be found in
the README in the parent directory.

Before running this sample, note the contents of 'inputFile.txt' in the 'iaf/'
directory, and an empty file 'outputFile.txt' alongside it. Alongside the IAF
configuration, these files will exist inside the 'iaf' container. After
running the sample, 'outputFile.txt' in this container will have identical
contents to 'inputFile.txt'. You can verify this by copying the file out of
the container to inspect it yourself:

    > docker cp adapter_iaf_1:/apama_work/Adapter/outputFile.txt .
    > cat outputFile.txt
