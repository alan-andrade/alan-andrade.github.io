---
layout: post
title: "Phoenix in 10 minutes"
modified:
categories:
excerpt:
tags: []
image:
  feature:
date: 2015-10-10T22:43:25-07:00
---

So you've been hearing good stuff about Elixir and Phoenix but you
haven't had a chance to check it out ? Well, you're reading the right blog
post. Today we're going to build an API that will echo the parameters of the
request with json format. Within 10 minutes. So don't go to lunch yet,
check this out.

# Resources

## Erlang

In order to use Elixir, we need to install Erlang first because Elixir
runs on top of Erlang's virtual machine. Install Erlang 18.0 from
[here](https://www.erlang-solutions.com/downloads/download-erlang-otp)

## Elixir

Brew it with `brew update && brew install elixir` or install it from [here](http://elixir-lang.org/install.html#distributions)

## Phoenix

1. Install 'hex', the dependency manager `mix local.hex`
2. Install Phoenix `mix archive.install
https://github.com/phoenixframework/phoenix/releases/download/v1.0.3/phoenix_new-1.0.3.ez`

Elixir comes with a tool called mix which is like ruby's rake, it runs
predefined tasks or "recipes".  Hex on the other hand, ensures that
every library that we use is compatible with each other. Hex plays the
role of Bundler or NPM, but for Elixir.

# Project creation

`mix phoenix.new echoAPI --no-brunch --no-ecto --no-html`

1. Create the project under the folder "echoAPI".
2. `--no-brunch` Skips the asset manager for front end stuff such as images
and javascripts.
3. `--no-ecto` Skips the database and the model layer.
4. `--no-html` Skips the generation of any html.

# The plan

Phoenix follows the MVC pattern and comes with conventions
similar to those present in rails or ember. Don't worry if you haven't
used any of those frameworks, I'll explain the general idea and you'll
be able to follow along.

To build our useful echo endpoint, we're going to work on two key components.
Remember that this app doesn't need any database so the "Model" is out
of the "MVC"[^1]. We need to define a **controller** that can handle the high
volume of incoming requests and also get it to render json.
We've tackled the 3 letters out of MVC, but to get this to work, we need
to define a new entry on the application's **router**. The routes are
like a map that hold information of which controller will process what
endpoint. e.g. `/api/echo` should be handled by the controller
"EchoController".

Let's begin !

# Controller

Create a new file under `web/controllers` and name it
`echo_controller.ex`.

The controller will process the request and should return a json with
all the parameters sent in the request. The file should look like this.

{% highlight elixir %}
defmodule EchoAPI.EchoController do
  use EchoAPI.Web :controller

  def index(conn, params)
    json(conn, params)
  end
end
{% endhighlight %}

1. `defmodule` Is used to define a new module in Elixir.
2. `EchoAPI.EchoController` is the name of the controller prefixed by
   the namespace of the application.
3. `use EchoApi.Web :controller` is where something like inheritance
   happens. This will inject code that comes from `web/web.ex' which
  gives this module the interface and features of a controller.
4. `def index(conn, params)` is a function definition which will called
   when the request comes in.
5. `conn` is the current connection.
6. `params` are the request parameters.
7. `json` Is a method defined in `Phoenix.Controller` which sends a json
   reponse with the second argument as the body.

This controller is ready to answer requests and echo the parameters
back. Let's connect it it the router.

# Routes

Open `web/router.ex`, it should look like this.

{% highlight elixir %}
defmodule EchoAPI.Router do
  use EchoAPI.Web, :router

  pipeline :api do
    plug :accepts, ["json"]
  end

  scope "/api", EchoAPI do
    pipe_through :api
  end
end
{% endhighlight %}

- `pipeline` is a wrapper for a set of operations that will be made on
  the request. Similar concept to Rack.
- a `plug` is an interface for operations that will run inside of a
  pipeline. This is like the Rack Middleware.
- `scope` is used to group endpoints and the second argument is an
  alias. This means that anywhare under scope, you can use your modules
without typing the namespace. e.g. "EchoAPI.EchoController" becomes
"EchoController".
- `pipe_through` is making sure that anything under that group, goes
  through the the set of operations of the pipeline "api".

Let's connect out new controller !

{% highlight elixir %}
# ...
scope "/api", EchoAPI do
  pipe_through :api

  get '/echo', EchoController, :index
end
# ...
{% endhighlight %}

- `get` is a macro that maps the controller to a `GET` request.
- `/echo` is the endpoint and since its under the "/api" scope, its
  final shape is "/api/echo"
- `EchoController` is the name of the controller that will serve.
- `:index` is the function of the controller that will take care of
  things.

# Play

1. Run you application with `mix phoenix.server`
2. Use curl or a browser to point to the new endpoint. `curl
   localhost:4000/api/echo\?foo=bar`
3. Feel proud. :godmode:

# Conclusion

If you come from the Rails world, Phoenix will look quite familiar.


Most of the framework's ideas can be traced back to Rails and friends.
This helps to understand more about how things work and connect the
dots.

[^1]: Not having a database doesn't mean our app doesn't have a model.  I made an exception and tight the two concepts together. It's perfectly normal to have "Models" that aren't dependant on a database.
