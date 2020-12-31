
Step 4 — Modifying the Application Layout
Our first step in integrating Bootstrap conventions and components into the project will be adding them to the main application layout file. This file sets the elements that will be included with each rendered view template for the application. In this file, we'll make sure our webpack entry point is defined, while also adding references to a shared navigation headers partial and some logic that will allow us to render a layout for the views associated with the shark application.
First, open app/views/layouts/application.html.erb, your application's main layout file:
   }
caption {
}
float: left;
clear: both;
   
 Currently, the file looks like this:
 ~/rails-
bootstrap/app/views/layouts/application.html.erb
  <!DOCTYPE html>
<html>
  <head>
    <title>Sharkapp</title>
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>
    <%= stylesheet_link_tag 'application', media: 'all', 'data
-turbolinks-track': 'reload' %>
    <%= javascript_pack_tag 'application', 'data-turbolinks-tr
ack': 'reload' %>
</head>
  <body>
    <%= yield %>
  </body>
</html>
The code renders things like cross-site request forgery protection parameters and tokens for dynamic forms, a csp-nonce for per-session
   nano app/views/layouts/application.html.erb
  
 nonces that allows in-line script tags, and the application's style sheets and javascript assets. Note that rather than having a javascript_link_tag , our code includes a javascript_pack_tag, which tells Rails to load our main webpack entry point at app/javascript/packs/application.js .
In the <body> of the page, a yield statement tells Rails to insert the content from a view. In this case, because our application root formerly mapped to the index shark view, this would have inserted the content from that view. Now, however, because we have changed the root view, this will insert content from the home controller's index view.
This raises a couple of questions: Do we want the home view for the application to be the same as what users see when they view the shark application? And if we want these views to be somewhat different, how do we implement that?
The first step will be deciding what should be replicated across all application views. We can leave everything included under the <header> in place, since it is primarily tags and metadata that we want to be present on all application pages. Within this section, however, we can also add a few things that will customize all of our application views.
First, add the viewport meta tag that Bootstrap recommends for responsive behaviors:
           
  Next, replace the existing title code with code that will render the application title in a more dynamic way:
  ~/rails-
bootstrap/app/views/layouts/application.html.erb
 <!DOCTYPE html>
<html>
<head>
    <meta name="viewport" content="width=device-width, initial
-scale=1.0">
    <title>Sharkapp</title>
    <%= csrf_meta_tags %>
    <%= csp_meta_tag %>
.. .
  
    Add a <meta> tag to include a description of the site:
 ~/rails-
bootstrap/app/views/layouts/application.html.erb
 <!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width, initial
-scale=1.0">
    <title><%= content_for?(:title) ? yield(:title) : "About S
harks" %></title>
    <%= csrf_meta_tags %>
<%= csp_meta_tag %> .. .
 
    With this code in place, you can add a navigation partial to the layout. Ideally, each of our application's pages should include a navbar component at the top of the page, so that users can easily navigate from one part of the site to another.
Under the <body> tag, add a <header> tag and the following render statement:
   ~/rails-
bootstrap/app/views/layouts/application.html.erb
 <!DOCTYPE html>
<html>
  <head>
    <meta name="viewport" content="width=device-width, initial
-scale=1.0">
    <title><%= content_for?(:title) ? yield(:title) : "About S
harks" %></title>
    <meta name="description" content="<%= content_for?(:descri
ption) ? yield(:description) : "About Sharks" %>">
    <%= csrf_meta_tags %>
<%= csp_meta_tag %> .. .

    This <header> tag allows you to organize your page content, separating the navbar from the main page contents.
Finally, you can add a <main> element tag and some logic to control which view, and thus which layout, the application will render. This code uses the content_for method to reference a content identifier that we will associate with our sharks layout in the next step.
Replace the existing yield statement with the following content:
     ~/rails-
bootstrap/app/views/layouts/application.html.erb
 <body>
    <header>
      <%= render 'layouts/navigation' %>
</header>
<%= yield %> .. .
 
   Now, if the :content block is set, the application will yield the associated layout. Otherwise, thanks to the ternary operator, it will do an implicit yield of the view associated with the home controller.
Once you have made these changes, save and close the file.
With the application-wide layout set, you can move on to creating the shared navbar partial and the sharks layout for your shark views.
Step 5 — Creating the Shared Partial and Specific Layouts
In addition to the changes you made to the application layout in the previous Step, you will want to create the shared navbar partial, the sharks
  ~/rails-
