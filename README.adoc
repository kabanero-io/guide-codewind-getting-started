---
permalink: /guides/guide-codewind/
---
:layout: guide
:duration: 30
:description: Take advantage of Codewind's tools to help build high quality cloud native applications regardless of which IDE or language you use.
:tags: ['kabanero', 'appsody', 'codewind', 'tekton']
:projectid: guide-codewind
:page-layout: guide
:page-duration: 30
:page-releasedate: TBD
:page-guide-category: developer tools
:page-essential: true
:page-essential-order: 1
:page-description: Take advantage of Codewind's tools to help build high quality cloud native applications regardless of which IDE or language you use. 
:guide-author: Kabanero
:page-tags: ['kabanero', 'appsody', 'codewind', 'tekton']
:page-permalink: /guides/{projectid}
:repo-description: TBD
= Getting Started with Codewind in Kabanero
:toc:

== Objectives

*Guide's objective*

* To explain how Codewind provides tools to help build high quality cloud-native applications; regardless of which IDE or language you use.

*Learning objectives*

You can either:

* Install Codewind and begin <<#developing-with-vs-code, developing with VS Code>> or <<#developing-with-eclipse, developing with Eclipse>>. 
* Develop a simple microservice with your chosen IDE. 

== Codewind in Kabanero 

Before working with Codewind, understand that Codewind works within Kabanero, an open source project in a microservices-based framework. Kabanero has three components:

* Appsody
* Codewind
* Tekton

For more information on how Kabanero and its components work, please see https://kabanero.io/guides/overview/#architecture[Kabanero's Architecture and Development Workflows] 

== Developing a simple microservice

=== *Developing with VS Code*

Why VS Code? You can use Codewind for Visual Studio Code to develop and debug your containerized projects from within VS Code.

Write code, track application and build statuses, view project logs, and run your application.
Codewind for VS Code supports development of Microprofile/Java EE, Java Lagom, Spring, Node.js, Go, Python, Swift, and Appsody containerized projects.
In addition, the tools support easily debugging Microprofile/Java EE, Spring, and Node.js applications.

The VS Code tools are open source. You can browse the code, open issues, and contribute.

*Installing Codewind for VS Code*

The Codewind installation includes two parts:

. The VS Code extension installs when you install Codewind from the VS Code Marketplace or when you install by searching in the `VS Code Extensions` view.
. The Codewind back end containers install after you click `Install` when you are prompted. After you click `Install`, the necessary images are downloaded from the internet. The download is approximately 1 GB.
* [Optional] If you don’t click `Install` when the notification window first appears, you can access the notification again. Go to `View->Explorer`. Then click `Codewind`, and hover the cursor over `Codewind` where there is a switch to turn Codewind on or off. Click the switch so that it is `On`. The notification window is displayed. 

The following images are pulled. These images together form the Codewind back end:
```
. eclipse/codewind-initialize-amd64
. eclipse/codewind-performance-amd64 
. eclipse/codewind-pfe-amd64
```
When the installation is complete, the extension is ready to use, and you are prompted to open the `Codewind workspace`.

Codewind creates the `~/codewind-workspace` folder to contain your projects. 
You can open the `codewind-workspace` or a project within the workspace as your VS Code workspace.

*Creating an Appsody project*

. Create a new MicroProfile project. Hover over the `Projects` entry underneath `Codewind in Visual Studio Code`, and press the `+` icon to create a project.
. Choose the `Appsody Eclipse Microprofile Template`, and give the project a name, like `Appsody Calculator` (in this example).
* [Optional] Use the `Command+P` key combination. 
* You now see a list of commands.
* Press `Shift+>`.
* Find and select `Codewind: Manage Template Sources`. 
* Enable the `Appsody Stacks - appsodyhub`.
. Press `Enter` to create the project. 

The project has now been generated and takes a little time to build because it is a Microprofile microservice with multiple dependencies. To monitor the progress, expand `Codewind -> Projects`, and right-click the menu option `Show all logs:`.

After several minutes, you should see the following message:
```
[Container] [�[1;34mINFO�[m] [AUDIT   ] CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 69.467 seconds.
```
And the project status should change to `Running`.

To access the application endpoint in a browser, select the Open App icon next to the project's name, or right-click the project and select the `Open App menu option`. This opens up the application in the running container showing the Welcome to your Appsody Microservice page.

