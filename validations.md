Step 3 — Reorganizing Views with Partials
You have created a Post model and controller, so the last thing to think about from a Rails perspective will be the views that present and allow users to input information about sharks. Views are also the place where you will have a chance to build out interactivity with Stimulus.
In this step, you will map out your views and partials, which will be the starting point for your work with Stimulus.
The view that will act as the base for posts and all partials associated with posts is the sharks/show view.
Open the file:
Currently, the file looks like this:
    nano app/views/sharks/show.html.erb

   When we created our Post model, we opted not to generate views for our posts, since we will handle them through our sharks/show view. So in this view, the first thing we will address is how we will accept user input for new posts, and how we will present posts back to the user.
Note: For an alternative to this approach, please see How To Create Nested Resources for a Ruby on Rails Application, which sets up post views using the full range of Create, Read, Update, Delete (CRUD) methods defined in the posts controller. For a discussion of these methods and how they work, please see Step 3 of How To Build a Ruby on Rails Application.
       ~/sharkapp/app/views/sharks/show.html.erb
 <p id="notice"><%= notice %></p>
<p>
  <strong>Name:</strong>
  <%= @shark.name %>
</p>
<p>
  <strong>Facts:</strong>
  <%= @shark.facts %>
</p>
<%= link_to 'Edit', edit_shark_path(@shark) %> |
<%= link_to 'Back', sharks_path %>

 Instead of building all of our functionality into this view, we will use partials — reusable templates that serve a particular function. We will create one partial for new posts, and another to control how posts are displayed back to the user. Throughout, we'll be thinking about how and where we can use Stimulus to manipulate the appearance of posts on the page, since our goal is to control the presentation of posts with JavaScript.
First, below shark facts, add an <h2> header for posts and a line to render a partial called sharks/posts :
   ~/sharkapp/app/views/sharks/show.html.erb
  .. . <p>
  <strong>Facts:</strong>
  <%= @shark.facts %>
</p>
<h2>Posts</h2>
<%= render 'sharks/posts' %> .. .
This will render the partial with the form builder for new post objects.
Next, below the Edit and Back links, we will add a section to control the presentation of older posts on the page. Add the following lines to the file to render a partial called sharks/all :
    
  The <div> element will be useful when we start integrating Stimulus into this file.
Once you are finished making these edits, save and close the file. With the changes you've made on the Rails side, you can now move on to installing and integrating Stimulus into your application.
Step 4 — Installing Stimulus
The first step in using Stimulus will be to install and configure our application to work with it. This will include making sure we have the correct dependencies, including the Yarn package manager and Webpacker, the gem that will allow us to work with the JavaScript pre-processor and bundler webpack. With these dependencies in place, we will be able to install Stimulus and use JavaScript to manipulate events and elements in the DOM.
Let's begin by installing Yarn. First, update your package list:
    ~/sharkapp/app/views/sharks/show.html.erb
 <%= link_to 'Edit', edit_shark_path(@shark) %> |
<%= link_to 'Back', sharks_path %>
<div>
  <%= render 'sharks/all' %>
</div>
  
 Next, add the GPG key for the Debian Yarn repository:
Add the repository to your APT sources:
Update the package database with the newly added Yarn packages:
And finally, install Yarn:
With yarn installed, you can move on to adding the webpacker gem to your project.
Open your project's Gemfile, which lists the gem dependencies for your project:
Inside the file, you will see Turbolinks enabled by default:
 curl -sS https://dl.yarnpkg.com/debian/pubkey.gpg | sudo apt-k
ey add -
  echo "deb https://dl.yarnpkg.com/debian/ stable main" | sudo t
ee /etc/apt/sources.list.d/yarn.list
   sudo apt update
  sudo apt install yarn
    nano Gemfile
 sudo apt update
  
    ~/sharkapp/Gemfile
 .. .
