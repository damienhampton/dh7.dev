---
title: "The Holy Grail: Cypress.io, Browsersync automatically rerun tests"
slug: the-holy-grail-cypressio-browsersync-automatically-rerun-tests
publishedAt: 2019-02-08
brief: "Cypress is an amazing front-end JavaScript test runner and framework. It simultaneously simplifies the set-up and test creation whilst providing a more useful features than most alternatives. Browsers"
tags: ["tutorial", "javascript", "development", "testing"]
---


![Screenshot-2019-02-08-at-15.32.36.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1647197927197/gGKPGh9qx.png)

[Cypress](https://www.cypress.io/) is an amazing front-end JavaScript test runner and framework. It simultaneously simplifies the set-up and test creation whilst providing a more useful features than most alternatives.

[Browsersync](https://www.browsersync.io/) is a bag of magic tricks on its own, but one thing it can do is provide a simple way to run a development webserver for static webpages and JavaScript web applications.

![Screenshot-2019-02-08-at-15.32.45.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1647197998464/VQgOHPGXj.png)

Together with nifty NPM add-on, [npm-run-all](https://www.npmjs.com/package/npm-run-all), it is possible to launch a webserver and run tests with a single command.

Out of the box, Cypress will watch your test scripts and re-run then automatically as you update them, which is awesome.

For me, there was still a missing piece of the puzzle. I wanted the Cypress tests to automatically run when I updated my website files. The solution is the [Cypress App Watcher Preprocesser](https://github.com/TheBrainFamily/cypress-app-watcher-preprocessor). This helpful Cypress plug-in wraps your webserver and watches for output that indicates that files have been changed. When it detects a change, it re-runs the tests.

Getting set-up
--------------

To test this, create a new folder and then run:

```
npm init
npm i --save-dev cypress browser-sync cypress-app-watcher-preprocessor npm-run-all
```

In your package.json file, replace the scripts entry with the following:

```
"scripts": {
    "serve": "browser-sync start -s src -w --no-open",
    "cypress": "cypress open",
    "serveWithCypress": "WAIT\_FOR\_MESSAGE='Reloading Browsers...' cypressAppWatcher npm run serve",
    "test": "npm-run-all --parallel serveWithCypress cypress"
  },
```
There is a lot of magic packed into that scripts section.

“serve” just provides the necessary commands to run browser-sync. I’ll be keeping my files in a folder called ‘src’, hence the ‘-s src’.

“-w” tells Browsersync to watch for changes to files.

“–no-open” tells BrowserSync not to open a web browser when the server starts (we will use Cypress’s browser).

“cypress” is just just a shortcut to launch the Cypress application.

“serveWithCypress” is the wrapper for Browsersync that will notify Cypress when Browsersync reloads the page. “Reloading Browsers…” is the tell-tale text that is output by Browsersync when this happens.

“test” is our entry point to the whole thing. It uses npm-run-all, which runs our other commands in parallel.

By running Cypress for the first time, it will create a bunch of folders and files, so go ahead and run that:

```
npm run cypress
```

This command takes advantage of the ‘cypress’ scripts entry, defined earlier.

Browser-sync runs on localhost, port 3000, by default, so let’s add that to the newly created cypress.json file:

```
{
  "baseUrl": "http://localhost:3000/"
}
```

Create a ‘src’ folder and, inside that, an empty ‘index.html’ file. Delete the ‘examples’ folder inside the ‘cypress/integrations’ folder.

Whilst we are in the Cypress folder, open up ‘plugins/index.js’ and replace the default module exports entry with:

```
const watchApp = require("cypress-app-watcher-preprocessor");
module.exports = (on, config) => {
  on("file:preprocessor", watchApp());
};
```

Lastly, create a new test script, called ‘homepage.spec.js’ inside ‘cypress/integrations/’.

At this point, your folder structure should look something like this

![folder-structure.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1647198049020/laDcrXdST.png)

Let’s fire up Cypress and Browsersync:

`npm run test`

![Screenshot-2019-02-08-at-16.48.59.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1647198082132/G98rWVc6p.png)

All being well, you will see that Browsersync has started and Cypress has opened a window that shows your homepage.spec.js test.

At the top right of the Cypress window, it shows you which browser is in use. Make sure it is set to Electron.

Click on ‘homepage.spec.js’. You should get another new Cypress window, showing a warning message.

![Screenshot-2019-02-08-at-16.52.35-1200x851.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1647198153271/aG8Ki55q0.png)

Writing tests and code  
-------------------------

Finally, we can now start writing our code.

Open ‘cypress/integrations/homepage.spec.js’ in your favourite editor and add the following:

```
context('Homepage tests', () => {
  it('should show the homepage', () => {
    cy.visit('/')
    cy.contains('Homepage')
  })
})
```

As soon as you save the file, the Cypress test should re-run. You should now see a different error.

![Screenshot-2019-02-08-at-16.56.05-1200x851.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1647198189602/VV3ulJ0V1.png)

Open ‘src/index.html’ and add:

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
</head>
<body>
  Homepage
</body>
</html>
```
When you save the file, your test should re-run and this time, it should pass.

![Screenshot-2019-02-08-at-16.58.18-1200x851.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1647198216838/hicqI-imw.png)

This all well and good, but static HTML files are probably not ideal candidates for test driven development. However, if you use ajax calls to load data for your pages, then it is very helpful.

Let’s update our test again:

```
context('Homepage tests', () => {
  it('should show the homepage', () => {
    cy.visit('/')
    cy.contains('Homepage')
  })
  it('should show a list of fruit', () => {
    cy.visit('/')
    const ul = cy.get('ul')
    
    ul.get('li').contains('apple')
    ul.get('li').contains('orange')
    ul.get('li').contains('pear')
  })
})
```
We now have a new failing test.

![Screenshot-2019-02-08-at-17.04.14-1200x838.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1647198304315/cfcpnohr1.png)

We can update index.html to this:

```
<head>
  <meta charset="utf-8" />
</head>
<body>
  Homepage
  <ul id="fruit-list">
    <li>apple</li>
    <li>pear</li>
    <li>orange</li>
  </ul>
</body>
</html>
```

And once again our test passes. Let’s now update the code to get the data from a web service.

First, let’s stub the web service. Update the test:

```
// ...
  it('should show a list of fruit', () => {
    cy.server()
    cy.route('/fruit.json', \[
      'apple',
      'orange',
      'pear'
    \])

    cy.visit('/')
    const ul = cy.get('ul')
    
    ul.get('li').contains('apple')
    ul.get('li').contains('orange')
    ul.get('li').contains('pear')
  })
// ...
```

We’ve added two commands here: [cy.server()](https://docs.cypress.io/api/commands/server.html#Syntax) and [cy.route()](https://docs.cypress.io/api/commands/route.html#Syntax). cy.server() starts a server so that Cypress can respond to requests. cy.route() is providing our stubbed response, in our case an array of fruit.

You should notice that the test still passes at this point. Small change made, test still passes.

If you delete all the &lt;li&gt; elements and save, you will see the test now fail. Let’s call our service and see if we can make it pass again. Update index.html:

```
<!DOCTYPE html>
<html>
<head>
  <meta charset="utf-8" />
</head>
<body>
  Homepage
  <ul id="fruit-list"></ul>

  <script>
    var fruitListUl = document.getElementById('fruit-list');
  
    function updateList(fruitList){
      fruitList.forEach(function(fruit){
        fruitListUl.appendChild(createLi(fruit));
      })
    }
    
    function createLi(label){
      var li = document.createElement('li');
      var text = document.createTextNode(label);
      li.appendChild(text);
      return li;
    }

    function getJsonData(url, callback){
      var xhttp = new XMLHttpRequest(); 
      xhttp.open('GET', url, true);
      xhttp.onreadystatechange = function() {
        if (this.readyState == 4 && this.status == 200) {
          callback(JSON.parse(this.responseText))
        }
      };
      xhttp.send();
    }

    getJsonData('/fruit.json', updateList)
    
  </script>
</body>
</html>
```

Now, there is obviously a fair amount going on here. We are getting a reference to our &lt;ul&gt; element:

```
var fruitListUl = document.getElementById('fruit-list');
```

And we are building our fruit list:

```
function updateList(fruitList){
  fruitList.forEach(function(fruit){
    fruitListUl.appendChild(createLi(fruit));
  })
}
```

`createLi(label)` is a helper to create an &lt;li&gt; element and `getJsonData(url, callback)` is a bit of a hacked wrapper to make an ajax request.

I originally used the [Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch), but it would appear that Cypress’s stubbing [doesn’t work with it yet](https://github.com/cypress-io/cypress/issues/687).

With index.html saved, our tests should pass again.

![Screenshot-2019-02-08-at-17.44.24-1200x822.png](https://cdn.hashnode.com/res/hashnode/image/upload/v1647198381421/GmPrZ272k.png)

The code for this project can be found on [Github](https://github.com/damienhampton/cypress-test-app).