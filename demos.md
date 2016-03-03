
# Demos

## Install ASP.NET 5

Using Homebrew...

```
brew tap aspnet/dnx
brew update
brew install icu4c
brew install dnvm
source dnvm.sh
dnvm
dnvm help
dnvm list
```

To install the latest version, run the command `dnvm upgrade`.

## .NET Version Manager (DNVM)

To install a different version

```
dnvm install 1.0.0-beta8
```

Switch to a different version:

```
dnvm use 1.0.0-beta8
dnvm use 1.0.0-beta8 -r coreclr
```

To change your default version:

```
dnvm use 1.0.0-beta8 -p
```

To delete a runtime:

```
dnvm uninstall 1.0.0-beta8
```

Or you can just delete it off of the file system.

## Create New Empty Application

```
yo aspnet
```

## .NET Development Utility (DNU)

* Build, package and publish DNX projects
* Building a project produces the binary outputs for the project
* Packaging produces a NuGet package that can be uploaded to a package feed
* Publishing collects all required runtime artifacts (the required DNX and packages)
    into a single folder so that it can be deployed as an application

## Restore

After creating a new application or updating your project's dependencies call DNU
to restore your packages.

```
dnu restore
```

* This also updates the `project.lock.json` file
* The `project.lock.json` file contains all of the dependency graph information for your project

While the packages are loading...

## Visual Studio Code

* Officially Microsoft supported editor
* Built on top of GitHub's Electron shell

## Atom

* Add OmniSharp and you've got another great editor

## DNX Projects

* A DNX project is a folder with a project.json file
* The name of the project is the folder name

## New Project System

Just add/remove files and folders using the file system (no more project file).

## Application Anatomy

* Applications are defined using a public Startup class

```
public class Startup
{
    // This method gets called by the runtime. Use this method to add services to the container.
    // For more information on how to configure your application, visit http://go.microsoft.com/fwlink/?LinkID=398940
    public void ConfigureServices(IServiceCollection services)
    {
    }

    // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
    public void Configure(IApplicationBuilder app)
    {
        app.UseIISPlatformHandler();

        app.Run(async (context) =>
        {
            await context.Response.WriteAsync("Hello World!");
        });
    }

    // Entry point for the application.
    public static void Main(string[] args) => Microsoft.AspNet.Hosting.WebApplication.Run<Startup>(args);
}
```

## Services

You "add" services to your application.

* Services are components intended for common consumption in your application
* Services are made available through dependency injection
* Three varieties of services
 * Singleton (one instance)
 * Scoped (per HTTP request)
 * Transient (per container request)

## Built-In Dependency Injection

DI is built into not just MVC, but into the entire ASP.NET framework and runtime.

## Middleware

Your application will "use" middleware.

* Compose your request pipeline using middleware
* Middleware perform asynchronous logic on an HttpContext
* Optionally invoke the next middleware in the sequence or terminate the request directly
* Use extension methods on IApplicationBuilder to configure middleware

## Use IIS Platform Handler

The call to `app.UseIISPlatformHandler()` adds middleware for interacting with the IIS HTTP Platform Handler reverse proxy module. The middleware handles forwarded Windows Authentication, request scheme, remote IPs, etc.

### IIS HTTP Platform Handler

The HTTP Platform Handler (HttpPlatformHandler v1.2) is an IIS module which does two things:

1. Process management of HTTP Listeners - this could be any process that can listen on a port for HTTP requests, for example Tomcat, Jetty, Node.exe, Ruby, etc.
1. Proxy requests to the process it manages

## Application Entry Point

This is a recent addition to the `Startup` class. Having a static `Main` method provides symmetry between ASP.NET and Console apps when DNX is starting your application.

## Web Root

* Root location in your project from which HTTP requests are handled
* "wwwroot" by default (but can be configured)

## Build

```
dnu build
```

Note: This step is not absolutely necessary as calling DNX to run your application
will also kick off a build.

Note: When using coreclr (at least on OS X) you cannot build using the .NET 4.5.1 target.

```
"frameworks": {
  "dnx451": {},
  "dnxcore50": {}
},
```

## Broken Builds

* Make sure that your versions line up
* There is a lot of change right now... so mixing and matching versions will often break your apps

## .NET Execution Environment (DNX)

* Software development kit (SDK) and runtime environment
* Provides a host process, CLR hosting logic and managed entry point discovery
* Takes care of hosting the CLR, handling dependencies and bootstrapping your application

## Running the App

```
dnx web
```

The new `dnx-watch` command is also available, which watches the file system and will restart your application when you make changes.

```
dnu commands install Microsoft.Dnx.Watcher
```

## Kestrel

* Server based on libuv
* Nginx can be used as a reserve proxy to forward requests to Kestrel
* See http://druss.co/2015/06/asp-net-5-kestrel-nginx-web-server-on-linux/

## Tag Helpers

Tag Helpers enable server-side code to participate in creating and rendering HTML elements in Razor files.

* An HTML-friendly development experience
* A rich IntelliSense environment for creating HTML and Razor markup
* A way to make you more productive and able to produce more robust, reliable, and maintainable code using information only available on the server

Creating a menu item in the `_Layout.cshtml` file using the `ActionLink` HTML helper method:

```
<li>@Html.ActionLink("Home", "Index", "Home")</li>
```

becomes this with Tag Helpers:

```
<li><a asp-controller="Home" asp-action="Index">Home</a></li>
```

## Tag Helpers Form Example

* Add `Models` folder
* Add a `Hero` class
 * Snippet: \_heroclass
* Update the `HeroesController.Index` method to return an instance of the `Hero` class
* Update the `Index.cshtml` view
 * Add model directive `@model WebApplicationBasic.Models.Hero`
 * Snippets: \_oldview and \_newview

## Web API

Snippet: \_apicontroller

* Add a `HeroesController` to the `Controllers` folder
* Use the snippet to add the code
* Walk through the code
* Run the app
* Show the result in POSTMAN (or the browser)

## Front End Build Support

JavaScript and CSS bundling has been removed from ASP.NET MVC. In ASP.NET Core, you rely upon external tools such as gulp.

In the `project.json` file, it's easy to specify commands to run.

```
"scripts": {
  "prepublish": [
    "npm install",
    "bower install",
    "gulp clean",
    "gulp min"
  ]
}
```

## Environment Variables

Use `export` to see a list of your environment variables. To set an environment variable:

```
export ASPNET_ENV=Development
```

This allows code in the `Startup` class to work properly.

```
if (env.IsDevelopment())
{
    app.UseDeveloperExceptionPage();
}
else
{
    app.UseExceptionHandler("/Home/Error");
}
```

For a list of the available environment variables, see https://github.com/aspnet/Home/wiki/Environment-Variables

## Deploying to Azure

TODO - Test deploying to Azure... has this been made easier now???

Easy to use the GitHub integration to deploy your application.

Beta6 is the current default version of ASP.NET 5. To ensure that Azure will use Beta7,
we need to add a global.json file in the root of our solution.

Tip: Don't put the global.json file in the root of your project!

Snippet globaljson

```
{
	"projects": [
		"src"
	],
	"sdk": {
		"version": "1.0.0-beta7"
	}
}
```

Since we setup automated deployments from GitHub to Azure, all we need to do is to push our changes.
