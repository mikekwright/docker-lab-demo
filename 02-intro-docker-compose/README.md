02 - Intro to Docker Compose
===============================================

One of the core philosophies that docker has is that a container should generally do 
only one thing.  However we rarely have an application that doesn't have some 
external dependency that needs to be met for the app to run.   

The point of `docker-compose` is to give us a way that we can create multiple different
containers in a group so that they can be associated with each other.   

## Pre-req

For us to use `docker-compose` we will need to have our docker client communicating with a
daemon somewhere, either on our local box or through a machine created using `docker-machine`. 
So before we can progress with these steps make sure that your docker client can communicate
with a daemon.  

        docker ps

## Step 1

The first thing to do is to get you all familiar with the different options that are used 
in `docker-compose` (like with did with `docker-machine`).  

        docker-compose

The options that you should get familiar with are listed below.   

1. `build`
2. `kill`
3. `up`
4. `run`
5. `start`
6. `stop`

**Note: You can get more details by passing --help after a given command**

## Step 2

This sample project that we are working on demonstrates a running application and a connected
database.  This is using the rails and postgres example provided on the docker site.    

The first thing we need to do is to get our rails app setup, rather than trying to get ruby
and everything running on our local box, we will instead use the same container we will run
in to setup the system.  Run this following command.  

        docker-compose run web rails new . --force --database=postgresql --skip-bundle

This last step used the ruby environment that we setup with rails and used that to generate
the entire application (which we see because of the mapped volume).   

        ls

## Step 3

We now need to configure our app to talk to our database.   

Open the file config/database.yml and make sure to update the entry to have the following
text:     

        default: &default
          adapter: postgresql
          encoding: unicode
          pool: 5
          database: postgres
          username: postgres
          password:
          host: appdb

        development:
          <<: *default
          database: src_development

## Step 4

Now we can start to run our app, so lets start up the application in the background.   

        docker-compose up -d 

If we want to view the output for our system we can run the following command

        docker-compose logs

Now we need to figure out which port we are bound to and test. 

        docker ps

Finally lets open a new webpage pointing to the ip and port of our stood up services.  

        docker-machine ip dev
        docker ps

## Step 5

So you probably had an error on the last command (opening the webpage).  This is because the 
application in question does not yet have its database schema applied to the running database.   

To fix this problem we will need to run the following command:  

        docker-compose run web rake db:create

Now, reopen the url and you should see a happier page (not pretty, but happier).   

