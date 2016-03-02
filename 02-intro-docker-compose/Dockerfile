FROM ruby:2.2.0

# Install dependencies
RUN apt-get update -qq && \
    apt-get install -y build-essential libpq-dev && \
    mkdir /src

# Set default directory for all commands
WORKDIR /src

ADD Gemfile /src/Gemfile
ADD Gemfile.lock /src/Gemfile.lock

RUN bundle install

ADD . /src
