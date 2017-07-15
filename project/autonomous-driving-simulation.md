---
layout: post
title:  Autonomous Driving Simulation
date:   2017-07-14 12:16:52 -0500
categories: Project
tags: [Autonomous Driving,]
permalink: /project/autonomous-driving-simulation.html
comments: true
---

Autonomous driving, considered only as science fiction a few years ago, has gotten more and more attentions with the evolution of related technologies. It is regarded as one of the solutions to human labor cost reduction and overall road usage efficiency. Despite these attractive advantages, people still concern about the safety issues. While they are sitting inside the car, they hope that the autonomous driving car can protect them from an accident. While they are crossing the road, they hope that it won’t bump into them.


In this project, I worked on

- Designing a state chart utility that specifies the behavioral actions of a self-driving vehicle in a set of driving contexts
- Simulating the hierarchical state machine on a virtual car in a virtual environment. 


It always takes time for us to make a decision. It does the same for a self driving vehicle as well. The time can be long especially if the computation is quite complex. Sadly, an accident may happen at the moment the decision engine gets stuck in a heavy computation. Therefore, a concurrent process model is needed to make sure while there is a heavy computation, the system remain responsive to the environment changes.


My design is a simplified version that takes traffic lights at the road intersection and pedestrians into consideration. I have three components in the system: decision, perception, and control unit. The control unit can generate outputs that alters the vehicle internal states. For example, it may accelerate to a higher rate or slow down to a stop state. The decision unit observes the environment and reacts to environment inputs periodically. It keeps monitoring the status of traffic lights. The last one is the pedestrian detection unit. It is a stand alone unit that only detects the pedestrians so the architecture of the unit is simple. When it detects an pedestrian event, it sends a message to both control unit and decision unit. When the control unit receives a message, it regards the message as an emergent event and transits to a state that deal with a series slow down procedure. In the same time, the decision unit also receive this event. It knows that the control unit will eventually enter a series of states handling slow down procedure. It then enters a state that keeps polling whether the emergent event is invalid. Once pedestrians complete their crossing, the decision unit must resume or re-calculate its next step.


The following snapshots illustrate my state chart architecture design, which was done using Simulink and Stateflow.


This one shows how does the decision unit make decision when a vehicle approaches a road intersection.
![state chart architecture 1][hfsm1]


The state chart is able to handle different kind of trajectory planning. As you can see, different trajectories are comprised of different state transition rules. The complexity is different as well.
![state chart architecture 2][hfsm2]


Other than the autonomous driving car, other objects in the environment can also be modeled using state chart.
![simulation setting][simulation]


This design adopts a hierarchical finite state machine design, which is good at handling inputs with different priorities. Assume that the cost of bumping into a pedestrian is always higher than the fine for violating traffic rules. In this case, we want the state chart to behave in a way that always checks pedestrians first and checks the traffic rule later. How could we design a state chart to capture this behavior? The idea is to make pedestrian checking as a transition of parent states and the traffic rules are transitions for the child states. Every time the state machine wakes up, it always checks the pedestrian first.


This approach makes dealing with priorities easier but it has a potential problem when being applied to a real driving scenario. We know that a variety of scenarios may happen in the real world. A bicyclist may show up. A front car may stop abruptly. Some pedestrians or other vehicle drivers may violate traffic rules. We have too many things to consider at the same time. Even we have structured them in a way that is able to deal with priority, the complexity grows exponentially. It becomes difficult to add new detection rules. A new components may affect other normal parts and creates bugs. That is the reason why I adopt concurrent model in this case.


The following video shows a simple simulation with multiple pedestrian and cars in the environment. The one that is labeled 2 is the autonomous driving car while the others are so-called ego vehicles, which drive selfishly and do not always follow traffic rules. The idea here is an intelligent vehicles should protect itself and minimize threats even if others do not have a current behavior.


<p align="center">
<div class="video-container">
<div class="video-wrapper">
<iframe width="560" height="315" src="https://www.youtube.com/embed/mWpOn0OVAvY" frameborder="0" allowfullscreen></iframe>
</div>
</div>
</p>



[hfsm1]: /assets/images/hfsm1.jpg "State chart 1"
[hfsm2]: /assets/images/hfsm2.jpg "State chart 2"
[simulation]: /assets/images/simulation.jpg "Simulink workspace"

<!-- You’ll find this post in your `_posts` directory. Go ahead and edit it and re-build the site to see your changes. You can rebuild the site in many different ways, but the most common way is to run `jekyll serve`, which launches a web server and auto-regenerates your site when a file is updated.

To add new posts, simply add a file in the `_posts` directory that follows the convention `YYYY-MM-DD-name-of-post.ext` and includes the necessary front matter. Take a look at the source for this post to get an idea about how it works.

Jekyll also offers powerful support for code snippets:

{% highlight ruby %}
def print_hi(name)
  puts "Hi, #{name}"
end
print_hi('Tom')
#=> prints 'Hi, Tom' to STDOUT.
{% endhighlight %}

Check out the [Jekyll docs][jekyll-docs] for more info on how to get the most out of Jekyll. File all bugs/feature requests at [Jekyll’s GitHub repo][jekyll-gh]. If you have questions, you can ask them on [Jekyll Talk][jekyll-talk].

[jekyll-docs]: http://jekyllrb.com/docs/home
[jekyll-gh]:   https://github.com/jekyll/jekyll
[jekyll-talk]: https://talk.jekyllrb.com/ -->
