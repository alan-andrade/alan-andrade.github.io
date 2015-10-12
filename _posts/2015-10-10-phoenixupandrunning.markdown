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

Brew it with `brew update && brew install elixir` or
install it from [here](http://elixir-lang.org/install.html#distributions)

## Phoenix

1. Install 'hex', the dependency manager `mix local.hex`
2. Install Phoenix `mix archive.install
https://github.com/phoenixframework/phoenix/releases/download/v1.0.3/phoenix_new-1.0.3.ez`

Elixir comes with a tool named `mix` which is like ruby's rake, it runs
predefined tasks or "recipes".  Hex on the other hand, ensures that every
library that we use is compatible with each other.
This acts like bundler in the ruby world.

# Project creation

`mix phoenix.new echoAPI --no-brunch --no-ecto --no-html`

1. Create the project under the folder "echoAPI".
2. Skips the asset manager for front end stuff such as images and
   javascripts. `--no-brunch`.
3. Skips the database and the model layer. `--no-ecto`.
4. Skips the generation of any html `--no-html`.

# Controller

Create a new file under `web/controllers` and name it
`echo_controller.ex`.

The controller will process the request and should return a json with
all the parameters sent in the request.

~~~elixir
defmodule EchoAPI.EchoController do
  use EchoAPI.Web :controller

  def index(conn, params)
    json(conn, params)
  end
end
~~~

1. `defmodule` Is used to define a new module.
2. `EchoAPI.EchoController` is the name of the controller prefixed by
   the namespace of the application.
3. `use EchoApi.Web :controller` is where something like inheritance
   happens. This will inject code that comes from `web/web.ex' that
   allows this module to be a Phoenix controller.
4. `def index(conn, params)` is the function that will dispatch requests
   for our endpoint.
5. `conn` is the current connection
6. `params` are the request parameters.
7. `index` is the name of the method and has this specific name by
   convention.