# Turbolinks makes navigating your web application faster. Rea d more: https://github.com/turbolinks/turbolinks
gem 'turbolinks', '~> 5'
.. .
Turbolinks is designed to improve performance by optimizing page loads: instead of having link clicks navigate to a new page, Turbolinks intercepts these click events and makes the page request using Asynchronous JavaScript and HTML (AJAX). It then replaces the body of the current page and merges the contents of the <head> sections, while the JavaScript
w and document objects and the <html> element persist between renders. This addresses one of the main causes of slow page load times: the reloading of CSS and JavaScript resources.
We get Turbolinks by default in our Gemfile, but we will need to add the we gem so that we can install and use Stimulus. Below the
s gem, add webpacker :
      windo
        bpacker
    turbolink
    
 ~/sharkapp/Gemfile
    .. .
# Turbolinks makes navigating your web application faster. Rea d more: https://github.com/turbolinks/turbolinks
gem 'turbolinks', '~> 5'
gem 'webpacker', '~> 4.x'
.. .
Save and close the file when you are finished.
Next, add the gem to your project's bundle with the bundle command:
This will generate a new Gemfile.lock file — the definitive record of gems and versions for your project.
Next, install the gem in the context of your bundle with the following
command:
Once the installation is complete, we will need to make one small adjustment to our application's content security file. This is due to the fact that we are working with Rails 5.2+, which is a Content Security Policy (CSP) restricted environment, meaning that the only scripts allowed in the application must be from trusted sources.
   bundle
    bundl
    e exec
   bundle exec rails webpacker:install
  
Open config/initializers/content_security_policy.rb , which is the default file Rails gives us for defining application-wide security policies:
Add the following lines to the bottom of the file to allow
er — the server that serves our application's webpack bundle — as an allowed origin:
   nano config/initializers/content_security_policy.rb
  ~/sharkapp/config/initializers/content_security_
policy.rb
  .. .
Rails.application.config.content_security_policy do |policy|
  policy.connect_src :self, :https, 'http://localhost:3035',
 'ws://localhost:3035' if Rails.env.development?
end
This will ensure that the webpacker-dev-server is recognized as a trusted asset source.
Save and close the file when you are finished making this change.
By installing webpacker , you created two new directories in your project's app directory, the directory where your main application code is located. The new parent directory, app/javascript, will be where your project's
JavaScript code will live, and it will have the following structure:
      webpack-dev-serv
   
  Output
 ├── javascript
│   ├── controllers
│   │   ├── hello_controller.js
│   │   └── index.js
│ └── packs
│       └── application.js
   app/javascript
app/javas
  cript/packs
     app/javascr
   ipt/controllers
       dle exec
 app/javascript/packs
  app/javascript/contr
   ollers
   webpacker
  bundle exec rails webpacker:install:stimulus
  The directory will contain two child directories:
, which will have your webpack entry points, and
, where you will define your Stimulus controllers. The bun
command that we just used will create the
directory, but we will need to install Stimulus for the
directory to be autogenerated.
With installed, we can now install Stimulus with the following
command:
You will see output like the following, indicating that the installation was successful:
 
  Output
 .. .
success Saved lockfile.
success Saved 5 new dependencies. info Direct dependencies
└─ stimulus@1.1.1
info All dependencies
├─ @stimulus/core@1.1.1
├─ @stimulus/multimap@1.1.1
├─ @stimulus/mutation-observers@1.1.1 ├─ @stimulus/webpack-helpers@1.1.1
└─ stimulus@1.1.1
Done in 8.30s.
Webpacker now supports Stimulus.js 🎉
   app/views/layouts/applicatio
    n.html.erb
     webpacker
app/javascript/packs/applica
    tion.js
  nano app/views/layouts/application.html.erb
 We now have Stimulus installed, and the main directories we need to work with it in place. Before moving on to writing any code, we'll need to make a few application-level adjustments to complete the installation process.
