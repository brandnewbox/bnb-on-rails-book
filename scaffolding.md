## Step 3 — Scaffolding the Application
To create our library application, we will need to create a model to manage our application data, views to enable user interaction with that data, and a controller to manage communication between the model and the views. To build these things we will use the `rails generate scaffold` command, which will give us a model, a [database migration](https://guides.rubyonrails.org/active_record_migrations.html) to alter the database schema, a controller, a full set of views to manage [Create, Read, Update, and Delete](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete) (CRUD) operations for the application, and templates for partials, helpers, and tests.

Because the `generate scaffold` command does so much work for us, we'll take a closer look at the resources it creates to understand the work that Rails is doing under the hood.

Our `generate scaffold` command will include the name of our model and the fields we want in our database table. Rails uses [Active Record](https://github.com/rails/rails/tree/master/activerecord) to manage relationships between application data, constructed as objects with models, and the application database. Each of our models is a [Ruby class](https://ruby-doc.org/core-2.5.3/Class.html), while also inheriting from the `ActiveRecord::Base` class. This means that we can work with our model class in the same way that we would work with a Ruby class, while also pulling in methods from Active Record. Active Record will then ensure that each class is mapped to a table in our database, and each instance of that class to a row in that table.
         
Type the following command to generate a `Book` model, controller, and associated views:
```
dip rails generate scaffold Book
```
When you type this command, you will again see a long list of output that explains everything Rails is generating for you. The output below highlights some of the more significant things for our setup:

```
# Output
invoke  active_record
create    db/migrate/20210318152156_create_books.rb
create    app/models/book.rb
invoke    test_unit
create      test/models/book_test.rb
create      test/fixtures/books.yml
invoke  resource_route
route    resources :books
invoke  scaffold_controller
create    app/controllers/books_controller.rb
invoke    erb
create      app/views/books
create      app/views/books/index.html.erb
create      app/views/books/edit.html.erb
create      app/views/books/show.html.erb
create      app/views/books/new.html.erb
create      app/views/books/_form.html.erb
invoke    resource_route
invoke    test_unit
create      test/controllers/books_controller_test.rb
create      test/system/books_test.rb
invoke    helper
create      app/helpers/books_helper.rb
invoke      test_unit
invoke    jbuilder
create      app/views/books/index.json.jbuilder
create      app/views/books/show.json.jbuilder
create      app/views/books/_book.json.jbuilder
invoke  assets
invoke    scss
create      app/assets/stylesheets/books.scss
invoke  scss
create    app/assets/stylesheets/scaffolds.scss
```
Rails has created the model at `app/models/book.rb` and a database migration to go with it: `db/migrate/20210318152156_create_books.rb`. The timestamp on your migration file will differ from what you see here.

It has also created a controller, `app/controllers/book_controller.rb` , as well as the views associated with our application's CRUD operations, collected under `app/views/books`. Among these views is a partial, `_form.html.erb`, that contains code used across views.
 
Finally, Rails added a new resourceful route, `resources :books`, to
`config/routes.rb`. This enables the Rails router to match incoming HTTP requests with the `books` controller and its associated views.

Though Rails has done much of the work of building out our application code for us, it is worth taking a look at some files to understand what is happening.

First, let's look at the controller file with the following command:
```
cat app/controllers/books_controller.rb
```
```
# Output
class BooksController < ApplicationController
  before_action :set_book, only: %i[ show edit update destroy ]

  # GET /books or /books.json
  def index
    @books = Book.all
  end

  # GET /books/1 or /books/1.json
  def show
  end

  # GET /books/new
  def new
    @book = Book.new
  end

  # GET /books/1/edit
  def edit
  end

  # POST /books or /books.json
  def create
    @book = Book.new(book_params)

    respond_to do |format|
      if @book.save
        format.html { redirect_to @book, notice: "Book was successfully created." }
        format.json { render :show, status: :created, location: @book }
      else
        format.html { render :new, status: :unprocessable_entity }
        format.json { render json: @book.errors, status: :unprocessable_entity }
      end
    end
  end

  # PATCH/PUT /books/1 or /books/1.json
  def update
    respond_to do |format|
      if @book.update(book_params)
        format.html { redirect_to @book, notice: "Book was successfully updated." }
        format.json { render :show, status: :ok, location: @book }
      else
        format.html { render :edit, status: :unprocessable_entity }
        format.json { render json: @book.errors, status: :unprocessable_entity }
      end
    end
  end

  # DELETE /books/1 or /books/1.json
  def destroy
    @book.destroy
    respond_to do |format|
      format.html { redirect_to books_url, notice: "Book was successfully destroyed." }
      format.json { head :no_content }
    end
  end

  private
    # Use callbacks to share common setup or constraints between actions.
    def set_book
      @book = Book.find(params[:id])
    end

    # Only allow a list of trusted parameters through.
    def book_params
      params.fetch(:book, {})
    end
end
```
The controller is responsible for managing how information gets fetched and passed to its associated model, and how it gets associated with particular views. As you can see, our `books` controller includes a series of methods that map roughly to standard CRUD operations. However, there are more methods than CRUD functions, to enable efficiency in the case of errors.

For example, consider the create method:
   ow the white list through.
    def shark_params
      params.require(:shark).permit(:name, :facts)
    end
end
   
  If a new instance of the Shark class is successfully saved, redirect_to will spawn a new request that is then directed to the controller. This will be a GE T request, and it will be handled by the show method, which will show the user the shark they've just added.
   ~/sharkapp/app/controllers/sharks_controller.rb
 .. .
def create
    @shark = Shark.new(shark_params)
    respond_to do |format|
      if @shark.save
        format.html { redirect_to @shark, notice: 'Shark was s
uccessfully created.' }
        format.json { render :show, status: :created, locatio
n: @shark }
      else
        format.html { render :new }
        format.json { render json: @shark.errors, status: :unp
rocessable_entity }
      end
end end
.. .
 
If there is a failure, then Rails will render the
erb template again rather than making another request to the router, giving users another chance to submit their data.
In addition to the sharks controller, Rails has given us a template for an ind ex view, which maps to the index method in our controller. We will use this as the root view for our application, so it's worth taking a look at it.
Type the following to output the file:
    cat app/views/sharks/index.html.erb
   app/views/sharks/new.html.
     
Output
   <p id="notice"><%= notice %></p>
<h1>Sharks</h1>
<table>
  <thead>
    <tr>
      <th>Name</th>
      <th>Facts</th>
      <th colspan="3"></th>
    </tr>
  </thead>
  <tbody>
    <% @sharks.each do |shark| %>
      <tr>
        <td><%= shark.name %></td>
        <td><%= shark.facts %></td>
        <td><%= link_to 'Show', shark %></td>
        <td><%= link_to 'Edit', edit_shark_path(shark) %></td>
        <td><%= link_to 'Destroy', shark, method: :delete, dat
a: { confirm: 'Are you sure?' } %></td>
      </tr>
    <% end %>
  </tbody>
 
The index view iterates through the instances of our Shark class, which have been mapped to the sharks table in our database. Using ERB templating, the view outputs each field from the table that is associated with an individual shark instance: name and facts .
The view then uses the link_to helper to create a hyperlink, with the provided string as the text for the link and the provided path as the
destination. The paths themselves are made possible through the helpers that became available to us when we defined the sharks resourceful route with the rails generate scaffold command.
In addition to looking at our index view, we can also take a look at the new view to see how Rails uses partials in views. Type the following to output the app/views/sharks/new.html.erb template:
              cat app/views/sharks/new.html.erb
 </table>
<br>
<%= link_to 'New Shark', new_shark_path %>
   
    Though this template may look like it lacks input fields for a new shark entry, the reference to render 'form' tells us that the template is pulling in the _form.html.erb partial, which extracts code that is repeated across views.
Looking at that file will give us a full sense of how a new shark instance gets created:
    cat app/views/sharks/_form.html.erb
Output
 <h1>New Shark</h1>
<%= render 'form', shark: @shark %>
<%= link_to 'Back', sharks_path %>
 
Output
   <%= form_with(model: shark, local: true) do |form| %>
  <% if shark.errors.any? %>
    <div id="error_explanation">
      <h2><%= pluralize(shark.errors.count, "error") %> prohib
ited this shark from being saved:</h2>
      <ul>
      <% shark.errors.full_messages.each do |message| %>
        <li><%= message %></li>
      <% end %>
      </ul>
    </div>
  <% end %>
  <div class="field">
    <%= form.label :name %>
    <%= form.text_field :name %>
</div>
  <div class="field">
    <%= form.label :facts %>
    <%= form.text_area :facts %>
  </div>
  <div class="actions">
 
This template makes use of the form_with form helper. Form helpers are designed to facilitate the creation of new objects from user input using the fields and scope of particular models. Here, form_with takes model: shark as an argument, and the new form builder object that it creates has field inputs that correspond to the fields in the sharks table. Thus users have form fields to enter both a shark name and shark facts .
Submitting this form will create a JSON response with user data that the rest of your application can access by way of the params method, which creates a ActionController::Parameters object with that data.
Now that you know what rails generate scaffold has produced for you, you can move on to setting the root view for your application.
