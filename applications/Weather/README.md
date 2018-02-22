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


Weather
=======

This sample deploys the Apama 'Weather' demo. As with previous samples,
various Apama components run in distinct containers.

The project has been converted (manually) to an init.yaml configuration file
so the correlator injects the monitors at startup.

This sample also creates a container for the dashboard server which
connects to the correlator just as in the original 'Weather' sample.

This sample also creates a Compose network called 'front' which both containers
connect to allow communication between the containers.  This network can be
viewed using:

    > docker network ls

Detailed instructions on running the sample either as a Compose-based
application or via Kubernetes can be found in the README in the parent
directory. If you are running via Kubernetes this sample creates the following
resources which can be accessed via logs and must be deleted via delete:

	* pod weather-engine
	* service weather-correlator
	* pod weather-dashboard
	* service weather

This sample uses an init-container for the dashboard server to wait for the
correlator service to become available before starting the dashboard server.

When running, this sample exposes the dashboard server's ports to the host
operating system, allowing you to visualise the demo in the Dashboard Viewer:

1. Run the Dashboard Viewer from any Windows machine with Apama installed

2. Log in with any username/password (any non-blank password will work)

3. Use the hostname of the machine running your sample, and the default port.
   If you're running using kubernetes you will need to use this command to
	find the port used:

	> kubectl describe service weather

4. File->Open and choose \demos\Weather\dashboards\Weather.rtv from your Apama
   installation

5. The table of weather conditions should be populated with data.

6. If you click on a row of the table, the trend chart will begin updating


NOTES
=====

init.yaml needs to be written manually from the files to inject from the project.

Images created by previous versions (such as 'weather_deployment') should be
deleted before running this sample otherwise you may see errors.
