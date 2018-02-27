
 
##  Recepie from scratch.

Create Docker host instance with block storage(or NAS, S3)
Ex: Vultur, Fedora (click 'Block Storage Compatible').
I use Linux, but if you like Windows most major hosts (including Vultur) offer it, and some smaller ones host OSX mini (but OSX hosts tend not to offer NAS options ).

1. Add Block Storage (NAS) to Linux.
Using Block Storage helps you manage storage size and Docker helps you migrate

	Before setting up the machine - test the internet speed. If speed is slow, setup on another provider:

		pip install speedtest-cli
		speedtest-cli
		
	Or you can move your image and storage to another provider later, there are a dozen hosting proviers that offer more than 10-gigabits/sec - but not of the big 3 AFAIK. Also, this is a good time to open a browser (|IOS|Anorid) based SSH.  
	

2. Attach the storage as per instructions of your host. Then 'format' and mount the storage. Ex:

    	 mount /mnt/blockstorage

3. Add security to your cloud host. Ex: iptables, fail2ban, etc.

4. Install docker - in the mounted block storage folder! Follow web instructions - this is just my block.


			yum update / upgrade
			dnf install https://download.docker.com/linux/fedora/25/x86_64/stable/Packages/docker-ce-17.06.0.ce-1.fc25.x86_64.rpm
	or		
	     dnf -y install dnf-plugins-core
	    
	     dnf config-manager \
	        --add-repo \
	        https://download.docker.com/linux/fedora/docker-ce.repo
	    
	    dnf install docker-ce
	    
	    systemctl start docker
	    systemctl enable docker
	    docker info

5. Don't install anything on linux - other than Docker. Install everything inside of Docker.

    
7. We start with a phusion baseimage. Make a Dockerfile. 
	
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


10. We need a web/http server. WedgeServer in golang. ( a Caddy Server fork )
		
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



11. ****** Save your own container! Before you stop it or kill it.

	    docker commit XID myBake
	    docker stop XID
    
    And test, so you sure you can back and restore your block storage mount. 

13. Moar

		add-apt-repository ppa:git-core/ppa

14. push the image
docker tag imagebuildinginprocess rusrushal13/get-started:part1

docker push rusrushal13/get-started:part1


