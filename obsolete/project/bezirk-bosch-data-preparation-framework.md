---
layout: post
title:  Android Data Preparation Framework for Identifying User Preferences and Interests
date:   2017-08-20 00:33:52 -0500
categories: Project
tags: [Android, Software Engineering]
permalink: /project/bezirk-data-preparation-framework.html
comments: true
---

Bezirk is interested in developing a normalization framework that will help
gather data from different sensor sources and normalize the data in different
ways such as time, location, text etc and abstract it for the inference engines.
Normalization is a way to represent the collected data in a unified format. For
example America, United States all of them will represented as The United States
of America. Since several vendors will be interested in performing different
kinds of inferences, the framework allows them to directly use the normalized
data without worrying about tailoring the sensor data to their need.


Our project scope is to use and refer to the design of the Bezirk middleware and
implement a data preparation framework that facilitates data scientists to easily
configure, insert, and remove individual data processing units. The project
started from requirement engineering, system design, low-level code structure
design, code implementation to quality assurance process, which nearly covers
the entire software development lifecycle.


After negotiation with our client, the project scope is limited to three data
gathering units, and two normalization units. In addition to functional requirements,
framework extensibility is another important concern while designing the framework.
We adopted the message passing pattern for the entire system and applied the
data access object design pattern in the design of message format. Our final
deliverables include our framework, example code to modify existing components
inside the framework, and an business use case that demonstrate how the framework
can be deployed and used.


The following is the context diagram of how a framework shall work with Android
applications. The one in the red is the Android Application using our Data
Preparation Framework. The framework collects data from user’s twitter, FB, and
GPS feed. Understands interests and passes the results to the application.
The application later uses these results to send recommendations to the user.


![business context diagram][dndi-context-diagram]{:width="70%"}


A key component of the framework is the Bezirk Middleware, which is used for
communication among the different processes. Bezirk Middleware is an open Source
middleware for IoT-on-the-Edge, which follows the pub-sub message passing
pattern. It is developed by the company and able to run in a Java environment or
on Android environment.

To communicate over the middleware, we need zirks. A zirk is a process that
wraps around functional units and abstract their behavior. The functional unit
maybe gathering twitter feed, normalizing location data, and etc.

![what is a zirk][dndi-what-is-a-zirk]{:width="90%"}

The following is the dynamic view of our system. You can see that the entire
view is within an Android device. The blue part is an Android application that
uses our framework. The framework is made up of a central message passing bus -
Bezirk middleware and zirks in orange communication over it.


There are zirks for

- Gathering: GPS, Twitter and FB
- Normalization: processing raw data - Text and Location
- Inference: which is out of our scope
- Manager: which manages lifecycle of other zirks and is started when application
is started.

The gathering zirks can run in 3 modes of operation: batch, event and periodic.
You can see that the text normalization zirk uses a SQLite DB to store inference
results. It loads a JSON file and parse a list of approved keywords at the
initialization phase.


To better understand the flow of operations inside the framework, I present
our data flow diagram below, which shows how a batch mode gathering is completed
from a command sent from a manager zirk and sent back to it if there is a keyword
match.


![data flow diagram][dndi-dataflow-diagram]{:width="90%"}


Other than functional features of our framework, we also came up with an example
business use case demonstrating how an Android application can use our product
to infer user preference. One may check the following youtube video for more
details.


<div class="video-container">
<div class="video-wrapper">
<iframe width="560" height="315" src="https://www.youtube.com/embed/ZY0XypcFOjs" frameborder="0" allowfullscreen></iframe>
</div>
</div>


[dndi-context-diagram]: /assets/images/dndi-context-diagram.jpg#center
[dndi-what-is-a-zirk]: /assets/images/dndi-what-is-a-zirk.jpg#center
[dndi-dataflow-diagram]: /assets/images/dndi-dataflow-diagram.jpg#center