bootstrap/app/views/layouts/application.html.erb
 .. . <body>
    <header>
      <%= render 'layouts/navigation' %>
</header>
    <main role="main">
    <%= content_for?(:content) ? yield(:content) : yield %>
    </main>
  </body>
</html>
 
  layout that you referenced in app/views/layouts/application.html.erb, and a view for the application landing page. You can also add Bootstrap styles to your application's current link_to elements in order to take advantage of built-in Bootstrap styles.
First, open a file for the shared navbar partial:
Add the following code to the file to create the navbar:
    nano app/views/layouts/_navigation.html.erb

 ~/rails-
bootstrap/app/views/layouts/_navigation.html.erb
   <nav class="navbar navbar-dark navbar-static-top navbar-expand
-md">
    <div class="container">
        <button type="button" class="navbar-toggler collapsed"
data-toggle="collapse" data-target="#bs-example-navbar-collaps
e-1" aria-expanded="false"> <span class="sr-only">Toggle navig
ation</span>
        </button> <%= link_to "Everything Sharks", root_path,
 class: 'navbar-brand' %>
        <div class="collapse navbar-collapse" id="bs-example-n
avbar-collapse-1">
            <ul class="nav navbar-nav mr-auto">
            <li class='nav-item'><%= link_to 'Home', home_inde
x_path, class: 'nav-link' %></li>
            <li class='nav-item'><%= link_to 'Sharks', sharks_
path, class: 'nav-link' %></li>
</li> </ul>
        </div>
    </div>
</nav>

  This navbar creates a link for the application root using the link_to method, which maps to the application root path. The navbar also includes two additional links: one to the Home path, which maps to the home controller's
index view, and another to the shark application path, which maps to the s hark index view.
Save and close the file when you are finished editing.
Next, open a file in the layouts directory for the sharks layout:
Before adding layout features, we will need to ensure that the content of the layout is set as the :content block that we reference in the main application layout. Add the following lines to the file to create the block:
         nano app/views/layouts/sharks.html.erb
  ~/rails-
bootstrap/app/views/layouts/sharks.html.erb
  <% content_for :content do %>
<% end %>
The code we're about to write in this block will be rendered inside the :con tent block in the app/views/layouts/application.html.erb file whenever a sharks view is requested by a controller.
Next, inside the block itself, add the following code to create a jumbotron component and two containers:
      
 ~/rails-
bootstrap/app/views/layouts/sharks.html.erb
  <% content_for :content do %>
    <div class="jumbotron text-center">
        <h1>Shark Info</h1>
    </div>
    <div class="container">
        <div class="row">
            <div class="col-lg-6">
                <p>
                    <%= yield %>
</p> </div>
            <div class="col-lg-6">
                <p>
                    <div class="caption">You can always count
 on some sharks to be friendly and welcoming!</div>
                    <img src="https://assets.digitalocean.com/
articles/docker_node_image/sammy.png" alt="Sammy the Shark">
</p>
            </div>
        </div>
</div>
<% end %>
 
 The first container includes a yield statement that will insert the content from the shark controller's views, while the second includes a reminder that certain sharks are always friendly and welcoming.
Finally, at the bottom of the file, add the following render statement to render the application layout:
    ~/rails-
bootstrap/app/views/layouts/sharks.html.erb
  .. .
    </div>
    <% end %>
        <%= render template: "layouts/application" %>
    </div>
</div>
This sharks layout will provide the content for the named :content block in the main application layout; it will then render the application layout itself to ensure that our rendered application pages have everything we want at the application-wide level.
Save and close the file when you are finished editing.
We now have our partials and layouts in place, but we haven't yet created the view that users will see when they navigate to the application home page, the home controller's index view.
   
  Open that file now:
The structure of this view will match the layout we defined for shark views, with a main jumbotron component and two containers. Replace the boilerplate code in the file with the following:
  nano app/views/home/index.html.erb

~/rails-bootstrap/app/views/home/index.html.erb
   <div class="jumbotron">
    <div class="container">
        <h1>Want to Learn About Sharks?</h1>
        <p>Are you ready to learn about sharks?</p>
        <br>
        <p>
            <%= button_to 'Get Shark Info', sharks_path, :meth
od => :get,  :class => "btn btn-primary btn-lg"%>
</p> </div>
</div>
<div class="container">
    <div class="row">
        <div class="col-lg-6">
            <h3>Not all sharks are alike</h3>
            <p>Though some are dangerous, sharks generally do
 not attack humans. Out of the 500 species known to researcher
