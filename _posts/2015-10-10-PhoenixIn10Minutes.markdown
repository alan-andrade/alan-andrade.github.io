---
layout: post
title: "Phoenix in 10 minutes"
modified:
categories:
excerpt: Build an API with Phoenix and get a feeling of the framework already.
tags: [api, functional, phoenix, erlang, elixir]
image:
  feature: https://raw.githubusercontent.com/phoenixframework/phoenix/master/priv/static/phoenix.png
date: 2015-10-10T22:43:25-07:00
---

So you've been hearing good stuff about Elixir and Phoenix but you
haven't had a chance to check it out ? Let's fix that by building an API
within 10 minutes. The server will echo the parameters of the request
with json format. Don't go to lunch yet ! :open_hands:

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

Elixir comes with a tool called mix which is like ruby's rake. It can run
predefined tasks or 'recipes'.  Hex on the other hand, ensures that
every library that's used within the project, is compatible with each other.
Hex plays the role of Bundler or NPM but in the Elixir world.

# Create the project

In order to create the skeleton of a Phoenix application run

`mix phoenix.new echoAPI --no-brunch --no-ecto --no-html`

1. This creates the project under the folder "echoAPI".
2. `--no-brunch` Skips the front-end build tool and no, we aren't
   mocking any community, brunch is a [library](http://brunch.io/).
3. `--no-ecto` Skips the database and the model layer.
4. `--no-html` Skips the generation of html.

# The plan

Phoenix follows the MVC pattern and comes with conventions
similar to those present in rails or ember. Don't worry if you haven't
used any of those frameworks, I'll explain the general idea and you'll
be able to follow along.

To build our useful echo endpoint, we're going to work on two key
components.  Remember that this app doesn't need any database so the
"Model" or the "M" is out of the "MVC"[^1]. We need to define a
**controller** (C) that handles the high volume of incoming requests and
also get it to render json (V).  We've tackled the 3 letters of MVC,
but to get this to work, we need to define a new entry in the
application's **router**. The routes are like a map that holds
information of which controller is processing which endpoint. e.g.
`/api/echo` should be handled by the controller "EchoController".

> During this pseudo tutorial, you're invited to type what goes on the
> file and use the numbered bullet points below the code as a guide.
> Copy/Paste is actually invalid. Leave that for your SOP[^2] Craft.

Let's begin ! :muscle:

# Controller

Create a new file under `web/controllers` and name it
`echo_controller.ex`. Type the following code while you whistle your
favorite song.

{% highlight elixir %}
defmodule EchoAPI.EchoController do
  use EchoAPI.Web :controller

  def index(conn, params)
    json(conn, params)
  end
end
{% endhighlight %}

Use these bullet points for reference as you type.

1. `defmodule` is used to define a new module in Elixir.
2. `EchoAPI.EchoController` is the name of the controller prefixed by
   the namespace of the application.
3. `use EchoApi.Web :controller` is where inheritance happens[^3]. This will inject code that comes from `web/web.ex' which
  gives this module the interface and features of a controller.
4. `def index(conn, params)` is a function definition which will called
   when the request comes in.
5. `conn` is the current connection.
6. `params` are the request parameters.
7. `json` is a method defined in
[Phoenix.Controller](http://hexdocs.pm/phoenix/Phoenix.Controller.html#json/2)
which sends a json reponse with the second argument as the body.


This controller is ready to roll (Just like [Rust](https://twitter.com/_alan_andrade/status/450003473510047744)). Let's connect it to the
router.

# Routes

Open `web/router.ex` and type the following while singing like Freddie
Mercury.

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

Mamaaa ! this was aaaaalready there ! :notes: hehe :trollface:

Just make sure to read the bullets to know what's going on.

- `pipeline` collects operations that transform the request in any possible
way.  This is a similar concept to [Rack](http://rack.github.io/).
- A `plug` is an interface for operations that will run inside of a
  pipeline. This is like the Rack Middleware.
- `scope` is used to group endpoints. The second argument is an
  alias, this means that anywhere under this scope, you can use your modules
without typing the namespace. e.g. "EchoAPI.EchoController" becomes
"EchoController".
- `pipe_through` is making sure that anything under that group, goes
  through the the set of operations of the :api pipeline.

Let's connect the controller for real ! Type the following

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

1. Run your application with `mix phoenix.server`
2. Use curl or a browser to point to the new endpoint. `curl
   localhost:4000/api/echo\?foo=bar`
3. Feel proud of yourself :godmode:.

# Conclusion

We just built a simple API that allowed us to get a feeling on Phoenix.
The setup is quick and easy and that's good for adoption !

Given that Elixir was inspired by Ruby and that Phoenix is very similar to
Rails, the learning curve for these technologies is light if you come from
that world. Learning Erlang and the functional style might bend your
brain in the beginning, but be patient, this will make you a better
programmer overall. I encourage you to learn functional approaches.

I think that Phoenix has a bright future because its allowing
programmers to build applications that are fast and scalable __by default__.
I've seen various companies having to spawn many workers in order to make
a rails application scale while using redis to do global locks. That's without
mentioning the pain of maintaining the monolithic. Don't forget however,
that's how we got here.

From the bussiness perspective, Phoenix applications can promise less
engineering costs with better results. Read about WhatsApp
infraestructure [here](http://highscalability.com/blog/2014/2/26/the-whatsapp-architecture-facebook-bought-for-19-billion.html)
to get a better idea of the magnitude this setup can reach.

Before you go, I want to thank you for reading this and good luck with
your new adventures !  \o/


[^1]: Not having a database doesn't mean our app doesn't have a model.  I made an exception and tight the two concepts together. It's perfectly normal to have "Models" that aren't dependant on a database.
[^2]: Stack Overflow Programming.
[^3]: This is not really OOP inheritance, it's more like Ruby mixins (which are similar, but different).
