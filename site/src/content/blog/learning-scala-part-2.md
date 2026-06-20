---
title: "Learning Scala – part 2"
slug: learning-scala-part-2
publishedAt: 2020-05-25
brief: "I’ve been asked to contribute to a Scala project and I’m documenting my journey learning Scala and making that contribution. This is part two. Here is part 1. Test driven learning Having started with "
tags: ["play", "development", "learning", "intellij", "scala", "sbt"]
---

I’ve been asked to contribute to a Scala project and I’m documenting my journey learning Scala and making that contribution. This is part two.

[Here is part 1](https://www.dh7.dev/beginning-scala).

**Test driven learning**
------------------------

Having started with the new company, I’ve learnt that they are using Scala with the Play Framework (link). The application already has a degree of maturity, so I’ve been thinking about how to get to the point where I can contribute effectively.

My starting point is to understand the things I feel I need to learn. These are:

*   [Scala](https://scala-lang.org/)
*   [Play Framework](https://www.playframework.com/) (including its [test framework](https://www.playframework.com/documentation/2.8.x/ScalaTestingYourApplication))
*   The code structure, architecture and flows

Additionally, I need to understand how the team works, but that is not really the subject of these posts.

I’ve spent the last four years trying to drive more of my code via tests. I have had good success in breaking down technical problems and in learning new thing by driving my progress through tests. Because of this, my focus in learning Scala, the Play Framework and the project is to understand the existing tests and to then write new tests. Driving learning via tests feels a bit like hacking the system.

Thankfully, the project already has decent test coverage, which gives me good starting point to understand the project.

* ✅ By running the tests I can get an overview of what the project does (tests print out indications of expected behaviour for different scenarios).
* ✅ I can run tests individually and then look closer at the code to see how individually pieces of code work whilst already knowing what they are supposed to do.
* ✅ The tests generally run fast, which means I can understand more complex flows faster than driving the code manually (e.g. via a browser).
* ✅ Tests should fake dependencies (databases, 3rd party systems, etc) which means you can focus on learning one application and how it interacts with others, without being distracted by the other systems. 
* ✅ I can make changes to tests, see them fail and then revert the code – this helps me play with the code, see how it breaks, be able to understand the effects, and then safely revert it.
* ✅ A byproduct of running the tests is that I will likely pick up much of how the test framework works, which means I have a head start when I come to writing my own tests.

Documenting the application
---------------------------

Any reasonably sophisticated project will have a number of components and flows. I find it helps me to understand that better by documenting this as a diagram.

I can’t share the document that I created for the project as it is a private project, but I can give you an idea of what I do.

Essentially, I systematically work my way through the application, in this case starting with the controllers. I represent each class or module that is connected (the custom classes / modules only – I ignore 3rd party libs). I use Omnigraffle, but something like Draw.io would probably work as well. Imaginatively, each class / module is a box and these are connected with arrows. I try to get all information on to one map first and then I will work out how to best represent the information so as not to overload the reader. I create a key and will sometimes use size to indicate connectivity (bigger boxes, more connectivity). I also use colour if I can see a clear categorisation of elements and will use different line styles to represent 1 way or 2 way connections. If there are too many lines, I make connections that seem less important lighter so that they do not distract.

Once the diagram is complete, I’ll share it with team members for two reasons. One, in case it helps them and two, to get feedback.  
The beauty of this type of process is that you quickly get a sense of the really important, central parts of the project. There are usually a few classes / modules that are connected to everything else. It lets you understand what you need to focus on and what you can ignore.

### **IntelliJ and sbt**

I was compiling my code using sbt, whilst using IntelliJ as my editor. I had noticed that I was finding the code hard to follow and eventually realised that the syntax highlighting wasn’t especially effective and when I highlighted an external class or reference, IntelliJ wasn’t giving me links to those files.

In VS Code, I know this as ‘Intellisense’, so I tried Googling this and eventually found this: [https://stackoverflow.com/a/47039808/1186928](https://stackoverflow.com/a/47039808/1186928). Sure enough, I had the project open one level too high. Opening the project with build.sbt in the project root resolved my issues. Syntax highlighting now makes code easier to read and I can easily get information on external files and references.