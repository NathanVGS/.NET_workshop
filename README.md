# Workshop .NET

## Goals:

- Learn some C#
- Learn the basic principles of an ASP.NET RazorPages application

## 1. Installation

1. This readme assumes that you have git installed (check with "git --version" in terminal), that you have MySQL installed and that you can work with a database manager like DBeaver. 
If you don't have MySQL, you need to install the SQLite package.

2. Go to https://dotnet.microsoft.com/download and click "Install .NET Core" (Linux) or "Download .NET Core" (MacOS/Windows)

3. Follow the steps for your OS, for Linux:
- first add the Microsoft package signing key to your list of trusted keys and add the package repository
- then install the .NET Core SDK (the SDK includes the runtime already)
- **IMPORTANT**: for the last command, specify the exact version of the SDK, in our experience, 
the newest version resulted in a bug when using one of the tools we'll be installing later. If your version is not 3.1.301 (`dotnet --info`), you can still continue and try for yourself if it works and install the working SDK later. Your last command should look something like this:

`sudo apt-get install -y dotnet-sdk-3.1=3.1.301-1` 

4. Optional (but recommended): install Jetbrains Rider. 
(Ubuntu: `sudo snap install rider --classic`)

If you do use Visual Studio Code, make sure to click yes when this window pops up on a new dotnet project: 

![Select yes when VS Code asks if it should add "Required assets"](confirmVSCode.png "Select yes")


## 2. Introduction to C# and .NET: Console Calculator

1. Make a folder for your calculator files, cd to it and run the command "dotnet new console". Open the folder with your text editor of choice. 
Alternatively, if you did choose to use Rider, you can create a project with the Rider's and select Console application in the menu.
2. One of your files is the Program.cs file, which contains a Program class and a Main method. It is this method that will
be triggered by the runtime, so this is where your code should start. For this initiation, however, you can just write the entire logic inside of the Program class.
3. Once you have written your logic (cf. workflow below), you can test your code by either typing `dotnet run` in your terminal or clicking the "Run" button in your editor.

**Workflow:** 
- Ask the user for two numbers and save them as variables 
(hint: use the built-in Console.WriteLine() en Console.ReadLine() methods). Remember: C# distinguishes between ints, floats and doubles.
- Next ask for the operation the user wants to do with those numbers. For instance, if he types "divide", then your function divides number one by number 2.
- Display the result in the console.

**Bonus:**

- Dividing by zero will give "infinite" as a result, handle that exception so your console tells you you can't divide by zero.
- Once your result is in, make it possible for the user to continue with another calculation. You can display a "Do you want to continue? y/n" message and wait for the user to press a key (hint: Console.ReadKey() may be a useful method here).

Congratulations! You just wrote your first C# and .NET application!


## 3. Time for the real work: a CRUD website with Razor Pages

1. Make a new folder where you want your files to go, cd into it and start a new .NET project. 

- Type `dotnet new webapp` in your terminal and open the folder with your text editor, with VS Code, click "Yes" when a message pops up to add required assets.

![Select yes when VS Code asks if it should add "Required assets"](confirmVSCode.png "Select yes")

With Rider you can also make a new webapp manually via the UI: choose the ASP.NET Core Web Application option from the menu, name it whatever you want and specify the type to be that of "web app". Then click "create".

2. A template application is automatically generated. 
A quick overview of the directories:

- Properties: you don't need to worry about this folder
- Dependencies: only shown in Rider, but you also won't be needing this folder
- wwwroot: contains "static" files, like css, and js files. If you want to change the css for your site, this is where you should go. It also includes Bootstrap library by default.
- Pages: this is where all your html-pages will go. There's already a couple of them in there, but as you can see, they have the .cshtml extension. .cshtml extension means it's a Razor Page, which, if you look at it, looks a lot like html, but can include some extra code.

The _ViewStart.cshtml file specifies which page to use as the default layout for the page, by default this is the _layout.cshtml file in the Shared folder. The Shared folder is used for html files that are used by every page of your application.

If you are using VS Code, or have the File System opened in the explorer menu in Rider, then you see two more folders. !["File System in explorer, Rider"](riderfilesystem.png "Select yes")

