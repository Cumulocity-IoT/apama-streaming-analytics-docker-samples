# Copyright (c) 2015-2017 Software AG, Darmstadt, Germany and/or Software AG USA Inc., Reston, VA, USA, and/or its subsidiaries and/or its affiliates and/or their licensors.
# Use, reproduction, transfer, publication or disclosure is prohibited except as specifically provided for in your License Agreement with Software AG

# Create an image from the base Apama image, with the MemoryStore application in it ready to deploy
FROM apama
COPY init.yaml MemoryStoreCounter.mon /apama_work/MemoryStore/

WORKDIR /apama_work/MemoryStoreData
CMD ["correlator", "--config", "/apama_work/MemoryStore/init.yaml"]
