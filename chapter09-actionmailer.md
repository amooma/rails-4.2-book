Action Mailer
=============

Even if we mainly use Ruby on Rails to generate web pages, it sometimes
is useful to be able to send an e-mail.

So let's go and build an example with minimal user management for a web
shop that automatically sends an e-mail to the user when a new user is
created:

    $ rails new webshop
      [...]
    $ cd webshop
    $ rails generate scaffold User name email
      [...]
    $ rake db:migrate
      [...]
    $

For the user model we create a minimal validation in the
`app/models/user.rb`, so that we can be sure that each user has a name
and a syntactically correct e-mail address.

    class User < ActiveRecord::Base
      validates :name,
                presence: true

      validates :email,
                presence: true,
                format: { with: /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\Z/i }
    end

There is a generator with the name `mailer` that creates the files
required for mailing. First, we have a look at the output of the
`rails generate mailer`, without passing any further arguments:

    $ rails generate mailer
    Usage:
      rails generate mailer NAME [method method] [options]

    Options:
          [--skip-namespace]        # Skip namespace (affects only isolated applications)
      -e, [--template-engine=NAME]  # Template engine to be invoked
                                    # Default: erb
      -t, [--test-framework=NAME]   # Test framework to be invoked
                                    # Default: test_unit

    Runtime options:
      -f, [--force]    # Overwrite files that already exist
      -p, [--pretend]  # Run but do not make any changes
      -q, [--quiet]    # Suppress status output
      -s, [--skip]     # Skip files that already exist

    Description:
    ============
        Stubs out a new mailer and its views. Passes the mailer name, either
        CamelCased or under_scored, and an optional list of emails as arguments.

        This generates a mailer class in app/mailers and invokes your template
        engine and test framework generators.

    Example:
    ========
        rails generate mailer Notifications signup forgot_password invoice

        creates a Notifications mailer class, views, and test:
            Mailer:     app/mailers/notifications.rb
            Views:      app/views/notifications/signup.text.erb [...]
            Test:       test/mailers/notifications_test.rb

    $

That is just what we are expecting. So let's now create the mailer
notification:

    $ rails generate mailer Notification
          create  app/mailers/notification.rb
          invoke  erb
          create    app/views/notification
          invoke  test_unit
          create    test/mailers/notification_test.rb
    $

In the file `app/mailers/notification.rb` you will find the controller:

    class Notification < ActionMailer::Base
      default from: "from@example.com"
    end

In it, we create a method new\_account(user), with which we send the
confirmation e-mail for a new account:

    class Notification < ActionMailer::Base
      default from: "from@example.com"

      def new_account(user)
        @user = user
        mail(to: user.email,
             subject: "The account #{user.name} is active.")
      end
    end

Now we create the view for this method. The file name
`app/views/notification/new_account.text.erb` is composed from the
method name and the ending `text.erb`.

    Hello <%= @user.name %>,

    your new account is active.

    Have a great day!
      A Robot

As we want to send this e-mail afer the create of a User, we still need
add an after\_create callback which triggers the delivery. That is done
in `app/models/user.rb`:

    class User < ActiveRecord::Base
      validates :name,
                presence: true

      validates :email,
                presence: true,
                format: { with: /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\Z/i }

      after_create :send_welcome_email

      private

      def send_welcome_email
        Notification.new_account(self).deliver
      end
    end