First, we'll need to make an adjustment to
to ensure that our JavaScript code is available and that the code defined in our main entry point,
, runs each time a page is loaded. Open that file:
 
Change the following javascript_include_tag tag to g to load app/javascript/packs/application.js :
    ~/sharkapp/app/views/layouts/application.html.er
b
  .. .
<%= stylesheet_link_tag 'application', media: 'all', 'd
ata-turbolinks-track': 'reload' %>
<%= javascript_pack_tag 'application', 'data-turbolinks-tr
ack': 'reload' %> .. .
Save and close the file when you have made this change. Next, open app/javascript/packs/application.js :
Initially, the file will look like this:
   nano app/javascript/packs/application.js
 ~/sharkapp/app/javascript/packs/application.js
  .. .
console.log('Hello World from Webpacker')
import "controllers"
   javascript;pack_ta
   
 ~/sharkapp/app/javascript/packs/application.js
  .. .
import { Application } from "stimulus"
import { definitionsFromContext } from "stimulus/webpack-helpe rs"
const application = Application.start()
const context = require.context("../controllers", true, /\.js
$/)
application.load(definitionsFromContext(context))
 p/javascript/controllers
    show
   harks/posts
  sharks/all
 Delete the boilerplate code that's there, and add the following code to load your Stimulus controller files and boot the application instance:
This code uses webpack helper methods to require the controllers in the ap directory and load this context for use in your
application.
Save and close the file when you are finished editing.
You now have Stimulus installed and ready to use in your application. Next, we'll build out the partials that we referenced in our sharks view — s and — using Stimulus controllers, targets, and
actions.
Step 5 — Using Stimulus in Rails Partials
 
  Our sharks/posts partial will use the form_with form helper to create a new post object. It will also make use of Stimulus's three core concepts:
controllers, targets, and actions. These concepts work as follows: - Controllers are JavaScript classes that are defined in JavaScript modules and exported as the module's default object. Through controllers, you have access to particular HTML elements and the Stimulus Application instance defined in app/javascript/packs/application.js. - Targets allow you to reference particular HTML elements by name, and are associated with particular controllers. - Actions control how DOM events are handled by controllers, and are also associated with particular controllers. They create a connection between the HTML element associated with the controller, the methods defined in the controller, and a DOM event listener.
In our partial, we're first going to build a form as we normally would using Rails. We will then add a Stimulus controller, action, and targets to the form in order to use JavaScript to control how new posts get added to the page.
First, create a new file for the partial:
Inside the file, add the following code to create a new post object using the form_with helper:
      nano app/views/sharks/_posts.html.erb

     ~/sharkapp/app/views/sharks/_posts.html.erb
         <%= form_with model: [@shark, @shark.posts.build] do |
form| %>
ost here" %>
<%= form.text_area :body, placeholder: "Your p
<br>
        <%= form.submit %>
<% end %>
So far, this form behaves like a typical Rails form, using the form_with helper to build a post object with the fields defined for the Post model. Thus, the form has a field for the post :body , to which we've added a
with a prompt for filling in a post.
Additionally, the form is scoped to take advantage of the collection methods that come with the associations between the Shark and Post models. In this case, the new post object that's created from user-submitted data will belong to the collection of posts associated with the shark we're currently viewing.
Our goal now is to add some Stimulus controllers, events, and actions to control how the post data gets displayed on the page. The user will ultimately submit post data and see it posted to the page thanks to a Stimulus action.
First, we'll add a controller to the form called posts in a <div> element:
      place
    holder
     
   Make sure you add the closing <div> tag to scope the controller properly.
Next, we'll attach an action to the form that will be triggered by the form submit event. This action will control how user input is displayed on the page. It will reference an addPost method that we will define in the posts Stimulus controller:
   ~/sharkapp/app/views/sharks/_posts.html.erb
 <div data-controller="posts">
        <%= form_with model: [@shark, @shark.posts.build] do |
