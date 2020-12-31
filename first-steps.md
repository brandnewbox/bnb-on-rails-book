Step 4 — Creating the Application Root View and Testing Functionality
Ideally, you want the landing page of your application to map to the application's root, so users can immediately get a sense of the application's purpose.
There are a number of ways you could handle this: for example, you could create a Welcome controller and an associated index view, which would
                 <%= form.submit %>
  </div>
<% end %>
  
 index
   config/route
   config/routes.rb                 nano
  nano config/routes.rb
 ~/sharkapp/config/routes.rb
  Rails.application.routes.draw do
  resources :sharks
  # For details on the DSL available within this file, see htt
p://guides.rubyonrails.org/routing.html
end
   http://localh
     ost:3000
 http://your_server_ip:3000
 index
  give users a generic landing page that could also link out to different parts of the application. In our case, however, having users land on our
sharks view will be enough of an introduction to the application's purpose for now.
To set this up, you will need to modify the routing settings in
s.rb to specify the root of the application.
Open for editing, using or your favorite editor:
The file will look like this:
 Without setting something more specific, the default view at
or will be the default Rails
welcome page.
In order to map the root view of the application to the view of the sharks controller, you will need to add the following line to the file:

    Now, when users navigate to your application root, they will see a full listing of sharks, and have the opportunity to create a new shark entry, look at existing entries, and edit or delete given entries.
Save the file and exit your editor when you are finished editing. If you used nano to edit the file, you can do so by pressing CTRL+X , Y , then ENTER
You can now run your migrations with the following command:
You will see output confirming the migration.
Start your Rails server once again. If you are working locally, type:
On a development server, type:
     rails db:migrate
  rails s
~/sharkapp/config/routes.rb
 Rails.application.routes.draw do
  resources :sharks
  root 'sharks#index'
  # For details on the DSL available within this file, see htt
p://guides.rubyonrails.org/routing.html
end
 
 rails s --binding=your_server_ip
    localhost:3000
http://your_se
   rver_ip:3000
     Application Landing Page
  sharks/new
  Navigate to if you are working locally, or
if you are working on a development server. Your application landing page will look like this:
To create a new shark, click on the New Shark link at the bottom of the page, which will take you to the route:

    Let's add some demo information to test our application. Input “Great White” into the Name field and “Scary” into the Facts field:
 Create New Shark
 
   Click on the Create button to create the shark.
This will direct you to the show route, which, thanks to the before_action filter, is set with the set_shark method, which grabs the id of the shark we've just created:
    Add Great White Shark
  
  ~/sharkapp/app/controllers/sharks_controller.rb
   class SharksController < ApplicationController
before_action :set_shark, only: [:show, :edit, :update, :des
troy] .. .
def show end
.. .
  private
    # Use callbacks to share common setup or constraints betwe
en actions.
    def set_shark
@shark = Shark.find(params[:id]) end
.. .

   You can test the edit function now by clicking Edit on your shark entry. This will take you to the edit route for that shark:
  Show Shark
   
  Change the facts about the Great White to read “Large” instead of “Scary” and click Update Shark. This will take you back to the show route:
    Edit Shark
   
  Finally, clicking Back will take you to your updated index view:
    New Index View
  Updated Shark
   
Now that you have tested your application's basic functionality, you can add some validations and security checks to make everything more secure.
Step 5 — Adding Validations
Your shark application can accept input from users, but imagine a case where a user attempts to create a shark without adding facts to it, or creates an entry for a shark that's already in the database. You can create mechanisms to check data before it gets entered into the database by adding validations to your models. Since your application's logic is located in its models, validating data input here makes more sense than doing so elsewhere in the application.
Note that we will not cover writing validation tests in this tutorial, but you can find out more about testing by consulting the Rails documentation.
If you haven't stopped the server yet, go ahead and do that by typing
C.
Open your shark.rb model file:
Currently, the file tells us that the Shark class inherits from cord , which in turn inherits from ActiveRecord::Base:
   CTRL+
    nano app/models/shark.rb
    ApplicationRe
      
  Let's first add some validations to our name field to confirm that the field is filled out and that the entry is unique, preventing duplicate entries:
  ~/sharkapp/app/models/shark.rb
  class Shark < ApplicationRecord
  validates :name, presence: true, uniqueness: true