Let's take a look at the code. In the `VS Code EXPLORER` you should see a `CODEWIND-WORKSPACE` entry with your project name. If you don't find it, right-click the project and choose `Add Folder to Workspace`. In the workspace view, expand the project and the sub-folders to show all the files created from the Appsody template. Note: The template is not intended to be a sample as most people would end up having to delete the code each time. It aims to provide the starter code, server configuration and build to which you can add your code.

The main Java files are StarterLivenessCheck, StarterReadinessCheck and StarterApplication. The first two provide the outlines for liveness and readiness checks that can be hooked up to Kubernetes liveness and readiness probes. These are implemented using MicroProflie Health. The third file is a JAX-RS application,which provides the Application Path for your REST API.

Let's add a REST service to your application. Navigate to the `src/main/java/dev/appsody/starter` directory, and create a file called `Calculator.java`, which will be our JAX-RS resource. Populate the file with the following code and save it:
```
package dev.appsody.starter;

import javax.ws.rs.ApplicationPath;
import javax.ws.rs.core.Application;
import java.util.List;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;
import javax.ws.rs.core.Response;
import javax.ws.rs.core.Response.Status;

import jdk.nashorn.internal.objects.annotations.Getter;

import java.util.ArrayList;
import javax.ws.rs.PathParam;

@Path("/calculator")
public class Calculator extends Application {
    
    @GET
    @Path("/aboutme")
    @Produces(MediaType.TEXT_PLAIN)
    public response aboutme(){
        return "You can add (+), subtract (-), and multiply (*) with this simple calculator.";
    }

	@GET
    @Path("/{op}/{a}/{b}")
	@Produces(MediaType.TEXT_PLAIN)
    public Response calculate(@PathParam("op") String op, @PathParam("a") String a, @PathParam("b") String b)
    {
        int numA = Integer.parseInt(a);
        int numB = Integer.parseInt(b);

      switch(op)
      {
          case "+":
              return Response.ok(a + "+" + b + "=" + (Integer.toString((numA + numB)))).build();

          case "-":
              return Response.ok(a + "-" + b + "=" + (Integer.toString((numA - numB)))).build();

          case "*":
              return Response.ok(a + "*" + b + "=" + (Integer.toString((numA * numB)))).build();

          default:
          	return Response.ok("Invalid operation. Please Try again").build();
      }
    }
}
```
Any changes you make to your code will automatically be built and re-deployed by Codewind, and viewed in your browser. Let's see this in action.

If you still have the logs `OUTPUT` tab open, you will see that the code is compiled and the application has restarted. You should see messages like:
```
[Container] [�[1;34mINFO�[m] Source compilation was successful.
[Container] [�[1;34mINFO�[m] [AUDIT   ] CWWKT0017I: Web application removed (default_host): http://04013dbc9c11:9080/
[Container] [�[1;34mINFO�[m] [AUDIT   ] CWWKZ0009I: The application starter-app has stopped successfully.
[Container] [�[1;34mINFO�[m] [WARNING ] CWMH0053W: The readiness health check reported a DOWN overall status because the following applications have not started yet: [starter-app]
[Container] [�[1;34mINFO�[m] [AUDIT   ] CWWKT0016I: Web application available (default_host): http://04013dbc9c11:9080/
```
Now we can do some interesting stuff with this new resource. You can point your browser at a couple of things. Note: Use the port number you saw when you first opened the application.

`http://127.0.0.1:/starter/calculator/aboutme` 

You should see the following response:

```
You can add (+), subtract (-), and multiply (*) with this simple calculator.
```

You could also try a few of the sample calculator functions: `http://127.0.0.1:<port>/starter/calculator/{op}/{a}/{b}`, where you can input one of the available operations `(+, _, *)`, and an integer a, and an integer b.

So for `http://127.0.0.1:<port>/starter/calculator/+/10/3` you should see: `10+3=13`.

*Testing the default endpoint*

. To make sure your code change was picked up, test your new endpoint.
    * Right-click the project, and select `Open App.` The project root endpoint opens in the browser, and the `Appsody Microservice` page is displayed.
    ** [Optional] To obtain your `<port>` number: 
    ** Right-click your project.
    ** Select `Open Project Overview`.
    ** Find `Exposed App Port`and here is the port value.
    ** Copy the 'Exposed App Port`value and replace `<port>` in the URL with this value. 
    ** Make sure to remove the `< >` symbol in the URL. 
    * Navigate to the new endpoint. If you copied the previous snippet, add `/health/test/` to the URL.
    * See the new response: `Yep, it worked!!`