form| %>
                 <%= form.text_area :body, placeholder: "Your
 post here" %>
                 <br>
                 <%= form.submit %>
        <% end %>
</div>
~/sharkapp/app/views/sharks/_posts.html.erb
 <div data-controller="posts">
<%= form_with model: [@shark, @shark.posts.build], dat
a: { action: "posts#addBody" } do |form| %> .. .
                 <%= form.submit %>
        <% end %>
</div>
 
 We use the :data option with form_with to submit the Stimulus action as an additional HTML data attribute. The action itself has a value called an
action descriptor made up of the following: - The DOM event to listen for. Here, we are using the default event associated with form elements, submit, so we do not need to specify the event in the descriptor itself. For more information about common element/event pairs, see the Stimulus documentation. - The controller identifier, in our case posts. - The method that the event should invoke. In our case, this is the addBody method that we will define in the controller.
Next, we'll attach a data target to the user input defined in the
rea> element, since we will use this inputted value in the addBody method.
Add the following :data option to the <textarea> element:
              :body
  ~/sharkapp/app/views/sharks/_posts.html.erb
  <div data-controller="posts">
        <%= form_with model: [@shark, @shark.posts.build], dat
a: { action: "posts#addBody" } do |form| %>
                <%= form.text_area :body, placeholder: "Your p
ost here", data: { target: "posts.body" } %> .. .
Much like action descriptors, Stimulus targets have target descriptors, which include the controller identifier and the target name. In this case, pos ts is our controller, and body is the target itself.
:body
<texta
    
  As a last step, we'll add a data target for the inputted body values so that users will be able to see their posts as soon as they are submitted.
Add the following <ul> element with an add target below the form and above the closing <div> :
    ~/sharkapp/app/views/sharks/_posts.html.erb
  .. .
<% end %>
  <ul data-target="posts.add">
  </ul>
</div>
As with the body target, our target descriptor includes both the name of the controller and the target — in this case, add .
The finished partial will look like this:
 
   Once you have made these changes, you can save and close the file.
You have now created one of the two partials you added to the sharks/show view template. Next, you'll create the second, sharks/all , which will show all of the older posts from the database.
Create a new file named _all.html.erb in the app/views/sharks/ directory:
Add the following code to the file to iterate through the collection of posts associated with the selected shark:
      nano app/views/sharks/_all.html.erb
~/sharkapp/app/views/sharks/_posts.html.erb
 <div data-controller="posts">
        <%= form_with model: [@shark, @shark.posts.build], dat
a: { action: "posts#addBody"} do |form| %>
                <%= form.text_area :body, placeholder: "Your p
ost here", data: { target: "posts.body" } %>
                <br>
                <%= form.submit %>
        <% end %>
  <ul data-target="posts.add">
  </ul>
</div>
  
  This code uses a for loop to iterate through each post instance in the collection of post objects associated with a particular shark.
We can now add some Stimulus actions to this partial to control the appearance of posts on the page. Specifically, we will add actions that will control upvotes and whether or not posts are visible on the page
Before we do that, however, we will need to add a gem to our project so that we can work with Font Awesome icons, which we'll use to register upvotes. Open a second terminal window, and navigate to your sharkapp project directory.
Open your Gemfile:
   ~/sharkapp/app/views/sharks/_all.html.erb
 <% for post in @shark.posts  %>
    <ul>
        <li class="post">
            <%= post.body %>
</li>
    </ul>
    <% end %>
[environment second]
nano Gemfile
  
 webpacker
 ~/sharkapp/Gemfile
  [environment second]
.. .
gem 'webpacker', '~> 4.x'
gem 'font-awesome-rails', '~>4.x' .. .
 [environment second]
bundle install
   app/assets/stylesheets/a
    pplication.css
   [environment second]
nano app/assets/stylesheets/application.css
   Below your gem, add the following line to include the font-awe some-rails gem in the project:
  Save and close the file. Next, install the gem:
