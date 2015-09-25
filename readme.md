# Aurelia 

This post is going to show how to set up a simple development environment using the Aurelia framework, but instead of writing JavaScript we are going to be writing TypeScript.
In addition to this we will be using the new tool from Microsoft called Visual Studio Code.

Coming from .Net and being used to the MVVM pattern in WPF, I was pretty happy to see that Rob Eisenberg has devoted his time into making web development as painless as possible. Rob is also the creator of Caliburn and Caliburn.Micro which both are excellent MVVM frameworks on the .Net platform. 

So I started out a few weeks ago to see what it takes to understand the development stack needed to start developing for Web 2.0.

I knew nothing about Gulp, Bower, SystemJS, Node, TypeScript, JSPM or any other hot new thingie out there. 

One of the main reasons that I have never picked up on web development and Javascript is because..well, it's Javascript. 

When being used to strongly typed languages like C# and the advantages of having a proper type system, Javascript always felt like a slippery-slope to me. Being able to write just about anything and wait for it to fail at runtime is just not my cup of tea.

## TypeScript to the rescue

TypeScript is a superset of JavaScript and allows us to start using actual types in our code. In addition to the added type-safety it also aligns nicely with the upcoming versions of JavaScript (ES6/ES7).

## Aurelia + TypeScript

With a framework that encourages views and view-models very much in the same way we would do it in .Net/WPF, doing a little web development suddenly did not seem so scary.
Combine Aurelia with the power of TypeScript we can leverage much of the same development experience we have with WPF if not even better.


## WPF
Windows Presentation Foundation was created as the successor for Winforms and is now the dominant player when it comes to GUI development on Windows based platforms. The problem with WPF is that it is Windows only. While .Net as a platform can be used practically everywhere these days, WPF gets left behind and to my knowledge, there are no immediate plans to port WPF to OS X or/and Linux. With the new tools emerging on the Web, I feel that native WPF applications becomes more and more irrelevant. 

The only place I would consider going native is when doing mobile development. The deployment story is pretty much streamlined (at least on iOS) and with tools like Xamarin it has become much easier to do cross-platform mobile development. 

## Transpilation

Writing the word "Transpilation" in any text editor is likely to appear as misspelled. This is probably because it is a relatively new word. It is used to describe the prosess of transforming (transforming/compiling) from one language version to another. Although ES6 and ES7 are in the making, it will take a LONG time before these specs are done and implemented in every browser out there. The route that Aurelia takes here is that it lets us write code in the language of the future. For instance we can write ES6, ES7 or TypeScript in this case and then the code will be transpiled into something that most browsers can run which is ES5. Thinking in terms of .Net, we can think of ES5 as the IL (Intermediate language) for future languages. When for instance ES6 becomes mainstream, we can omit the transpilation stage and live happily ever after. 

The transpilation process itself can be done either at build time or on-the-fly in the browser.

## Getting started 

With almost no previous knowledge I started this journey towards getting Aurelia and TypeScript to play nicely together.

### First thing first

