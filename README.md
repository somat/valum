# Valum Framework. Relax :)

Valum is a web framework written in Vala.

# Quickstart

Debian/Ubuntu:

    sudo apt-get install git-core build-essential valac-0.14 libgee-dev \
         libsoup2.4-dev libjson-glib-dev memcached libmemcached-dev \
         libluajit-5.1-dev libctpl-dev


    git clone git://github.com/antono/valum.git
    cd valum && make run


Visit http://localhost:3000/

Search for other routes in ./app/app.vala

# Examples

## Setup application
   
Somwhere in app/myfile.vala

    var app  = new Valum.App();
    app.port = 8080;
    app.listen();

## Simple GET request

    app.get('hello', (req, res) => {
      res.status = 200;
      res.mime = "text/plain";
      res.headers["Hello"] = "Browser";
      res.append("Hello World!");
    });
    
    // GET /hello

## Route scoping

    app.scope("admin", (admin) => {
      admin.get("user/:id", (req, res) => {
        var id = req.params["id"];
        res.append(@"User id is $id");
      });
    });
    
    // GET /admin/user/11
    // GET /admin/user/antono
    
## Simple POST request (TODO)

    app.post("/form", (req, res) => {
      req.params["title"]; // NOT YET IMPLEMENTED
    });

## Scripting languages!

### Embedded Lua

Currently it works this way:

    // GET /lua
    app.get("lua", (req, res) => {
      res.append(lua.eval("""
        require "markdown"
        return markdown('## Hello from lua.eval!')
      """));
      
      res.append(lua.run("app/hello.lua"));
    });


This code works with either Lua or LuaJIT depending
on --pkg option in Makefile. See ./vapi/lua[jit].vapi for
details.

We are going to implement simplier syntax for lua scripts:

Vala code:

    // GET /lua.html
    app.get("lua.html", app.lua("hello"));

Lua code:

    -- VALUM_ROOT/scripts/hello.lua
    require 'markdown'
    return markdown("# Hello from Lua!!!")
    -- returned value will be appended to response body

Resulted html:

    <h1> Hello from Lua!!! </h1>
   
### Embedded scheme! (TODO)

Vala code:

    // GET /hello.scm
    app.get("hello.scm", app.scm("hello"));

Scheme code:

    ;; VALUM_ROOT/scripts/hello.scm
    (+ 1 2 3)
    ;; returned value will be casted to string
    ;; and appended to response body

## Persistance

### Memcached

    var mc = new Valum.NoSQL.Memcached();
    
    // GET /hello
    app.get('hello', (req, res) => {
      var value = mc.get("hello");
      res.append(value);
      mc.set("hello", @"Updated $value");
    });

### Redis (TODO)

We need vapi for hiredis: https://github.com/antirez/hiredis

    var redis = new Valum.NoSQL.Redis();
    
    app.get('hello', (req, res) => {
      var value = redis.get("hello");
      res.append(value);
      redis.set("hello", @"Updated $value");
    });


### MongoDB (TODO)

This is not yet implemented. But mongo client for 
vala is on the way: https://github.com/chergert/mongo-glib

    var mongo = new Valum.NoSQL.Mongo();
    
    // GET /hello.json
    app.get('hello.json', (req, res) => {
      res.mime = 'application/json';
      res.append(mongo.find("hello"));
    });


### CouchDB (TODO)

https://code.launchpad.net/~adiroiban/couchdb-glib/vala-bindings


# Contributing

 - add self@antono.info to your jabber contact list
 - fork reository
 - pick one task from TODO.md
 - and add your name after it in TODO 
 - code
 - make pull request
 - enjoy :)

# Discussions and help

 - Mailing list: [vala](https://mail.gnome.org/mailman/listinfo/vala-list).
 - IRC channel: #vala at irc.gimp.net
 - [Google+ page for Vala](https://plus.google.com/115393489934129239313/)
 - Issues at [github] (https://github.com/antono/valum/issues)
