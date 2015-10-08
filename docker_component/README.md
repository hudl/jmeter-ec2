# Docker Integration with JMeter

<br>
##Credits
* Almost all of the credit for this project goes to Srivaths: http://srivaths.blogspot.com/2014/08/distrubuted-jmeter-testing-using-docker.html (see this for background information)
* I took what he had an tweaked some of the Dockerfiles as they were outdated and not working.
* I also tweaked the bash script a bit and consolidated the project to make it easier.

<br>

## Pre-Work
1. Install docker on the EC2 instance you are working with: http://docs.aws.amazon.com/AmazonECS/latest/developerguide/docker-basics.html#install_docker
2. Build the docker images from the 3 different Dockerfiles.  These will be used to spin up the JMeter Client and Server that will be leveraged when the `driver.sh` script gets ran.

```
Either pull down the project or copy the jmeter-base, jmeter-client, and jmeter-server directories to the EC2 instance you installed Docker on.  The names of the images will matter, so please follow the below commands exactly and in order:

1. cd jmeter-base; docker build -t jmeter/base .
2. cd ../jmeter-client; docker build -t jmeter/client .
3. cd ../jmeter-server; docker build -t jmeter/server .
```

<br>

## Running Tests

1. Inside of the `jmeter-driver` directory, there is a script `driver.sh` (if you did not clone the project - copy this directory to the instance).
2. There are 5 different parameters the script can take:

```
Usage: ./driver.sh [-d data-dir] [-n num-jmeter-servers] [-s jmx] [-w work-dir]
-d      The data directory for data files used by the JMX script.
-h      This help message
-n      The required number of servers
-s      The JMX script file
-w      The working directory. Logs are relative to it.
```

An example run: `./driver.sh -s my_jmx_file.jmx -n 25`

This will spawn 25 containers via the server image and leverage one client container to provide the JMX/any data files the JMX uses (-d flag).  Additionally, the results are aggregated and written out to /tmp by default.  Upon completion of the test all the containers are spun down and removed.  Typically I like to generate the JMX file via the JMeter GUI Program and then leverage it (just copy it over to the instance this is all setup on).  Conceivably, you could edit the JMX with your favorite editor as it is just XML.

**Important Note**: If your test plan is communicating to other instances - you will need a Security Group configured for this communication to take place.