The first thing we need to do is to install [Node](https://nodejs.org/en/). We simply can't do anything without Node as it is the foundation for everything we are going to do from here.

### Project structure

Create a new folder for our demo project
```c
mkdir aurelia-typescript-simple
cd aurelia-typescript-simple/
```

### JSPM

[JSPM](http://jspm.io/) is a Javascript package manager that can pull in packages from just about anywhere including GitHub and the NPM (Node Package Manager) repository. It is built upon SystemJS that is capable loading multiple module formats such as AMD, ES6 and so on. For instance when installing Aurelia using JSPM, the latest bits are pulled in from GitHub.

Let't make sure we are in the project root folder (aurelia-typescript-simple)  and issue the following command.

```c
npm install jspm --save-dev
```
This command uses the Node Package Manager to install JSPM that we will be using to install Aurelia.

With JSPM installed we need to give it some basic configuration 

```c
jspm init
```
This command brings up a number of questions for us to answer.

```c
Package.json file does not exist, create it? [yes]:
Would you like jspm to prefix the jspm package.json properties under jspm? [yes]:
Enter server baseURL (public folder path) [./]:
Enter jspm packages folder [./jspm_packages]:
Enter config file path [./config.js]:
Configuration file config.js doesn't exist, create it? [yes]:
Enter client baseURL (public folder URL) [/]:
Do you wish to use a transpiler? [yes]:
Which ES6 transpiler would you like to use, Babel, TypeScript or Traceur? [babel]:typescript
```	

The only question that differs from the default value is the transpiler that we need to set to "typescript". Simply because we are going to be write TypeScript instead of Javascript.

### Aurelia 

Now that we have installed and initialized JSPM, we are ready to actually install Aurelia.

```c
jspm install aurelia-framework
jspm install aurelia-bootstrapper
```
 This pulls inn the core of the framework along with the bootstrapper that we need to start the Aurelia framework within our application.

Next we will create an index.html file in the root folder to bootstrap Aurelia.

```html
<!doctype html>
<html>
  <head>
    <title>Aurelia</title>    
  </head>
  <body aurelia-app>    
    <script src="jspm_packages/system.js"></script>
    <script src="config.js"></script>
    <script>
      System.import('aurelia-bootstrapper');
    </script>
  </body>
</html>
```

In addition to system.js (the module loader), there is also a config.js file is used by JSPM to keep track of installed packages. This file sort of resembles the packages.json file that is used by NuGet.

The config.js file needs a little bit of modification to make sure that we can transpile TypeScript correctly.

```json
paths: {
    "*": "src/*",
    "src": "src",
    "github:*": "jspm_packages/github/*",
    "npm:*": "jspm_packages/npm/*"
  },

  packages: {
    "/src": {
      "defaultExtension": "ts"
    }
  },
```

Without going into the details, this modification tells the module loader to add the .ts extension to every file located under the src folder that will be creating next.

```c
mkdir src
```

### App.ts

Within the src folder we will create a file called app.ts that sets up the routing for our new Aurelia application.

```typescript
import {Router} from 'aurelia-router';
import {RouterConfiguration} from 'aurelia-router';
export class App {
  router : Router;
  configureRouter(config : RouterConfiguration, router: Router) {        
    config.title = 'Aurelia';
    config.map([
        { route: ['','helloworld'],  name: 'helloworld',      moduleId: 'helloworld',      nav: true, title:'Hello world' });    
    this.router = router;
  }
} 
```

As we can see the route configuration only contains one (default) route to the helloworld module which we will be creating next.

In the src folder we create a new file call helloworld.ts that is going to be the view-model for the helloworld.html (view) page.

```typescript
export class HelloWorld
{
	message : String;			
	constructor()
	{
		this.message = "Hello from TypeScript! Awesome stuff";
	}	
}
```

Now we only need the actual view that is going to be displayed in the browser so we create a new file called helloworld.html
```html
<template>	
	<section>
		<h2>${message}</h2>	
	</section>	
</template>
```

All views in Aurelia starts with the Template tag and further on we can see that we display a header that is bound to the message field of our view-model (helloworld.ts).

That's it, we are pretty much done with our first Aurelia application written in TypeScript.

## live-server

In order to serve up our application we can use a Node module called live-server

```c
npm install live-server -g
```
 This installs the live-server globally on our machine so that we can run it from any folder.

Standing in the root folder of our application we can now launch the server and it will automatically open up a browser and run the application. live-server also watches for changes so that it will refresh whenever we make changes to the source files. Nice!

## Visual Studio Code

Visual Studio Code (VsCode) is the new cross-platform editor from Microsoft. It is lightweight, completely free and has great support for a number of languages. We might think of VsCode as Microsoft's answer to Atom.

One important thing here is that there is nothing in our project that ties it to VsCode. We can work with the source code in ANY text editor. 

I do however recommend using an IDE that can provide intellisense since that seriously improves the developer experience. One of the reasons that TypeScript exists is that it allows tooling to take advantage of a type system with regards to intellisense and refactoring.

In order to make VsCode happy we need to create a *tsconfig.json* file that contains compilation options and information about the files to be compile .
It also contains references to the typescript definition files used by the Visual Studio Code.

The current state of  things is that we need to specify each of these files explicitly rather than using a file pattern. It is possible to do "globbing" of all these files using the [tsconfig-glob](https://www.npmjs.com/package/tsconfig-glob) Node module, but that would require us to implement a "build system" using gulp. 

So here is the tsconfig.json file used in the project.

```json
{
	"compilerOptions": {
		"target": "ES5",
		"module": "amd",
		"noImplicitAny": true,
		"experimentalDecorators": true,
		"emitDecoratorMetadata": true
	},
	"files": [
		"./jspm_packages/github/aurelia/router@0.12.0/aurelia-router.d.ts",
		"./src/app.ts",
		"./src/helloworld.ts",
		"./src/nav-bar.ts",
		"./jspm_packages/github/aurelia/framework@0.16.0/aurelia-framework.d.ts",
		"./jspm_packages/github/aurelia/binding@0.9.1/aurelia-binding.d.ts",
		"./jspm_packages/github/aurelia/templating-binding@0.15.0/aurelia-templating-binding.d.ts",
		"./jspm_packages/github/aurelia/templating@0.15.3/aurelia-templating.d.ts"	
	]
}
```

One important compilation option is the *noImplicitAny* option that tells the transpiler to throw an exception if it encounters an "any" datatype.

