# How do websites work?

At the most basic level Rails is a tool for listening to HTTP requests from user's web browers and returning HTTP responses. When you visit any website on the internet your browser constructs an HTTP request and sends it to the server that is running that website. Every website on the internet has a piece of software running that listens for those HTTP requests and prepares an HTTP response to send back to your browser. You can think of it like your browser is asking the server a question and the server is giving you back an answer.

As an example when you visit https://brandnewbox.com your browser sends a request to the server that runs brandnewbox.com that says that you would like to see the content at "/" (the root path of every website). Our server takes that request, figures out which page to show you and then sends you an HTTP 200 response with the HTML content of the page which your browser then renders and displays. Every properly configured website server will return an HTTP response for _every_ HTTP request. As another example if you try to visit https://brandnewbox.com/this-page-does-not-exist from your browser your browser sends a HTTP request to brandnewbox.com requesting the content at "/this-page-does-not-exist". The brandnewbox.com server returns an HTTP 404 response which indicates that the server doesn't have any content to show you at the path that you requested.

There are many different [HTTP Request Methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods) which are ways that your browser can tip the server off about what kind of question you are asking.

* The `GET` method retrieves data. The browser says "can you give me the data I'm asking for?".
* The `POST` method submits data, often causing a change in state or side effects on the server. This is often used in Rails to submit _new_ data, which is used to create information on the server. The browser says "can you create a new resource with the data I am sending you?"
* The `PUT` and `PATCH` methods submit data, causing a change in state or side effects on the server. This is often used in Rails to update _existing_ data. The browser says "can you update an existing resource with this new data?".
* The `DELETE` method deletes data. The browser says "can you get rid of this data?".

HTTP request methods can be a little bit confusing because the server doesn't have to strictly listen to them. If you send a GET request the server is still allowed to update data in a database if it wants. But best practice when building websites is to try to build requests and responses that align with the semantics of the request methods.

There are many different [HTTP Response Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status) which are grouped into categories.

* 100s - Informational codes (servers send a bit of information to the browser without finishing the cycle)
* 200s - Successful response (everything is operating as normal, these are the most common)
* 300s - Redirects (used for servers to indicate that the browser should look elsewhere)
* 400s - Browser errors (these mean that the request that your browser submitted was incorrect in some way)
* 500s - Server errors (the server had issues processing the request)

# How does Rails fit into this?

