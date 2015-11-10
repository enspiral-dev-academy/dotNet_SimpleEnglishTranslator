# dotNet_SimpleEnglishTranslator

This is a challenge to pratice working with MVC and working the db. 

The goal of this challenge is:

 - Understanding how then ASP.NET MVC pattern holds together
 - Understanding how the ASP.NET View templating language works
 - Understanding how to access data (DB) using EF (and the repository pattern)
 - Creating EF database migrations 
 - Creating webAPI controlers


 -------------------------------


# Simple English


 - Have a look at this comic: [Computer problems https://xkcd.com/722/](https://xkcd.com/722/)
 - Then have a look at this one: [Up Goer Five https://xkcd.com/1133/](https://xkcd.com/1133/)
 
Both of those comics are explaining technical things using the ~1,000~ ten hundred most common words in english.

Also if you have time, watch these two music videos
 - ["Space Oddity” By David Bowie (orignal)](https://www.youtube.com/watch?v=D67kmFzSh_o) <-- Only need to watch first twenty seconds; unless you are a big David Bowie liking-person.
 - ["Weird space thing" (Simple English) ](https://www.youtube.com/watch?v=ygrdAvmr-MA)
 
 
We are going to make our own version of this tool: [simplewriter](http://xkcd.com/simplewriter/)
with a few improvments.

The first version of this is going to be a little rubbish, & may be better if you just built in javascript. However we are going to improve on it ant the point is to learn about database access in .net not js.


 #Release 0 EF models and reading in some data
 
 
  - Create a new ASP.NET MVC solution (Or create a branch on an [emptyish one](../../../dotNet_SillyLittleSiteOnAzure)
  - In the Models Folder create a new class `SimpleWord`
   - You can create a new class by either:
    - Press `Shift+Alt+C`
    - Right click a folder -> Add -> Class
  - Add an `int` proptery called `Id`
   - Hint quick way: 
    - type `prop`
    - press `Tab`
    - type `int`
    - press `Tab`
    - type `Id` 
  - Add another `String` proptery `Word`
  
It should look like this:

``` cs

  public class SimpleWord
  {
      public int Id { get; set; }
      public String Word { get; set; }
  }

```
 - Open the file `Models/IdentityModels.cs`
 - Find the `ApplicationDbContext` class
 - Add a proptery of type `DbSet<SimpleWord>` called `SimpleWords`

It should look something like this:

``` cs

  public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
  {
      public DbSet<SimpleWord> SimpleWords { get; set; }
      protected override void OnModelCreating(ModelBuilder builder)
      {
          base.OnModelCreating(builder);
          // Customize the ASP.NET Identity model and override the defaults if needed.
          // For example, you can rename the ASP.NET Identity table names and more.
          // Add your customizations after calling base.OnModelCreating(builder);
      }
  }

```
 
The ApplicationDbContext is the class we will use to access the database. We have added a `DbSet` proptery of type `SimpleWord` called `SimpleWords`. __ Note how the class name is singular and the proptery name is plural. __
Once we have run a migration (we will get to that in a moment) we will have created a Database with a table called `SimpleWords` with a couple of columns, `Id` and `Word`. Later we will be able to create an instance of the `ApplicationDbContext` class. This is an abstraction of the database. On that instance we can access the `SimpleWords` proptery, which is an abstraction of the table in the DB. `SimpleWords` acts like an collection of the rows in the database. We can perform CRUD operations against it. 


``` cs

 ApplicationDbContext db = new ApplicationDbContext();


            //Create
            SimpleWord wordToInsert = new SimpleWord()
            {
                Word = "Coffee"
            };

            db.SimpleWords.Add(wordToInsert);
            db.SaveChanges(); //This pushes the changes from memory into the db

            //Read by id
             SimpleWord wordReadOut = db.SimpleWords.Find(1);

            //Read (filter words which begin with "C")
            IQueryable<SimpleWord> wordsReadOut = db.SimpleWords.Where(w => w.Word.StartsWith("C"));


            //Update
            SimpleWord wordToUpdate = db.SimpleWords.Find(1);
            wordToUpdate.Word = "Tea";
            db.SaveChanges(); //This pushes the changes from memory into the db


            //Delete

            SimpleWord wordToDelete = db.SimpleWords.Find(1);
            db.SimpleWords.Remove(wordToDelete);
            db.SaveChanges(); //This pushes the changes from memory into the db



```



 