end
Next, add a validation for the facts field to ensure that it, too, is filled out:
  ~/sharkapp/app/models/shark.rb
  class Shark < ApplicationRecord
validates :name, presence: true, uniqueness: true validates :facts, presence: true
end
We are less concerned here with the uniqueness of the facts, as long as they are associated with unique shark entries.
Save and close the file when you are finished.
~/sharkapp/app/models/shark.rb
 class Shark < ApplicationRecord
end
  
    rails s
http://localhost:3000
rails s --binding=
    your_server_ip
     http://yo
  ur_server_ip:3000
     Unique Validation Warning
   Start up your server once again with either or
, depending on whether you are working locally or with a development server.
Navigate to your application's root at or .
Click on New Shark. In the form, add “Great White” to the Name field and “Big Teeth” to the Facts field, and then click on Create Shark. You should see the following warning:
Now, let's see if we can check our other validation. Click Back to return to the homepage, and then New Shark once again. In the new form, enter “Tiger Shark” in the Name field, and leave Facts blank. Clicking Create Shark will trigger the following warning:

  With these changes, your application has some validations in place to ensure consistency in the data that's saved to the database. Now you can turn your attention to your application's users and defining who can modify application data.
Step 6 — Adding Authentication
With validations in place, we have some guarantees about the data that's being saved to the database. But what about users? If we don't want any and all users adding to the database, then we should add some authentication measures to ensure that only permitted users can add sharks. In order to do this, we'll use the http_basic_authenticate_with method, which will allow us to create a username and password combination to authenticate users.
   Fact Presence Warning
   
 There are a number of ways to authenticate users with Rails, including working with the bcrypt or devise gems. For now, however, we will add a method to our application controller that will apply to actions across our application. This will be useful if we add more controllers to the application in the future.
Stop your server again with CTRL+C .
Open the file that defines your ApplicationController :
Inside, you will see the definition for the ApplicationController class, which the other controllers in your application inherit from:
      nano app/controllers/application_controller.rb
  ~/sharkapp/app/controllers/application_controlle
r.rb
  class ApplicationController < ActionController::Base
end
To authenticate users, we'll use a hardcoded username and password with the http_basic_authenticate_with method. Add the following code to the file:
  
  In addition to supplying the username and password here, we've also restricted authentication by specifying the routes where it should not be required: index and show . Another way of accomplishing this would have been to write only: [:create, :update, :destroy]. This way, all users will be able to look at all of the sharks and read facts about particular sharks. When it comes to modifying site content, however, users will need to prove that they have access.
In a more robust setup, you would not want to hardcode values in this way, but for the purposes of demonstration, this will allow you to see how you can include authentication for your application's routes. It also lets you see how Rails stores session data by default in cookies: once you authenticate on a specified action, you will not be required to authenticate again in the same session.
Save and close app/controllers/application_controller.rb when you are finished editing. You can now test authentication in action.
Start the server with either rails s or rails s --binding=your_server_ip and navigate to your application at either http://localhost:3000 or htt
       ~/sharkapp/app/controllers/application_controlle
r.rb
  class ApplicationController < ActionController::Base
http_basic_authenticate_with name: 'sammy', password: 'shar k', except: [:index, :show]
end
  
 p://your_server_ip:3000
     User Authentication
   app/con
   trollers/application_controller.rb
   .
On the landing page, click on the New Shark button. This will trigger the following authentication window:
If you enter the username and password combination you added to
, you will be able to securely create a
new shark.
You now have a working shark application, complete with data validations and a basic authentication scheme.
Conclusion
The Rails application you created in this tutorial is a jumping off point that you can use for further development. If you are interested in exploring the Rails ecosystem, the project documentation is a great place to start.
You can also learn more about adding nested resources to your project by reading How To Create Nested Resources for a Ruby on Rails Application,
  
 which will show you how to build out your application's models and routes.
Additionally, you might want to explore how to set up a more robust frontend for your project with a framework such as React. How To Set Up a Ruby on Rails Project with a React Frontend offers guidance on how to do this.
If you would like to explore different database options, you can also check out How To Use PostgreSQL with Your Ruby on Rails Application on Ubuntu 18.04, which walks through how to work with PostgreSQL instead of SQLite. You can also consult our library of PostgreSQL tutorials to learn more about working with this database.
       