Rails is one of the pieces of software that runs on the  server and listens for HTTP requests from browsers (from here on out we'll just call browsers "clients") and returns HTTP responses to those clients.

The most basic server in the world could just return the same response for every request that they receive which printed "Hello World!" to the client. Here's what that response would look like.

```
HTTP/1.1 200 OK
Date: Fri, 01 Jan 2021 12:00:00 GMT
Content-Length: 88
Content-Type: text/html

<html>
<body>
<h1>Hello, World!</h1>
</body>
</html>
```

However, as you can guess, a server which returns the same response for every request wouldn't be very helpful.

So this software that prepares the HTTP responses for clients usually has a structure to help you quickly and efficiently generate a **useful** response for the client.

The structure that Rails uses to aid you in generating responses is called [MVC](https://guides.rubyonrails.org/getting_started.html#mvc-and-you), Model-View-Controller. MVC is used across a wide variety of web frameworks and Rails has a component to handle each piece of MVC (which I've linked to in parentheses below).

## Model ([ActiveRecord](https://guides.rubyonrails.org/active_record_basics.html))
Models are for representing concepts in your application. Most of the time models are also saved to your database, but that's not the case for every model. For instance if you were building an ecommerce website you would probably have database-backed models like Product, Order, User, and Cart, etc. And you would also have some models that aren't backed by a database like CheckoutProcessor which would handle the checkout for a user's cart by converting it to an order. 

Models that are saved to the database are backed by ActiveRecord in Rails. ActiveRecord is based on a programming pattern by the (confusingly!) same name, [Active Record pattern](https://en.wikipedia.org/wiki/Active_record_pattern). The main crux of the pattern is that every model has the ability to save itself to the database without communication with an external object. This means if you have a reference to a Cart you can save that cart to the database by calling a method on the object.

The goal of ActiveRecord is to provide a way to write Ruby code to interact with the database. Each ActiveRecord model has a database table with a corresponding name which data is inserted and read from (the Cart model has a database table called "carts", Order is "orders", etc.)

ActiveRecord also provides tools for building relationships between models in your application (for instance you may want to say that a User has many Orders). ActiveRecord can also validate the data that a user enters to make sure it's in the proper format before being inserted into the database.

## View ([ActionView](https://guides.rubyonrails.org/layouts_and_rendering.html))
The view layer of MVC is what is sent back to clients in HTTP responses that we talked about earlier. In Rails you can have views that generate HTML, JSON, XML, PDFs or any other type of content you might want to respond to a HTTP request with. In order to generate different content in a view based on the requested page or what user is viewing the page you can expose template variables to your views and use a special templating language to use those variables. Every request to a Rails application will render a view (there's always an exception to rules like this but it's a good thing to keep in mind for 99% of cases). By default Rails uses the [ERb](https://en.wikipedia.org/wiki/ERuby) templating language but at BNB we usually switch that out for a language called [Haml](https://haml.info/).

As part of ActionView, Rails also provides a multitude of helpers to assist you in displaying data in more human-friendly ways. For instance, Rails provides us with a `number_to_human` helper which takes a number like "1234567890123" and turns it into the text "1.23 Trillion" to be more readable.

## Controller ([ActionController](https://guides.rubyonrails.org/action_controller_overview.html))
The controller is the component which connects Models and Views together. When a request enters the Rails application the controller is able to read the data out of the request, load the proper Models that are required for the request, and render the view that is necessary to return a response to the client.

Remember when we said that every request in Rails renders a view? That is because every request is routed (remember this term "routed", it will come up later!) to a controller action and the result of every action is rendering a view.

Controllers are in charge of arranging the data in the way that the request asks for. If the request is for a list of orders with the most recent ones listed first then the controller will load the orders (a model) sorted in reverse chronological order and assign that list of orders to a template variable. The view for the controller action will take that template variable and render it in whatever way the client has asked for (HTML, JSON, XML, etc). In this way the controller stands as a communication layer between the client and the Model and View layers.

# How does an HTTP request work it's way through Rails?

We've covered what HTTP is in general and how the structure of Rails works, but how do these two ideas tie together? How does a request from your browser hit a Rails app and ultimately result in a response back to your browser?

Rails applications have the concept of a "routes" file. The routes file creates a mapping for how specific paths are mapped to controller actions in your application.

When a request comes into your Rails application that is asking for the path "/orders". Rails checks your route file to see if you have a route for "/orders" and if you do then it sends the request to that controller action (conventionally this would route to a controller called the `OrdersController` and would call an action called `index` which is the Rails term for a "list" view).

Once the controller action receives the request it will load up whatever data is necessary using models and set the data as template variables. In our /orders example there would be an method named "index" in the controller which would load the data and assign it to a template variable. `Order.all` is used to load all the orders from the database and `@orders` is the template variable. We'll cover all of this in more detail later.
```ruby
def index
  @orders = Order.all
end
```

At the end of the controller action the proper view to be rendered will be looked up using convention and the view uses the template variables to render the content that will be displayed to the user. Rails again has a convention for this where the name of the action is also the name of the view file that is looked for and the name of the controller is the directory that the view is located in. So if we have a `OrdersController#index` action then you could expect to find the view file at `orders/index.html.haml`. Most view files in Rails have two extensions. The first extension tells Rails what kind of file it is, `html` means HTML, `js` means Javascript, `pdf` means PDF , etc. The second extension tells Rails what kind of templating language to use to process the file, in this case that is Haml.

Your view file can reference the templating variables to render content to the user (again, we'll cover the specifics in more detail later).
```haml
%ul
  - @orders.each do |order|
    %li= order.id
```

Once Rails has found the controller action to execute, run the action, and rendered the view it is then ready to send a response to the user. Rails takes the view that you have rendered and puts it into an HTTP response like we saw above. It then calculates the various HTTP headers that necessary like Content-Length, Date, etc and sends the response back to the browser!

At this point the users browser can render the content that was returned and interact with the webpage. A common question that comes up is how do I change the content in a users browser after the view has been rendered? Because of the way that HTTP works you can't update a user's webpage after your server has returned the response. The response that you return is static, it's one-way communication. If you want dynamic features on your webpage you have to use Javascript to send additional requests to your server that can return new responses to change the webpage. There are also technologies that are now available like [WebSockets](https://developer.mozilla.org/en-US/docs/Web/API/WebSockets_API/Writing_WebSocket_client_applications) to provide two-way communication between a client and your server to update a users page.

<br />
<hr />
<br />

We will endeavour to teach all of these concepts in more depth throughout this tutorial as your learn Rails. Always try to keep in mind how the HTTP request/response lifecycle works and how that interacts with Rails as you're building your applications. Hopefully this has given you a good overview of how Rails works and how you will structure your application.

