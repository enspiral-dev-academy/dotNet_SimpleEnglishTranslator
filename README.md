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
 
Both of those comics are explaining technical things using the ~~1,000~~ *ten hundred* most common words in english.

Also if you have time, watch these two music videos
 - ["Space Oddity” By David Bowie (orignal)](https://www.youtube.com/watch?v=D67kmFzSh_o) <-- Only need to watch first twenty seconds; unless you are a big David Bowie ~~fan~~ _liking-person_.
 - ["Weird space thing" (Simple English) ](https://www.youtube.com/watch?v=ygrdAvmr-MA)
 
 
We are going to make our own version of this tool: [simplewriter](http://xkcd.com/simplewriter/)
with a few improvments.

The first version of this is going to be a little rubbish, & may be better if you just built in javascript. However we are going to improve on it; and the point is to learn about database access in .net, not js.


 #Release 0 EF models and reading in some data
 
 Entity Framework (EF) is one of several ORM (Object Relationl Mapper) tools for .Net It's the comtempory of Ruby's ActiveRecord.
 
 
  - Create a new ASP.NET MVC solution (Or create a branch on an [emptyish one](../../../dotNet_SillyLittleSiteOnAzure))
  - In the Models Folder create a new class `SimpleWord`
   - *You can create a new class by either*:
     - Press `Shift+Alt+C`
     - Right click a folder -> Add -> Class
  - Add an `int` property called `Id`
   - *Hint quick way*: 
     - type `prop`
     - press `Tab`
     - type `int`
     - press `Tab`
     - type `Id` 
  - Add another `String` property `Word`
  
It should look like this:

```C#

  public class SimpleWord
  {
      public int Id { get; set; }
      public String Word { get; set; }
  }

```
 - Open the file `Models/IdentityModels.cs`
 - Find the `ApplicationDbContext` class
 - Add a property of type `DbSet<SimpleWord>` called `SimpleWords`

It should look something like this:

```csharp

  public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
  {
      public DbSet<SimpleWord> SimpleWords { get; set; }  //Bit you should have added
      protected override void OnModelCreating(ModelBuilder builder)
      {
          base.OnModelCreating(builder);
          // Customize the ASP.NET Identity model and override the defaults if needed.
          // For example, you can rename the ASP.NET Identity table names and more.
          // Add your customizations after calling base.OnModelCreating(builder);
      }
  }

```
 
The `ApplicationDbContext` is the class we will use to access the database. We have added a `DbSet` property of type `SimpleWord` called `SimpleWords`. _Note how the class name is singular and the property name is plural._
Once we have run a migration (we will get to that in a moment) we will have created a Database with a table called `SimpleWords` with a couple of columns, `Id` and `Word`. Later we will be able to create an instance of the `ApplicationDbContext` class. This is an abstraction of the database. On that instance we can access the `SimpleWords` property, which is an abstraction of the table in the DB. `SimpleWords` acts like an collection of the rows in the database. 

We can perform CRUD operations against the `ApplicationDbContext` like this:
```C#

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

##Migrations

Cool! So we have a Model which is an abstraction of the database table we want.

We need to create a dataBase migration file, and update the database with our table schema.


 - Find the package manager window
  - Press `Ctrl+Q` or click the Quick findbox (Top Right)
  - Start typing _Package_
  - Select the option `View > OtherWindows > Package Manager Console`
 - Type `enable migrations` (Enter)
  -_This will generate some files_
 - Type `add-migration <migration name>`
  -_The `<migration name>` needs to be diffrent for each db migration. Something like `add-migration Add_SimpleWord_Table`
  -This will generate a Migration with `Up()` and `Down()` methods* 
 - Type `update-database`
  - This will actually update the database schema 
 
*The `Up()` and `Down()` methods have instructions to add and remove schema from the database. This lets you version control you database schemma (_Very useful, saves a lot of pain_).

You may also find that the `Up()` and `Down()` methods have instructions for some tables like `"dbo.AspNetRoles"`. These are some out of the box identiy (user login) management functionality. You should be able to find the `"dbo.SimpleWords"` instructions in there some where (_Normally it is a good idea to create a migration, for the identiy management tables first, before you add a `DbSet` of anything else so that they dont get mixed together._)

Now have a look at the database and see what the migratiion did.

 - Select the Quick Search tool (`Ctrl+Q`)
 - Type `sql`
 - Select `View > SQL Object Explorer`
  - This should open a panel on the left
 - Drill down `SQL Server > Databases > asp-<YOUR PROJECT NAME>-<Date>`
 - Drill into `Tables`
 - Have a look around (right click on the table see what options you have)


## Adding Controlers and Views


Lets add some controlers and views so we can interact with the `SimpleWord` model/table.

 - Right click on the `Controlers` Folder
 - Add > Controler
 - Choose MVC 5 Controler with Views, using Entity Framework
 - Choose `SimpleWord` for the Model Class
 - Choose `ApplicationDbContext` for the Data Context class
 - Click Add
 
This will generate a Controler `SimpleWordControler` with actions for CRUD operations; and Views for each action.

ASP.NET MVC follows a convention that if you navigate to http://<siteName>/<controlerName>/<action> (i.e. http://<siteName>/SimpleWord/Index) it will take you to the class <controlername>Controler (i.e, `SimpleWordControler`) and call the method <action>  (i.e, `Index()`). If you don't supply an action name it will default to `Index`. If you don't supply a controler name it will default to the `HomeControler` class. 


Have a look at The methods in the `SimpleWordControler` See what it is doing with the `ApplicationDbContext`. You will see that inside each action method there is a call to `View()` In some, an object is passed into the `View()` method. The View() method follows a convention. It will render a View template in the `Views` folder. Inside the `Views` folder the will be a subfolder `SimpleWord` inside that there will be a view for each of the action methods (i.e., `Index.cshtml` for the `Index()` method). 
Have a look at the `Views/SimpleWord/Index.cshtml`
 - Right click inside the `Index()` method.
 - Click Go To View
  - This is a quick way of navagating to the view for a given action method
  - You can also click on `View()`, press `F12` , select the view 
  - 
  
You will see html mixed up with a bunch of `@`s and some code. This is the razor templating language. Where you see `@`s is where we are mixing in code elements with the html.

At the top it says: `@model IEnumerable<WebApplication1.Models.SimpleWord>` this mean that it is expecting to be passed a model that is an IEnumerable collection of `SimpleWord`s











 

