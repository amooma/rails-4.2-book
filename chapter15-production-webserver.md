Web Server in Production Mode
=============================

Introduction
------------

Until now we were working with the development system. Let's have
another close look at the output of that development system:

    $
    => Booting WEBrick
    => Rails 4.0.0 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2013-07-18 10:20:30] INFO  WEBrick 1.3.1
    [2013-07-18 10:20:30] INFO  ruby 2.0.0 (2013-06-27) [x86_64-darwin12.4.0]
    [2013-07-18 10:20:30] INFO  WEBrick::HTTPServer#start: pid=43853 port=3000

The second line tells us that we are in "development" mode and that the
application can be accessed at the URL <http://0.0.0.0:3000>. The web
server used here is WEBrick (see
<http://en.wikipedia.org/wiki/Webrick>). WEBrick is a very simple HTTP
web server and component of the Ruby standard library. But WEBrick is
only suitable for development.

For a production system, you would normally use a standard web server
such as Apache, lighttpd or Nginx, to serve as reverse proxy and load
balancer for the Rails system. The Rails system is then not run by the
slow WEBrick, but by more powerful solutions such as Unicorn
(<http://unicorn.bogomips.org/>), Mongrel
(<http://en.wikipedia.org/wiki/Mongrel_(web_server)>), Thin
(<http://code.macournoyer.com/thin/>) or Puma (<http://puma.io/>).

This chapter walks you through the setup process of a production server
which runs Nginx as a reverse proxy webserver and unicorn as the Ruby on
Rails webserver behind the Nginx. We start with a fresh Debian system
and install all the software we need. The Rails project will be run with
Ruby 2.0.0 which gets installed with RVM and runs for the user deployer.
Feel free to customize the directorystructure once everything is up and
running.

The example Rails application we use is called `blog`.

> **Warning**
>
> If you have never set up a Nginx or Apache webserver by yourself
> before you will get lost somewhere in this chapter. You probably get
> it up and running but without understanding how things work.

Debian 7.1
----------

We build our production web server on a minimal Debian 7.1 system. To
carry out this installation, you need to have root rights on the web
server!

This description assumes that you have a freshly installed Debian
GNU/Linux 7.1 (“Wheeze”). You will find an ISO image for the
installation at <http://www.debian.org>. I recommend the approximately
250 MB net installation CD image. For instructions on how to install
Debian-GNU/Linux, please go to <http://www.debian.org/distrib/netinst>.

> **Note**
>
> VMware or any other virtual PC system is a nice playground to get a
> feeling how this works.

### Buildsystem

First, we install a few debian packages we are going to need.

    root@debian:~#
    [...]
    root@debian:~#

### Node.js

To make the most of the asset pipeline, we install Node.js. Please go to
the homepage of Node.js (<http://nodejs.org/>), search for the current
stable release and adapt the version numbers in the commands listed here
accordingly.

    root@debian:~#
    root@debian:/usr/src#
    [...]
    root@debian:/usr/src#  
    root@debian:/usr/src#
    root@debian:/usr/src/node-v0.10.13#  
    [...]
    root@debian:/usr/src/node-v0.10.13#
    [...]
    root@debian:/usr/src/node-v0.10.13#
    [...]
    root@debian:/usr/src/node-v0.10.13#
    [...]
    root@debian:~#

### nginx

Nginx will be our web server to the outside world.

    root@debian:~#
    [...]
    root@debian:~#

### User Deployer

Our Rails project is going to run within a Ruby and Rails installed via
RVM in the user space. So we create a new user with the name `deployer`:

    root@debian:~#
    Lege Benutzer »deployer« an ...
    Lege neue Gruppe »deployer« (1002) an ...
    Lege neuen Benutzer »deployer« (1002) mit Gruppe »deployer« an ...
    Erstelle Home-Verzeichnis »/home/deployer« ...
    Kopiere Dateien aus »/etc/skel« ...
    Geben Sie ein neues UNIX-Passwort ein:
    Geben Sie das neue UNIX-Passwort erneut ein:
    passwd: Passwort erfolgreich geändert
    Benutzerinformationen für deployer werden geändert.
    Geben Sie einen neuen Wert an oder drücken Sie ENTER für den Standardwert
     Vollständiger Name []: Deployer
     Raumnummer []:
     Telefon geschäftlich []:
     Telefon privat []:
     Sonstiges []:
    Sind die Informationen korrekt? [J/n] J
    root@debian:~#

#### Setting up Rails Environment for User Deployer

With `su - deployer` we'll become the user deployer:

    root@debian:~#
    deployer@debian:~$

As user `deployer`, please carry out the steps for installing Ruby 2.0.0
and Rails 4.0 via RVM.

    deployer@debian:~$
    [...]
    deployer@debian:~$
    [...]
    deployer@debian:~$

To be able to start Unicorn with the RVM environment from within an
Init.d script, we now need to generate a corresponding wrapper:

    deployer@debian:~$
    [...]
    deployer@debian:~$
    deployer@debian:~$
    root@debian:~$

### Database

Usually, you want to use a "big" database in a production system, such
as PostgreSQL or MySQL. So here is how to install a MySQL database on
this system and what you need to adapt in the Rails project.

#### MySQL Installation

Next, we install the database MySQL. You will be asked for a database
password. Please remember this password. Later, `root` can use it to log
in to the database.

    root@debian:~#
    [...]
    root@debian:~#

#### Creating Database with Rights

In the MySQL database, we need to create the database `blog` with access
rights for the user `deployer`:

    deployer@debian:~$
    Enter password:
    Welcome to the MySQL monitor.  Commands end with ; or \g.
    Your MySQL connection id is 40
    Server version: 5.1.63-0+squeeze1 (Debian)

    Copyright (c) 2000, 2011, Oracle and/or its affiliates. All rights reserved.

    Oracle is a registered trademark of Oracle Corporation and/or its
    affiliates. Other names may be trademarks of their respective
    owners.

    Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

    mysql>
    Query OK, 1 row affected (0.00 sec)

    mysql>
    Query OK, 0 rows affected (0.00 sec)

    mysql>
    Query OK, 0 rows affected (0.00 sec)

    mysql> ;
    Bye
    deployer@debian:~$

> **Warning**
>
> Please DO CHANGE THE PASSWORD! Otherwise it will be the same password
> for everybody who read this book.

### Memcache

If you are working with a cache server (highly recommended), you of
course have to install the appropriate software. For memcached
(<http://memcached.org/>) you would enter this:

    root@debian:~#
    [...]
    root@debian:~#

Setting Up a New Rails Project
------------------------------

To keep this guide as simple as possible, we create a simple blog in the
homedirectory of the user `deployer`.

    root@debian:~#
    deployer@debian:~$
    [...]
    deployer@debian:~$
    deployer@debian:~/blog$
    [...]
    deployer@debian:~/blog$

### Adapting Gemfile

Please add the following content into the file `Gemfile`:

    gem 'mysql', group: :production
    gem 'unicorn', group: :production

Then install all gems with `bundle
      install`:

    deployer@debian:~/blog$
    [...]
    deployer@debian:~/blog$

To get a root URL we'll change to `config/routes.rb` file to this:

    Blog::Application.routes.draw do
      resources :posts
      root 'posts#index'
    end

### Production Database Configuration

In the file` config/database.yml` you need to enter the database
configuration for the MySQL database for the production system. Please
make sure you enter the correct password.

    # SQLite version 3.x
    #   gem install sqlite3
    #
    #   Ensure the SQLite 3 gem is defined in your Gemfile
    #   gem 'sqlite3'
    development:
      adapter: sqlite3
      database: db/development.sqlite3
      pool: 5
      timeout: 5000

    # Warning: The database defined as "test" will be erased and
    # re-generated from your development database when you run "rake".
    # Do not set this db to the same as development or production.
    test:
      adapter: sqlite3
      database: db/test.sqlite3
      pool: 5
      timeout: 5000

> **Warning**
>
> Again: Please change the password!

### Unicorn Configuration

For the Unicorn configuration, we use the file
<https://raw.github.com/defunkt/unicorn/master/examples/unicorn.conf.rb>
as basis and save it as follows in the file `config/unicorn.rb` after we
adapt it to our server:

    # Sample verbose configuration file for Unicorn (not Rack)
    #
    # This configuration file documents many features of Unicorn
    # that may not be needed for some applications. See
    # http://unicorn.bogomips.org/examples/unicorn.conf.minimal.rb
    # for a much simpler configuration file.
    #
    # See http://unicorn.bogomips.org/Unicorn/Configurator.html for complete
    # documentation.

    # Use at least one worker per core if you're on a dedicated server,
    # more will usually help for _short_ waits on databases/caches.
    worker_processes 4

    # Since Unicorn is never exposed to outside clients, it does not need to
    # run on the standard HTTP port (80), there is no reason to start Unicorn
    # as root unless it's from system init scripts.
    # If running the master process as root and the workers as an unprivileged
    # user, do this to switch euid/egid in the workers (also chowns logs):
    user "deployer", "www-data"

    # Help ensure your application will always spawn in the symlinked
    # "current" directory that Capistrano sets up.
    APP_PATH = "/home/deployer/blog"
    working_directory APP_PATH # available in 0.94.0+

    # listen on both a Unix domain socket and a TCP port,
    # we use a shorter backlog for quicker failover when busy
    listen "/tmp/.unicorn_blog.sock", :backlog => 64
    listen 8080, :tcp_nopush => true

    # nuke workers after 30 seconds instead of 60 seconds (the default)
    timeout 30

    # feel free to point this anywhere accessible on the filesystem
    pid "/var/run/unicorn_blog.pid"

    # By default, the Unicorn logger will write to stderr.
    # Additionally, ome applications/frameworks log to stderr or stdout,
    # so prevent them from going to /dev/null when daemonized here:
    stderr_path APP_PATH + "/log/unicorn_blog.stderr.log"
    stdout_path APP_PATH + "/log/unicorn_blog.stdout.log"

    # combine Ruby 2.0.0dev or REE with "preload_app true" for memory savings
    # http://rubyenterpriseedition.com/faq.html#adapt_apps_for_cow
    preload_app true
    GC.respond_to?(:copy_on_write_friendly=) and
      GC.copy_on_write_friendly = true

    # Enable this flag to have unicorn test client connections by writing the
    # beginning of the HTTP headers before calling the application.  This
    # prevents calling the application for connections that have disconnected
    # while queued.  This is only guaranteed to detect clients on the same
    # host unicorn runs on, and unlikely to detect disconnects even on a
    # fast LAN.
    check_client_connection false

    before_fork do |server, worker|
      # the following is highly recomended for Rails + "preload_app true"
      # as there's no need for the master process to hold a connection
      defined?(ActiveRecord::Base) and
        ActiveRecord::Base.connection.disconnect!

      # The following is only recommended for memory/DB-constrained
      # installations.  It is not needed if your system can house
      # twice as many worker_processes as you have configured.
      #
      # # This allows a new master process to incrementally
      # # phase out the old master process with SIGTTOU to avoid a
      # # thundering herd (especially in the "preload_app false" case)
      # # when doing a transparent upgrade.  The last worker spawned
      # # will then kill off the old master process with a SIGQUIT.
      # old_pid = "#{server.config[:pid]}.oldbin"
      # if old_pid != server.pid
      #   begin
      #     sig = (worker.nr + 1) >= server.worker_processes ? :QUIT : :TTOU
      #     Process.kill(sig, File.read(old_pid).to_i)
      #   rescue Errno::ENOENT, Errno::ESRCH
      #   end
      # end
      #
      # Throttle the master from forking too quickly by sleeping.  Due
      # to the implementation of standard Unix signal handlers, this
      # helps (but does not completely) prevent identical, repeated signals
      # from being lost when the receiving process is busy.
      # sleep 1
    end

    after_fork do |server, worker|
      # per-process listener ports for debugging/admin/migrations
      # addr = "127.0.0.1:#{9293 + worker.nr}"
      # server.listen(addr, :tries => -1, :delay => 5, :tcp_nopush => true)

      # the following is *required* for Rails + "preload_app true",
      defined?(ActiveRecord::Base) and
        ActiveRecord::Base.establish_connection

      # if preload_app is true, then you may also want to check and
      # restart any other shared sockets/descriptors such as Memcached,
      # and Redis.  TokyoCabinet file handles are safe to reuse
      # between any number of forked children (assuming your kernel
      # correctly implements pread()/pwrite() system calls)
    end

### rake db:migration

We still need to create the database:

    deployer@debian:~/blog$
    [...]
    deployer@debian:~/blog$

> **Important**
>
> Please ensure that the `rake db:migrate` concludes with a
> `RAILS_ENV=production`. This is to migrate the production database.

### rake assets:precompile

`rake assets:precompile` ensures that all assets in the asset pipeline
are made available for the production environment (see ?).

    deployer@debian:~/blog$
    [...]
    deployer@debian:~/blog$

### Unicorn Init Script

Now you need to continue working as user `root`:

    deployer@debian:~$
    Abgemeldet
    root@debian:~#

Create the init script `/etc/init.d/unicorn_blog` with the following
content:

    #!/bin/bash

    ### BEGIN INIT INFO
    # Provides:          unicorn
    # Required-Start:    $remote_fs $syslog
    # Required-Stop:     $remote_fs $syslog
    # Default-Start:     2 3 4 5
    # Default-Stop:      0 1 6
    # Short-Description: Unicorn webserver
    # Description:       Unicorn webserver for the blog
    ### END INIT INFO

    UNICORN=/home/deployer/.rvm/bin/bootup_unicorn
    UNICORN_ARGS="-D -c /home/deployer/blog/config/unicorn.rb -E production"
    KILL=/bin/kill
    PID=/var/run/unicorn_blog.pid

    sig () {
      test -s "$PID" && kill -$1 `cat $PID`
    }

    case "$1" in
            start)
                    echo "Starting unicorn..."
                    $UNICORN $UNICORN_ARGS
                    ;;
            stop)
                    sig QUIT && exit 0
                    echo >&2 "Not running"
                    ;;
            restart)
                    $0 stop
                    $0 start
                    ;;
            status)
                    ;;
            *)
                    echo "Usage: $0 {start|stop|restart|status}"
                    ;;
    esac

You still have to activate the init script and start Unicorn:

    root@debian:~#  
    root@debian:~#
    update-rc.d: using dependency based boot sequencing
    root@debian:~#
    root@debian:~#

Your Rails project is now accessible via the IP address of the web
server.

### nginx Configuration

For the Rails project, we add a new configuration file
`/etc/nginx/sites-available/blog.conf` with the following content:

    upstream unicorn {
      server unix:/tmp/.unicorn_blog.sock fail_timeout=0;
    }

    server {
      listen 80 default deferred;
      # server_name example.com;
      root /home/deployer/blog/public;

      location / {
        gzip_static on;
      }

      location ^~ /assets/ {
        gzip_static on;
        expires max;
        add_header Cache-Control public;
      }

      try_files $uri/index.html $uri @unicorn;
      location @unicorn {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;
        proxy_pass http://unicorn;
      }

      error_page 500 502 503 504 /500.html;
      client_max_body_size 4G;
      keepalive_timeout 10;
    }

We link this configuration file into the /etc/nginx/sites-enabled
directory to have it loaded by Nginx. The default file can be deleted.
After that we restart Nginx and are all set. You can access the Rails
application through the IP address of this server.

    root@debian:~#
    root@debian:~#
    root@debian:~#
    Restarting nginx: nginx.
    root@debian:~#

### Loading Updated Versions of the Rails Project

If you want to activate Updates to the Rails project, you need to copy
them into the directory `/home/deployer/blog` and log in as user
`deployer` to run `rake
      assets:precompile` (see ?).

    deployer@debian:~/blog$
    [...]
    deployer@debian:~/blog$

If you bring in new migrations, you of course also need to do a
`rake db:migrate RAILS_ENV=production`:

    deployer@debian:~/blog$
    [...]
    deployer@debian:~/blog$

Then you need to restart Unicorn as user `root`:

    root@debian:~#
    root@debian:~#

Misc
----

### Alternative Setups

The RVM, unicorn and Nginx way is fast and makes it possible to setup
different Ruby versions on one server. But many admins prefer an easier
installation process which is promised by Phusion Passenger. Have a look
at <https://www.phusionpassenger.com> for more information about
Passenger. It is a very good and reliable solution.

### What Else There Is To Do

Please always consider the following points - every admin has to decide
these for him- or herself and implement them accordingly:

-   Automatic and regular backup of database and Rails project.

-   Set up log rotations of log files.

-   Set up monitoring for system load and hard drive space.

-   Regularly install Debian security updates as soon as they become
    available.

### 404 and Co.

Finally, please look into the `public` directory in your Rails project
and adapt the HTML pages saved there to your own requirements.
Primarily, this is about the design of the pages. In the default
setting, these are somewhat sparse and do not have any relation to the
rest of your website. If you decide to update your web page and shut
down your Unicorn server to do so, nginx will deliver the web page
`public/500.html` in the meantime.

You will find a list of HTTP error codes at
<http://en.wikipedia.org/wiki/List_of_HTTP_status_codes>

### Multiple Rails Servers on One System

You can runs several Rails servers on one system without any problems.
You need to set up a separate Unicorn for each Rails server. You can
then distribute to it from nginx. With nginx you can also define on
which IP address a Rails server is accessible from the outside.

Cloud Platform as Service Provider
----------------------------------

If you do not have a web server available on the internet or want to
deploy to a PaaS (Platform as a Service) system right from the start,
you should have a look at what the various providers have to offer. The
two US market leaders are currently Heroku (<http://www.heroku.com/>)
and Engine Yard (<http://www.engineyard.com/>).

PaaS as platform usually offers less options than your own server. But
you have 7x24 support for this platform if anything does not work
properly.
