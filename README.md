# Remote Docker Image Setp
## with mounted (block) storage 


Most cloud hosting providers offer Docker with block storage. With docker and blockstorage volume, it makes it easier to develop or change your cloud provider. 

The image BOM has git, java, npm, gulp, grunt, http server (WedgeServer), sdk man, apt-vim pre-installed so you can get up and running. It is based on __baseimage from phusion(ubuntu)__.


## Installation 

#### Note: All commands assume 'sudo '.

1. Rent a host instance, Ex: AWS LightSail AMI, Vultr/Fedora, Ali Baba Cloud, Azure or 50 others. I like to use a different hosts for different docker instances :-). Make sure it "Supports Blockstorage" during signup. Start the instance. Test the network speed, ex:

		pip install speedtest-cli
		speedtest-cli

	If it is slow - go to a different provider!

2. Set up blockstorage and attach it to the instance. Then follow the instructions to mount the storage (as root) with the last command similar to (note the name of the common folder you crate on your monut):

		mount /mnt/blockstorage
		mkdir /mnt/blockstorage/common
		
	- [AWS is a bit harder](http://archive.is/hehgz) and mounts common in /mnt/blockstorage 
	
	Maybe test create a text file in common folder with a text editor (ex: nano ).
	
2. Secure the box, ex: iptables, fail2ban. Install docker, with the last command being:

		docker info
		systemctl enable docker

	I would now stop and restart the host and make sure the mounted storage is auto mounted and docker auto runs at reboot (docker ps).

1. From docker, fetch our docker image, http://hub.docker.com/r/cekvenich/bake. 

		docker pull cekvenich/bake
		docker images


3. Start the container with the 'bake' image and mount the host to your container. Add this to the run command.

		docker run -d -p 8080:8080 --mount type=bind,source=/mnt/blockstorage/common,target=/common cekvenich/bake /sbin/my_init

	Almost done. Anytime you want to access you docker container:
	
		docker ps
	    docker exec -ti ID /bin/bash
	
	... where ID is the process id from above command. That last line you may want to make into a con.sh shell command since you'll do it a lot.

4. Now to start the http server, serving files from common folder but running insider your docker container:

		cd /common
		create /root/common/Wedgefile
	      :8080
	      gzip
	      root public
	      tls off
	    
	Create a index.html('oh hi') inside a ./public folder.From inside the running Docker continer, run WedgeServer
	
	     /root/wedge

	Test from host (outside of the docker):

	     curl localhost:8080
	    
	 If you get a port in use error:
	 
		 lsof -i tcp:8080

5. If using git form common:

		git status
		export GIT_DISCOVERY_ACROSS_FILESYSTEM=1

	Just so you are aware it is a diffrent file system. I could have put that in the image - but better that you be aware of the boundary in case you install other software or make another image.

5. Last, for hardcore programmers only: setup cloud IDE (ex codeanywhere)  as SFTP to the host. Edit the common 'git' code in host, that is mounted to docker container.  (You can also setup a web based git client)
The Docker container could auto build for you (ex: gulp, gradle, etc). 
   
      
Here is a video of demo of the optional (for hardcore programmers) remote docker IDE.     
	- [Youtube video](http://youtu.be/0BkUom01XVc)


 

## Alternative installation: Paid install
Paypal me $5 with your host (ex: Vultr, AWS) temp password, and I'll set it up docker on your host, with NAS/block storage.
Then after the install, change your host and root password and load what ever docker image container you like


## Bonus ideas: 

- You may want to setup a browser proxy in your docker host, and then configure your local browser to use that proxy. (ex: littleProxy)
- You may want to setup a terminal email client in your docker host.  (ex: http://www.tecmint.com/send-email-attachment-from-linux-commandline )
- You may want to secure your git server to firewall to your docker host IP range. 
- Heck, uload your photos, videos and music files. Make a private radio station (ex: icecast ).
- Add PGP to your webhost.
- Of course, host your statically generated website and blog there.
Honestly, you should not have any storage or run anything locally other than the browser - just put it all in remote docker.


# Goal
Ideally, we'd like to see a webmaster be able to deploy a remote docker image with NAS. If you can install MySQL, Apache and PHP, you should be able to install Docker, NAS(storing static files) and WedgeServer.