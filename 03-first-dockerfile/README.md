03 - First Dockerfile & docker-compose.yml
=====================================================

So, now we have had an opportunity play around with `docker-machine` and `docker-compose`, lets go 
through an example in creating our own docker enviornment (`Dockerfile` and `docker-compose.yml`).  
In this case we are going to create a webapp using sinatra in ruby.   

## Step 1

The first thing we need to do is to create our `Dockerfile` so first, open the file in your favorite
editor (or the best editor VIM).    

Next we need to decided what this image will be based on.  This is the common base for all images and 
can allow us the opportunity to reuse containers where possible.  In step 02 we used a container based
on `ruby:2.2.0` so lets start with that as our base.  

        FROM ruby:2.2.0

The next line we put in is more of an informative line, basically letting people know who the maintainer
is for the image (who the creator or owner is).   

        MAINTAINER you@email.com

At this point lets test this Dockerfile to make sure that we can create an image based on the content of 
the file, by running the following command.  

        docker build -t mysinatra .

If all went well we should now have a new image that we can create containers from, this will appear when 
we run the following command to list the images.

        docker images

Finally lets try to use our container

        docker run -it mysinatra /bin/bash

## Step 2

So we have the basis of our `Dockerfile` but at this time we should start to add the dependencies that we 
would like to have for our application.  Since this is a sinatra application we just need to make sure
that we have the Sinatra gem installed.  So add the following line to the `Dockerfile`

        RUN gem install sinatra

Now that we have sinatra installed, lets create our sinatra app file.  

Open a new file named `app.rb` in your favorite editor and add the following lines.  

        require 'sinatra'

        set :bind, '0.0.0.0'

        get '/' do
          "Hello Sinatra!"
        end

So we now have our app, but we need to have a way to have the file added to the container, so open 
`Dockerfile` again and lets add the app to our image

        ADD app.rb /app.rb

We can now test these changes by running the build command again

        docker build -t mysinatra .

If successfully lets try to run the application

        docker run -it mysinatra /bin/bash
        ruby /app.rb

We should see that we have an app running on port 4567 so open a webpage to your docker host and port 4567. 

If you didn't get a webpage, then you are correct, we have our image setup so that a container that runs will have
most of the required info, however it is missing some commands that we need to make sure that our app will be
accessible.  

## Step 3

So for the next step, lets finish off our `Dockerfile` by exposing the port that our app will listen on     

        EXPOSE 4567

And lets also tell the image how it should start off when the image is run (by default)

        CMD ["ruby", "/app.rb"]

Those should be the final two lines that we need for our `Dockerfile` so at this time lets rebuild our image    

        docker build -t mysinatra .

And now lets run the container letting it startup on its own, with the port exposed for our app.   

        docker run -p 4567:4567 mysinatra

Open your browser to the webpage of the docker host and port 4567 and you should see a sweet message of 

        Hello Sinatra!

## Step 4

So we now have our `Dockerfile` complete, lets create a compose file that we can use to interact with our
application (instead of having to do a build command, remember the image name and start it).     

Start by create opening a new file in your favorite editor named `docker-compose.yml`.    

In the `docker-compose.yml` file add the following content.   

        app:
          build: .
          ports:
            - "4567:4567"

Save the file and lets test it out by running the below commands:    

        docker-compose build
        docker-compose up

We should be able to now see our application running on the same port on the docker host.    

## Step 5

So we have our image building and managed by `docker-compose`, lets say we want to scale our application so that
we can have 3 containers servicing the content.  Luckily we can scale that ourselves.  So lets start by running 
our application in the background.   

        docker-compose up -d

Now if you run `docker ps` we see that we should only have one container running, so lets scale that up to 3.  

        docker-compose scale app=3

At this point the command should fail, because we already have a container bound to port 4567.  What we need to 
do is make it so that our application is able to work with a dynamic port that is supplied by the docker daemon.   

Open the `docker-compose.yml` file and change the ports line to the below value.  

        ports:
          - 4567

Here we are basically saying that you need to bind to this port for our app, but we don't care what you bind from.     

So lets stop our current process:    

        docker-compose kill

Now lets re-run with changed `docker-compose.yml`    

        docker-compose up -d

Finally we will scale up our app:    

        docker-compose scale app=3

We should be successfully which can see by looking at the running processes.    

        docker ps

Finally lets go ahead and kill our running applications.  This will luckily know the scale of our containers and will
kill all of the running containers.   

        docker-compose kill


