# Copyright (c) 2015-2017 Software AG, Darmstadt, Germany and/or Software AG USA Inc., Reston, VA, USA, and/or its subsidiaries and/or its affiliates and/or their licensors.
# Use, reproduction, transfer, publication or disclosure is prohibited except as specifically provided for in your License Agreement with Software AG

# Create an image from the base Apama image, with an EPL application in it ready to deploy
FROM apama
COPY init.yaml FileForwarder.mon /apama_work/Adapter/

WORKDIR /apama_work/Adapter
CMD ["correlator", "--config", "init.yaml"]
