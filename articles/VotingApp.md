# Building the FreeCodeCamp Voting App

[<img src="https://images.unsplash.com/photo-1495316364083-b5916626072e?dpr=2&auto=format&fit=crop&w=1080&h=720&q=80&cs=tinysrgb&crop=&bg=">](
https://unsplash.com/photos/OwMTchwUTNw)
Photo by Johannes Plenio on Unsplash - https://unsplash.com/photos/OwMTchwUTNw

In my opinion the voting app challenge on FreeCodeCamp was the first one, that was really hard in the curriculum. I just couldn't do it as easy as all the other challenges. Too much knowledge in too many fields is required. Also, I didn't find any tutorials or examples, that broke this challenge down with up-to-date tools. So I decided to document it my building process.

I will use:
- MongoDB
- Express
- React + Redux
- Node.js

also known as the "MERN-Stack".


- Repository on Github is available [here](https://github.com/DDCreationStudios/votingApp).
- Live version of the result is available [here](https://ddcs-votingapp.herokuapp.com/).
- Learnings and numbers are available [here](https://github.com/DDCreationStudios/Writing/blob/master/articles/LearningsFirstFullStack.md).


![gif](
https://camo.githubusercontent.com/2c9cfb5ddf6b658bc9502facbba7b5b083d9b0e7/687474703a2f2f672e7265636f726469742e636f2f31687a643849537a6e742e676966
)


## 📄 Table of contents

- [What this article is about](#what-this-article-is-about)
    - [Structure](#structure)
    - [Development Environment](#development-environment)
    - [Packages / Features / Dependencies](#packages--features--dependencies)
- [First things first](#first-things-first)
- [Backend](#backend)
    - [Set up Packages, Middleware and Mongoose](#set-up-packages-middleware-and-mongoose)
    - [Set up your routes](#set-up-your-routes)
    - [Set up Mongoose and your Schemas and connect everything to your routes](#set-up-mongoose-and-your-schemas-and-connect-everything-to-your-routes)
    - [Establish authentication and authorization with Twitter](#establish-authentication-and-authorization-with-twitter)
    - [Establish local authentication and authorization](#establish-local-authentication-and-authorization)
- [Frontend](#frontend)
    - [Think before you do!](#think-before-you-do)
    - [Necessary setup with Babel and Webpack](#necessary-setup-with-babel-and-webpack)
    - [Structure components](#structure-components)
    - [Design and build components](#design-and-build-components)
    - [Adding Redux](#adding-redux)
    - [Visualization](#visualization)
- [Connect Frontend to the Express Backend with React Router](#connect-frontend-to-the-express-backend-with-react-router)
    - [Rendering client- and server-side](#rendering-client--and-server-side)
    - [Serving everything all together](#serving-everything-all-together)
- [Deployment](#deployment)
- [Conclusion](#conclusion)


---
>"I fear not the man who has practiced 10 000 kicks once, but I fear the man who has practiced one kick 10 000 times."  - Bruce Lee
---

## What this article is about

In this article I will describe the process of building the voting app for the [FreeCodeCamp Challenge](https://www.freecodecamp.com/challenges/build-a-voting-app).

This is not an optimized example for building the application. I am open for feedback of any kind. I am still a beginner and also left some things open. :)

___
This is not designed as a tutorial! It's simply a documentation I wrote while building the app.
___


#### Structure

To break down this application I will divide it into sections for backend, frontend, data visualization and the deployment process. The project will be available as open source code on Github, where you can follow up with commits and the end result.


#### Development Environment

- Plain JavaScript
- Node.js
- Express (JS framework)
- MongoDB (Database)
- Yarn (package management)
- Visual Studio Code as editor
- [Postman](https://www.getpostman.com/) (testing APIs)
- [Robomongo / Robo 3T](https://robomongo.org/) (working faster with MongoDB)


#### Packages / Features / Dependencies

General
- ([ES 6](http://es6-features.org/) (JS scripting-language specification))
- [eslint](https://www.npmjs.com/package/eslint) with Airbnb extension (for writing higher quality code)
- [nodemon](https://github.com/remy/nodemon) (restarting server when changes occur)
- [Babel](https://babeljs.io/) (javascript compiler)
- [Webpack](https://webpack.github.io/) (module bundler/builder)
- [dotenv](https://www.npmjs.com/package/dotenv) (for configuring environment variables)
- [shortid](https://github.com/dylang/shortid) (random ID generator)

Backend
- [Node.js](https://nodejs.org/) (JS runtime environment for server-side)
- [MongoDB](https://www.mongodb.com/what-is-mongodb) (document based database)
- [connect-mongo](https://github.com/jdesboeufs/connect-mongo) (for storing sessions in MongoDB)
- [body-parser](https://github.com/expressjs/body-parser) (for parsing incoming requests)
- [express](http://expressjs.com/de/) (to make the application run)
- [mongoose](http://mongoosejs.com/docs/) (object data modeling to simplify interactions with MongoDB)
- [morgan](https://www.npmjs.com/package/morgan) (HTTP request logger middleware)
- [passport](http://passportjs.org/) (authentication middleware for Node.js)

Frontend
- [React](https://facebook.github.io/react/) (JS framework)
- [Redux](http://redux.js.org/docs/introduction/) (state management for React)
- [Redux Thunk](http://redux.js.org/docs/introduction/) (asynchronous Redux)
- [Materialize CSS](http://materializecss.com/) (framework for material design)
- [React Router](https://github.com/ReactTraining/react-router) (routing in the frontend)




Visualization

- [React Google Charts](https://www.npmjs.com/package/react-google-charts) (React wrapper for Google Charts)
- [Google Charts](https://developers.google.com/chart/) (for visiualizing data)


Deployment / DevOps

- [Heroku](https://www.heroku.com/) (PaaS to run applications in the cloud) 


(Unit)Testing

- Not implemented in this app (but normally it should)

## First things first

First I will set up my environment:
- add git for version control
- create your package management with yarn init
- add express for a fast web development
- add the nodemon package for restarting your server on changes
- add eslint.rc for your eslint configuration
- add babel and corresponding plugins for compiling JS


As additional integration I'll use:
- Travis CI (for continuous integration)
- Code Climate (for Code quality)
- Assertible (Monitoring Web Services, especially checking deployment  - Quality Assurance)

[Check out my commit on Github after the setup.](https://github.com/DDCreationStudios/votingApp/tree/23a05a550f6e2e34cb182d3a271a4c44028d07f9)

## Backend

I will start with the backend, since it's the most difficult in my opinion.

#### Set up Packages, Middleware and Mongoose

I will use:
- [body-parser](https://github.com/expressjs/body-parser) for parsing request bodies
- [morgan](https://www.npmjs.com/package/morgan) for logging out HTTP requests
- [compression](https://www.npmjs.com/package/compression) for compressing response bodies
- [helmet](https://www.npmjs.com/package/helmet) for setting basic security with HTTP headers
- [mongoose](https://www.npmjs.com/package/mongoose) object modeling tool for asynchronous database connection


Next
- create a constants file to set you different environment variables and corresponding settings
- create a middleware file to pass in middleware to your app and differentiate for environments (especially use bodyparser and morgan packages here)
- create a database file to set up the mongoDB connection
- modularize your code and outsource your constants, middleware and database connection for keeping smaller files
- don't forget to import everything in your app.js file, pass in the middleware function and test your setup with a simple http request

[Check out my commit on Github after the setup.](https://github.com/DDCreationStudios/votingApp/tree/88a2436697be4147302ce2dbcd3104ed564c86fe)

#### Set up your routes

Revisit  the User stories and lay out your routes accordingly

Following the [CRUD principle:](https://en.wikipedia.org/wiki/Create,_read,_update_and_delete)

As an unauthenticated user I want to
- see all polls (R)
- see individual polls (R)
- vote on available polls (C)

As an authenticated user I want to
- see/read all polls (R)
- see individual polls (R)
- vote on available polls (C)
- create new polls (C)
- create new options/votes (C)
- delete polls (D)

Therefore:
- set up [error handling as middleware](http://expressjs.com/en/guide/error-handling.html)
- set up your [router object](http://expressjs.com/en/4x/api.html#router)
- create your GET, POST, DELETE routes and response with json objects
- test the set up routes with postman (all should have a status code of 200)
- connect your routes to your middleware and app.js

#### Set up Mongoose and your Schemas and connect everything to your routes

When setting up Schemas think about how you want to structure you documents that have to be stored in the database. In this example we need to store user for the authentication process and polls with answers.

For polls we need:
- the question
- answers/votes

- create your mongoose schemas and models
- connect to [mlab](https://mlab.com/) to monitor your DB actions better
___
Be aware that mlab creates those "System Collections" that throw "duplicate key error index dup key: { : null }" error in postman, when creating new polls. Till now I didn't find a solution but deleting all collections allows to start again. ;)
___
- use the dotenv package to store your credentials in the environment and add the .env file to .gitignore (if you make your project open source)
- connect you routes with your mongoose model to handle the documents in MongoDB


> BE SURE TO READ THE [DOCS](http://mongoosejs.com/docs/models.html) when you are stuck. This part is pretty hard when you haven't done a lot with mongoose and MongoDB!

[Check out my commit on Github after these steps.](https://github.com/DDCreationStudios/votingApp/tree/5dcd7359d2cb1b31e28a08869461b927094550c0)

#### Establish authentication and authorization with Twitter

I want to use the twitter sign-on as an [OAuth](https://oauth.net/) provider to authenticate. It provides better user experience and I also got to explore OAuth.

>OAuth is a standard protocol that allows users to authorize API access to web and desktop or mobile applications. Once access has been granted, the authorized application can utilize the API on behalf of the user.


Of course I found [the great article](https://scotch.io/tutorials/easy-node-authentication-twitter) on how to set up the authentication process in Nodejs. After failing to implement it properly in my app (took me a whole day) I decided to dive straight into the [documentation of passport](http://passportjs.org/docs)!

I love the quote they put up there:

>Despite the complexities involved in authentication, code does not have to be complicated.

___
⭐ Again, as a reminder: Read the Documentation!
___

- first register your app on [twitter apps](https://apps.twitter.com/) and get your settings right (access level and the Callback URL have to be determined!)
- add passport, passport-twitter and express-session packages to your application
- create a file defining a passport strategy (twitter)
- to support login session passport has to serialize and deserialize user
- pass passport to your passport configuration and connect both `passport.initialize` and `passport.session` to your app as middleware (make sure that the express-session is used before!)
- set up routes for authenticating and the callback


[Check out my commit on Github after these steps.](https://github.com/DDCreationStudios/votingApp/tree/d398cce56b1df2d042c07d2849223e88a5a2ed7f)

After that, connect the authentication process to your database
___
⭐ Tip: Use for your callback and testing always `http://127.0.0.1:3000/` instead of `http://localhost:3000/`, since it solves a lot of problems, that might occur using passport-twitter. 😉
___
- create a mongoose Schema for your users (to track them in your database)
- fill the callback function of your passport.js file when implementing the twitter strategy, with filtering your database for the user and creating a new one if a user is not existing
- use the connect-mongo package to create a mongoStore and store your sessions in MongoDB
- create a function to test if a user is authenticated and implement it in your desired routes (providing sufficient **authorization**)
- the implementation can look like this:
```javascript
passport.use(
		new Strategy(constants.TWITTER_STRATEGY, (req, token, tokenSecret, profile, cb) => {
  process.nextTick(() => {
    if (!req.user) {
      User.findOne({ 'twitter.id': profile.id }, (err, user) => {
        if (err) return cb(err);
        if (user) {
          if (!user.twitter.token) {
            user.twitter.token = token;
            user.twitter.username = profile.username;
            user.twitter.displayName = profile.displayName;
            user.save(() => {
              if (err) return cb(err);
              return cb(null, user);
            });
          }
          return cb(null, user);
        }

						// if no user is found create one
        const newUser = new User();

        newUser.twitter.id = profile.id;
        newUser.twitter.token = token;
        newUser.twitter.username = profile.username;
        newUser.twitter.displayName = profile.displayName;

        newUser.save(() => {
          if (err) return cb(err);
          return cb(null, newUser);
        });
      });
    } else {
					// when user already exists and is logged in
      const user = req.user;

      user.twitter.id = profile.id;
      user.twitter.token = token;
      user.twitter.username = profile.username;
      user.twitter.displayName = profile.displayName;

      user.save((err) => {
        if (err) return cb(err);
        return cb(null, user);
      });
    }
  });
}),
	);
```

After that your authentication and authorization with twitter is done.

[Check out my commit on Github after these steps.](https://github.com/DDCreationStudios/votingApp/tree/cb96c8b2062f5c634efcba2b258e3ad054799c48)

#### Establish local authentication and authorization

The next step is to authenticate locally. There is actually not much to it, since we have already set up the environment.

- first, update your user schema for local (defining email and password)
- add the bcrypt-nodejs package for securing passwords
- add hashing and validating password methods to your Schema
- then define the routes (this process always clarifies what I actually want to implement)
- here happened actually a main issue, which I was only able to resolve after many, many hours of searching (here the example from the [docs](http://passportjs.org/docs/configure)):
```javascript
app.get('/login', function(req, res, next) {
  passport.authenticate('local', function(err, user, info) {
    if (err) { return next(err); }
    if (!user) { return res.redirect('/login'); }
    req.logIn(user, function(err) {
      if (err) { return next(err); }
      return res.redirect('/users/' + user.username);
    });
  })(req, res, next);
});
```

Only this way, passing in the authentication in the callback function,  provided enough flexibility for displaying errors, but it's very important to create the session explicitly with `logIn()`!

- make sure to differentiate in the routes between signup and login!
- I installed EJS as view engine to actually being able to test my signup and login properly and efficient
- create a logout route, that destroys your session


___
I set so many hours on that Error that I want to display it here again:
`MongooseError: Cast to ObjectId failed for value "favicon.ico" at path "_id"`

I solved it through checking all middleware (there was a major error) and routes. Turned out that setting a route to ('/:pID') is not good when working in development. :D
___

[Check out my commit on Github after the backend setup.](https://github.com/DDCreationStudios/votingApp/tree/30e9627ba3ac2c6b45c64bf2bad4df5726e15a67)

Of course at this point the backend is not perfect. But it's stable enough to go to the next step, the frontend.

Things to do:
- use validation ( with [joi](https://www.npmjs.com/package/joi))
- write unit tests

___

## Frontend

#### Think before you do!

First of all think about what you want to create. Draw out some sketches to visualize what you want to build. Then consider appropriate frameworks.
I will choose React.js and the state management library Redux. The size of this application does NOT necessarily require the use of Redux, however since I want to build it as a single page experience, want to have scalability and like to practice the use of Redux, it's a good fit.

Start planning everything out [thinking in React](https://facebook.github.io/react/docs/thinking-in-react.html).

#### Necessary setup with Babel and Webpack

It's important to realize that Babel and Webpack are not too complicated to set it up yourself. There are so many tutorials for both that you can do it easily yourself.

- add babel for react and es2015 with adding `babel-preset-react babel-preset-es2015` to your dev dependencies (to compile JSX into JS and having all es6 features)
- update your .babelrc file
- update your webpack config and add the react-hot-loader package

At first I want to structure my frontend without the backend in order to connect the whole frontend with the backend at the end. The reason for that is because for now I don't know how my Redux implementation will look like and therefore progressively connecting to the backend wouldn't be efficient.

Therefore:

- restructure your current app.js into an own folder
- create a new app.js as entry point and provide the [basic setup code](https://medium.com/@dabit3/beginner-s-guide-to-react-router-53094349669) for rendering a simple page
- the get the setup working install the react-router, webpack-dev-server and of course react and react-dom packages
- opening a page on the dev-server port should display your react component

[➡️ Check out my commit on Github after the new setup ⬅️](https://github.com/DDCreationStudios/votingApp/tree/9ed98937551ad5eb3208be392040b39c35d4d231)

#### Structure components

I sketched everything out on a paper and came to the conclusion to build 14 components:

- the app component, that hosts everything
- a header
- a footer
- a sidebar
- a signup/login/social media component
- a home screen
- a list of all polls
- the display of a single poll
- a component for the poll and it's answers
- the answers as a list
- the chart
- a 404 page

That layout was for the start and should provide an overview. It is very natural to adapt the component structure when the application is evolving. 


#### Design and build components


- first of all I lay out all components and simply style them with materialize css
- remember that styling with React is more complicated than styling normal HTML elements. For simplicity reasons I fix everything with inline styling on the component itself

>Tip: For 100vh on your main content use this inline style on a div, it fits perfectly into the materialize flexbox:
```javascript
style={{
  display: 'flex',
  minHeight: '100vh',
  flexDirection: 'column',
}}
```

- in the process of building components you will slowly get a feeling on how you need to structure your state management with React and Redux


[➡️ Check out my commit on Github after the components are built and styled ⬅️](https://github.com/DDCreationStudios/votingApp/tree/eb17c360e09515f22f8ac38574f576a53855037b)

- Now we have to set up React Router to get a basic functionality and feeling for the app
- enable `historyApiFallback: true` on your webpack dev server to allow proper routing with react router
- add state and it's management to the components
- realize that Redux might be a good next step


Here is a list of painful learnings I had to undergo throughout this process:

- for accessing object properties dynamically use bracket instead of dot notation (used here: `javascript answers = answers.concat(this.refs[temp].value);`)
- importing everything as * (`import * as Polls from './ducks/polls';`) from ducks, because else it's not working
- I have often read to not use the index of a map function as key value for a component. However, when rendering with onChange and generating an unique key, the input loses focus and is not working properly (`const answerList = this.state.answers.map((answer, ind) => {
return (<div className="input-field col s10" key={ind}>`)
- when iterating over an array of objects and you want to change properties on an object, you have to return an object, like:  `return{ answer: answ.answer, votes: 0};` (took me 4 hours to understand 😞)


#### Adding Redux

The [Principles](http://redux.js.org/docs/introduction/ThreePrinciples.html) of Redux are:
- Single source of truth
- State is read-only
- Changes are made with pure functions

Keep in mind, that local state doesn't need to take part in Redux when it's state isn't used by other components.

- add the [react-redux](https://github.com/reactjs/react-redux) and [redux](https://github.com/reactjs/redux) packages
- make use of the [ducks structure](https://github.com/erikras/ducks-modular-redux) to manage the redux files better
- create a store in Redux and wrap your rendering app in a `Provider` tag from react-redux
- connect state to your application with `connect`
- add the [Redux DevTool](https://github.com/zalmoxisus/redux-devtools-extension) to debug faster

Now that State is available through Redux, it's time to create the event handlers and render everything accordingly. At least now you should validate your propTypes as well.


#### Visualization

For displaying the results I chose between:

- [Recharts](http://recharts.org/#/en-US/examples/SimpleAreaChart)
- [Victory](http://formidable.com/open-source/victory/docs)
- [React-Vis](http://uber.github.io/react-vis/#/)
- [ReactD3](http://www.reactd3.org/docs/basic/#area)
- [React-Google-Charts](https://github.com/RakanNimer/react-google-charts)

Quickly skimming all docs and trying a few things out I ended up choosing React-Google-Charts. Google provides many options and the React wrapper makes it easy to implement in a React application.

With the React Wrapper (listed above) this step was super easy and fast. 

That's basically it: 

```javascript
const resultChart = (props) => {
  basic = [['Answer', 'Votes']];
  (() => props.poll.answers.map(ans => basic.push([ans.answer, ans.votes])))();
  return (
    <Chart
      chartType="PieChart"
      data={basic}
      options={{
        title: `${props.poll.question}`,
        pieSliceText: 'label',
        slices: {
          1: { offset: 0.1 },
          2: { offset: 0.1 },
          3: { offset: 0.1 },
          4: { offset: 0.1 },
        },
        is3D: true,
        backgroundColor: '#616161',
      }}
      graph_id="PieChart"
      width="100%"
      height="400px"
      legend_toggle
    />
  );
};
```

## Connect Frontend to the Express Backend with React Router

#### Rendering client- and server-side

As this was my first real full-stack app, connecting the frontend and backend was a mystery to me

I found a pretty good answer to my question on [Stackoverflow](https://stackoverflow.com/questions/27928372/react-router-urls-dont-work-when-refreshing-or-writting-manually).

To summarize and quote the answer of [Stijn](https://stackoverflow.com/users/286685/stijn-de-witt):

>With client-side routing, which is what React-Router provides, things are less simple. At first, the client does not have any JS code loaded yet. So the very first request will always be to the server. That will then return a page that contains the needed script tags to load React and React Router etc. Only when those scripts have loaded does phase 2 start. In phase 2, when the user clicks on the 'About us' navigation link for example, the URL is changed locally only to http://example.com/about (made possible by the History API), but no request to the server is made. Instead, React Router does it's thing on the client side, determines which React view to render and renders it.

- Combining server- and client-side routing

>If you want the http://example.com/about URL to work on both the server- and the client-side, you need to set up routes for it on both the server- and the client side.

>And this is where your choices begin. Solutions range from bypassing the problem altogether, via a catch-all route that returns the bootstrap HTML, to the full-on isomorphic approach where both the server and the client run the same JS code.


- Bypassing the problem altogether: Hash History

>With Hash History i.s.o Browser History, your URL for the about page would look something like this: http://example.com/#/about The part after the hash (#) symbol is not sent to the server. So the server only sees http://example.com/ and sends the index page as expected. React-Router will pick up the #/about part and show the correct page.

>Downsides:
'ugly' URLs
Server-side rendering is not possible with this approach. As far as SEO is concerned, your website consists of a single page with hardly any content on it.

- Catch-all

>With this approach you do use Browser History, but just set up a catch-all on the server that sends /* to index.html, effectively giving you much the same situation as with Hash History. You do have clean URLs however and you could improve upon this scheme later without having to invalidate all your user's favorites.

>Downsides:
More complex to set up
Still no good SEO


- Hybrid

>In the hybrid approach you expand upon the catch-all scenario by adding specific scripts for specific routes. You could make some simple PHP scripts to return the most important pages of your site with content included, so Googlebot can at least see what's on your page.

>Downsides:
Even more complex to set up
Only good SEO for those routes you give the special treatment
Duplicating code for rendering content on server and client


- Isomorphic

>What if we use Node JS as our server so we can run the same JS code on both ends? Now, we have all our routes defined in a single react-router config and we don't need to duplicate our rendering code. This is 'the holy grail' so to speak. The server sends the exact same markup as we would end up with if the page transition had happened on the client. This solution is optimal in terms of SEO.

>Downsides:
Server must (be able to) run JS. I've experimented with Java i.c.w. Nashorn but it's not working for me. In practice it mostly means you must use a Node JS based server.
Many tricky environmental issues (using window on server-side etc)
Steep learning curve

In the end I was going with the catch-all solution: See in my [routes.js file](https://github.com/DDCreationStudios/votingApp/blob/master/src/serverSideES6/routes.js).

```javascript
//routes.js
router.get('/*', (req, res) => {
  const options = {
    root: `${__dirname}/../../public/`,
    dotfiles: 'deny',
  };
  res.sendFile('index.html', options);
});
```

It was easy and fast to implement and covers the basic problems.

#### Serving everything all together

To understand that, the best way is to take a look at my [package.json file](https://github.com/DDCreationStudios/votingApp/blob/master/package.json).

The scripts say:

```json
"scripts": {
		"start": "node src/serverSide/server.js",
		"serve": "babel-node src/serverSideES6/server.js",
		"dev": "npm-run-all --parallel dev:*",
		"dev:client": "webpack-dev-server --hot",
		"dev:server": "nodemon src/serverSide/server.js",
		"build": "npm-run-all --parallel build:*",
		"build:client": "webpack --progress",
		"build:server": "babel src/serverSideES6 --out-dir src/serverSide"
	},
```

The `build` script builds the files on the client and server side.
- It compiles all my ES6 node.js code into ES5 (so Heroku can read it as well)
- Webpack starts the bundeling and transpiling of the client side. (ES6 to ES5, JSX to JavaScript, etc. - see webpack config )

The `dev` script serves everything in a development environment and (hot) reloading, so that everything is as fast and smooth as possible, when changing the codebase.

The `start` script actually starts the backend server, which also consumes the built and bundled frontend HTML/CSS/JS, presenting the whole application.


## Deployment 

For deploying the app, Heroku once again had proven to be the way to go.

Using the [Heroku CLI](https://devcenter.heroku.com/articles/heroku-cli), the `Heroku logs` command helps a lot. I always had trouble setting up my app on the platform, but after simply solving all the errors the logs show, it becomes really easy.

Always important:
- Be aware that devDependencies are not installed 
- Use the adequate buildpack (in this case for NodeJS)
- Have a start script or define one in your Procfile
- Be sure to push the right branch from the right repository

## Conclusion

As you can see my documentation for this article gets worse and worse with the progress of the app. This is due to the fact, that I got completely overwhelmed with Redux. I did other projects on the side and wasn't able to keep track. 

But don't worry! I tried to name my commits as clear as possible. So, you can traverse all commits for details in my Repository. See [Commits here](https://github.com/DDCreationStudios/votingApp/commits/master). 

If you have questions feel free to ask :) 

- Repository on Github is available [here](https://github.com/DDCreationStudios/votingApp).
- Live version of the result is available [here](https://ddcs-votingapp.herokuapp.com/).
- Learnings and numbers are available [here](https://github.com/DDCreationStudios/Writing/blob/master/articles/LearningsFirstFullStack.md).

![gif](
https://camo.githubusercontent.com/2c9cfb5ddf6b658bc9502facbba7b5b083d9b0e7/687474703a2f2f672e7265636f726469742e636f2f31687a643849537a6e742e676966
)

---

## Many, many thanks to [Edo Rivai](https://twitter.com/EdoRivai), who gave very valuable tips along the way. :) 

---

Thanks for reading my article! Feel free to leave any feedback!


<!-- Written by Daniel Deutsch (deudan1010@gmail.com) -->
