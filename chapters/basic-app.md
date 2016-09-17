# A Basic Dancer Web App

In this chapter we will get the most basic Dancer2 application up and
running. It will take about a minute.

## Generating an App

Dancer2 comes with a command line program which allows you to start a
new application easily. It's called `dancer2` and you run it like
this:

    $ dancer2 gen -a [AppName]

Our application is a Todo list. So we'll call it "Todo" and run
`dancer2` like this:

    $ dancer2 gen -a Todo

This command will create a directory called `Todo` and will write a
lot of new files into that directory - telling you what it is doing as
it does it. I got the following output:

    + Todo
    + Todo/cpanfile
    + Todo/Makefile.PL
    + Todo/MANIFEST.SKIP
    + Todo/config.yml
    + Todo/.dancer
    + Todo/public
    + Todo/public/dispatch.fcgi
    + Todo/public/500.html
    + Todo/public/dispatch.cgi
    + Todo/public/favicon.ico
    + Todo/public/404.html
    + Todo/public/images
    + Todo/public/images/perldancer-bg.jpg
    + Todo/public/images/perldancer.jpg
    + Todo/public/css
    + Todo/public/css/style.css
    + Todo/public/css/error.css
    + Todo/public/javascripts
    + Todo/public/javascripts/jquery.js
    + Todo/t
    + Todo/t/001_base.t
    + Todo/t/002_index_route.t
    + Todo/lib
    + Todo/lib/Todo.pm
    + Todo/views
    + Todo/views/index.tt
    + Todo/views/layouts
    + Todo/views/layouts/main.tt
    + Todo/environments
    + Todo/environments/production.yml
    + Todo/environments/development.yml
    + Todo/bin
    + Todo/bin/app.psgi

We'll look in more detail at these files soon, but firstly, let's run
our new Dancer2 app. Because what `dancer2` has given us is already a
runnable application. It doesn't do much yet, but that's what the rest
of this book is for.

Dancer2 (like most modern Perl web frameworks) is based on a protocol
called PSGI (Perl Server Gateway Interface). PSGI allows us to write
web applications that are completely independent of their deployment
environment and this makes it far easier to write, debug and test web
applications that it has ever been before. There is also a toolkit for
working with PSGI applications which is called Plack. And we need to
turn to Plack in order to demonstrate our new web application working.

At the bottom of our list of new files, you'll see `app.psgi`. That is
the actual program that is used to run our application. Just drop that
file into any PSGI-aware environment to make your web application
available. But what do I mean by a "PSGI-aware environment"? Well,
we'll look into that in more detail later on, but, for now, we'll use
a program called `plackup` which is part of the Plack toolkit and
allows use to run a PSGI application very easily. Simply run this on a
command line:

    $ plackup bin/app.psgi

If all is well, then you'll see the following:

    HTTP::Server::PSGI: Accepting connections at http://0:5000/

This tells us that `plackup` is using HTTP::Server::PSGI to run a web
server which is running on our local system and listening on
port 5000. If you open a web browser and visit http://localhost:5000/
you will see your web application looking like this:

![](img/step01.png)
