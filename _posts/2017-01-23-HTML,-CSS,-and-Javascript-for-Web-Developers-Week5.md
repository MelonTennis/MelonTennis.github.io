---
layout: post
title: HTML, CSS, and Javascript for Web Developers Week5
subtitle: 
date: 2017-01-23
categories: Coursera
tags: [Web]
catalog: true
---

#### DOM Manipulation

[git repo for this class](https://github.com/YijiaJin/fullstack-course4)

#### Introduction to Ajax

**HTTP**: HyperText Transfer Protocol

Based on request/response stateless protocol

**URN**: Uniform Resouce Name

Uniquely identifies resource or name of resource

Do not tell us how to get the resources

**URI**: Uniform Resource Identifier

Uniquely identifies resource or location of resource

Do not neessarily tell us how to get the resource

**URL**: Uniform Resource Locator

Form of URI that provides info on how to get the resource

HTTP request structure: Method / URI String Query String HTTP version

**HTTP Methods:**

GET: Retrives the resource/ Data is passed to the server as part of URI

POST: Sends data to server in order to be processed/ Data is sent in the message body

HTTP Response Structure:  HTTP version+ '/s' + Response status code  + '/s' + Engish phrase describing status code

**Ajax**: Asynchronous Javascript And XML

Ajax Web App Flow: Faster response

Asynchronous Execution: Execution of more than one instruction at a time

Asynchronous instruction returns right away.

**Processing JSON**: JavaScript Object Notation

Lightweight data-interchange format textual representation of data

Syntax: Subset of JS object literal syntax but property names must be in **double quotes**, String values must be in **double quotes**, others just like objects. 

Great format for passing data from server to client & back

JSON.parse to convert from JSON string to object

JSON.stringify to convert from object to JSON string