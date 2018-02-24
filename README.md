
We and other only support portable containers such as Docker with
NAS storage such as blockstorage or S3.

ALL major and most other cloud hosting providers offer Docker with block storage. With docker and blockstorage volume, it makes it easier to change your cloud provider. 


# A: Recommended installation, via provided image:
0. Install docker in your cloud host. Before that - secure your host.

1. From docker, fetch our docker image: 'cekvenich/bake'.

2. Setup your block storage, and mount the block storage(NAS| S3) to your host. 

3. Start the container with the 'bake' image and mount the host to your container. Add this to the run command.

     --mount type=bind,source=/mnt/blockstorage/vol,target=root/vol

	Done!

5. You likely don't want Docker running as root, so you can fix that.

5. Optional, for hardcore programmers only: setup codeanywhere as SFTP to the host. Edit the vol code in host (that is mounted to docker container). The Docker container should auto build for you. Maybe make it easier to find.

      ln -s /mnt/blockstorage/vol
      
 XXX Here is a video of sample remote docker IDE.     
      

# B: Alternative source code installation.

Create Docker host instance with block storage(or NAS, S3)
Ex: Vultur, Fedora (click 'Block Storage Compatible').

1. Add Block Storage (NAS) to Linux.
Using Block Storage helps you manage storage size and Docker helps you migrate

2. Attach the storage as per instructions. Then mount the storage. Ex:

     mount /mnt/blockstorage

3. Add security.

4. Install docker - in the mounted block storage folder!
    
	     dnf -y install dnf-plugins-core
	    
	     dnf config-manager \
	        --add-repo \
	        https://download.docker.com/linux/fedora/docker-ce.repo
	    
	    dnf install docker-ce
	    
	    systemctl start docker

5. Don't install anything on linux - other than Docker. Install everything inside of Docker.


6. Optional.In case you have issues, this may help:

	    inside /etc/selinux/config
	    SELINUX=permissive
    
7. Make image, make a Dockerfile:
	
	    FROM phusion/baseimage
	    MAINTAINER vic (vic@eml.cc)
	     
	    docker build -t bake:latest .
 
8. *Start* the container:

	    docker run -d -p 8080:8080 --mount type=bind,source=/mnt/blockstorage/vol,target=/root/vol bake /sbin/my_init
	      
	    docker ps
	    docker exec -ti ID /bin/bash
	    docker exec -ti 9337faa3fc9d /bin/bash

9. Almost there. Check:
    
	    if [ -f /.dockerenv ]; then
	        echo "I'm inside the matrix";
	    else
	        echo "I'm living in real world!";
	    fi

	Optional if you like the Vi Visual Editor: install NERDTree (via apt-vim )


10. XXX We need a web/http server. WedgeServer.
		
		apt-get update / upgrade	    
	   apt-get install mlocate
	   updatedb
	    apt-get install net-tools
	    apt-get install git
	    
		add-apt-repository ppa:longsleep/golang-backports
		apt-get update
		apt-get install golang-go

	   	 go get github.com/WedgeServer/builds
	    go get github.com/WedgeServer/wedge/caddy
	    cd   go/src/github.com/WedgeServer/wedge/caddy
	    go run build.go
	    cp wedge /root
	    cd /root/vol
	    mkdir public
	    (create an index.html in public)
	    cd ..
	    create /root/vol/Wedgefile
	      :8080
	      gzip
	      root public
	      tls off
	    
	run WedgeServer
	     /root/wedge

	from host:	
	     curl localhost:8080


11. Now that it works local, lets try WWW. Start the container and go inside. Start the Wedge server inside the container.

        nohup ./wedge &
  
  	Now open your local browser to the host ip:8080 
  	
  	Joy?

		docker commit 12d0b07e566d myBake
		docker stop 12d0b07e566d


11. More. Install other usefull tools: 

	      apt-get install findutils
	      apt-get install unzip
	      apt-get install zip
  
	Now install SDKMan: http://sdkman.io/install.html
	And install node js.

	      sdk version
	      npm -version
	
			npm i gulp
			sdk install gradle
			add-apt-repository ppa:webupd8team/java
			apt update
			apt install oracle-java8-installer
			

	Done with docker host. You can now git check out to your /vol. Let docker build and host.



11. Save your own container. Before you stop it or kill it.

	    docker commit XID myBake
	    docker stop XID
    
    And test, so you sure you can back and restore your block storage volume. 




