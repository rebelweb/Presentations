layout: true
<header>
  <p class="left">Effingham Software Developer Group</p>
  <p class="right">@rebelwebdev</p>
</header>

---

class: title, middle, center
# Docker For Small Applications
## Ryan Condron

---

class: center, middle
# What is Docker?
Docker is a service that each part or service of an application is ran in a
container. The only thing we install on the host is docker and everything else
is ran in a container.

???

- Often used in microservice environments
- great for web apps as they require multiple services to get the app to run

---

class: middle
# When to use Docker
- Applications with Multiple Services
- Development
- When hosting multiple different types of applications on the same host
- Demos

???
- Docker can make app setup easier for end users by offloading setup work to
  developers
---

# Terminology

- Registry - where container images are stored
- Image - an immutable state of a container (i.e. version)
- Container - a app or service build on docker infrastructure

---

class: center, middle, title
# Docker Setup & Commands

---

# Installation

- Go To the docker website and follow the instructions to install for your
  platform
- Available on multiple platforms
  - Windows
  - Linux
  - MacOS

---
class: middle
# Running a container

```sh
docker run nginx --name nginx
```

```sh
docker stop nginx
```

---

class: middle
# View running containers

```sh
docker ps
```

```sh
docker ps -a
```

---

class: middle
# Viewing Container Logs
```sh
docker logs container_name
```

---

class: middle
# Run an One Off Command In a Container
```sh
docker exec -it container_name /bin/bash
```
---

class: title, center, middle
# Application Setup

---

# Command To Run In the Container
- The last line in your Dockerfile should specify the command to run or put the
  commands in your documentation
- Create shell or batch files to run your app or service and make sure they are
  excutable

```sh
#!/bin/bash

create_db() {
  bundle exec rake db:create
  bundle exec rake db:migrate
}

run_server() {
  bundle exec puma -C /app/config/puma.rb
}

create_db

run_server
```

---

# Logging

- Everything in your containers log to standard out
- Logging to standard out will allow you to view logs using docker commands
- Prevents contianer disk space growth (where log cleanup isn't easily done)

```sh
docker logs container_name
```
---

# The Dockerfile
- builds the service in a container
- contains commands such as RUN, ENV, COPY, FROM, WORKDIR
  - FROM - sets the start container
  - RUN - runs a command while building the image
  - COPY - copies files into the container
  - WORKDIR - the directory for your application
  - ENV - sets an environment variable

---

# The Dockerfile

```Dockerfile
# use Ruby as a base image
FROM ruby:2.5.0

# install nodejs PPA
RUN curl -sL https://deb.nodesource.com/setup_9.x | bash

RUN curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | apt-key add -

RUN echo "deb https://dl.yarnpkg.com/debian/ stable main" | tee /etc/apt/sources.list.d/yarn.list

# update & install dependencies
RUN apt-get update -qq && apt-get -y install yarn nodejs imagemagick libc6-dev

# set APP_PATH Environment Variable
ENV APP_PATH /app

# create APP_PATH directory
RUN mkdir -p $APP_PATH

```
---

# The Dockerfile
```Dockerfile
# set working directory to APP_PATH
WORKDIR $APP_PATH

# Move Gemfiles to WORKDIR
COPY Gemfile Gemfile.lock ./

# Install Gems
RUN bundle install --without development test --binstubs

# Copy Application to Work Dir
COPY . .

# install nodejs assets
RUN yarn install

# compile assets
RUN bundle exec rake webpacker:compile RAILS_ENV=production
```

---

# Talking between containers
- Docker has a internal network so you can talk between containers
- Always talk to containers by name, because their IP will change when updates occur
- We will need to take this into account while developing the application

---

# Container Environment Variables
- Used to store container settings
  - database params
  - api keys for external services
- changes behavior of a container
- container does not need rebuilt when changing variables

---
