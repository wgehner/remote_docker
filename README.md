# Remote Docker Image Setp
## with mounted (block) storage 


Most cloud hosting providers offer Docker with block storage. With docker and blockstorage volume, it makes it easier to develop or change your cloud provider. 

The image BOM has java, npm, gulp, grunt, http server (WedgeServer), sdk man pre-installed so you can get up and running. It is based on baseimage from phusion(ubuntu).


## Installation 

1. Rent a host instance, ex Fedora. Ex: AWS LightSail, Vultr, Ali Baba cloud or 50 others. Make sure it "Supports Blockstorage" during signup. Start the instance. Test the network speed, ex:

		pip install speedtest-cli
		speedtest-cli

	If it is slow - go to a different provider!

2. Set up blockstorage and attach it to the instance. Then follow the instructions to mount the storage (as root) with the last command being:

		mount /mnt/blockstorage
		
	
2. Secure the box, ex: iptables, fail2ban. Install docker, with the last command being:

		systemctl enable docker


1. From docker, fetch our docker image, http://hub.docker.com/r/cekvenich/bake. 

		docker pull cekvenich/bake
		docker images

2. Setup your block storage, and mount the block storage(NAS| S3) to your host. 
	In /mnt/blockstorage
			
			mkdir common

3. Start the container with the 'bake' image and mount the host to your container. Add this to the run command.

		docker run -d -p 8080:8080 --mount type=bind,source=/mnt/blockstorage/common,target=/root/common cekvenich/bake /sbin/my_init

	Done! Anytime you want to access you docker container:
	
		docker ps
	    docker exec -ti ID /bin/bash

4. Now to start the http server, serveing files from common folder but running in ocker.

		cd /root/common
		create /root/common/Wedgefile
	      :8080
	      gzip
	      root public
	      tls off
	    
	From inside the running Docker continer, run WedgeServer
	
	     /root/wedge

	Test from host:	

	     curl localhost:8080
	     

5. If using git form common:

		export GIT_DISCOVERY_ACROSS_FILESYSTEM=1

	Just so you are aware it is a diffrent file system.

5. Optional, for hardcore programmers only: setup cloud IDE (ex codeanywhere)  as SFTP to the host. Edit the common 'git' code in host, that is mounted to docker container.  (You can also setup a web based git client)
The Docker container could auto build for you (ex: gulp, gradle, etc). 
   
      
Here is a video of demo of the optional (for hardcore programmers) remote docker IDE.     
- [Youtube demo](http://youtu.be/0BkUom01XVc)


 

## Alternative installation: Paid install
Paypal me $5 with your host (ex: Vultr, AWS) temp password, and I'll set it up.
Then after the install, change your host and root password. 

## Support
Supported via git issue tracker is:

- Linux remote host (running locally or OSX/Windows will work but is not supported)
- with 'NAS' mounts (it runs fine without mounting blocstorage, but why )
- Running Wedge Server, something from staticgen.com or similar. 

## Bonus: 

- You may want to setup a browser proxy in your docker host, and then configure your local browser to use that proxy. (ex: littleProxy)
- You may want to setup a terminal email client in your docker host.  (ex: http://www.tecmint.com/send-email-attachment-from-linux-commandline )
- You may want to secure your git server to firewall to your docker host IP range. 
- Heck, uload your photos, videos and music files. Make a private radio station (ex: icecast ).
- Add PGP to your webhost.
- Of course, host your statically generated website and blog there.
Honestly, you should not have any storage or run anything locally other than the browser - just put it all in remote docker.


# Goal
Ideally, we'd like to see a webmaster be able to deploy a remote docker image with NAS. If you can install MySQL, Apache and PHP, you should be able to install Docker, NAS(storing static files) and WedgeServer.