*Debuggging the app*

 . You can debug your application within the container. To debug a containerized project, restart it in `Debug` mode.
    * Right-click the project, and select `Restart in Debug Mode`.
    * The project restarts into the `Debugging` state.
    * A debug launch configuration is created in `.vscode/launch.json`.
    * The debugger attaches, and VS Code opens the `Debug` view.
    * You can detach and reattach the debugger at any time, as long as the project is still in `Debug` mode.
    
 . All of the VS Code debug functionality is now available.
    * Refresh the new endpoint page that you opened in <<Testing the default endpoint-title, Testing the default endpoint>> so that a new request is made, and the breakpoint is identified.

If debugging does not work, complete these steps:

. Select the `debug icon`.
. At the top, you see a green arrow, highlighting the debug session. 
. Use the drop-down arrow to select the debug session that corresponds to your project. 
    
VS Code suspends your application at the breakpoint. Here you can step through the code, inspect variables, see the call stack, and evaluate expressions in the `Debug Console`.

*Nice work! Where to next?*

You've completed the steps to create a simple microservice using the VS Code IDE. 

=== *Developing with Eclipse*

Why Eclipse? You can use Codewind for Eclipse to develop and debug your containerized projects from within Eclipse.

Use the Eclipse IDE to create and make modifications to your application, see the application and build status, view the logs, and run your application.
Codewind for Eclipse supports development of Microprofile/Java EE, Java Lagom, Spring, Node.js, Go, Python, Swift, and Appsody containerized projects. 
In addition, Microprofile/Java EE, Spring, and Node.js applications can be debugged.

The Eclipse tools are open source. You are encouraged to browse the code, open issues, and contribute.

*Installing Codewind for Eclipse*

The Codewind installation includes two parts:

.  The Eclipse plug-in installs when you install Codewind from the Eclipse Marketplace or when you install by searching in the `Eclipse Extensions` view.
. When you click install, the Codewind backend containers are installed. After you click `Install`, the necessary images are downloaded from the internet. The download is approximately 1 GB.

The following images are pulled. These images together form the Codewind backend:
```
. eclipse/codewind-initialize-amd64
. eclipse/codewind-performance-amd64
. eclipse/codewind-pfe-amd64
```

To view Codewind Explorer: 

. From the top command bar, click `Window->Show View->Other`. 
. Find the `Codewind` folder, and click the drop-down arrow.
. Select `Codewind Explorer`.

When the installation is complete, the extension is ready to use, and you are prompted to open the `Codewind workspace`. 

Codewind creates the `~/codewind-workspace` folder to contain your projects.
You can open the `codewind-workspace` or a project within the workspace as your Eclipse workspace. 

*Creating an Appsody project*

. Double-click the `Codewind Explorer` tab.
. Expand `Codewind` by clicking the drop-down arrow.
. Right-click `Projects (Local)`.
. Select `Create New Project...`
. Under `Template`, select `Appsody Eclipse MicroProfile template`. 
* [Optional] Double-click the `Codewind Explorer` tab.
* Expand `Codewind` by clicking the drop-down arrow.
* Right-click `Projects (Local).`
* Select `Create New Project...`
* If you don't see an Appsody template, select the `Manage Tenplate Sources...` link at the end of the page.  
* Select the `Appsody Stacks - appsodyhub` checkbox. 
* Click `OK`.
* The templates are refreshed, and the Appsody templates are available. 
. Name your project `Appsody Calculator` (for this example) in the `Project` name bar.
. Click `Finish`.
. Your project is displayed in the `Projects (Local)` section. The progress for creating your project is tracked at the end of the page. 
. When you see `[Running] [Build successful]` next to your project, then your project is ready. 
. Note, since this project is a MicroProfile, it takes a long time to create due to all its dependencies. 
. Right-click your project, and select `Open Application` to begin work.
. To see the backend of your project, right-click on your project, and select `Show Log Files`.
. Select `Show All`. Then a `console tab` is displayed where you see your project's backend. 

Let's take a look at the code. In the `Eclipse EXPLORER` you should see a `CODEWIND-WORKSPACE` entry with your project name. If you don't find it, right-click the project and choose `Add Folder to Workspace`. In the workspace view, expand the project and the sub-folders to show all the files created from the Appsody template. Note: The template is not intended to be a sample as most people would end up having to delete the code each time. It aims to provide the starter code, server configuration and build to which you can add your code.

The main Java files are StarterLivenessCheck, StarterReadinessCheck and StarterApplication. The first two provide the outlines for liveness and readiness checks that can be hooked up to Kubernetes liveness and readiness probes. These are implemented using MicroProflie Health. The third file is a JAX-RS application, which provides the Application Path for your REST API.

