---
layout: post
title: "Create-react-app w/ rails 5 --api"
date: 2017-08-11
---

This is a discussion of a trendy way to build web apps using two componets: an agnostic frontend with an API interface to the backend.  Essentially the front end is just an empty shell asking for the data it needs, usually in the form of a JSON object, and the back end responds to user requests.  From the viewpoint of the application there is no state beyond the last HTTP request.  Theoretically this should be a simpler way to build easily distrubtable web applications.

Lets put this to the test by trying to build a simple React app that sends requests to a Rails API only server.  The end result should be pretty simple, you visit the server hosting your React app and get a message from the Rails server. Something like below:

![A greetings from the Rails API server!]({{ "/assets/Screenshot from 2017-10-28 16-02-36.png" | absolute_url }})

## Why Rails? ##
[The official Rails doc says it best](http://edgeguides.rubyonrails.org/api_app.html).

First off I know it and I like it.  I like Ruby and Rails even though the *magic* can be a little too magical.  But that feeling means you probably need to stop what you're doing and read some docs/source code.  If you feel that things happen too automagikally at the command line just remind yourself that it doesn't need to be a `rails generate ...` command.  You can do all of it by hand.  You can type out those `mkdir` and `touch` commands all you want.  And you probably should.  But for an experiment into Rails 5 --api only mode the automagick made testing fast, easy, and pleasant.

I can safely say that if you're building an API for the 2nd or 3rd time you'll *REALLY* start to feel the deja vu.  The business logic and names of resources are really the only things that change.  Beyond that you're always going to be putting up the same old skeleton and pumping life into it just like you did last time.  So why not use Rails?


## What does splitting the frontend and backend look like? ##
React sits on its own server or server farm and responds to users as they connect.  Whenever the user does something like a Save or Update to a resource an HTTP request gets sent to the API server.  The API processes the client request, interacts with some resource, and sends the response back to the source.  There's no need to store the Users session or state on the API server because this is a RESTful architecture that is stateless by design.

We may want to use a queue or cache to persist the user's state on the React front end so that they can pick up where they left off if they're interrupted but beyond that the API just exists and responds to requests.  When we use the word stateless what we mean is that no single HTTP request from the client needs to know about any previous request.  All necessary data is contained within the HTTP request: authentication for protected resources, the location of the resource, and what we want to do with it.

### The 'ping' route: 'localhost:3000/api/v1/ping' ###

```ruby
../controllers/api/v1/ping_controller

class Api::V1::PingController < ApplicationController
  respond_to :json
  def ping
    render json: { message: "Hello and welcome to Helpdesk!" }, status: :ok 
  end
end
```

This is an excellent example of what most of the routes in your API will look like.  If this was a running server you would see the JSON message appear in the browser as a regular JSON object.  If you were a client device calling the API you would probably process the message somehow and make some changes to the client's state.  Or maybe just print it out.

# The Build #

## Building the Front End ##
For our front end application we'll use [create-react-app](https://github.com/facebookincubator/create-react-app). This is the easiest way to build react apps.  If you want to really learn how the sausage is made just find an old guide from 2014 and follow that.  If you followed the most recent guide to get your create-react-app app up and running you'll run `$ npm start` and see a brand new React application!

## Building the Back End ##
Now we need to generate the Rails API using a few of Rail's builtin commands.  This will be somewhat similar to the create-react-app process in that you'll supply a name and a few optional paramaters to the command line.  But unlike the create-react-app we'll also be able to generate most of the resources we will use.  A resource here is defined as something that a user would be interested in creating, viewing, or modifying.  We can further define a resource as being the final path in the HTTP request to the API.  

For example if we have an API that lets you make and modify hotel reservations and you wanted to delete your reservation it would look like: 

```
DELETE https://www.hotelApi.com/reservations/2017-08-25
```  

Method:  `DELETE`

Hostname:  `www.hotelApi.com`

Path:  `/reservations/2017-08-25`  

These components makeup the request header that React will send to our API.  In response to this request the API could return a `true` or `false`, a confirmation, or an error saying this user doesn't have access to a protected resource (it would be chaos if anyone could delete anyone else's hotel reservations).  If you reference other modern APIs though the typical thing to do is to return a JSON message.  Since this is a `DELETE` operation  it will probably just be a simple confirmation so that the React application can say something like, "Successfully deleted!".

