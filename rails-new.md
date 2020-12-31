Step 2 — Creating a New Rails Project
With our database installed, we can create a new Rails project and look at some of the default boilerplate code that Rails gives us with the rails new command.
Create a project called sharkapp with the following command:
You will see a good deal of output telling you what Rails is creating for your new project. The output below highlights some significant files, directories, and commands:
     rails new sharkapp

Output

```bash
create
. ..
create Gemfile . ..
create app
.. .
create app/controllers/application_controller.rb .. .
create app/models/application_record.rb
.. .
create app/views/layouts/application.html.erb .. .
create config
create config/routes.rb
create config/application.rb
.. .
create config/environments
create config/environments/development.rb
create config/environments/production.rb
create config/environments/test.rb
.. .
create config/database.yml
create db
create db/seeds.rb
.. .
 
 run bundle install .. .
Bundle complete! 18 Gemfile dependencies, 78 gems now installe
d.
Use `bundle info [gemname]` to see where a bundled gem is inst
alled.
.. .
* bin/rake: Spring inserted * bin/rails: Spring inserted
 Gemfile
    app/models/application_record.rb
              app/views/layouts/application.html.erb    config
config/environments
development production test config/database.yml
app/controllers/application_c
      ontroller.rb
    config/r
  outes.rb
     config/ap
  plication.rb
```
The output highlighted here tells you that Rails has created the following: - : This file lists the gem dependencies for your application. A gem is a Ruby software package, and a Gemfile allows you to manage your project's software needs. - app: The app directory is where your main application code lives. This includes the models, controllers, views, assets, helpers, and mailers that make up the application itself. Rails gives you some application-level boilerplate for the MCV model to start out in files
like ,
: Your application's route declarations live in this file. -
: General settings for your application components are located in this file. - : This directory is where configuration settings for your environments live. Rails includes three environments by default: , , and . - : Database configuration settings live in this file, which is broken into four
. - : This directory contains your application's configuration settings: -
, and

  sections: default, development, production, and test. Thanks to the Gemfile that came with the rails new command, which included the sqli te3 gem, our config/database.yml file has its adapter parameter set to sq
already, specifying that we will use an SQLite database with this application. - db: This folder includes a directory for database migrations called migrate, along with the schema.rb and seeds.rb files. schema.db contains information about your database, while seeds.rb is where you can place seed data for the database.
Finally, Rails runs the bundle install command to install the dependencies listed in your Gemfile .
Once everything is set up, navigate to the sharkapp directory:
You can now start the Rails server to ensure that your application is working, using the rails server command. If you are working on your local machine, type:
Rails binds to localhost by default, so you can now access your application by navigating your browser to locahost:3000, where you will see the following image:
             lite3
            cd sharkapp
    rails server
  
   If you are working on a development server, first ensure that connections are allowed on port 3000 :
Then start the server with the --binding flag, to bind to your server IP:
Navigate to http://your_server_ip:3000 in your browser, where you will see the Rails welcome message.
Once you have looked around, you can stop the server with CTRL+C .
   sudo ufw allow 3000
   rails server --binding=your_server_ip
   Rails Landing Page
  
 With your application created and in place, you are ready to start building from the Rails boilerplate to create a unique application.
