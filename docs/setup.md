---
layout: default
title: Run TCA
nav_order: 2
description: "Run TCA"
published: true
---

## Running TCA service as a container

The simplest way of running TCA api as a service is by running the publicly available TCA image
inside Openshift or Docker.

```
podman run -p 8000:8000 quay.io/konveyor/tackle-container-advisor:latest
```
or
```
docker run -p 8000:8000 quay.io/konveyor/tackle-container-advisor:latest
```

## Running TCA using Command Line Interface (CLI)

```
usage: tca_cli.py [-h] -input_json INPUT_JSON -operation OPERATION -catalog CATALOG -output_json OUTPUT_JSON


optional arguments:
  -h, --help     show this help message and exit
  -input_json INPUT_JSON  input to the application in json format
  -operation OPERATION    operation to perform: standardize (default)| containerize
  -catalog CATALOG  catalog: dockerhub (defailt) | openshift | operator
  -output_json OUTPUT_JSON  output from the application in json format
```

## Running TCA service inside a Virtual Machine (VM)
```
vagrant up
vagrant ssh
cd /vagrant
bash run.sh
```

4. Deploy TCA image as a container inside Redhat Openshift Container Platform.
```
bash deploy.sh
```

## Building TCA from source 

Requires Python >= 3.6 environment. You cannot run this code without having 
a proper Python environment first. We recommend that you follow the 
instructions in the [Developer's Guide](docs/development.md) before 
proceeding further. Follow the steps below once the environment has
been setup correctly.

```
bash setup.sh
```
Make sure *setup.sh* finishes without any errors. You should 
see a message at the end of the run suggesting TCA finished successfully. 

```
gunicorn --workers=2 --threads=500 --timeout 300 service:app
```
Guincorn may not be supported on some windows systems. In that case, 
waitress may be used instead.
```
waitress-serve --listen=*:8000 service:app
```

## Run a performance test for TCA service
A performance test measures the response time of TCA service under
various load conditions. Before running
performance test, update *config/test.ini* with the hostname
and port where TCA service has been deployed

```
python test/performance/run_payload.py
```

The table below summarizes performance testing results for TCA service api. The service was run as a podman container on IntelCore i9-10885H CPU @ 2.40GHz with 8 Cores, 16 Logical Processors with 4GB Memory limit.
	
<table>
<tr>
<td> #Records </td>
<td> Avg. Response Time </td>
<td> Server Peak Mem.   </td>
<td> Network I/O        </td>	  
</tr>
<tr>	
<td> 10 </td>
<td> 4.7s </td>
<td> 477.1MB / 4.194GB </td>
<td> 10.57kB / 8.129kB </td>
</tr>
<tr>	
<td> 100 </td>
<td> 13.73s </td>
<td> 470.5MB / 4.194GB </td>
<td> 81.78kB / 50.87kB </td>
</tr>
<tr>	
<td> 1000 </td>
<td> 42.75s </td>
<td> 446.8MB / 4.194GB </td>
<td> 644.3kB / 377.4kB </td>
</tr>
<tr>	
<td> 5000 </td>
<td> 226.29s </td>
<td> 497.8MB / 4.194GB </td>
<td> 3.814MB / 2.375MB </td>
</tr>
</table>

## Running TCA with a new version of Knowledge Base

Please perform the following steps.

1. Replace the existing .sql file with the new *new_db*.sql file in the db folder

2. Change the *common.ini* file in the config folder as follows

    version = *new_db*

3. Modify the *setup.sh* and *clean.sh* scripts to reflect the version accordingly.
    
    version=*new_db*

4. Re-run *setup.sh* and then deploy the service.