Let's add a REST service to your application. Navigate to the `src/main/java/dev/appsody/starter` directory, and create a file called `Calculator.java`, which will be our JAX-RS resource. Populate the file with the following code and save it:
```
package dev.appsody.starter;

import javax.ws.rs.ApplicationPath;
import javax.ws.rs.core.Application;
import java.util.List;
import javax.ws.rs.GET;
import javax.ws.rs.Path;
import javax.ws.rs.Produces;
import javax.ws.rs.core.MediaType;
import javax.ws.rs.core.Response;
import javax.ws.rs.core.Response.Status;

import jdk.nashorn.internal.objects.annotations.Getter;

import java.util.ArrayList;
import javax.ws.rs.PathParam;

@Path("/calculator")
public class Calculator extends Application {
    
    @GET
    @Path("/aboutme")
    @Produces(MediaType.TEXT_PLAIN)
    public response aboutme(){
        return "You can add (+), subtract (-), and multiply (*) with this simple calculator.";
    }

	@GET
    @Path("/{op}/{a}/{b}")
	@Produces(MediaType.TEXT_PLAIN)
    public Response calculate(@PathParam("op") String op, @PathParam("a") String a, @PathParam("b") String b)
    {
        int numA = Integer.parseInt(a);
        int numB = Integer.parseInt(b);

      switch(op)
      {
          case "+":
              return Response.ok(a + "+" + b + "=" + (Integer.toString((numA + numB)))).build();

          case "-":
              return Response.ok(a + "-" + b + "=" + (Integer.toString((numA - numB)))).build();

          case "*":
              return Response.ok(a + "*" + b + "=" + (Integer.toString((numA * numB)))).build();

          default:
          	return Response.ok("Invalid operation. Please Try again").build();
      }
    }
}
```
Any changes you make to your code will automatically be built and re-deployed by Codewind, and viewed in your browser. Let's see this in action.

If you still have the logs `OUTPUT` tab open, you will see that the code is compiled and the application has restarted. You should see messages like:
```
[Container] [�[1;34mINFO�[m] Source compilation was successful.
[Container] [�[1;34mINFO�[m] [AUDIT   ] CWWKT0017I: Web application removed (default_host): http://04013dbc9c11:9080/
[Container] [�[1;34mINFO�[m] [AUDIT   ] CWWKZ0009I: The application starter-app has stopped successfully.
[Container] [�[1;34mINFO�[m] [WARNING ] CWMH0053W: The readiness health check reported a DOWN overall status because the following applications have not started yet: [starter-app]
[Container] [�[1;34mINFO�[m] [AUDIT   ] CWWKT0016I: Web application available (default_host): http://04013dbc9c11:9080/
```
Now we can do some interesting stuff with this new resource. You can point your browser at a couple of things. Note: Use the port number you saw when you first opened the application.

http://127.0.0.1:/starter/calculator/aboutme 

You should see the following response:

```
You can add (+), subtract (-), and multiply (*) with this simple calculator.
```

You could also try a few of the sample calculator functions: `http://127.0.0.1:<port>/starter/calculator/{op}/{a}/{b}`, where you can input one of the available operations `(+, _, *)`, and an integer a, and an integer b.

So for `http://127.0.0.1:<port>/starter/calculator/+/10/3` you should see: `10+3=13`

*Editing the project files*

Editing actions are available by right-clicking on the project in the `Codewind Explorer` view. Most actions are only available if the project is enabled.

Some actions open the default Eclipse browser. If you find that the default Eclipse browser cannot handle the content, change the default browser by navigating to `Window -> Web Browser` and selecting a different browser from the list.

Project settings tell Codewind more about the specifics of your project and can affect the status and/or behavior of your application. You can configure project settings when you:

. Go to Project Overview page that is accessible from a project’s context menu, or, 
. Find the project settings in the `.cw-settings` file of the project which you can edit.

*Testing the new endpoint*

. To make sure your code change was picked up, test your new endpoint.
    * Right-click the project and select `Open App`. The project root endpoint opens in the browser, and the `Appsody Microservice` page appears.
    ** [Optional] To obtain your `<port>` number:
    ** Right-click your project.
    ** Select `Open Project Overview`.
    ** Under 'General' you find `External Application Port'.
    ** Copy the 'External Application Port` value and replace `<port>` in the URL with this value. 
    ** Make sure to remove the `< >` symbol in the URL. 
    * Navigate to the new endpoint. If you copied the previous snippet, add `/health/test/` to the URL.
    * See the new response: `Yep, it worked!!`

