01 - Intro to Docker Machine
===================================================

This is the first step that we will have with docker.  The reason that we start with `docker-machine` and not just
the docker client is that for systems other than linux systems, you will need to create a linux vm to use when
running your docker containers.   

`docker-machine` is a tool that has been created to simplify the processes of creating this docker capable
guest vms.    

## Step 1

The first thing we will need to do is to run the `docker-machine` command to see what options we have available to use.   

        docker-machine

There are a few interesting commands that you will want to use moving forward, so start by 
reading the definition of these commands.   

1. `ls`
2. `create`
3. `env`
4. `ip`
5. `rm`

**Note: You can get more details by passing --help after a given command** 

## Step 2

At this point you should have a basic understanding of the different parts of docker-machine, so lets start 
by creating a new docker server that we can use.   

        docker-machine create --driver virtualbox killme

**Note: At this point you can run into an issue where the machine is unreachable, check your host networks to
make sure DHCP is enabled**

We can make sure that we have a box setup by first looking to see it in the list.   

        docker-machine ls

At this point if we try to run the docker client we will not get a response (likely will hang) unless it is
pointing to another location, so we first need to tell the docker client the location of the new docker
daemon to connect to.   

        eval $(docker-machine env killme)   # bash/zsh
        eval (docker-machine env killme)    # fish

The windows version isn't nearly as clean:    

        FOR /f "tokens=*" %i IN ('docker-machine env killme') DO %i     # Windows

Now running the below docker commond should be able to give us some useful output.   

        docker ps

## Step 3

At this point we are going to test destroying the created machine and re-creating.  

        docker-machine kill killme
        docker-machine rm killme
        docker-machine create --driver virtualbox dev

## Conclusion

So at this point you should be familiar with creating and deleting this docker daemon hosts, you can also have more
than one vm created at a time, they are just identified by the unique name that we supply.    



