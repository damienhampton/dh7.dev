---
title: "Debugging Vue Mocha unit tests in VSCode"
slug: debugging-vue-mocha-unit-tests-in-vscode
publishedAt: 2020-03-06
brief: "I have been struggling to be able to debug Vue Mocha unit tests in VSCode, but I think I might have the answer. Running a Vue application is easy enough and debugging the running application in Chrome"
tags: ["mocha", "unit-tests", "development", "webpack", "vue", "vscode"]
---

I have been struggling to be able to debug [Vue](https://vuejs.org/) [Mocha](https://mochajs.org/) unit tests in [VSCode](https://code.visualstudio.com/), but I think I might have the answer.

Running a Vue application is easy enough and debugging the running application in Chrome or VSCode is relatively straightforward.

Creating and running unit tests for Vue is also relatively straightforward, thanks to the excellent [Vue Test Utils](https://vue-test-utils.vuejs.org/).

However, when you want to debug those unit tests, things seem to get a little trickier.

The problem I was having was that either my breakpoints in VSCode would not break or, in the case of the Vue files, they would break in the wrong place (presumably because of confusion caused by the template section).

I’ve create a demo project that can be installed and run. It should run in a browser and you should be able to run the unit tests from the command line or from within VSCode. You can add breakpoints in VSCode to either test files or Vue files and it should break in the correct place.

[https://github.com/damienhampton/debug-vue-unit-tests-demo](https://github.com/damienhampton/debug-vue-unit-tests-demo)

I’ve used [Mochapack](https://github.com/sysgears/mochapack), which is an excellent module that makes working with Mocha and [Webpack](https://webpack.js.org/) easy. To get the breakpoints working in VSCode, the correct source map config needs to be used.

Mochapack requires source maps be inlined, so the Webpack config needs to be:

```
{
  devtool: 'inline-source-map',
}
```

VSCode needs source maps to be enabled and the source map path overrides to be set correctly:

```
{ 
  "sourceMaps": true,
  "sourceMapPathOverrides": {
    "webpack:///\*": "${workspaceFolder}/\*"
  },
}
```

Hopefully this helps others trying to combine Vue, unit tests and debugging!

If you want me to look into any other tech subjects, please contact me on [Twitter: @damien\_hampton](https://twitter.com/damien_hampton).