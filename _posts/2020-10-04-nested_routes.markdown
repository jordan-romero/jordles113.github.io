---
layout: post
title:      "Let's Make Fetch Happen!"
date:       2020-10-04 22:18:12 -0400
permalink:  nested_routes
---

For my Javascript SPA, I knew I wanted to have full CRUD for two of my models. That meant a whole lot of fetching happening. I used the adapter pattern to handle all of my fetches.  I will review the adapter patern as well as fetch and promises in this blog. 
## What is Fetch? 
Fetch is a very important part of using JavaScript to communicate with various API's (even your own backend). Fetch requests are a modern and easy way for us to use JavaScript to communicate with the server and get new information whenever we need it. That's pretty awesome stuff. Fetch gives us access to GET requests, POST requests, PUT/PATCH requests, and DELETE requests. 
For example, we can use Fetch requests to: 
Get user information and display it on a profile page
populate an update form with the current data 
submit an order 

Fetch uses AJAX (Asynchronous JavaScript And XML). Basically, it is the use of the XMLHttpRequest object to communicate with servers. Luckily, we don't have to use XML anymore, that's old news cause of our friend Fetch. 
AJAX relies on a few different technologies:
*Promises
*XMLHttpRequestObjects
*Serialization called JSON for "JavaScript Object Notation"
*asynchronous Input / Output
*The event loop

## Promises
The Promise object represents the soon-to-be completed (or failed) asynchronous operation and its resulting value. A promise can be in three states. 
*fulfilled: meaning that the operation was completed successfully.
*rejected: meaning that the operation failed.Basic Fetch Request

Ref: MDN Promises

A promise is a value holder of sorts. It's holding on to the 'promised' value of our fetch request. A pending request is awaiting completion and does not yet have a value. As soon as the promise if fulfilled we have successfully gathered the information asynchronously from our fetch. As we learn more about making fetch requests you will see that this proxy value held in our promise will allow us to use .then and .catch within our requests to handle those values as they come or to catch them as they fail. 
Let's Fetch!
Below I have a basic example of a Fetch request: 
```
fetch('http://example.com/dogs.json')
  .then(response => response.json())
  .then(data => console.log(data));
```
Let's unpack this to really understand what's happening under the hood. This simple fetch() only takes in one argument - the path to the resource you want to fetch - and returns a promise containing a Response object.

## The Adapter Pattern

One of the hardest barriers I faced when shifting from Rails to Javascript was the lawlessness of the JS world. You can put a function anywhere? Cool cool cool *brain explodes*. So when we learned about some design patterns I felt a sense of organization and calm wash over me. The Adapter Pattern is a design patterns that allows us to put the fetch requests into it's own class/file. 

Here is an example from my project of a basic get fetch to an index action in my Events controller. 

```
class ApiService { 

    static getAllEvents(){
        return fetch(EVENT_URL)
        .then(response => response.json())
    }
}
```

You may notice that we don't have the second `.then` like we did in the example above. That is because we finish our fetch in where we want to initialize our new data. In my project, I handled this in the index.js so that the data was rendered when the Events button was clicked. 

```
function initEvents() {
    ApiService.getAllEvents().then(events => {
        events.forEach(event => new Event(event));
    });
}

```

## Resources
[Video Walkthrough](https://www.youtube.com/watch?v=_6drB8hulrw)
[Frontend-Repo](https://github.com/jordles113/get-together-frontend)
[Backend-Repo](https://github.com/jordles113/get-together-backend)
