---
layout: post
title:      "Client-Side Routing"
date:       2020-12-08 16:39:23 +0000
permalink:  client-side_routing
---


**What is client-side routing**
     Client-side routing happens when the route is handled by the front end code (Javascript/JSX) that is loaded on the page. When a link is clicked, the route will be changed in the URL but the server request is prevented. The url will be changed by the state. This change in state will result in a different view of the webpage.
     Let’s look at a real-world example, let’s say you have an application that allows you to view your blogs, your profile page, and your drafts. Each of those pages is a different view of the same SPA (single page application). With Client-Side routing, you’ll get the data you need to be able to render all of those pages on the first-page load. When a user clicks to view their drafts, the content is already ready to go, and therefore it will render faster than if you were making a request to the server for that page.
    **Speed** is the major benefit of client-side routing. We make only one request to the server and so we don’t have to wait around for the server to get back to us. Everything is stored on the client-side and accessible to us as we need it.
		
		
*Note: Our whole page will not refresh when using client-side routing. Instead, only the elements on the page will change*.

**Drawbacks of Client-Side routing**
   Since we are loading all of our code on the initial GET request it can be pretty slow for the initial page render, especially if you have a really large application. This is one of the biggest drawbacks of client-side routing.
	
   Another drawback is search engine crawling which is less optimized for SPAs. Google is working on improving crawling on SPAs but it is still much less efficient than doing so on server-side applications.


**React Router**
  React Router is a routing library for React. It lets us link to specific URLs then play hide and seek with components depending on which URL is being displayed.
	
As React Router’s documentation states:

> Components are the heart of React’s powerful, declarative programming model. React Router is a collection of navigational components that compose declaratively with your application. Whether you want to have bookmarkable URLs for your web app or a composable way to navigate in React Native, React Router works wherever React is rendering — so take your pick!
> 

The first step in using React-Router is installing that bad boy:

```
npm install react-router-dom
```

Next, we will need to import `BrowserRouter `and` Route `from `react-router-dom`. To note, conventionally, `BrowserRouter` is aliased as `Router`. Now we can add our first route. We will be setting up a home route. See the example below.

```
import React from 'react';
import React from 'react';
import ReactDOM from 'react-dom';

// Step 1. Import react-router functions

import { BrowserRouter as Router, Route } from 'react-router-dom';

const Home = () => {
     return (
       <div>
          <h1>Home!</h1>
       </div>
     );
};

// Step 2. Changed to have router coordinate what is displayed

ReactDOM.render((
    <Router>
       <Route path="/" component={Home} />
    </Router>),
    document.getElementById('root')
);
```

Let’s break down step 2: The `Router` (our alias for` BrowserRouter`) component is the base for our routing. It is the component that we will use to declare how React Router will be used. Inside the `Router` component, we have the route component. The `Route` component has two props in our example: `path` and `component`. This component decides what is rendered based on whether or not a path matches the URL.

**Adding Additional Routes**
Before we add in additional code, let’s go ahead and extract our home code to its own file. We can then make two more files one called `Protected.js` and one called `Login.js.` We should have three total, `Home.js`, `Protected.js`, & `Login.js`. These files should look like this:

Home:

```
import React from 'react'; 

class Home extends React.Component {  
   render() {    
     return 
       <h1>Home!</h1>  
   }} 
	 
export default Home;
```

Protected: 

```
import React from 'react';

class Protected extends React.Component {
  render() {
    return <h1>This route should be protected and not visitable by someone who is not logged in</h1>;
  }
}

export default Protected;
```

Login: 

```
import React from 'react';

class Login extends React.Component {
  render() {
    return (
      <form>
        <h1>Login</h1>
        <div>
          <input type="text" name="username" placeholder="Username" />
          <label htmlFor="username">Username</label>
        </div>
        <div>
          <input type="password" name="password" placeholder="Password" />
          <label htmlFor="password">Password</label>
        </div>
        <input type="submit" value="Login" />
      </form>
    );
  }
}

export default Login;
```


