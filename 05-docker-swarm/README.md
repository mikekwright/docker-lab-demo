05 - Docker Swarm
==============================================

The one thing to think about with docker moving forward is the fact that it will usually run in a 
fabric of some wort (like kuburnetes).  Docker provides this functionality built in, as long as it
has a service discovery mechanism of some sort in place.   

For this step we will pull down the configuration that was setup before to run consul with docker
in a swarm fashion and work with that swarm for starting up services.   

## Step 1

The first step is to clone the repo that holds the swarm details.   

        git clone https://github.com/mikekwright/docker-swarm-demo

After pulling it down, start by creating the cluster. 

        ./setup-cluster start

This step will take a while, but we should have a swarm setup that you can see by running the `ls`
command from `docker-machine`.    

## Step 2

Now we need to point our docker client to the swarm so that operations will happen against the swarm, 
this can be down by running the following command.  

        eval $(docker-machine env --swarm swarmmaster)   

Now when we run docker ps we will see all the containers that are running on all of the docker hosts
that are part of the swarm.  

        docker ps

## Step 3

Finally lets start up a new service and see where it places these services so we can see how it can 
balance the services across the swarm as the swarmmaster dictates. 

        docker run -p 80 nginx
        docker run -p 80 nginx
        docker run -p 80 nginx
        docker run -p 80 nginx
        docker run -p 80 nginx

To see all the containers run the client `ps` command and notice the hosts they are running on.   

        docker ps
