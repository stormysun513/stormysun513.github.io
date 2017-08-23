---
layout: post
title:  Mobile Application - EasyOrder
date:   2017-08-23 00:13:52 -0500
categories: Project
tags: [Android, Objective C, Django]
permalink: /project/mobile-easyorder.html
comments: true
---

### Introduction

Easy Order is developed to help restaurant owners (retailers) and customers to
make deals in an efficient manner. Under the environment of ubiquitous computing,
Easy Order uses various cutting-edge technologies to serve needs for both kinds
of users within just one application. With Easy Order and our self-developed web
service, users are able to manage menus, order dishes and process payments. We
have also provided location service to facilitate food pickup.

This app is a cross-platform mobile app with a backend web service that stores
all information required for meal ordering. In this post, I include only
the snapshots of iOS version and the Android version is almost the same but
slightly different in UI. I developed the web server using Django-REST-Framework
and deployed the service on the AWS EC2 machine.

### Feature Overview

Once user launches the app, a login page is displayed. User may choose to login
as a retailer or as a customer. Both of them will redirect to a tabbed view as
soon as the user is authenticated by the server.


![login page][easyorder-login]{:width="30%"}


The first tabbed view shows dish menu provided by the retailer. Retailers can
upload, or delete a dish from this panel. They can also check rates of each dish
and the amount of each dish. From the customer side, they can increase and
decrease their orders.


![main page][easyorder-main]{:width="60%"}
![order confirmation][easyorder-order-confirmation]{:width="60%"}



In addition, user may share dishes by tweeting the dish along with its figure.  



![twitter sharing][easyorder-sharing]{:width="80%"}



After the order is taken by the retailer, customer may switch to our map tab
and check the real-time updates of the delivery guys. A geofencing mechanism
can be configured from retailer side. Once they are close to the pickup locations.
Our app will broadcast to all customers and notify them of their arrival.


![location tracking][easyorder-pickup-locations]{:width="60%"}


Our application offer not only ordering and tracking function but also payments.
We integrate our web server with the Braintree Payment framework developed by
PayPal to complete our in-app payment. In order to avoid real payment for now,
all the transaction is completed in a sandbox environment provided by PayPal.


![in-app payment][easyorder-in-app-payment]{:width="60%"}


### Technical Challenge

#### Authentication
We built our own web server to allow different mobile app users to share
information with one another. It provides a set of RESTful APIs for both
Android and iOS application to interact with the data stored on the server.
One technical challenge here is the authentication procedure. The web server is
 supposed to maintain two types of user and ensure that they have different
 authorization permissions to update data on the database. We do not implement
 the authentication logic on the web server but just direct the user to
 different tabbed view.

#### Payment
Initially, we plan to use the Venmo API or Paypal API to implement the payment
logic. However, we do not fully survey these two before we actually implemented
it. When we worked on this part, we found that the existing API retires and they
no longer accept new registration anymore. Therefore, we turned to their new
platform called Braintree, which incorporate the payment with the mobile
application and the server logic. All these transactions are done in sandbox
just for demonstration purpose. If we would like to deploy the app to the real
environment, the setting here may changes slightly.


#### System workload
Our current project scope just cover a retailer and some customers. The traffics
between phones and the server is trivial and communication protocol is simple as
well. If we would like to scale the app to accommodate more users the
architecture may need a significant redesign.


### Design Rationale
Since this project is a proof of concept, we are more focusing on implementing
the functionalities to demonstrate the feasibility of the intended features.
Within this context, we have the following design rationale.


#### Websocket vs. Long polling
In our project, there is a massive amount of communication between mobile
devices and the web server. As has been described above, both retailers and
customers will receive notifications and information update when certain events
happen. In order to handle these things in a timely manner, websocket is an
ideal option. However, websocket is hard to implement and requires a much larger
workload. As we only have a limited amount of resource and time, we decided to
use the technique of long polling to handle requests.

One thing to mention is that we made this decision not only because we want to
decrease the workload, but based on the other two analyses:
Eventual consistency fits better with our application.
We want to decrease the load imposed on our web server and distribute the load
to each device.

First, there’s no need to have all the updated information at real time. For
example, retailers don’t need to get the information as to who pays or not
immediately after the payment has been processed. Retailers only want to know
about it when they deliver the food, which is typically a while after the
customers have ordered and paid.

Second, since our server which is deployed on AWS has limited capacity, we don’t
want its load to linearly increase as the number of connected users increases.
Thus, we make the mobile device to initiate the communication when needed. Every
30 seconds, our messaging mechanism will be triggered, and if there’s any new
information updated on the server, it will be reflected on the user’s devices.


#### Normalized database vs. Big tables
We expect to have a large number of users in terms of retailers and customers
and we want to provide good performance to all of them. Thus, we have designed
our web-server database to have big tables, i.e. information is aggregated rather
than normalized into different tables. By doing this, we are able to achieve
higher database performance so as to make our web server more responsive to
requests.


#### Real payment vs. Sandbox
As explained above, this project is a proof of concept. Therefore, we planned
to make our payment process light weighted. For the purpose of demonstrating
this application, we make the payment use sandbox which is provided by the
payment provider. This can enable us to spend less time in dealing with
transaction and regulation problems and to focus more on developing other
key functionalities.


### Video Demo


In this video, we walkthrough all use cases for iOS version, the second half
of the video covers the Android version.


<div class="video-container">
<div class="video-wrapper">
<iframe width="560" height="315" src="https://www.youtube.com/embed/2JiupeR6n-A" frameborder="0" allowfullscreen></iframe>
</div>
</div>


<!-- specify image sources here -->
[easyorder-login]: /assets/images/easyorder-login.jpg#center
[easyorder-main]: /assets/images/easyorder-main-menu.jpg#center
[easyorder-order-confirmation]: /assets/images/easyorder-order-confirmation.jpg#center  
[easyorder-pickup-locations]: /assets/images/easyorder-pickup-locations.jpg#center  
[easyorder-in-app-payment]: /assets/images/easyorder-in-app-payment.jpg#center  
[easyorder-sharing]: /assets/images/easyorder-sharing.jpg#center