Be sure you are importing these new components into your` index.js `file so you have access to the code.
With that, we can start to add our second and third routes. A quick note, a `Router` can only have one child so listening to all of your routes within the `Router` component will result in an error. We will solve that by wrapping all of our `Route` components in a <div> to avoid errors. Here is our code so far:

```
import React from 'react';
import ReactDOM from 'react-dom';
import Home from './Home'
import Protected from './Protected'
import Login from './Login'
import { BrowserRouter as Router, Route } from 'react-router-dom';

ReactDOM.render((
  <Router>
    <div>
      <Navbar />
      <Route exact path="/" component={Home} />
      <Route exact path="/protected" component={About} />
      <Route exact path="/login" component={Login} />
    </div>
  </Router>),
  document.getElementById('root')
);
```

The React Router API provides two components that enable us to trigger our routing: `Link` and `NavLink`. They both update the browser and render the correct `Route` component. Let’s make a `Navbar.js` and add the following code (be sure to import this into your `index.js`):

```
import React from 'react'
import { NavLink } from 'react-router-dom';

const link = {
  width: '100px',
  padding: '12px',
  margin: '0 6px 6px',
  background: 'blue',
  textDecoration: 'none',
  color: 'white',
}

class Navbar extends React.Component {
  render() {
    return (
      <div>
        <NavLink
          to="/"
          /* set exact so it knows to only set activeStyle when route is deeply equal to link */
          exact
          /* add styling to Navlink */
          style={link}
          /* add prop for activeStyle */
          activeStyle={{
            background: 'darkblue'
          }}
        >Home</NavLink>
        <NavLink
          to="/protected"
          exact
          style={link}
          activeStyle={{
            background: 'darkblue'
          }}
        >About</NavLink>
        <NavLink
          to="/login"
          exact
          style={link}
          activeStyle={{
            background: 'darkblue'
          }}
        >Login</NavLink>
      </div>
    )
  }
}

export default Navbar;
```

At this point, if you were to spin up a browser you should see some lovely blue navlinks and you should be able to go to each component and see changes on the page and in the URL!

But what about that `Protected` route! In this project, I used auth and I wanted to be sure that my routes were protected. For that I used something called private routing. A private route is something that is given to us by the React Router API. To use that, I made a `PrivateRoute.js` container that I then imported into `App.js` so that I would have access to it. 

PrivateRoute: 

```
import { connect } from "react-redux"
const { Route, Redirect } = require("react-router-dom")

const PrivateRoute = ({ component: Component,loggedIn, ...rest }) => {
  return <Route {...rest} render={(props) => (
    loggedIn
      ? <Component {...props} />
      : <Redirect to='/' />
  )}
  />
}

const mapStateToProps = (state) => {
  return { loggedIn: !!state.currentUser }
}


export default connect(mapStateToProps)(PrivateRoute)
```

Updated Router Code: 

```
import React from 'react';
import ReactDOM from 'react-dom';
import Home from './Home'
import Protected from './Protected'
import Login from './Login'
import { BrowserRouter as Router, Route } from 'react-router-dom';
import PrivateRoute from './containers/PrivateRoute';

ReactDOM.render((
  <Router>
    <div>
      <Navbar />
      <Route exact path="/" component={Home} />
      <PrivateRoute exact path="/protected" component={About} />
      <Route exact path="/login" component={Login} />
    </div>
  </Router>),
  document.getElementById('root')
);
```

**Conclusion:**
Client-Side Routing is a very cool way for us to make use of the URLs and keep our application running smoothly and quickly. React Router is an awesome library that helps us to do so and is quite easy to master with a little practice. We were able to slap some links on the DOM and get a simple SPA working in no time! Go us!

**Resources:**

[React Router Docs](https://reactrouter.com/web/guides/quick-start)

[Pros and Cons of Client Side Routing](https://www.pluralsight.com/guides/pros-and-cons-of-client-side-routing-with-react)