*Debugging the app*

Codewind for Eclipse supports debugging Microprofile/Java EE and Spring projects.
The tools also help you set up a debug session for Node.js projects in a Chromium based browser.

Debugging Microprofile/Java EE and Spring projects:

[[bookmark-a]]Prerequisites:

. If you have not done so already, import your project into Eclipse to make the source available to debug.
    * Right-click your project in the `Codewind Explorer` view.
    * Select `Import Project`. 
. If you need to debug any initialization code, set breakpoints in this code now. You can also set breakpoints in your application code at this time.
. [Optional] If you want to use Java hot code replace and change your code while you debug, disable automatic builds.
    * To disable automatic builds, right-click your project in the `Codewind Explorer` view and select `Disable Auto Build`. 
    * If you want to start a build while automatic builds are disabled, right-click your project and select `Build`.
    * Enable automatic builds again after you finish debugging. To enable automatic builds again, right-click your project and select `Enable Auto Build`.

Debugging:

 . To restart your Microprofile/Java EE or Spring application in debug mode, right-click on the project in the `Codewind Explorer` view and select `Restart in Debug Mode`.
 . If you did not import your project into Eclipse, you are prompted to do so now. Select one of the following: 
    * `Yes:` To import your project into Eclipse and make the source available for debugging. 
    * `No:` To continue restarting in debug mode without importing your project. There might be no source available for debugging if you choose this option.
    * `Cancel:` To cancel restarting your application in debug mode.
 . Wait for the project state to change to `Debugging` or for the debugger to stop at a breakpoint, if you are debugging initialization code. If you have hit a breakpoint in initialization code, skip to step 6.
 . If you have not done so already, set up any breakpoints that you need in your application.
 . Reload your application in the browser, or if you have not already opened it, right-click the project in the `Codewind Explorer` view, and select `Open Application`. 
 . Eclipse prompts you to switch to the `Debug` perspective when a breakpoint is hit or you can switch manually by clicking `Window > Perspective > Open Perspective > Debug`. All of the Java debug capabilities provided by Eclipse including various breakpoint types, the `Variables` and `Expression` views, and hot code replace are available to you.
 . When you have finished debugging, you can switch back to run mode. Right-click on your project in the `Codewind Explorer` view, and select `Restart in Run Mode`.
 
Attaching to a project in debug mode: 
 
If you detached from the debugger or you restarted Eclipse, you can attach the debugger without restarting again:
 
 . Make sure to do any of the setup you need, such as importing your project into Eclipse and setting breakpoints. For more information, see <<#bookmark-a, Prerequisites>>.
 . Right-click your project in the `Codewind Explorer` view, and select `Attach Debugger`. The `Attach Debugger` menu item is only available for Codewind/Java EE or Spring applications in debug mode if a debugger is not already attached.
 
Debugging Node.js projects:

You can restart your Node.js application in debug mode, and the tools help you launch a debug session in a Chromium based web browser:

. To restart your Node.js application in debug mode, right-click the project in the `Codewind Explorer` view, and select `Restart in Debug Mode`. 
. If you are prompted to select a Chromium based web browser for launching the debug session:
	* Select a Chromium based browser from the list of browsers, or use the `Manage` link to add one.
	* Optionally, select to always use this browser for Node.js debugging.
	* Click `OK` to continue.
. Launch a debug session using the information on the `Node.js Debug Inspector URL` dialog:
	* Click the `Copy URL to Clipboard` button to copy the debug URL.
	* Click the `Open Browser` button to open the browser you selected in the previous dialog. 
	* Paste the URL into the address bar of the browser to start the debug session.

Launching a debug session for a Node.js project in debug mode: 

You can launch a debug session for a Node.js project that is already in debug mode.

. Right-click your project in the `Codewind Explorer` view, and select `Launch Debug Session`. This menu item is only available for Node.js projects in debug mode if a debug session is not already started.
. Follow the steps in Debugging Node.js projects to launch a Node.js debug session, starting with step 2. 

*Nice work! Where to next?*

You've completed the steps to create a simple microservice using the VS Code IDE. 

== What you have learned 

Now that you have completed this guide, you have:

. Installed Codewind on your preference of VS Code or Eclipse.
. Developed your own microservice using Codewind.
. Practiced how to use some of the basic features of Codewind on your preferred IDE.