- bin
- obj

These folders are automatically created/updated whenever you build, run or restore your project. You could just delete them right before launching your application again and they will be recreated.

You can also see a file with .csproj extension. This is basically a xml file where the target framework is mentioned, and where installed packages are referenced.

And of course, just like our Calculator app before, we have a Program.cs file as the initiation point for our program.

This time our Main method in the Program.cs creates a web page. It is also linked to the Startup.cs file, where we can configure our app, for instance by adding extra services. More on that later. 

To check the auto-generated page that our Main method triggers, click the run or launch button, or use your terminal: "dotnet run" and follow the link. 
You should now see a warning message like this:

![Your browser will tell you your connection is not safe](localhostnotsafe.png ""). 

Since we are in development mode on our localhost, there is no safety issue and we don't need to worry about the certificate, so click "advanced" and proceed to the localhost.


With just two simple steps, you have already generated a web page with .NET Razor Pages!

3. Next we need to make classes (which are called models) that represent the database objects. If you know Symfony: it is quite similar to that.
- Make a folder "Models" where you can store your database classes.
- In that folder, make a "Teacher.cs" and a "Student.cs" file.
- Add the following code to your Teacher class: 
```
namespace YourApplicationName.Models
   {
       public class Teacher
       {
           //add properties of model here
       }
   }
``` 
- To add a property, just type "prop" and you should get an autocomplete option. 
Press enter and you should now have something like this: `public int MyProperty { get; set; }`.
Add Id, LastName, FirstName and Email properties and specify the correct types for them. You can add more properties if you like.
- Do the same thing for your Student class but also add a public virtual Teacher property and a TeacherId property of type int.
Typehint Teacher as being of the Teacher Type.
- Since your Teacher property is going to be the property that links your tables together, we have to add a data annotation above the property:
- With data annotations, add [key] to both the Id tables to indicate the Id property as the primary key.
- Then use data annotations to tell EF that our Teacher object has as foreign key the TeacherId column using:


