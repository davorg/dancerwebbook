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

Of course, the program doesn't do anything useful yet (that's what the
rest of the book is for) but it runs. And that's a good start.

## Files

Let's take a closer look at some of the files that the `dancer2 gen`
command created for us.

In the top level directory, you will find the following four files:

* **cpanfile** is a file that various parts of the Perl toolchain can
  use to ensure that a user has installed all of the modules that are
  required to run your application. As our app uses more modules, we
  will need to add information to this file.
* **Makefile.PL** is a file that will be familiar to anyone who has
  installed Perl modules manually. Running this file in the correct
  environment will generate a `Makefile` which can be used to test and
  install our application. This is another file which we will need to
  keep up to date as we add things to our application.
* **MANIFEST.SKIP** is another file that the Perl toolchain uses. It
  lists files that don't need to be included in the distribution
  tarball for our application. Usually, you'll find that the version
  that has been created will handle all of your requirements, so
  there's rarely any need to edit this.
* **config.yml** is the main configuration file for our
  application. It is in YAML (YAML Ain't a Markup Language) format
  which makes it simple to read and edit. We'll need to make various
  changes to this file during the development of our application.

Then there are a few subdirectories. We'll look at some of these in
more detail in the following chapters but, for now, we'll just
describe the contents in high-level terms.

* **public** contains files that will be served directly by our
  application. These are generally static files like images or CSS
  files.
* **t** contains tests for our application. We'll try to add new tests
  to this directory whenever we add new features to our application.
* **lib** is where (most of) the magic happens. This is where you will
  add the Perl code that drives your application. We'll be doing a lot
  of work in this directory.
* **views** contains the templates that are used to create the web
  pages that the users see. We will be making a lot of changes in this
  directory in the next chapter.
* **environments** contains two further configuration files called
  `production.yml` and `development.yml`. Dancer apps can run in
  different modes and these extra configuration files can be used to
  make the app work in slightly different ways in different
  environments. If you look at the two files, for example, you will
  see that logging is configured differently in them.
* **bin** contains the main application file. This is a ".psgi" file
  instead of the more usual ".pl" file as Dancer apps are all built on
  top of the "Perl Server Gateway Interface". I'll explain a little
  more about that later on.

There are a lot of files there. And we'll look at many of them in more
detail as we develop the app. For now, I just wanted to look at two of
them - the main application file and the Todo.pm library. The main
application file (called `bin/app.psgi`) looks like this:

    #!/usr/bin/env perl

    use strict;
    use warnings;
    use FindBin;
    use lib "$FindBin::Bin/../lib";

    use Todo;
    Todo->to_app;

There's not very much to it. And most of that is boilerplate. Let's
look at each line in turn.

    #!/usr/bin/env perl

This is the "shebang" line. It's a Unix feature. If the first line of
an executable Unix (or Linux) file starts with `#!` then the Unix
shell assumes that the rest of the line contains the path to a program
that should be used to execute the code. It's common to see shebang
lines like `#!/usr/bin/perl`, but that assumes that the Perl compiler
is in a specific directory. Using the `env` program means that the
shell will search the user's path for the first Perl compiler that it
can find.

    use strict;
    use warnings;

These two lines should be in every Perl program that your write. They
are basically "safety nets" which let you know if you're writing code
that looks a bit strange.

    use FindBin;
    use lib "$FindBin::Bin/../lib";

These two lines tell Perl to add our app's `lib` directory to the list
of places where it looks for modules. The "FindBin" module is a
standard tool that is included with every Perl installation. Once
FindBin is loaded, you have access to a variable called
`$FindBin::Bin` which contains the name of the directory containing
the currently executing program. That's `Todo/bin`, so we append
`../lib` in order to get `Todo/lib`. We pass all that to `use lib`
which is a Perl standard library tool which adds any strings it is
given to the library search path. That allows us to then include the
next line.

    use Todo;

As mentioned above, `Todo.pm` is the main library for our
application. This line finds and loads this library. And that only
succeeds because we've added `lib` to the library search path with the
FindBin magic above.

    Todo->to_app;

All of that was just preamble which allows us to run the important
line. As we will see later, a PSGI application needs to be a reference
to a subroutine. And Dancer gives our application class a method
called `to_app` which returns the required reference. The PSGI
environment (usually `plackup` while we're developing the app)
arranges to call that subroutine with the correct parameters.

So there's not much there. Surely the main library code must be a lot
more complex. Well, not really. Here is `Todo.pm`.

    package Todo;
    use Dancer2;

    our $VERSION = '0.1';

    get '/' => sub {
        template 'index';
    };

    true;