Let's create a new User in the console:

    $ rails console
    Loading development environment (Rails 4.0.0)
    >> User.create(name: 'Wintermeyer', email: 'stefan.wintermeyer@amooma.de')
       (0.1ms)  begin transaction
      SQL (4.1ms)  INSERT INTO "users" ("created_at", "email", "name", "updated_at") VALUES (?, ?, ?, ?)  [["created_at", Wed, 17 Jul 2013 17:12:19 UTC +00:00], ["email", "stefan.wintermeyer@amooma.de"], ["name", "Wintermeyer"], ["updated_at", Wed, 17 Jul 2013 17:12:19 UTC +00:00]]
      Rendered notification/new_account.text.erb (0.8ms)

    Sent mail to stefan.wintermeyer@amooma.de (11.1ms)
    Date: Wed, 17 Jul 2013 19:12:20 +0200
    From: from@example.com
    To: stefan.wintermeyer@amooma.de
    Message-ID: <51e6d07411000_a49d3fd87c8606d457513@SW.local.mail>
    Subject: The account Wintermeyer is active.
    Mime-Version: 1.0
    Content-Type: text/plain;
     charset=UTF-8
    Content-Transfer-Encoding: 7bit

    Hello Wintermeyer,

    your new account is active.

    Have a great day!
      A Robot

       (2.9ms)  commit transaction
    => #<User id: 1, name: "Wintermeyer", email: "stefan.wintermeyer@amooma.de", created_at: "2013-07-17 17:12:19", updated_at: "2013-07-17 17:12:19">
    >> exit
    $

That was straightforward. In the development mode we see the e-mail in
the log. In production mode it would be send to the configured SMTP
gateway.

Configuring the E-Mail Server
-----------------------------

Rails can use a local sendmail or an external SMTP server for delivering
the e-mails.

### Sending via Local Sendmail

sendmail
If you want to send the e-mails in the traditional way via local
`sendmail`, then you need to insert the following lines into your
configuration file `config/environments/development.rb` (for the
development environment) or `config/environments/production.rb` (for
your production environment):

    config.action_mailer.delivery_method = :sendmail
    config.action_mailer.perform_deliveries = true
    config.action_mailer.raise_delivery_errors = true

### Sending via Direct SMTP

SMTP
If you want to send the e-mail directly via a SMTP server (for example
Google Mail), then you need to insert the following lines into your
configuration file `config/environments/development.rb` (for the
development environment) or `config/environments/production.rb` (for
your production environment):

    config.action_mailer.delivery_method = :smtp
    config.action_mailer.smtp_settings = {
      address:              "smtp.gmail.com",
      port:                 587,
      domain:               'example.com',
      user_name:            '<username>',
      password:             '<password>',
      authentication:       'plain',
      enable_starttls_auto: true  }

Of course you need to adapt the values for `:domain`, `:user_name` and
`:password` in accordance with your configuration.

Custom X-Header
---------------

e-mail
X-header
If you feel the urge to integrate an additional X-header then this is no
problem. Here is an example for expanding the file
`app/mailers/notification.rb`:

    class Notification < ActionMailer::Base
      default from: "from@example.com"

      def new_account(user)
        @user = user
        headers["X-Priority"] = '3'
        mail(to: user.email,
             subject: "The new account #{user.name} is active.")
      end
    end

This means the sent e-mail would look like this:

    Sent mail to stefan.wintermeyer@amooma.de (50ms)
    Date: Wed, 30 May 2012 17:35:21 +0200
    From: from@example.com
    To: stefan.wintermeyer@amooma.de
    Message-ID: <4fc63e39e356a_aa083fe366028cd8803c7@MacBook.local.mail>
    Subject: The new account Wintermeyer is active.
    Mime-Version: 1.0
    Content-Type: text/plain;
     charset=UTF-8
    Content-Transfer-Encoding: 7bit
    X-Priority: 3

    Hello Wintermeyer,

    your new account is active.

    Have a great day!
      A Robot

HTML E-Mails
------------

e-mail
HTML
Creating HTML e-mails is really easy. Please create the file
`app/views/user_mailer/new_account_email.html.erb` with the following
content:

    <!DOCTYPE html>
    <html>
      <head>
        <meta content="text/html; charset=UTF-8" http-equiv="Content-Type" />
      </head>
      <body>
        <p>Hello <%= @user.name %>,</p>

        <p>your new account is active.</p>

        <p><i>Have a great day!</i></p>
        <p>A Robot</p>
      </body>
    </html>

