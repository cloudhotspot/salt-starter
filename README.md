# Salt Starter

A Docker Compose environment for developing and testing Salt configurations.

This environment is originally based from <a href="https://github.com/jacksoncage/salt-docker" _target="blank">jacksoncage/salt-docker</a>.

## Introduction

The default configuration for this environment establishes a Salt master and two Salt minions running Ubuntu 14.04.2 LTS.

The intention is for this environment to work against a local Docker daemon, with file sharing enabled between your machine and Docker containers so that you can maintain your Salt configurations on your local machine and test them in your Docker environment.  

This is simple on Ubuntu machines, but harder on Windows/OS X machines where you need a virtual machine to run the Docker daemon.

See my <a href="http://github.com/cloudhotspot/fusion-docker" target="_blank">Fusion Docker</a> project for establishing an OS X environment on Virtualbox/VMWare Fusion with file sharing enabled.

The Salt master has a salt root mounted at `/srv/salt` that maps to a shared folder on your Docker host of `/share/cloudhotspot/salt-starter/salt/roots` by default.  

A mount point is also installed at `/etc/salt/master.d`, which is mapped to `/share/cloudhotspot/salt-starter/salt/master.d` by default.  This allows you to add settings as required to the Salt master configuration file. 

The specific Docker host mount points can be changed in the `docker-compose.yml` file:

	master:
	  image: jacksoncage/salt:latest
	  ...
	  ...
	  
	  volumes:
	    # Only modify the first path which specifies the Docker host path to share
	    - /share/cloudhotspot/salt-starter/salt:/srv/salt/:rw
	  ...


## Prerequisites

* <a href="https://docs.docker.com/installation/#installation" target="_blank">Docker</a>
* <a href="https://docs.docker.com/compose/" target="_blank">Docker Compose</a>
* Your Docker client environment needs to be pointed to a working Docker daemon

## Instructions

### Start the environment

Check out the repository:

	$ git clone https://github.com/cloudhotspot/salt-starter
	...
	...
	$ cd salt-starter

Now start the environment with all services running in the background:

	$ docker-compose up -d
	Creating saltstarter_master_1...
	Creating saltstarter_minion1_1...
	Creating saltstarter_minion2_1...
	
### Services and Containers

The default configuration establishes the following *services*:

* master
* minion1
* minion2

The above services are the top-level items in the `docker-compose.yml` file:

	master:
	  image: jacksoncage/salt:latest
	  ...
	  ...
	minion1:
	  ...
	  ...
	minion2:
	  ...
	  ...

It is important to note that `docker-compose` commands typically refer to services whilst `docker` commands refer to the container that runs each service.

You can view the container names using `docker-compose ps`:

	$ docker-compose ps
	         Name                   Command                   State                    Ports
	-------------------------------------------------------------------------------------------------
	saltstarter_master_1     bash start.sh            Up                       4505/tcp, 4506/tcp, 
	                                                                           0.0.0.0:8080->8080/tcp,
	                                                                           0.0.0.0:8081->8081/tcp
	saltstarter_minion1_1    bash start.sh            Up                       4505/tcp, 4506/tcp,
	                                                                           8080/tcp, 8081/tcp
	saltstarter_minion2_1    bash start.sh            Up                       4505/tcp, 4506/tcp,
	                                                                           8080/tcp, 8081/tcp
																			   
### Reviewing Logs

	# Tail logs from all services 
	$ docker-compose logs	
	...
	...
	
	# Tail logs from a single service
	$ docker-compose logs master
	...
	master_1 | [DEBUG   ] Updating fileserver cache
    master_1 | [DEBUG   ] This salt-master instance has accepted 3 minion keys.

### Attaching to a Running Container

	$ docker exec -it saltstarter_master_1 /bin/bash
	
	# Now inside the Salt master container
	root@master:/# salt-key -L
	Accepted Keys:
	master
	minion1
	minion2
	Unaccepted Keys:
	Rejected Keys:
	
	root@master:/# salt '*' test.ping
	minion1:
	    True
	minion2:
	    True
	master:
	    True	
	
### Cleaning Up

Kill your containers:
	
	$ docker-compose kill
	
Destroy your containers:

	$ docker-compose rm
	 
