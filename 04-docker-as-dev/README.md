04 - Docker as a Dev Environment
===========================================

So far we have had an oppurtunity to work with some of the tools that docker provides for us
to run applications as containers.  Another advantage that we get from using docker comes from
our understanding that as developers, everything we use is an application and as such, can be
dockerized or containorized.    

In this section we will quickly go over how to setup a development environment that you can use
to make your development experience significantly better, by removing the roadblocks of setting
up an environment to work on your machine, and keeping it up to date with the latest dependencies
introduced by other developers.   

## Step 1

Lets create a new Dockerfile that defines our development environment that we are working with. One
of the first things that we can start running is our web application, since this one uses node we 
will setup an environment that lets us develop in node.   

Lets first start by pulling down the latest version of our web code.   

        git clone https://github.com/alianza-dev/admin-portal
        cd admin-portal

## Step 2

Start by creating the compose file that we need to use to work with our system.   

        webui:
          image: node:5.7.0
          working_dir: /src
          command: npm start
          ports: 
            - 8888:8888
          volumes:
            - .:/src

The first thing we will want to do is to install our node_modules which we can do by
running the `npm install` command.  There is one error that comes up in this case though, 
and that is the error that exists because of a permission error that is in docker, we
need to install the files in a different directory and then move them over once they
are installed.   So lets create a new file called `install.sh` that is a peer to the 
`docker-compose.yml`.   

        #!/usr/bin/env bash
        if [ -d node_modules ]; then
          rm -rf node_modules
        fi

        cp /src/package.json /
        cd /
        npm install 
        mv node_modules /src/node_modules

Now we can run the install command in the container to have it install all of our 
dependencies.    

        docker-compose run webui ./install.sh

After this install is complete we should be able to run the application    

        docker-compose up



