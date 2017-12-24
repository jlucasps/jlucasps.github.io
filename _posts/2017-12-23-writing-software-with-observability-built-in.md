---
layout: post
comments: true
title: "Writing software with observability built-in"
author: "João Lucas"
---


### Introduction

Either you are in a DevOps world (where your team is responsible for keeping the code running in production) or in a more siloed organization (where operational duties are taken care by other teams), it is your responsibility to write code that it is observable in production. Independently of whom is the responsible to keep the application running, someone within the organization needs to be able to identify errors before the users do.

The objective of this article is to describe some common indicators and metrics that may help you to **observe** your application in production environment, giving some examples of my past experiences when possible. 

Since I've used the term "observe" in the previous paragraph, I'll bring one of the many _"-ilities"_ that you must take into consideration to help me expose my point, which is **observability**.

Ensuring the observability of a software is a challenging task, that may involve several aspects:

* **Development processes**: If you pass through huge cycles of development (months or years) for only after that deploy your software in production, observe your application will be hard. If the development teams does not have sufficient information about what's happening in production, it will be quite complex for them to cover edge cases and provided helpful errors messages and logs to support the operations team. They would need to "guess" those scenarios and the data necessary to debug the corner cases, leading to too many or too few logs.

* **Tooling**: Using the best tool for the job is always helpful, specially on microservices architectures, on which you may have hundreds or thousands of services running, it is almost impossible to log into each machine and search for logs or access monitoring applications to identify errors. You'll need some kind of log aggregation tool to grab the output of every application and concentrate it in a single platform, capable of indexing and respond to queries in various formats (chats, tables, lists or raw text).

* **Team experiences**: Working in a team that has experiences monitoring, troubleshooting and fixing bugs definitely is helpful when when you're writing brand new code. Usually experienced people know how to identify edge cases in the execution path and circumvent them or add proper error messages to help fix future problems.

* **Team skills**: Having people in your team that is interested in different types of problems is also a plus. You may find people that have expertise on identifying bottlenecks of performance, others that are experts on networking and infrastructure (I mean RAM/VM memory availability, disk space, caching, etc), others that have deep understanding of the platform and frameworks you're relying on… all those different types of interests help you write better software, where every person brings his/hers concerns to the code being created. As the result you'll have a better code, which it turns out to be more observable.   

To narrow down the scope of this article and be able to expose practical examples and advices, I'll focus on metrics and monitoring practices to help increase observability, in the context of one single application or service, and its first downstream dependencies. Talking about monitoring and metrics for integration between many applications could be a separate article.

Also, I'll consider that you have in place some log aggregation tool (Splunk, LogEntries, ELK or any other) to collect, consolidate and present logs in a friendly manner.

I'll not cover the responsibilities of create alarms and reacting to incidents. It is a extense topic and could be a separate article.

### Types of metrics


There are several metrics or indicators that you can collect, and in the initial stages of your application in production, I advise you to collect all of them and make them visible in radiators or build monitors. If necessary, make a fine tuning and prune some of them later, when you're application is stable and you are sure you are fine without them.

I've categorized them in a few types:

* **Identification**: Expose artifacts versions or revision numbers.
	* Git commit SHA number
	* SVN Revision number
	* A version of a snapshot, etc.
	* Timestamp of the last deploy

* **Health**: Expose application and dependencies healthiness, and its internal configuration.
	* Service uptime 
	* Current configuration of circuit-breakers
	* Downstream dependencies uptime

* **Infrastructure**
	* CPU usage 
	* RAM and JVM memory usage
	* JVM threads
	* HTTP connection pool size

* **Effectiveness**
	* Business effectiveness 
	* Technical effectiveness 

* **Operation**
	* Requests per second (hits per unit of time)
	* Response time
	* Common errors
		* Timeline of errors
		* Error distribution (pie chart)
		* Top 20 errors detailed in a table containing, identification, number of occurrences, description, etc