How To Create Nested Resources for a Ruby on Rails Application
Written by Kathleen Juell
Ruby on Rails is a web application framework written in Ruby that offers developers an opinionated approach to application development. Working with Rails gives developers: - Conventions for handling things like routing, stateful data, and asset management. - A firm grounding in the model-view- controller (MCV) architectural pattern, which separates an application's logic, located in models, from the presentation and routing of application information.
As you add complexity to your Rails applications, you will likely work with multiple models, which represent your application's business logic and interface with your database. Adding related models means establishing meaningful relationships between them, which then affect how information gets relayed through your application's controllers, and how it is captured and presented back to users through views.
In this tutorial, you will build on an existing Rails application that offers users facts about sharks. This application already has a model for handling shark data, but you will add a nested resource for posts about individual sharks. This will allow users to build out a wider body of thoughts and opinions about individual sharks.
Prerequisites
      
  Shark Post
  Shark
    Post
rails genera
   te scaffold
   To follow this tutorial, you will need: - A local machine or development server running Ubuntu 18.04. Your development machine should have a non-root user with administrative privileges and a firewall configured with
ufw . For instructions on how to set this up, see our Initial Server Setup with Ubuntu 18.04 tutorial. - Node.js and npm installed on your local machine or
development server. This tutorial uses Node.js version <>10.16.3<> and npm version <>6.9.0<>. For guidance on installing Node.js and npm on Ubuntu 18.04, follow the instructions in the “Installing Using a PPA” section of How To Install Node.js on Ubuntu 18.04. - Ruby, rbenv, and Rails installed on your local machine or development server, following Steps 1-4 in How To Install Ruby on Rails with rbenv on Ubuntu 18.04. This tutorial uses Ruby <>2.5.1<>, rbenv <>1.1.2<>, and Rails <>5.2.3<>. - SQLite installed, and a basic shark information application created, following the directions in How To Build a Ruby on Rails Application.
Step 1 — Scaffolding the Nested Model
Our application will take advantage of Active Record associations to build out a relationship between and models: posts will belong to particular sharks, and each shark can have multiple posts. Our and P ost models will therefore be related through belongs_to and has_many associations.
The first step to building out the application in this way will be to create a
model and related resources. To do this, we can use the
command, which will give us a model, a database migration to alter the database schema, a controller, a full set of views to manage
            
  standard Create, Read, Update, and Delete (CRUD) operations, and templates for partials, helpers, and tests. We will need to modify these resources, but using the scaffold command will save us some time and energy since it generates a structure we can use as a starting point.
First, make sure that you are in the sharkapp directory for the Rails project that you created in the prerequisites:
Create your Post resources with the following command:
With body:text, we're telling Rails to include a body field in the posts database table — the table that maps to the Post model. We're also including the :references keyword, which sets up an association between the Shark and Post models. Specifically, this will ensure that a foreign key representing each shark entry in the sharks database is added to the posts database.
Once you have run the command, you will see output confirming the resources that Rails has generated for the application. Before moving on, you can check your database migration file to look at the relationship that now exists between your models and database tables. Use the following command to look at the contents of the file, making sure to substitute the timestamp on your own migration file for what's shown here:
     cd sharkapp
   rails generate scaffold Post body:text shark:references
          
   You will see the following output:
 Output
  class CreatePosts < ActiveRecord::Migration[5.2]
  def change
create_table :posts do |t|
t.text :body
t.references :shark, foreign_key: true
      t.timestamps
    end
end end
As you can see, the table includes a column for a shark foreign key. This key will take the form of model_name_id — in our case, shark_id .
Rails has established the relationship between the models elsewhere as well. Take a look at the newly generated Post model with the following command:
     cat app/models/post.rb
cat db/migrate/20190805132506_create_posts.rb
 
  The belongs_to association sets up a relationship between models in which a single instance of the declaring model belongs to a single instance of the named model. In the case of our application, this means that a single post belongs to a single shark.
