# Docker & Docker Compose

When working within Docker containers you are going to need both Docker and docker-compose installed on your machine. Follow the official documentation [here](https://docs.docker.com/get-docker/) for installing Docker.

If you are running on an operating system other than Mac or Windows you will need to install Docker Compose [separately](https://docs.docker.com/compose/install/) as it is not included in Docker Desktop like it is for Mac or Windows.

# Dip

Dip is a command-line utility that gives the "native" interaction with applications configured with Docker Compose. In the _dark ages_ we would bash into a container with commands like 
``` 
docker-compose run app bash
```
and migrating a database would take multiple steps.. *BUT NO LONGER!*

By adding a global *dip.yml* file, we can run alias commands to save time and repetition. eg.
```
# starting a server
dip up     vs     docker-compose up
```
```
# migrating a database
dip rake db:migrate

vs 

docker-compose run app bash
bundle exec rake db:migrate
```

### Documentation
For official documentation, look here: [Dip](https://github.com/bibendi/dip)

Head over to [bnb-dip-defaults](https://github.com/brandnewbox/bnb-dip-defaults) to see our steps for standard configuration and installation.