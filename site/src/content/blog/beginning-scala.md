---
title: "Beginning Scala"
slug: beginning-scala
publishedAt: 2020-05-19
brief: "I thought it might be interesting to document my journey from complete Scala beginner to contributing to a real Scala project (hopefully). I will split this journey over a number of posts and hopefull"
tags: ["development", "learning", "beginner", "scala", "articles", "spark", "udemy"]
---

I thought it might be interesting to document my journey from complete [Scala](https://scala-lang.org/) beginner to contributing to a real Scala project (hopefully).

I will split this journey over a number of posts and hopefully it will end up in a good place.

The journey begins…
-------------------

I was about to join a company that uses Scala for some of their work. I’d not come across Scala before, but a few things about it sounded interesting, so I thought I’d do some research. I picked up an [Apache Spark with Scala course on Udemy](https://www.udemy.com/course/apache-spark-with-scala-hands-on-with-big-data/). Scala seems to be used a lot in data science and specifically around [Apache Spark](https://spark.apache.org/), so I started there.

The course has been awesome but is more based on Spark than Scala. Aside from learning about Scala and Spark, I got a lot of benefit from dealing with a number of (self imposed) issues.

The course recommended the use of the [Scala IDE](http://scala-ide.org/). Because I like to be difficult (and because I don’t like Eclipse), I wanted to use [JetBrains IntelliJ IDEA](https://www.jetbrains.com/idea/), as that seems to be what the cool kids use. Trying to translate commands from the Scala IDE to Intellij was helpful in forcing me to learn my way around the IDE.

Apache Spark requires a specific version of Scala, which is _not_ the latest one. I had the latest one. This caused me a number of issues, notably when trying to run my compiled code against Apache Spark.

I also had issues around the version of the JDK. I had 13, but Spark wants 1.8. This introduced me to another issue: JDK naming. The JDK naming was changed a while back. They changed JDK names retrospectively, which is super confusing as a beginner. Spark asks for v1.8, but that actually corresponds to a download version of 8. It seems that Oracle basically changed the versioning so that the minor version number became the major version number. So the names v1.8 and v8 can be used interchangeably.

Lastly, I also had issues with mismatches between my Scala version and the version of some of the libraries I was using. This helped me to understand that when searching [Maven](https://search.maven.org) for libraries, it was important to make sure the Scala version was correct!

Each of the issues above took me time to resolve. Many of the things I was using were new to me. I was outside my comfort zone and it felt difficult. As usual, there is a constant battle between wanting to give up and wanting to push ahead. Also as usual, I can measure how lost I feel at a particular point by how many browser tabs I have open with various pages that _might_ relate to my current problem. I had a lot of tabs open.

I have not finished the course, but I now have a small amount of Scala knowledge, but not enough to write a useful application. I know what [sbt](https://www.scala-sbt.org/) is (note: Scala Build Tool – a utility to build, run and test Scala projects), I can open a Scala project in IntelliJ and I can compile and run simple code.

Next
----

Contributing to a project \[coming soon\]