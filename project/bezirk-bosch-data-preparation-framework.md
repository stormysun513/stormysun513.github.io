---
layout: post
title:  The Zirks - A Data Preparation Framework on Android
date:   2017-08-13 00:33:52 -0500
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

Here is our context diagram of how the framework shall work with Android 
applications. The one in the red is the Android Application using our Data
Preparation Framework. The framework collects data from user’s twitter FB and
GPS feed. Understands interests and passes the results to the application.
The application later uses these results to send recommendations to the user.

A key component of the framework is the Bezirk Middleware, which is used for
communication among the different processes.