Finally, open your application's main stylesheet, :
Add the following line to include Font Awesome's styles in your project:

  Save and close the file. You can now close your second terminal window.
Back in your app/views/sharks/_all.html.erb partial, you can now add two button_tags with associated Stimulus actions, which will be triggered on click events. One button will give users the option to upvote a post and the other will give them the option to remove it from the page view.
Add the following code to app/views/sharks/_all.html.erb :
    ~/sharkapp/app/assets/stylesheets/application.cs
s
 [environment second] .. .
*
*= require_tree .
*= require_self
*= require font-awesome */
  
  Button tags also take a :data option, so we've added our posts Stimulus controller and two actions: remove and upvote. Once again, in the action
descriptors, we only need to define our controller and method, since the default event associated with button elements is click. Clicking on each of these buttons will trigger the respective remove and upvote methods defined in our controller.
Save and close the file when you have finished editing.
The final change we will make before moving on to defining our controller is to set a data target and action to control how and when the sharks/all partial will be displayed.
      ~/sharkapp/app/views/sharks/_all.html.erb
 <% for post in @shark.posts  %>
    <ul>
<li class="post">
<%= post.body %>
<%= button_tag "Remove Post", data: { controller:
 "posts", action: "posts#remove" } %>
            <%= button_tag "Upvote Post", data: { controller:
 "posts", action: "posts#upvote" } %>
</li>
    </ul>
    <% end %>
 
 Open the show template again, where the initial call to render sharks/all is currently defined:
At the bottom of the file, we have a <div> element that currently looks like this:
    nano app/views/sharks/show.html.erb
  ~/sharkapp/app/views/sharks/show.html.erb
  .. . <div>
  <%= render 'sharks/all' %>
</div>
First, add a controller to this <div> element to scope actions and targets:
  ~/sharkapp/app/views/sharks/show.html.erb
  .. .
<div data-controller="posts">
  <%= render 'sharks/all' %>
</div>
Next, add a button to control the appearance of the partial on the page. This button will trigger a showAll method in our posts controller.
Add the button below the <div> element and above the render statement:
    
     ~/sharkapp/app/views/sharks/show.html.erb
 .. .
<div data-controller="posts">
<button data-action="posts#showAll">Show Older Posts</button>
  <%= render 'sharks/all' %>
Again, we only need to identify our posts controller and showAll method here — the action will be triggered by a click event.
Next, we will add a data target. The goal of setting this target is to control the appearance of the partial on the page. Ultimately, we want users to see older posts only if they have opted into doing so by clicking on the
button.
We will therefore attach a data target called show to the sharks/all partial, and set its default style to visibility:hidden. This will hide the partial unless users opt in to seeing it by clicking on the button.
Add the following <div> element with the show target and style definition below the button and above the partial render statement:
     Show Ol
    der Posts
       
  Be sure to add the closing </div> tag.
The finished show template will look like this:
  ~/sharkapp/app/views/sharks/show.html.erb
 .. .
<div data-controller="posts">
<button data-action="posts#showAll">Show Older Posts</button>
<div data-target="posts.show" style="visibility:hidden">
  <%= render 'sharks/all' %>
</div>
  
 ~/sharkapp/app/views/sharks/show.html.erb
 <p id="notice"><%= notice %></p>
<p>
  <strong>Name:</strong>
  <%= @shark.name %>
</p>
<p>
  <strong>Facts:</strong>
  <%= @shark.facts %>
</p>
<h2>Posts</h2>
<%= render 'sharks/posts' %>
<%= link_to 'Edit', edit_shark_path(@shark) %> |
<%= link_to 'Back', sharks_path %>
<div data-controller="posts">
<button data-action="posts#showAll">Show Older Posts</button>
<div data-target="posts.show" style="visibility:hidden">
  <%= render 'sharks/all' %>
 
Save and close the file when you are finished editing.
With this template and its associated partials finished, you can move on to creating the controller with the methods you've referenced in these files.
