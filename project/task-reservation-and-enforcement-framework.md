---
layout: post
title:  Real-Time Task Reservation and Enforcement Framework
date:   2017-08-23 00:33:52 -0500
categories: Project
tags: [C, Linux Kernel, Android NDK]
permalink: /project/task-reservation-and-enforcement-framework.html
comments: true
---

In real-time systems, it’s critical to guarantee that threads will never exceed
their allowed computation time. However, threads may overrun their budget due to
inference from non-realtime best effort tasks or an inaccurately estimated worst
case execution time. To maintain the safety of the system, we implement a task
reservation framework in the kernel that will keep track of and enforce the
thread budgets.


This framework includes

- System calls - Allow developers to register and cancel resource reservation on
particular CPU
- The sysfs interface - Allow developers to configure framework behaviors by
override setting from pre-defined file descriptors
- Android native interface - Allow Android apps configure, register, and cancel
reservation by application from user spaces  


Once a task ID is specified and the registration system call is called. Our
framework will first check whether input arguments are valid. (An evil user may
specify a pointer from kernel spaces and corrupts kernel operations.) Later,
it will check whether the target CPU is available and whether the new task is
schedulable. If so, the system will conduct a series preparation for task
registration.


![registration flow][rtes-reservation-flow]{:width="60%"}


Generally, a high resolution timer is used to monitor statuses of tasks. Each time
when the timer is fired. It traverses a linked list, that we used to maintain
information of reserved tasks. If a task is running out of budget, unschedule it
from current run queue and set its flag into uninterruptible to prevent existing
task scheduler in the Linux kernel reschedule tasks and insert it into run queue
again. There is another timer came along with each reserved task that is used to
clear the uninterruptible flag and restart these tasks.


By doing so, normal tasks, tasks without being reserved can still be scheduled
by original scheduler but those reserved tasks encounter another constraints
imposed by our framework.


The following is the overall system architecture of our framework. One can see
that, an Android app can communicate with our framework using Java Native Interface.
(root permission must be set to gain access right). It fetches the result indirectly
by reading the files under sysfs directory.


![system architecture][rtes-architecture]{:width="60%"}


[rtes-reservation-flow]: /assets/images/rtes-reservation-flow.jpg#center
[rtes-architecture]: /assets/images/rtes-architecture.jpg#center
