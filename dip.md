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

If you are a Brand New Box employee, then head over to [bnb-dip-defaults](https://github.com/brandnewbox/bnb-dip-defaults) to see our steps for standard configuration and installation.