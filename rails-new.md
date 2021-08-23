# Step 1 — Creating an Empty Container

We are going to house our Rails Application within a container, so in order to do this, we must first create it! On your machine, make an empty directory.

```
mkdir bnb-library
cd bnb-library
```

Instead of having to manage the versions of multiple different dependancies for your application such as *Ruby*, and *Postgres*, we maintain our own set of images that are:
- Based on Ruby
- Have PostgreSQL Client installed (you might opt for mysql-client)
- Have NodeJS installed (for compiling assets)
- There is some configuration of bundler so that it will install our gems in the `/bundle` directory.

In your new directory make a `docker-compose.yml` file and paste in:

<figure><strong><code>docker-compose.yml</code></strong></figure>

```ruby
version: "3"
services:
  app:    
    image: brandnewbox/bnb-ruby:2.7-postgresql
    command: bundle exec puma -C config/puma.rb
    volumes:
      - .:/app:cached
      - bundle_cache:/usr/local/bundle
    ports:
      - "3000:3000"
    depends_on:
      - postgres
  postgres:
    image: postgres:11-alpine
    environment:
      - POSTGRES_PASSWORD=password
    ports:
      - '5432:5432'
    volumes:
      - my_dbdata:/var/lib/postgresql/data
volumes:
  bundle_cache:
  my_dbdata:
```

Now let's bash into this container
```ruby
dip run bash

# output

Creating network "bnb-library_default" with the default driver
Creating volume "bnb-library_bundle_cache" with default driver

bash-5.1#
```
YAY! You are now at a bash terminal inside the container you just created.

## Step 2 — Creating a New Rails Project

Let's install rails and setup a new application. We'll start by going into the root folder to make a app named *bnb-library*. Then we will move the files into their final resting place in `/app`. The `shopt` command in there helps us move the hidden files (like `.gitignore`) as well.

```ruby
# from within your bash terminal

cd ..
gem install rails
rails new bnb-library --database=postgresql
shopt -s dotglob nullglob
mv bnb-library/* app
exit
```

You will see a good deal of output telling you what Rails is creating for your new project. We are going to highlight some of the significant files, directories, and commands that you can find in the output of running `rails new`:

- *gemfile* : This file lists the gem dependencies for your application. A gem is a Ruby software package, and a Gemfile allows you to manage your project's software needs. 
- *app*: The app directory is where your main application code lives. This includes the models, controllers, views, assets, helpers, and mailers that make up the application itself. Rails gives you some application-level boilerplate for the MCV model to start out in files like `app/models/application_record.rb`, `app/controllers/application_controller.rb`, and `app/views/layouts/application.html.erb`.
- *config*: This directory contains your application's configuration settings.
- *config/routes.rb*: Your application's route declarations live in this file.
- *config/application.rb*: General settings for your application components are located in this file. 
- *config/environments*: This directory is where configuration settings for your environments live. Rails includes three environments by default: `development`, `test`, and `production`. 
- *config/database.yml*: Database configuration settings live in this file, which is broken into four sections: `default`, `development`, `production`, and `test`. Thanks to the Gemfile that came with the `rails new bnb-library --database=postgresql`, which included the `pg` gem, our `config/database.yml` file has its adapter parameter set to postgresql already, specifying that we will use an postgresql database with this application. 
- *db*: This folder includes a directory for database migrations called migrate, along with the `schema.rb` and `seeds.rb` files. `schema.db` contains information about your database, while `seeds.rb` is where you can place seed data for the database.

## Step 3 - Version Control

Now that you've got your awesome app already to go, let's setup version control so we don't lose any of our work. Create a new repository on GitHub with nothing in it. Then, from within your working directory:

```
git init
git add .
git commit -m "First commit"
git branch -M main
git remote add origin git@github.com:brandnewbox/my-great-project.git
git push -u origin main
```

## Step 4 - Starting Your Application

We are going to want to interact with our app in the browser, but first, we must setup our database.

We are going to add a *url* to our `database.yml` file which is going to tell our rails application a few things. Here is the breakdown...
```
postgres://DATABASE_USERNAME:DATABASE_PASSWORD@DATABASE_HOST:PORT/DATABASE_NAME
```
Under `development` we will add the appropriate configuration to point Rails at the Postgres service we have setup in our `docker-compose.yml` file.

<figure><strong><code>config/database.yml</code></strong></figure>

```yml
development:
  <<: *default
  database: bnb_library_development
  url: postgres://postgres:password@postgres:5432
```
The `test` environment will use the same database information on your local machine. But we use `ENV.fetch` to allow for the value to be overridden with an environment variable. This comes in handy when running tests elsewhere, i.e. CircleCI or a similar CI/CD service.

<figure><strong><code>config/database.yml</code></strong></figure>

```yml
test:
  <<: *default
  database: bnb_library_test
  url: <%= ENV.fetch("DATABASE_URL") { "postgres://postgres:password@postgres:5432" } %>
```

Then run the setup command. This will spin up an instance of the `app` container to run the command in (as well as an instance of the `postgres` service that the `app` depends on).

```
dip rails db:setup
```
You can expect an output similar to 
```
Running via Spring preloader in process 19
Created database 'bnb_library_development'
Created database 'bnb_library_test'
```
Now let's start your application's server and see the landing page rails has created for us.
```
dip up
```
Navigate to `http://localhost:3000` in your browser to see the magic.

![Hello Rails](images/hello-rails.png)

### Congratulations, you have built a Rails app inside of a container!
