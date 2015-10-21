# Create Code-first Database in .NET

## Getting Started

### Create project
- Create a new ASP.NET MVC Web Application
- Click on New | Project | Visual C# | Web and then clicking on ASP.NET Web Application
- Change the Name to MyFirstDatabase
- Click on the OK Button
- Select Empty and check the MVC box
- Click on the OK button and the project will be created for you

### Import Entity Framework

- Click on the Tools | NuGet Package Manage | Manage NuGet Packages for Solution
- Click on Online | nugget.org and look for the EntityFramework
- Click on the EntityFramework and then click on the Install button
- Click the OK button on the Select Projects window and if necessary, Click on the I Accept button
- Repeat this process for installing Bootstrap and JQuery
- Click on the Close Button

## Create data model

This tutorial will consist of creating one model and one table called Player

### Add model

- Right-mouse click the Models folder and choose Add | Class
- Name the new model class Player.cs and click on the Add button

Add the following code:

```csharp
using System.ComponentModel.DataAnnotations;
using System.ComponentModel.DataAnnotations.Schema;
...
public class Player
{
  [DatabaseGenerated(DatabaseGeneratedOption.None)]
  [Key]
  public int playerID { get; set; }
  public String playerLastName { get; set; }
  public String playerFirstName { get; set; }
  public String playerPosition { get; set; }
}
```

### Create DBContext

- Create a folder in the project by right-clicking the project in Solution Explorer and click Add, and then click New Folder
- Name the new folder DAL
- In that DAL folder, right-click and choose Add | Class to create a new class file
- Name the file SportsContext.cs and click Add

```csharp
using MyFirstDatabase.Models;
using System.Data.Entity;
using System.Data.Entity.ModelConfiguration.Conventions;

namespace MyFirstDatabase.DAL
{
    public class SportsContext : DbContext
    {   
        public SportsContext() : base("SportsContext")
        {
        }
        
        public DbSet<Player> Players { get; set; }

        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder.Conventions.Remove<PluralizingTableNameConvention>();
        }
    }
}
```

### Add connection string

- Open web.config 
- In the <configuration> node add:

```
<connectionStrings>
    <add name="SportsContext" connectionString="Data Source=(LocalDb)\v11.0;AttachDbFilename=|DataDirectory|\SportsContext.mdf;Initial Catalog=SportsContext;Integrated Security=SSPI;" providerName="System.Data.SqlClient" />
  </connectionStrings>
```

- In the <entityFramework> node add:

```
<contexts>
      <context type="MyFirstDatabase.DAL.SportsContext, MyFirstDatabase">
        <databaseInitializer type="MyFirstDatabase.DAL.SportsInitializer, MyFirstDatabase" />
      </context>
</contexts>
```

### Create Initializer
- In the DAL folder, right-click and choose Add | Class
- Name the file SportsInitializer.cs

Add the following code: 
```
using MyFirstDatabase.Models;
...
public class SportsInitializer : System.Data.Entity.DropCreateDatabaseAlways<SportsContext>
{

}
```

### Add Application Start Function

- Open the Global.asax.cs file
- Add the following code to the Application_Start method:

```csharp
using MyFirstDatabase.DAL;
using System;
using System.Collections.Generic;
using System.Data.Entity;
using System.Linq;
using System.Web;
using System.Web.Mvc;
using System.Web.Routing;

namespace MyFirstDatabase
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
            Database.SetInitializer<SportsContext>(new CreateDatabaseIfNotExists<SportsContext>());

            AreaRegistration.RegisterAllAreas();
            RouteConfig.RegisterRoutes(RouteTable.Routes);
        }
    }
}
```
### Build Scaffoled Controller

**Save and build the project**

- Right-mouse click on the Controllers folder
- Choose Add | New Scaffolded item
- Select MVC 5 Controller with views, using Entity Framework and click the Add button
- Select Player as the Model class
- Select SportsContext as the Data context class
- Name the Controller PlayersController
- Click the Add button




