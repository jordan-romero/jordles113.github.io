---
layout: post
title:      "Understanding State"
date:       2020-12-16 00:07:07 +0000
permalink:  understanding_state
---


Today I wanted to dive into React, a JavaScript Framework I have been learning. It’s got a lot of magic under the hood, which is awesome. We can pass information from Parent components to their children components through props (properties) using something called state and it’s pretty powerful stuff.
## What is State?
React’s state is data that is changed inside your components. That is, you set a default value and you can change it depending on which component you send the information to or what you decide to do with it. State gives us a way to maintain and update info inside a component without even talking to the parent component.
Initial State
We set initial state or a default value for our state in a constructor of a class level component. Why? We set initial state in the constructor because it runs first in the React Component Lifecycle. Let’s take a look at an example here. The end goal is to change the background color of a Cell in a table onClick.
import React, { Component } from 'react'; 

```export default class Cell extends Component {    
     constructor(props) {    
        super()    
        this.state = {      
          color: #FFFFFF   
        }  
      }
}``` 

Let’s break this down further to make sure that we understand what’s happening. We are setting up the initial value of state in our constructor using the this keyword. Back to vanilla JS, this is in this case an instance of our class. So what this.state = is doing is for this instance of the Cell class component we want to set state to #FFFFFF, which is the hexadecimal value for white. Now we want to make sure that we update state onClick. But how?
## setState()
`setState()` is as intuitive as it sounds: it literally allows us to set/update state! Although it seems too simple, there is one key factor: it sets state asynchronously.

```handleClick = () => {    
console.log(`before setState: ${this.state.color}`)
  this.setState({      
    color: '#333'    
  })  
console.log(`after setState: ${this.state.color}`)
}```

You would expect the second console.log to return the updated value of state (#333), right? Remember the important, key factor above 👆🏼. It’s updating state asynchronously. Both of those console.log’s would return the default or initial state: #FFFFFF. It’s non-blocking, meaning it does not block the UI from doing other things. For example, let’s say you logged on to Twitter and nothing showed up. Yips, you have to wait! No that’s not what happens, some stuff shows up, usually stuff that allows you to wildly click buttons wondering if you have to reset the modem or something. State is basically waiting, running in the background, waiting to update state until after. So you will see the color change, BEFORE state is updated in our code.

Let’s see the full code:
```
import React, { Component } from 'react';
export default class Cell extends Component {    
     constructor(props) {    
        super()    
        this.state = {      
          color: #FFFFFF   
        }  
      }
handleClick = () => {
this.setState({      
    color: '#333'    
  })
}
render() {    
 return (     
   <div         
     className="cell"        
     style={{backgroundColor: this.state.color}}        
     onClick={this.handleClick}>      
   </div>    
  )  
}
}

```

## When to use it?
State is very powerful and should only be used when necessary. State can do a lot but it can also add unnecessary complexity and you can lose track of it easily. State should only be used for values that we are expecting or want to change during the components life.

## Conclusion
`state` is for values that are expected to change
we set an initial state in the constructor
we update state with `setState()`
`setState()` is asynchronous

### Resources:
[React State Docs](http://https://reactjs.org/docs/state-and-lifecycle.html)