```
[ForeignKey("TeacherID")]
```
With this, we are basically telling the EntityFramework that we want our student to link to a teacher and that the database can find this teacher using the foreign key "TeacherID". This way the TeacherID column in our Student table will point to the normal ID in the Teacher table.
There are a lot more data annotations you can use, for instance if you want to make properties not nullable (`[Required]'), if you want properties to have a min or max length, if you want your properties to be formatted in a certain way, etc.

4. Now it's time to add some extra packages and useful tools. Run the following commands:

(If, with Rider, you get an error "Could not find any project in ...", run `dotnet add YourProjectName package packageName` instead, or look for the package name in your NuGet package manager (click the NuGet button at the bottom))

```
    dotnet tool install --global dotnet-ef

TRY 
    dotnet tool install --global aspnet-codegenerator --version 3.1.3
ELSE    
    (dotnet tool install --global dotnet-aspnet-codegenerator)

    dotnet add package MySql.Data.EntityFrameworkCore
(OR dotnet add package Microsoft.EntityFrameworkCore.SQLite)
    dotnet add package Microsoft.VisualStudio.Web.CodeGeneration.Design
    dotnet add package Microsoft.EntityFrameworkCore.Design
    dotnet add package Microsoft.EntityFrameworkCore.SqlServer
    dotnet add package Microsoft.Extensions.Logging.Debug
```

Check if aspnet-codegenerator version is the same as SDK (if you used `dotnet tool install --global dotnet-aspnet-codegenerator`, a newer version may have been installed):

```
dotnet tool list -g
dotnet --list-sdks
```

The dotnet-ef tool adds the Entity Framework, which is an ORM meaning it allows you to convert C# Objects to database entries and back. Again, if you've worked with Symfony already,
 this should be familiar to you!

The codegenerator tool makes some useful commands available with which you can create your CRUD pages based on the models.

The SQL packages are .NET packages we need to add to a project when we need to connect to a database to store information.

5. We still haven't created a class to work with our database yet. 
Create a new folder, give it an appropiate name, like "Data", and create a new file.
By convention this file is named "YourAppName" + "Context.cs", so name it YourAppNameContext.cs.

Inside the file, specify that its contents are inside off the namespace "YourAppName.Data" (`namespace YourAppName.Data{}`) if your editor doesn't do this for you.

6. Inside this namespace we have to create our Context class, which extends the EntityFramework's DbContext class.

This is what that class should look like:
```
public class YourAppNameContext : DbContext
    {
        public YourAppNameContext (
            DbContextOptions<YourAppNameContext> options)
            : base(options)
        {
        }

        public DbSet<YourAppName.Models.Student> Student { get; set; }
        public DbSet<YourAppName.Models.Teacher> Teacher { get; set; }    
}
```

If you don't have an option to import the DbContext class, add `using Microsoft.EntityFrameworkCore;` manually.

A short explanation of this code is in order...

Inside of our class' constructor method (`public YourAppNameContext`) we pass the options we will specify later. 
Notice that the body of the constructor is empty. 

The way we pass the options is by using `: base(options)`, which means we are passing the options to the constructor of the base class,
DbContext. 

Since our Context class extends this class, it will have an options property by default, because this property is inherited from the base DbContext class.

Lastly, we have two DbSet properties that correspond to the tables we want in our database, so one for Student and one for Teacher.


7. We now have a class to connect to our database, but we still have to work on our "options" that we want to pass to this class.
One of the options our Context class needs is a connection string for our database.

We can add one or more connection strings in our appsettings.json file. 
```
"ConnectionStrings": {
    "StudentTeacherContext": "server=localhost;database=databaseName;user=userName;password=password;"
  }
```

It doesn't matter what you call your connection string (in our example "StudentTeacherContext"), just remember where you can find the name (or remember it) for a next step.

Replace "databaseName" with whatever name you want your database to get, you don't have to create a database yet with MySQL! Then replace "userName" and "password" with your MySQL username and password.

8. Now we have to tell our code it should use the DbContext with as option the connection string we just created. For this we need to go into the Startup.cs file.

In this file, add two new using statements (or import them later with Rider): 

```
using YourAppName.Data;
using Microsoft.EntityFrameworkCore;
```

Then in the ConfigureServices() method, we need to add a new service, so let's add 
```
services.AddDbContext<YourAppNameContext>(options =>
        options.UseMySQL(Configuration.GetConnectionString("StudentTeacherContext")));```
```
Here we basically add our class that deals with our database and tell the configuration it should look for the connection string we named earlier. If you forgot which name you gave your connection string, go check in appsettings.json.

If you want to understand the program's logic, then continue reading. Otherwise, continue to step 9. 

The Startup class is called like this:

*I.* Our runtime fires the Main() method in Program.cs.

*II.* The Main() method calls the CreateHostBuilder() method, also in the Program.cs class.

*III.* Inside the CreateHostBuilder() method we tell which class to use as startup (configuration, services) with `UseStartup<Startup>()`.
 
9. We now have our database class, models that represent the database entries, and our configuration with the connection string. 

It's time to "create" some pages for our view where we can display, create, update and delete database entries. One of the good things about frameworks is that they have tools with which you can quickly generate basic pages that are based on your models.

Remember that dotnet-aspnet-codegenerator tool we installed? The name should give you a hint as to what it does already!

Now run this command, but change "YourAppNameContext" to the name you've given your database context:

```
dotnet aspnet-codegenerator razorpage -m Student -dc YourAppNameContext -udl -outDir Pages/Students --referenceScriptLibraries
``` 

This command basically specifies to the codegenerator to make razorpages based on the model of Student and your context class, and to put those CRUD pages inside of the Pages/Students directory.

10. Run the same command again, but this time for your teacher model.

11. It's finally time to create our database! This is done with the EF tool we also installed earlier. 

This means we can again run a command to create a database based on the models we created earlier. 
Each creation, update or deletion of a table or column, is stored in a migration file.

The EF tool checks your models, DbContext and configuration (such as the database name specified in the connection string). If it sees that there is a change, it will add that change in a new migration file.

Since we don't have a database yet, let's just start with our first migration, shall we? Run these commands:

```
dotnet ef migrations add YourAppropiateMigrationName
dotnet ef database update
```

The first command checks your models etc. with the database, and if there are any changes, makes a new migration file containing code to specify how the database should be adapted to match our models again. 

Since we don't have our database yet, our first migration file will generate code to create that database with the tabels for our models.

"InitialCreate" is the name we've given to our migration so we can easily recognize it as the first creation of our database tables.

The second command runs the code in the migration files and updates the database based on that code. If you want to make changes to your model later, these are the two commands you'll have to run again.

You can check the result of the first command in the newly created Migrations folder. In the ..._IntitialCreate.cs file you see two methods: Up() and Down(). Up() is the method that is called when you make changes to your database, Down() is to undo those changes.
If you have worked with databases before, you can more or less read what the migrations file is going to do (what columns it's creating, what constraints it's adding, foreign keys etc.).
Remember that ForeignKey data annotation we added? In the migration file you should see this for constraints:

```
table.ForeignKey(
                        name: "FK_Student_Teacher_TeacherID",
                        column: x => x.TeacherID,
                        principalTable: "Teacher",
                        principalColumn: "ID",
                        onDelete: ReferentialAction.Restrict);
```
So with just two commands we created a database after the models we set up and with data annotations to specify the characteristics of our columns!

12. If we now run our app (click the green "play" button in your editor or use "dotnet run" in your terminal), 
we can go to our indexpages for students and teachers by adding /Students or /Teachers to the URL, where you can see the links that lead to every CRUD page that the codegenerator has created for us!.

Add two links using the asp-page tag helper to the navbar. The navbar was in one of the shared files, remember which one? You can add more links in your web page whenever you think it's useful.

13. Create one or more teachers first so we have at least one teacher in our database.

14. Now let's add a dropdown list to the Student creation page so we can select the teacher for our student that way! To do that we'll need to add this to the model for that page:

```
        public IList<Teacher> Teacher { get;set; }
        public async Task<IActionResult> OnGetAsync()
        {
            Teacher = await _context.Teacher.ToListAsync();
            return Page();
        }
```

Now our model also has acces to the teachers in the database!

15. Now that our model has a list of teachers available, we can use this list in the Create.cshtml page. Add the following div to the form:

```
            <div class="form-group">
                <label asp-for="Student.TeacherId" class="control-label"></label>
                <select asp-for="Student.TeacherId" class="form-control">
                    <option value="" selected disabled hidden>Select teacher</option>
                    
                    @foreach (Teacher teacher in Model.Teacher)
                    {
                        <option value="@teacher.Id">
                            @Html.Label($"{teacher.FirstName} {teacher.LastName}")
                        </option>
                    }
                    
                </select>
            </div>
```

The "@" before code in .cshtml means that the following bit is an expression, so when we use an `@foreach`, we can iterate over every teacher in our list (Model.Teacher) and do something with every teacher-instance. 

With this code we basically added options to our <select> where the value is the Id of the teacher, and the text for our option uses string interpolation to display the teachers full name.

When this gets posted, the OnPostAsync() method in your Create.cshtml.cs file triggers. 

16. Add a student and check if the database adds the TeacherId (as well as all the other properties). Once you add a student, you get redirected to the Students index page, but we can't see yet which teacher our student has! 

So let's start working on that, shall we?

Even if we have our Teacher Teacher property in the Student model, this Teacher object doesn't load automatically. We need to add something to the code in the pagemodel for the Students index page for that. The method that fetches your students from the database should be changed to look like this:

```
public async Task OnGetAsync()
        {
            Student = await _context.Student
                .Include(student => student.Teacher)
                .ToListAsync();
```

By default, EF doesn't load complex datastructures related to an entity, so we need to specify that our Student entities have to include their Teacher Teacher property with .Include().

Now you can use Student.Teacher in the Razor Page's view! Add another header to the table for our teacher, and add the teacher names by extracting FirstName and LastName from the Teacher object. Remember to use string interpolation!  


17. **CONGRATULATIONS!** You have survived the workshop, and that on a Friday afternoon!

But wait... There's a **Bonus:**

* For our Teachers detail page, display a list with all the students that teacher has.
* For the front-end lovers: style your page a bit!