s, only 30 have been known to attack humans.
            </p>
        </div>
        <div class="col-lg-6">
            <h3>Sharks are ancient</h3>
            <p>There is evidence to suggest that sharks lived
 up to 400 million years ago.
</p>
 
Now, when landing on the home page of the application, users will have a clear way to navigate to the shark section of the application, by clicking on the Get Shark Info button. This button points to the shark_path — the helper that maps to the routes associated with the sharks controller.
Save and close the file when you are finished editing.
Our last task will be to transform some of the link_to methods in our application into buttons that we can style using Bootstrap. We will also add a way to navigate back to the home page from the sharks index view.
Open the sharks index view to start:
At the bottom of the file, locate the link_to method that directs to the new shark view:
       nano app/views/sharks/index.html.erb
  ~/rails-
bootstrap/app/views/sharks/index.html.erb
  .. .
<%= link_to 'New Shark', new_shark_path %>
         </div>
    </div>
</div>
  
  "btn bt
   n-primary btn-sm"
   ~/rails-
bootstrap/app/views/sharks/index.html.erb
  .. .
<%= link_to 'New Shark', new_shark_path, :class => "btn btn-pr imary btn-sm" %>
 ~/rails-
bootstrap/app/views/sharks/index.html.erb
  .. .
<%= link_to 'New Shark', new_shark_path, :class => "btn btn-pr imary btn-sm" %> <%= link_to 'Home', home_index_path, :class = > "btn btn-primary btn-sm" %>
  nano app/views/sharks/new.html.erb
 link_to
  Modify the code to turn this link into a button that uses Bootstrap's
class:
Next, add a link to the application home page:
Save and close the file when you are finished editing. Next, open the new view:
Add the button styles to the method at the bottom of the file:

  Save and close the file. Open the edit view:
Currently, the link_to methods are arranged like this:
   nano app/views/sharks/edit.html.erb
  ~/rails-bootstrap/app/views/sharks/edit.html.erb
  .. .
<%= link_to 'Show', @shark %> | <%= link_to 'Back', sharks_path %>
Change their arrangement on the page and add the button styles, so that the code looks like this:
  ~/rails-bootstrap/app/views/sharks/new.html.erb
 .. .
<%= link_to 'Back', sharks_path, :class => "btn btn-primary bt n-sm" %>
~/rails-bootstrap/app/views/sharks/edit.html.erb
 .. .
<%= link_to 'Show', @shark, :class => "btn btn-primary btn-sm" %> <%= link_to 'Back', sharks_path, :class => "btn btn-primary btn-sm" %>
  
 Save and close the file. Finally, open the show view:
Find the following link_to methods:
   nano app/views/sharks/show.html.erb
  ~/rails-bootstrap/app/views/sharks/show.html.erb
  .. .
<%= link_to 'Edit', edit_shark_path(@shark) %> | <%= link_to 'Back', sharks_path %>
.. .
Change them to look like this:
 ~/rails-bootstrap/app/views/sharks/show.html.erb
  .. .
<%= link_to 'Edit', edit_shark_path(@shark), :class => "btn bt n-primary btn-sm" %> <%= link_to 'Back', sharks_path, :class = > "btn btn-primary btn-sm" %>
.. .
Save and close the file.
You are now ready to test the application.

  Start your server with the appropriate command: - rails s if you are working locally - rails s --binding=your_server_ip if you are working with a development server
Navigate to localhost:3000 or http://your_server_ip:3000, depending on whether you are working locally or on a server. You will see the following landing page:
Click on Get Shark Info. You will see the following page:
       Application Landing Page
 
  You can now edit your shark, or add facts and posts, using the methods described in How To Add Stimulus to a Ruby on Rails Application . You can also add new sharks to the conversation.
As you navigate to other shark views, you will see that the shark layout is always included:
   Sharks Index Page
   
  You now have Bootstrap integrated into your Rails application. From here, you can move forward by adding new styles and components to your application to make it more appealing to users.
Conclusion
You now have Bootstrap integrated into your Rails application, which will allow you to create responsive and visually appealing styles to enhance your users' experience of the project.
To learn more about Bootstrap features and what they offer, please see the Bootstrap documentation. You can also look at the documentation for Sass, to get a sense of how you can use it to enhance and extend your CSS styles and logic.
If you are interested in seeing how Bootstrap integrates with other frameworks, please see How To Build a Weather App with Angular,
    Sharks Show Page
   
 Bootstrap, and the APIXU API. You can also learn about how it integrates with Rails and React by reading How To Set Up a Ruby on Rails Project with a React Frontend.