If you now create a user, the log file `log/development.log` will show
you what was rendered and the e-mail that was sent:

      Rendered notification/new_account.html.erb (2.0ms)
      Rendered notification/new_account.text.erb (0.4ms)

    Sent mail to stefan.wintermeyer@amooma.de (69ms)
    Date: Wed, 30 May 2012 17:46:49 +0200
    From: from@example.com
    To: stefan.wintermeyer@amooma.de
    Message-ID: <4fc640e930bff_aa173fd61d828ce0501e4@MacBook.local.mail>
    Subject: The new account Wintermeyer is active.
    Mime-Version: 1.0
    Content-Type: multipart/alternative;
     boundary="--==_mimepart_4fc640e921794_aa173fd61d828ce04987d";
     charset=UTF-8
    Content-Transfer-Encoding: 7bit



    ----==_mimepart_4fc640e921794_aa173fd61d828ce04987d
    Date: Wed, 30 May 2012 17:46:49 +0200
    Mime-Version: 1.0
    Content-Type: text/plain;
     charset=UTF-8
    Content-Transfer-Encoding: 7bit
    Content-ID: <4fc640e926312_aa173fd61d828ce04998e@MacBook.local.mail>

    Hello Wintermeyer,

    your new account is active.

    Have a great day!
      A Robot

    ----==_mimepart_4fc640e921794_aa173fd61d828ce04987d
    Date: Wed, 30 May 2012 17:46:49 +0200
    Mime-Version: 1.0
    Content-Type: text/html;
     charset=UTF-8
    Content-Transfer-Encoding: 7bit
    Content-ID: <4fc640e928055_aa173fd61d828ce0500ea@MacBook.local.mail>

    <!DOCTYPE html>
    <html>
      <head>
        <meta content="text/html; charset=UTF-8" http-equiv="Content-Type" />
      </head>
      <body>
        <p>Hello Wintermeyer,</p>

        <p>your new account is active.</p>

        <p><i>Have a great day!</i></p>
        <p>A Robot</p>
      </body>
    </html>

    ----==_mimepart_4fc640e921794_aa173fd61d828ce04987d--

Rails has automatically generated an e-mail in the MIME format
`multipart/alternative`, as the view was present both in the format
`html.erb` and `text.erb`.

Attachments
-----------

attachments
e-maill
E-mail attachments are also defined in the controller.

As an example we add in `app/mailers/notification.rb` the Rails image
`app/assets/images/rails.png` to an e-mail as attachment:

    class Notification < ActionMailer::Base
      default from: "from@example.com"

      def new_account(user)
        @user = user
        attachments['rails.png'] = File.read("#{Rails.root}/app/assets/images/rails.png")
        mail(to: user.email,
             subject: "The new account #{user.name} is active.")
      end
    end

### Inline Attachments

For *inline attachments* in HTML e-mails, you need to use the method
inline when calling `attachments`. In our example controller
`app/mailers/notification.rb`:

    class Notification < ActionMailer::Base
      default from: "from@example.com"

      def new_account(user)
        @user = user
        attachments.inline['rails.png'] = File.read("#{Rails.root}/app/assets/images/rails.png")
        mail(to: user.email,
             subject: "The new account #{user.name} is active.")
      end
    end

In the HTML e-mail, you can access the hash `attachments[]` via
image\_tag. In our example the
`app/views/notification/new_account.html.erb` would look like this:

    <!DOCTYPE html>
    <html>
      <head>
        <meta content="text/html; charset=UTF-8" http-equiv="Content-Type" />
      </head>
      <body>
        <%= image_tag attachments['rails.png'].url, :alt => 'Rails Logo' %>
        <p>Hello <%= @user.name %>,</p>

        <p>your new account is active.</p>

        <p><i>Have a great day!</i></p>
        <p>A Robot</p>
      </body>
    </html>

Further Information
-------------------

The Rails online documentation has a very extensive entry on
ActionMailer at
<http://guides.rubyonrails.org/action_mailer_basics.html>.

Ryan Bates offers an excellent screencast about this topic at
<http://railscasts.com/episodes/206-action-mailer-in-rails-3>.
