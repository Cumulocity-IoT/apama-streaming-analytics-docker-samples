# Copyright (c) 2015, 2017 Software AG, Darmstadt, Germany and/or Software AG USA Inc., Reston, VA, USA, and/or its subsidiaries and/or its affiliates and/or their licensors.
# Use, reproduction, transfer, publication or disclosure is prohibited except as specifically provided for in your License Agreement with Software AG

# Create an image from the base Apama image, with an IAF (File adapter) configuration in it
FROM apama
COPY File.xml inputFile.txt File-static.xml /apama_work/Adapter/

WORKDIR /apama_work/Adapter
CMD ["iaf", "File.xml"]
