# Copyright (c) 2015-2017 Software AG, Darmstadt, Germany and/or Software AG USA Inc., Reston, VA, USA, and/or its subsidiaries and/or its affiliates and/or their licensors.
# Use, reproduction, transfer, publication or disclosure is prohibited except as specifically provided for in your License Agreement with Software AG

# Create an image from the base Apama image, copying the project in it ready to deploy
FROM apama
COPY init.yaml /apama_work/Weather/
COPY monitors /apama_work/Weather/monitors
COPY bundle_instance_files /apama_work/Weather/bundle_instance_files

CMD ["correlator", "--config", "/apama_work/Weather"]
