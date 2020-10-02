# This docker-compose project shows how to build a private docker registry instance ([similar to docker hub](https://hub.docker.com)) with a nginx proxy.

The docker-compose file creates two images:

1. registry:2
2. nginx (1.19.2)

To download images, build and run the project run:

`docker-compose up -d`

To clean up the project run:

`docker-compose down`

This project was tested on Ubuntu 20.04 Desktop. A static IP (192.168.1.80) was given to the server and nginx was configured as a proxy to provide remote access across my LAN. Further, this server was created to be a Certificate Authority (CA) and a CA signed server certificate was installed. The CA was used to sign the server certificates (registry.jkirn.com.crt). The CA certificate was also moved to and installed on all computers that required access to the docker registy created by this project (registry.jkirn.com). Also, I am running my own local DNS server(s) on my LAN. Therefore, I installed records for registry.jkirn.com on that DNS server(s).

The storage for the docker images is provided by mounting a NFS share (/mnt/freenas1). The NFS server is a [FreeNAS](https://freenas.org) install configured to provide an NFS server access. Mounting of the remote NFS directories at boot was configured by modifying the /etc/fstab as described via [this article](https://digitalocean.com/community/tutorials/how-to-set-up-an-nfs-mount-on-ubuntu-20-04).

Testing of the registry can be done on the registry host machine via execution of the following steps under "Copy an image from Docker Hub to your registry" at [this location](https://docs.docker.com/registry/deploying/#run-the-registry-as-a-service).

After completing the steps to upload the "my-ubuntu" image to local registry using the localhost:5000 method successfully, you now should be able to use the https secure method on your LAN for any host that has the CA installed.

`docker push registry.jkirn.com:5043/my-ubuntu`
`docker pull registry.jkirn.com:5043/my-ubuntu`

To examine what images have been uploaded to the regisry execute the following in a terminal window:

`curl -X GET https://registry.jkirn.com:5043/v2/_catalog`

I hope this helps you set up your own private docker (image) registry. If you need to add basic authentication to your nginx instance, it can easily be added via following steps from [here](https://docs.docker.com/registry/recipes/nginx/). Look for the two lines that start with "auth_basic" to see what needs to be added.