## Generating an API ##
The below assumes you have Rails 5 installed along with Postgres.  Refer to this guide by Digital Ocean for instructions to [install RVM, Rails, and Postgres](https://www.digitalocean.com/community/tutorials/how-to-setup-ruby-on-rails-with-postgres).  Lets build an API that can help us manage tickets submitted to the IT department.

First setup the Database, in our case a [Postgres database](https://wiki.postgresql.org/wiki/Detailed_installation_guides).  You'll probably want to refer back to this and plan on taking an extra 30 minutes to locate all the shortcut commands you need.

Tip:

`psql=> \du`  lists all the roles and their attributes


```terminal
$ rails new HelpdeskApi --api --database=postgresql
```

To test that he server was built correctly lets turn the server on with `$ rails s`.  Then navigate to `localhost:3000` in your browser.  If you see a hello message from Rails you'll know that your API server was generated and is now actively responding to requests.  In the terminal you can see the requests from your browser and the responses that the Rails server is providing.  You can even see the errors!  Try going to the resouce `www.localhost.com:3000/fake-resource` and watch the beautiful error screen on the browser. Then learn to love it and study what you did wrong.

You've officially setup a Rails API server and a React node server, now we have to get them talking to each other.

## Preparing for the Cross Origin Resource Request (CORS). ##
This is assuming you're using Rack for your're middleware.  Read more about [Rack-CORS here](https://github.com/cyu/rack-cors).


The Rails api server and the React server are technically going to be on different domains  this is a bit of a security issue since we don't know if we can trust other web service calls.  Since we're going to be passing HTTP requests to the rails server and sending data back in response we need to permit the Rails application to enable [Cross-Origin Resource Requests](https://developer.mozilla.org/en-US/docs/Web/HTTP/Access_control_CORS) by default.  We know we can trust our React server we can enable CORS for the time being.

Essentially we're going to uncomment what's in the `cors.rb` file and add a wildcard to the origin.
```ruby
# config/initializers/cors.rb
Rails.application.config.middleware.insert_before 0, Rack::Cors do
   allow do
     origins '*'

     resource '*',
       headers: :any,
       methods: [:get, :post, :put, :patch, :delete, :options, :head]
   end
 end
```
## Adding the Ping Controller ##
For our first API resource we'll build `/ping`.  The React server will "ping" the Rails server with a request for the Ping resource.  The Rails server should return a JSON message like, "Pong" or "Hello from the Rails server".  In practice this won't be useful but this request for a resource followed by a JSON response is going to be the typical flow of all requests.  Once we have the JSON object we can manipulate the data however we want with React.  We can test this easily at the command line with [curl](https://curl.haxx.se/) and once we like the response we'll just make an AJAX or Fetch request from React.

Here's an example of curl command line output when we make a request to the locally running API server:
![Ping-API-Example]({{ "/assets/ping failure and success example.png" | absolute_url }})
You can see the first test passed and returned a HTTP 200 response.  The second test was made after the server was shutdown so we didn't get a response to our /ping request.

Later when we add a custom JSON message we should be able to visit the URL in a browser and see an actul text response, not just an HTTP response:
![Ping-API-Message-Example]({{ "/assets/heroku rails sever working.png" | absolute_url }})




The first step is to add the Controller via the Rails generator.  Using a little Rails magic we'll use the [generate](http://guides.rubyonrails.org/command_line.html#rails-generate) command to build a controller:
```terminal
$ rails generate controller Ping
```

Lets modify our Routes after generating the controller:
```ruby
#config/routes.rb
Rails.application.routes.draw do
  namespace :api, defaults: { format: :json } do
    namespace :v1 do
      match  'ping', to: 'ping#ping', via: [:get]
    end
  end
end
```

We'll want this route so we can version our API.  Maybe a month from now we want to phase out the /ping route but we want to still support it in Version 1 of the API.  So we'll just add `namespace :v2 ... ` to the routes and controllers!  We'll leave all our existing logic and build on top of it.  As it appears now our ping resource will be available by calling `www.localhost.com:3000/api/v1/ping`.


## What goes in the Ping Controller And What Status Do We Return ##
First we'll need to match our routes that we previously defined.  Create the `api` then `api/v1` directories.  Then move the ping_controller.rb to the proper path so it looks like this: `../app/controllers/api/v1/ping_controller.rb`.


You're most likely looking at something like the below in your Ping Controller:
```ruby
#app/controllers/api/v1/ping_controller.rb
class PingController < ApplicationController
end
```

We need to update our controller so that our namespaces match, we return a nice little message, add a `ping` action, and respond to JSON requests.

```ruby
#app/controllers/api/v1/ping_controller.rb
class Api::V1::PingController < ApplicationController
  respond_to :json
  def ping
    render json: { message: "Hello and welcome from the Rails Server!" }, status: :ok 
  end
end
```

You can now run the rails server in your terminal `$ rails s` and vist the URL in your browser: `0000:3000/api/v1/ping`.  You may get an error becuase you don't have all the proper Gems to serialize JSON data and execute respond_to directives in the controller but it is offically a URL you can visit!

## Gems We'll Need to Respond with JSON and to Serialize the JSON ##

[Acitve Model Serializers](https://github.com/rails-api/active_model_serializers/tree/0-10-stable)

[Responders](https://github.com/plataformatec/responders)

Both of these gems will allow easy serializtion of JSON as well as a way to respond when a JSON request is sent to the Rails server.

## Issuing an API Call from React ##

Once you're done following your favorite React or create-react-app tutorial you can just add the below snippet to the `componentDidMount()` and `constructor()` functions.  This will make the ping API call as soon as your React app loads in the browser.

```javascript
constructor() {
    super();
    this.state={
      ping: null,
    }
  }
  
  componentDidMount() {
    var pingURL = "/api/v1/ping";
    fetch(pingURL)
    .then((response) => response.json())
    .then((json) => this.setState({ ping: json.message }));
  }
```

You should now be able to follow the above guidelines and make API calls to your backend server!

## Closing Thoughts ##
I didn't like this type of architectural approach from a workflow perspective.  If I was not working alone this would be much, much simpler. I would spin up my frontend OR backend dev environment and just start working in that dedicated environment.  Constantly switching, mentally and physically, between various screens, servers, and terminals wore me out quickly.

This is an excellent approach if your businenss needs demand a distributed application.  But if there is no direct need I would have to recommend to the tried and true monolithic approach.