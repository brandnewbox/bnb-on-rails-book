## Rails Console

Now that you have scaffolded a resource let's talk about ways in which you can access your application outside of a browser. Rails provides a command line interface for you to access various parts of your application.

The Rails console helps with:
- interacting with objects outside the browser to discover what methods and properties they have
- accessing your model data and inserting/updating new models
- testing out a new chunk of code

The Rails console is accessed inside of your container and can be run with `dip rails console` (or `dip rails c` for short).

The Rails console is a full Ruby [REPL](https://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) (e.g. try typing `1 + 1` or `Time.now` and hitting enter. You'll get the result!). But it also has access to all of your application code as well. Let's experiment with some things to show that off.

### Rails console experimentation

- You can insert records by using the [Rails ActiveRecord API](https://guides.rubyonrails.org/active_record_basics.html#crud-reading-and-writing-data)
  Let's insert a new `Book` record for the model that we scaffolded out. From your Rails console run:
  ```
  > Book.create(title: "BNB on Rails", description: "A book about learning Ruby on Rails from the ground up", price: 8.99)
  => #<Book id: ...>
  ```
  Now, in your browser navigate to http://localhost:3000/books. You should see your new inserted book in the list!
  
- You can update existing records.
  In order to update a record you need to specify which record you will want to update and store it in a variable. Rails provides [several ways to find a record in the database](https://guides.rubyonrails.org/active_record_querying.html#retrieving-a-single-object). To access the last record that we inserted in the step above we'll use [`Book.last`](https://api.rubyonrails.org/classes/ActiveRecord/FinderMethods.html#method-i-last). Once we have it stored in a variable we can use the [`Book#update`](https://api.rubyonrails.org/classes/ActiveRecord/Persistence.html#method-i-update) method in order to update the properties that we want.
  
  ```
  > book = Book.last
  > book.update(price: 10.99)
  ```
  You should see log messages which show that Rails is generating the proper SQL statements to update your record. After you have run the update method, try looking at the price on your book to confirm it's updated.
  ```
  > book.price
  => 10.99
  ```
  
- You can delete a record
  Using the `destroy` method on an `ActiveRecord` allows us to remove it from the database.
  ```
  > book = Book.last
  > book.destroy
  ```
  The console should show you Rails generating the SQL to remove that record from the database. If you visit http://localhost:3000/books you should not longer see that book in the list.


### Further learning

The Rails console isn't only about accessing your models (though that is a very common use case!). Check out this [blog post](https://pragmaticstudio.com/tutorials/rails-console-shortcuts-tips-tricks) for some additional tips and capabilities of the console including how to access your route and path helpers, autocompleting, etc.
