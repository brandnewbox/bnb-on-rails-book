## Viewing data in databases

Our Rails applications store their data in databases and we use ActiveRecord to create/update records in those databases. Sometimes when debugging an issue that is happening, or just wanting to see how your data is being stored, it can be helpful to look at the actual data in the database.

A database viewer is an application which allows you to connect to your database, look at the records, and execute SQL statements against the data. Several people at Brand New Box use one called Beekeeper Studio to do this. Beekeeper supports MySQL and PostgreSQL. All of our applications use one of those two databases (historically Rails recommended using MySQL so some of our older projects are on that, all newer applications are on PostgreSQL).

[Beekeeper Studio](https://www.beekeeperstudio.io/)

### Setting up your database connection

Since our databases run inside of Docker there are a few steps we need to take in order to connect to it.

1. Look at what port your database is exposed on in your `docker-compose.yml` file. Underneath your `db` or `postgres` service you should have a line something like
    ```yaml
    ports:
      - 5433:5432
    ```
    The number on the right is the port inside the container that is being exposed (5432 is the default postgres port) and the number on the left is the port to expose it to your development machine on.


2. Forward the exposed port to your local machine. From the VS Code ports tab ensure that the exposed port on the development machine is forwarded to your local computer. So in our example above we would need to ensure port `5433` is forwarded to our local machine.

3. Now that our local computer can connect to the database through the port tunnels we can set up the connection in Beekeeper.
    - Click the "+ New Connection" button in Beekeper
    - For connection type, select Postgres
    - Fill in all of the fields:
        - **Host**: `localhost`
        - **Port**: `5433` (matches the port you forwarded in VS Code)
        - **User**: `postgres` (most likely this by default, but it should match whatever is specified in your docker-compose.yml file)
        - **Password**: `password` (this should also match whatever is in your docker-compose.yml file)
        - **Default Database**: Set this to the development database name in database.yml
    - Test and ensure the connection works and then you are set up! 🎉
        
### Navgating data in the database

When Beekeeper first connects to your database it will show you a list of your tables in the left sidebar and a query pane on the right.

You can view a list of records in any table by double clicking the table in the left sidebar. Try double-clicking your `books` table and viewing all of the data!

If you have `reviews` in your database you can also view record relationships. Double-click your `reviews` table, then hover over one of the values in the `book_id` column. A "share"-like icon will appear and clicking that icon will take you to the related record in the books table. This is convenient for viewing relationships at the database level and how they are stored.

This is how Rails interprets a `belongs_to` relationship. When you say that a `Review` `belongs_to :book`, Rails reads the value of the `book_id` column for the record and loads the `Book` record corresponding to that ID!

### Executing queries

In the tab bar of Beekeeper, clicking the "+" icon will give you a new tab which is a query editor. You can execute any queries that you want from here. A good basic query is to select all of the data in the books table.

```sql
SELECT * FROM books
```

Run that query and Beekeeper will show you all the records from your books table. This is the equivalent of running `Book.all` in your Rails app.

You can also filter the data you get back from a query.

```sql
SELECT * FROM books WHERE created_at > '2022-03-14'
```

This is equivalent to running `Book.where(created_at: Date.new(2022, 3, 14)..)` in your Rails app to select all books created after 3/14/22. 

The most important thing to remember is _all_ ActiveRecord calls boil down to queries being run against your database. You can at any time copy a query from your Rails log and paste it into the query editor to see what the raw data that is being returned.

    