In addition to setting this relationship, the rails generate scaffold command also created routes and views for posts, as it did for our shark resources in Step 3 of How To Build a Ruby on Rails Application.
This is a useful start, but we will need to configure some additional routing and solidify the Active Record association for the Shark model in order for the relationship between our models and routes to work as desired.
Step 2 — Specifying Nested Routes and Associations for the Parent Model
Rails has already set the belongs_to association in our Post model, thanks to the :references keyword in the rails generate scaffold command,
but in order for that relationship to function properly we will need to specify a has_many association in our Shark model as well. We will also need to make changes to the default routing that Rails gave us in order to make post resources the children of shark resources.
           Output
 class Post < ApplicationRecord
  belongs_to :shark
end
  
     app/models/sha
 has_many Shark nano
    rk.rb
   nano app/models/shark.rb
 ~/sharkapp/app/models/shark.rb
  class Shark < ApplicationRecord
  has_many :posts
  validates :name, presence: true, uniqueness: true
  validates :facts, presence: true
end
 dependent
 destroy
  To add the association to the model, open using or your favorite editor:
Add the following line to the file to establish the relationship between sharks and posts:
One thing that is worth thinking about here is what happens to posts once a particular shark is deleted. We likely do not want the posts associated with a deleted shark persisting in the database. To ensure that any posts associated with a given shark are eliminated when that shark is deleted, we can include the option with the association.
Add the following code to the file to ensure that the action on a given shark deletes any associated posts:

   Once you have finished making these changes, save and close the file. If you are using nano , you can do this by pressing CTRL+X , Y , then ENTER .
Next, open your config/routes.rb file to modify the relationship between your resourceful routes:
Currently, the file looks like this:
      nano config/routes.rb
 ~/sharkapp/config/routes.rb
  Rails.application.routes.draw do
  resources :posts
  resources :sharks
  root 'sharks#index'
  # For details on the DSL available within this file, see htt
p://guides.rubyonrails.org/routing.html
end
~/sharkapp/app/models/shark.rb
 class Shark < ApplicationRecord
has_many :posts , dependent: :destroy
validates :name, presence: true, uniqueness: true validates :facts, presence: true
end
 
 The current code establishes an independent relationship between our routes, when what we would like to express is a dependent relationship between sharks and their associated posts.
Let's update our route declaration to make :sharks the parent of :posts. Update the code in the file to look like the following:
    ~/sharkapp/config/routes.rb
  Rails.application.routes.draw do resources :sharks do
resources :posts end
  root 'sharks#index'
  # For details on the DSL available within this file, see htt
p://guides.rubyonrails.org/routing.html
end
Save and close the file when you are finished editing.
With these changes in place, you can move on to updating your posts controller.
Step 3 — Updating the Posts Controller
The association between our models gives us methods that we can use to create new post instances associated with particular sharks. To use these methods, we will need to add them our posts controller.
 
 Open the posts controller file:
Currently, the file looks like this:
  nano app/controllers/posts_controller.rb
 
~/sharkapp/controllers/posts_controller.rb
   class PostsController < ApplicationController
  before_action :set_post, only: [:show, :edit, :update, :dest
roy]
  # GET /posts
  # GET /posts.json
  def index
    @posts = Post.all
  end
  # GET /posts/1
  # GET /posts/1.json
  def show
  end
  # GET /posts/new
  def new
    @post = Post.new
  end
  # GET /posts/1/edit
  def edit
  end
# POST /posts
 
   # POST /posts.json
  def create
    @post = Post.new(post_params)
    respond_to do |format|
      if @post.save
        format.html { redirect_to @post, notice: 'Post was suc
cessfully created.' }
        format.json { render :show, status: :created, locatio
n: @post }
      else
        format.html { render :new }
        format.json { render json: @post.errors, status: :unpr
ocessable_entity }
      end
end end
  # PATCH/PUT /posts/1
  # PATCH/PUT /posts/1.json
  def update
    respond_to do |format|
      if @post.update(post_params)
        format.html { redirect_to @post, notice: 'Post was suc
cessfully updated.' }
st }
format.json { render :show, status: :ok, location: @po
 
       else
        format.html { render :edit }
        format.json { render json: @post.errors, status: :unpr
ocessable_entity }
      end
end end
  # DELETE /posts/1
  # DELETE /posts/1.json
  def destroy
    @post.destroy
    respond_to do |format|
      format.html { redirect_to posts_url, notice: 'Post was s
uccessfully destroyed.' }
      format.json { head :no_content }
    end
end
  private
    # Use callbacks to share common setup or constraints betwe
en actions.
    def set_post
      @post = Post.find(params[:id])
    end
    # Never trust parameters from the scary internet, only all
 
Like our sharks controller, this controller's methods work with instances of the associated Post class. For example, the new method creates a new instance of the Post class, the index method grabs all instances of the class, and the set_post method uses find and params to select a particular post by id . If, however, we want our post instances to be associated with particular shark instances, then we will need to modify this code, since the
Post class is currently operating as an independent entity.
Our modifications will make use of two things: - The methods that became available to us when we added the belongs_to and has_many associations to our models. Specifically, we now have access to the build method thanks to the has_many association we defined in our Shark model. This method will allow us to create a collection of post objects associated with a particular shark object, using the shark_id foreign key that exists in our po sts database. - The routes and routing helpers that became available when we created a nested posts route. For a full list of example routes that become available when you create nested relationships between resources, see the Rails documentation. For now, it will be enough for us to know that for each specific shark — say sharks/1 — there will be an associated route for posts related to that shark: sharks/1/posts . There will also be routing
                   ow the white list through.
    def post_params
      params.require(:post).permit(:body, :shark_id)
    end
end
    
helpers like shark_posts_path(@shark) and rk) that refer to these nested routes.
In the file, we'll begin by writing a method, get_shark , that will run before each action in the controller. This method will create a local @shark instance variable by finding a shark instance by shark_id. With this variable available to us in the file, it will be possible to relate posts to a specific shark in the other methods.
Above the other private methods at the bottom of the file, add the following method:
       ~/sharkapp/controllers/posts_controller.rb
  .. . private
  def get_shark
    @shark = Shark.find(params[:shark_id])
end
  # Use callbacks to share common setup or constraints between
actions.
.. .
Next, add the corresponding filter to the top of the file, before the existing filter:
   edit_sharks_posts_path(@sha
   
  This will ensure that get_shark runs before each action defined in the file.
Next, you can use this @shark instance to rewrite the index method. Instead of grabbing all instances of the Post class, we want this method to return all post instances associated with a particular shark instance.
Modify the index method to look like this:
      ~/sharkapp/controllers/posts_controller.rb
  .. .
def index
@posts = @shark.posts end
.. .
The new method will need a similar revision, since we want a new post instance to be associated with a particular shark. To achieve this, we can make use of the build method, along with our local @shark instance variable.
Change the new method to look like this:
   ~/sharkapp/controllers/posts_controller.rb
 class PostsController < ApplicationController
  before_action :get_shark
  
  This method creates a post object that's associated with the specific shark instance from the get_shark method.
Next, we'll address the method that's most closely tied to new : create . The create method does two things: it builds a new post instance using the parameters that users have entered into the new form, and, if there are no errors, it saves that instance and uses a route helper to redirect users to
where they can see the new post. In the case of errors, it renders the new template again.
Update the create method to look like this:
     ~/sharkapp/controllers/posts_controller.rb
 .. .
def new
@post = @shark.posts.build end
.. .
  
~/sharkapp/controllers/posts_controller.rb
   def create
@post = @shark.posts.build(post_params)
        respond_to do |format|
         if @post.save
format.html { redirect_to shark_posts_path(@shark) , notice: 'Post was successfully created.' }
            format.json { render :show, status: :created, loca
tion: @post }
         else
            format.html { render :new }
            format.json { render json: @post.errors, status: :
unprocessable_entity }
      end
end end
  update @post
  efore_action
    Next, take a look at the method. This method uses a instance variable, which is not explicitly set in the method itself. Where does this variable come from?
Take a look at the filters at the top of the file. The second, auto-generated b filter provides an answer:
 
  The update method (like show , edit , and destroy ) takes a @post variable from the set_post method. That method, listed under the get_shark method with our other private methods, currently looks like this:
         ~/sharkapp/controllers/posts_controller.rb
  .. . private .. .
  def set_post
    @post = Post.find(params[:id])
end .. .
In keeping with the methods we've used elsewhere in the file, we will need to modify this method so that @post refers to a particular instance in the collection of posts that's associated with a particular shark. Keep the build method in mind here — thanks to the associations between our models, and the methods (like build) that are available to us by virtue of those
   ~/sharkapp/controllers/posts_controller.rb
 class PostsController < ApplicationController
  before_action :get_shark
  before_action :set_post, only: [:show, :edit, :update, :dest
roy] .. .
  
 associations, each of our post instances is part of a collection of objects that's associated with a particular shark. So it makes sense that when querying for a particular post, we would query the collection of posts associated with a particular shark.
Update set_post to look like this:
  ~/sharkapp/controllers/posts_controller.rb
  .. . private .. .
def set_post
@post = @shark.posts.find(params[:id])
end .. .
Instead of finding a particular instance of the entire Post class by id , we instead search for a matching id in the collection of posts associated with a particular shark.
With that method updated, we can look at the update and destroy methods.
The update method makes use of the @post instance variable from
st , and uses it with the post_params that the user has entered in the edit form. In the case of success, we want Rails to send the user back to the ind
          set_po
   
  edit
 redirect_to
  shark_post
    _path(@shark)
  index
 ~/sharkapp/controllers/posts_controller.rb
  .. .
def update
    respond_to do |format|
      if @post.update(post_params)
format.html { redirect_to shark_post_path(@shark), not ice: 'Post was successfully updated.' }
        format.json { render :show, status: :ok, location: @po
st }
else
        format.html { render :edit }
        format.json { render json: @post.errors, status: :unpr
ocessable_entity }
end end
end .. .
 ex view of the posts associated with a particular shark. In the case of errors, Rails will render the template again.
In this case, the only change we will need to make is to the statement, to handle successful updates. Update it to redirect to
shark's posts:
, which will redirect to the view of the selected

  Next, we will make a similar change to the destroy method. Update the re method to redirect requests to shark_posts_path(@shark) in the
case of success:
      direct_to
  ~/sharkapp/controllers/posts_controller.rb
  .. .
def destroy
    @post.destroy
     respond_to do |format|
format.html { redirect_to shark_posts_path(@shark), noti ce: 'Post was successfully destroyed.' }
      format.json { head :no_content }
    end
end .. .
This is the last change we will make. You now have a posts controller file that looks like this:

~/sharkapp/controllers/posts_controller.rb
   class PostsController < ApplicationController
  before_action :get_shark
  before_action :set_post, only: [:show, :edit, :update, :dest
roy]
  # GET /posts
  # GET /posts.json
  def index
    @posts = @shark.posts
  end
  # GET /posts/1
  # GET /posts/1.json
  def show
  end
  # GET /posts/new
  def new
    @post = @shark.posts.build
  end
  # GET /posts/1/edit
  def edit
  end
 
   # POST /posts
  # POST /posts.json
  def create
    @post = @shark.posts.build(post_params)
        respond_to do |format|
         if @post.save
            format.html { redirect_to shark_posts_path(@shar
k), notice: 'Post was successfully created.' }
            format.json { render :show, status: :created, loca
tion: @post }
         else
            format.html { render :new }
            format.json { render json: @post.errors, status: :
unprocessable_entity }
      end
end end
  # PATCH/PUT /posts/1
  # PATCH/PUT /posts/1.json
  def update
    respond_to do |format|
      if @post.update(post_params)
        format.html { redirect_to shark_post_path(@shark), not
ice: 'Post was successfully updated.' }
        format.json { render :show, status: :ok, location: @po
 
 st }
else
        format.html { render :edit }
        format.json { render json: @post.errors, status: :unpr
ocessable_entity }
end end
end
  # DELETE /posts/1
  # DELETE /posts/1.json
  def destroy
    @post.destroy
    respond_to do |format|
      format.html { redirect_to shark_posts_path(@shark), noti
ce: 'Post was successfully destroyed.' }
      format.json { head :no_content }
    end
end private
   def get_shark
     @shark = Shark.find(params[:shark_id])
   end
    # Use callbacks to share common setup or constraints betwe
en actions.
 
 The controller manages how information is passed from the view templates to the database and vice versa. Our controller now reflects the relationship between our Shark and Post models, in which posts are associated with particular sharks. We can move on to modifying the view templates themselves, which are where users will pass in and modify post information about particular sharks.
Step 4 — Modifying Views
Our view template revisions will involve changing the templates that relate to posts, and also modifying our sharks show view, since we want users to see the posts associated with particular sharks.
Let's start with the foundational template for our posts: the form partial that is reused across multiple post templates. Open that form now:
         def set_post
      @post = @shark.posts.find(params[:id])
end
    # Never trust parameters from the scary internet, only all
ow the white list through.
    def post_params
      params.require(:post).permit(:body, :shark_id)
end end
  
   Rather than passing only the post model to the form_with form helper, we will pass both the shark and post models, with post set as a child resource.
Change the first line of the file to look like this, reflecting the relationship between our shark and post resources:
      ~/sharkapp/views/posts/_form.html.erb
  <%= form_with(model: [@shark, post], local: true) do |form| %> .. .
Next, delete the section that lists the shark_id of the related shark, since this is not essential information in the view.
The finished form, complete with our edits to the first line and without the deleted shark_id section, will look like this:
  nano app/views/posts/_form.html.erb
 
  Save and close the file when you are finished editing.
~/sharkapp/views/posts/_form.html.erb
 <%= form_with(model: [@shark, post], local: true) do |form| %>
  <% if post.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(post.errors.count, "error") %> prohibi
ted this post from being saved:</h2>
      <ul>
      <% post.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
      </ul>
    </div>
  <% end %>
  <div class="field">
    <%= form.label :body %>
    <%= form.text_area :body %>
</div>
  <div class="actions">
    <%= form.submit %>
  </div>
<% end %>
 
  Next, open the index view, which will show the posts associated with a particular shark:
Thanks to the rails generate scaffold command, Rails has generated the better part of the template, complete with a table that shows the body field of each post and its associated shark .
Much like the other code we have already modified, however, this template treats posts as independent entities, when we would like to make use of the associations between our models and the collections and helper methods that these associations give us.
In the body of the table, make the following updates:
First, update post.shark to post.shark.name , so that the table will include the name field of the associated shark, rather than identifying information about the shark object itself:
   nano app/views/posts/index.html.erb
     
  Next, change the Show redirect to direct users to the show view for the associated shark, since they will most likely want a way to navigate back to the original shark. We can make use of the @shark instance variable that we set in the controller here, since Rails makes instance variables created in the controller available to all views. We'll also change the text for the link from
Show to Show Shark , so that users will better understand its function. Update the this line to the following:
       ~/sharkapp/app/views/posts/index.html.erb
 .. . <tbody>
    <% @posts.each do |post| %>
      <tr>
        <td><%= post.body %></td>
<td><%= post.shark.name %></td> .. .
~/sharkapp/app/views/posts/index.html.erb
 .. . <tbody>
    <% @posts.each do |post| %>
      <tr>
<td><%= post.body %></td>
<td><%= post.shark.name %></td>
<td><%= link_to 'Show Shark', [@shark] %></td>
  
 sts/post_id/edit
  sharks/shark_id/posts/post_i
      d/edit
  Edit
shark_post_path
  ~/sharkapp/app/views/posts/index.html.erb
  .. . <tbody>
    <% @posts.each do |post| %>
      <tr>
<td><%= post.body %></td>
<td><%= post.shark.name %></td>
<td><%= link_to 'Show Shark', [@shark] %></td>
<td><%= link_to 'Edit Post', edit_shark_post_path(@sha
rk, post) %></td>
            Destroy
shark     post
    In the next line, we want to ensure that users are routed the right nested path when they go to edit a post. This means that rather than being directed to po
, users will be directed to
. To do this, we'll use the routing helper and our
models, which Rails will treat as URLs. We'll also update the link text to make its function clearer.
Update the line to look like the following:
 Next, let's add a similar change to the link, updating its function in the string, and adding our and resources:

 ~/sharkapp/app/views/posts/index.html.erb
  .. . <tbody>
    <% @posts.each do |post| %>
      <tr>
        <td><%= post.body %></td>
        <td><%= post.shark.name %></td>
        <td><%= link_to 'Show Shark', [@shark] %></td>
        <td><%= link_to 'Edit Post', edit_shark_post_path(@sha
rk, post) %></td>
<td><%= link_to 'Destroy Post', [@shark, post], metho
d: :delete, data: { confirm: 'Are you sure?' } %></td>
 New Post
   new_shark_post_pat
    h(@shark)
  ~/sharkapp/app/views/posts/index.html.erb
  .. .
<%= link_to 'New Post', new_shark_post_path(@shark) %>
   Finally, at the bottom of the form, we will want to update the path to take users to the appropriate nested path when they want to create a new post. Update the last line of the file to make use of the
routing helper:
The finished file will look like this:

~/sharkapp/app/views/posts/index.html.erb
   <p id="notice"><%= notice %></p>
<h1>Posts</h1>
<table>
  <thead>
    <tr>
      <th>Body</th>
      <th>Shark</th>
      <th colspan="3"></th>
    </tr>
  </thead>
  <tbody>
    <% @posts.each do |post| %>
      <tr>
        <td><%= post.body %></td>
        <td><%= post.shark.name %></td>
        <td><%= link_to 'Show Shark', [@shark] %></td>
        <td><%= link_to 'Edit Post', edit_shark_post_path(@sha
rk, post) %></td>
        <td><%= link_to 'Destroy Post', [@shark, post], metho
d: :delete, data: { confirm: 'Are you sure?' } %></td>
      </tr>
<% end %>
 
   </tbody>
</table>
<br>
<%= link_to 'New Post', new_shark_post_path(@shark) %>
  form link_to
                         form
app/views/posts/new.html.erb
link_to
     nano app/views/posts/new.html.erb
   hark_posts_path(@shark)
   ~/sharkapp/app/views/posts/new.html.erb
  .. .
<%= link_to 'Back', shark_posts_path(@shark) %>
 Save and close the file when you are finished editing.
The other edits we will make to post views won't be as numerous, since our other views use the partial we have already edited. However, we will want to update the references in the other post templates to reflect the changes we have made to our partial.
Open :
Update the reference at the bottom of the file to make use of the s helper:
 Save and close the file when you are finished making this change.

 Next, open the edit template:
In addition to the Back path, we'll update Show to reflect our nested resources. Change the last two lines of the file to look like this:
   nano app/views/posts/edit.html.erb
   ~/sharkapp/app/views/posts/edit.html.erb
  .. .
<%= link_to 'Show', [@shark, @post] %> |
<%= link_to 'Back', shark_posts_path(@shark) %>
Save and close the file.
Next, open the show template:
Make the following edits to the Edit and Back paths at the bottom of the file:
   nano app/views/posts/show.html.erb
   ~/sharkapp/app/views/posts/edit.html.erb
  .. .
<%= link_to 'Edit', edit_shark_post_path(@shark, @post) %> | <%= link_to 'Back', shark_posts_path(@shark) %>
Save and close the file when you are finished.

  As a final step, we will want to update the show view for our sharks so that posts are visible for individual sharks. Open that file now:
Our edits here will include adding a Posts section to the form and an Add Post link at the bottom of the file.
Below the Facts for a given shark, we will add a new section that iterates through each instance in the collection of posts associated with this shark, outputting the body of each post.
Add the following code below the Facts section of the form, and above the redirects at the bottom of the file:
   nano app/views/sharks/show.html.erb
      
  Next, add a new redirect to allow users to add a new post for this particular shark:
 ~/sharkapp/app/views/sharks/show.html.erb
  .. .
<%= link_to 'Edit', edit_shark_path(@shark) %> |
<%= link_to 'Add Post', shark_posts_path(@shark) %> | <%= link_to 'Back', sharks_path %>
~/sharkapp/app/views/sharks/show.html.erb
 .. . <p>
  <strong>Facts:</strong>
  <%= @shark.facts %>
</p>
<h2>Posts</h2>
<% for post in @shark.posts %>
    <ul>
      <li><%= post.body %></li>
  </ul>
<% end %>
<%= link_to 'Edit', edit_shark_path(@shark) %> | .. .
 
 Save and close the file when you are finished editing.
You have now made changes to your application's models, controllers, and views to ensure that posts are always associated with a particular shark. As a final step, we can add some validations to our Post model to guarantee consistency in the data that's saved to the database.