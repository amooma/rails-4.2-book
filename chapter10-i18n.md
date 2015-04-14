Internationalization
====================

Introduction
------------

If you are in the lucky situation of only creating web pages in English,
then you can skip this chapter completely. For you, everything is set up
correctly by default. But even if you want to create a web page that
only uses one language (other than English), you will need to dive into
this chapter. It is not enough to just translate the views. Because
already if you use scaffolding, you will need to take care of the
English and therefore not yet translated validation errors.

The class I18n is responsible for anything to do with translation in the
Rails application. It offers two important methods for this purpose:

-   I18n.translate or I18n.t

    Takes care of inserting previously defined text blocks. These can
    contain variables.

-   I18n.localize or I18n.l

    Takes care of adapting time and date specifications to the local
    format.

With I18n.locale you define the language you want to use in the current
call. In the configuration file `config/application.rb`, the entry
`config.i18n.default_locale` sets the default value for I18n.locale. If
you do not make any changes there, this value is set by default to `:en`
for English.

For special cases such as displaying numbers, currencies and times,
there are special helpers available. For example, if you want to create
a German web page, you can ensure that the number 1000.23 can be
correctly displayed with a decimal comma as "1.000,23" on the German
page and with a decimal point on an English web page as "1,000.23".

Let's create an example application which includes the rails-i18n gem by
Sven Fuchs (<https://github.com/svenfuchs/i18n>). The gem provides a
couple of language files with translations and format info.

    $
      [...]
    $  
    $
    $
      [...]
    $

In the console we can see the different output of a number depending on
the language setting:

    $
    Loading development environment (Rails 4.0.0)
    >>
    => 1000.23
    >>
    => "1.000,23 €"
    >>
    => "$1,000.23"
    >>
    => "1 000,23 €"
    >>
    $

You see it is not just about translating some views.

### I18n.t

I18n
I18n.t
I18n
I18n.translate
With I18n.t you can retrieve previously defined translations. The
translations are saved by default in YAML format in the directory
`config/locales/`. Technically, you do not have to use YAML as format.

In `config/locales/` you can find an example file
`config/locales/en.yml` with the following content:

    en:
      hello: "Hello world"

In the Rails console we can try out how I18n.t works:

    $
    Loading development environment (Rails 4.0.0)
    >>
    => "Hello world"
    >>
    => :en
    >>
    $

Let's first create a `config/locales/de.yml` with the following content:

    de:
      hello: "Hallo Welt"

In the console we can set the system language with `I18n.locale = :de`
to German.

    $
    Loading development environment (Rails 4.0.0)
    >>
    => :en
    >>
    => :de
    >>
    => "Hallo Welt"
    >>

I18n.t looks by default for the entry in the language defined in
`I18n.locale`. It does not matter if you are working with I18n.t or
I18n.translate. Nor does it matter if you are searching for a symbol or
a string:

    >>
    => :en
    >>
    => "Hello world"
    >>
    => "Hello world"
    >>
    => "Hello world"
    >>

If a translation does not exist, you get an error message that says
"`translation missing:`". This also applies if a translation is only
missing in one language (then all other languages will work, but for the
missing translation you will get the error message). In that case, you
can define a default with `:default
      => 'any default value'`:

    >>
    => "translation missing: en.asdfasdfasdf"
    >>
    => "asdfasdfasdf"
    >>
    $

In the YAML structure you can also specify several levels. Please amend
the `config/locale/en.yml` as follows:

    en:
      hello: "Hello world"
      example:
        test: "A test"
      aaa:
        bbb:
          test: "An other test"

You can display the different levels within the string with dots or with
a `:scope` for the symbols. You can also mix both options.

    $
    Loading development environment (Rails 4.0.0)
    >>
    => "A test"
    >>
    => "An other test"
    >>
    => "An other test"
    >>
    => "An other test"
    >>
    $

It is up to you which structure you choose to save your translations in
the YAML files. But the structure described in ? does make some things
easier and that's why we are going to use it for this application as
well.

#### Using I18n.t in the View

In the view, you can use I18n.t as follows:

    <%= t :hello-world %>

    <%= I18n.t :hello-world %>

    <%= I18n.translate :hello-world %>

    <%= I18n.t 'hello-world' %>

    <%= I18n.t 'aaa.bbb.test' %>

    <%= link_to I18n.t('views.destroy'), book, confirm: I18n.t('views.are_you_sure'), method: :delete %>

### Localized Views

I18n
localized views
In Rails, there is a useful option of saving several variations of a
view as "localized views", each of which represents a different
language. This technique is independent of the potential use of I18n.t
in these views. The file name results from the view name, the language
code (for example, `de` for German) and `html.erb` for ERB pages. Each
of these are separated by a dot. So the German variation of the
`index.html.erb` page would get the file name `index.de.html.erb`.

Your views directory could then look like this:

    |-app
    |---views
    |-----products
    |-------_form.html.erb
    |-------_form.de.html.erb
    |-------edit.html.erb
    |-------edit.de.html.erb
    |-------index.html.erb
    |-------index.de.html.erb
    |-------new.html.erb
    |-------new.de.html.erb
    |-------show.html.erb
    |-------show.de.html.erb
    |-------
    |-----page
    |-------index.html.erb
    |-------index.de.html.erb

The language set with `config.i18n.default_locale` is used automatically
if no language was encoded in the file name. In a new and not yet
configured Rails project, this will be English. You can configure it in
the file `config/application.rb`.

A Rails Application in Only One Language: German
------------------------------------------------

I18n
German only
In a Rails application aimed only at German users, it is unfortunately
not enough to just translate all the views into German. The approach is
in many respects similar to a multi-lingual Rails application (see ?).
Correspondingly, there will be a certain amount of repetition. I am
going to show you the steps you need to watch out for by using a simple
application as example.

Let's go through all the changes using the example of this bibliography
application:

    $
      [...]
    $
    $
      [...]
    $
      [...]
    $

To get examples for validation errors, please insert the following
validations in the `app/models/book.rb`:

    class Book < ActiveRecord::Base
      validates :title,
                presence: true,
                uniqueness: true,
                length: { within: 2..255 }

      validates :price,
                presence: true,
                numericality: { greater_than: 0 }
    end

Please search the configuration file `config/application.rb` for the
value `config.i18n.default_locale` and set it to `:de` for German. In
the same context, we then also insert two directories in the line above
for the translations of the models and the views. This directory
structure is not a technical requirement, but makes it easier to keep
track of things if your application becomes big:

    config.i18n.load_path += Dir[Rails.root.join('config', 'locales', 'models', '*', '*.yml').to_s]
    config.i18n.load_path += Dir[Rails.root.join('config', 'locales', 'views', '*', '*.yml').to_s]
    config.i18n.default_locale = :de

You then still need to create the corresponding directories:

    $
    $
    $

Now you need to generate a language configuration file for German or
simply download a ready-made one by Sven Fuchs from his Github
repository at <https://github.com/svenfuchs/rails-i18n>:

    $
    $
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100  4940  100  4940    0     0   4296      0  0:00:01  0:00:01 --:--:--  4862
    $

> **Note**
>
> If you know how Bundler works, you can also insert the line
> `gem 'rails-i18n'` into the file `Gemfile` and then execute
> `bundle install`. This gives you all language files from the
> repository.

In the file `config/locales/de.yml`, you have all required formats and
generic wordings for German that you need for a normal Rails application
(for example, days of the week, currency symbols, etc). Have a look at
it with your favorite editor to get a first impression.

Next, we need to tell Rails that a model 'book' is not called 'book' in
German, but 'Buch'. The same applies to all attributes. So we create the
file `config/locales/models/book/de.yml` with the following structure.
As side effect, we get the methods Model.model\_name.human and
Model.human\_attribute\_name(attribute), with which we can insert the
model and attribute names in the view.

    de:
      activerecord:
        models:
          book: 'Buch'
        attributes:
          book:
            title: 'Titel'
            number_of_pages: 'Seitenanzahl'
            price: 'Preis'

In the file `config/locales/views/book/de.yml` we insert a few values
for the scaffold views:

    de:
      views:
        show: Anzeigen
        edit: Editieren
        destroy: Löschen
        are_you_sure: Sind Sie sicher?
        back: Zurück
        edit: Editieren
        book:
          index:
            title: Bücherliste
            new: Neues Buch
          edit:
            title: Buch editieren
          new:
            title: Neues Buch
          flash_messages:
            book_was_successfully_created: 'Das Buch wurde erfolgreich angelegt.'
            book_was_successfully_updated: 'Das Buch wurde erfolgreich aktualisiert.'

Now we still need to integrate a "few" changes into the views. We use
the I18n.t helper that can also be abbreviated with t in the view.
I18n.t reads out the corresponding item from the YAML file. In the case
of a purely monolingual German application, we could also write the
German text directly into the view, but with this method we can more
easily switch to multilingual use if required. You can find more
information on I18n.t in ?.

`app/views/books/_form.html.erb`

    <%= form_for(@book) do |f| %>
      <% if @book.errors.any? %>
        <div id="error_explanation">
          <h2><%= t 'activerecord.errors.template.header', :model => Book.model_name.human, :count => @book.errors.count %></h2>
          <ul>
          <% @book.errors.full_messages.each do |msg| %>
            <li><%= msg %></li>
          <% end %>
          </ul>
        </div>
      <% end %>

      <div class="field">
        <%= f.label :title %><br>
        <%= f.text_field :title %>
      </div>
      <div class="field">
        <%= f.label :number_of_pages %><br>
        <%= f.number_field :number_of_pages %>
      </div>
      <div class="field">
        <%= f.label :price %><br>
        <%= f.text_field :price %>
      </div>
      <div class="actions">
        <%= f.submit %>
      </div>
    <% end %>

`app/views/books/edit.html.erb`

    <h1><%= t 'views.book.edit.title' %></h1>

    <%= render 'form' %>

    <%= link_to I18n.t('views.show'), @book %> |
    <%= link_to I18n.t('views.back'), books_path %>

`app/views/books/index.html.erb`

    <h1><%= t 'views.book.index.title' %></h1>

    <table>
      <thead>
        <tr>
          <th><%= Book.human_attribute_name(:title) %></th>
          <th><%= Book.human_attribute_name(:number_of_pages) %></th>
          <th><%= Book.human_attribute_name(:price) %></th>
          <th></th>
          <th></th>
          <th></th>
        </tr>
      </thead>

      <tbody>
        <% @books.each do |book| %>
          <tr>
            <td><%= book.title %></td>
            <td><%= number_with_delimiter(book.number_of_pages) %></td>
            <td><%= number_to_currency(book.price) %></td>
            <td><%= link_to I18n.t('views.show'), book %></td>
            <td><%= link_to I18n.t('views.edit'), edit_book_path(book) %></td>
            <td><%= link_to I18n.t('views.destroy'), book, method: :delete, data: { confirm: I18n.t('views.are_you_sure')} %></td>
          </tr>
        <% end %>
      </tbody>
    </table>

    <br>

    <%= link_to I18n.t('views.book.index.new'), new_book_path %>

`app/views/books/new.html.erb`

    <h1><%= t 'views.book.new.title' %></h1>

    <%= render 'form' %>

    <%= link_to I18n.t('views.back'), books_path %>

`app/views/books/show.html.erb`

    <p id="notice"><%= notice %></p>

    <p>
      <strong><%= Book.human_attribute_name(:title) %>:</strong>
      <%= @book.title %>
    </p>

    <p>
      <strong><%= Book.human_attribute_name(:number_of_pages) %>:</strong>
      <%= number_with_delimiter(@book.number_of_pages) %>
    </p>

    <p>
      <strong><%= Book.human_attribute_name(:price) %>:</strong>
      <%= number_to_currency(@book.price) %>
    </p>

    <%= link_to I18n.t('views.edit'), edit_book_path(@book) %> |
    <%= link_to I18n.t('views.back'), books_path %>

> **Note**
>
> In the show and index view, I have integrated the helpers
> number\_with\_delimiter and number\_to\_currency so the numbers are
> represented more attractively for the user.

Right at the end, we still need to adapt a few flash messages in the
controller `app/controllers/books_controller.rb`:

    class BooksController < ApplicationController
      before_action :set_book, only: [:show, :edit, :update, :destroy]

      # GET /books
      # GET /books.json
      def index
        @books = Book.all
      end

      # GET /books/1
      # GET /books/1.json
      def show
      end

      # GET /books/new
      def new
        @book = Book.new
      end

      # GET /books/1/edit
      def edit
      end

      # POST /books
      # POST /books.json
      def create
        @book = Book.new(book_params)

        respond_to do |format|
          if @book.save
            format.html { redirect_to @book, notice: I18n.t('views.book.flash_messages.book_was_successfully_created') }
            format.json { render action: 'show', status: :created, location: @book }
          else
            format.html { render action: 'new' }
            format.json { render json: @book.errors, status: :unprocessable_entity }
          end
        end
      end

      # PATCH/PUT /books/1
      # PATCH/PUT /books/1.json
      def update
        respond_to do |format|
          if @book.update(book_params)
            format.html { redirect_to @book, notice: I18n.t('views.book.flash_messages.book_was_successfully_updated') }
            format.json { head :no_content }
          else
            format.html { render action: 'edit' }
            format.json { render json: @book.errors, status: :unprocessable_entity }
          end
        end
      end

      # DELETE /books/1
      # DELETE /books/1.json
      def destroy
        @book.destroy
        respond_to do |format|
          format.html { redirect_to books_url }
          format.json { head :no_content }
        end
      end

      private
        # Use callbacks to share common setup or constraints between actions.
        def set_book
          @book = Book.find(params[:id])
        end

        # Never trust parameters from the scary internet, only allow the white list through.
        def book_params
          params.require(:book).permit(:title, :number_of_pages, :price)
        end
    end

Now you can use the views generated by the scaffold generator entirely
in German. The structure of the YAML files shown here can of course be
adapted to your own preferences. The texts in the views and the
controller are displayed with I18n.t. At this point you could of course
also integrate the German text directly if the application is purely in
German.

### Paths in German

I18n
paths in German
Our bibliography is completely in German, but the URLs are still in
English. If we want to make all books available at the URL
<http://0.0.0.0:3000/buecher> instead of the URL
<http://0.0.0.0:3000/books> then we need to add the following entry to
the `config/routes.rb`:

    Bibliography::Application.routes.draw do
      resources :books, path: 'buecher', path_names: { new: 'neu', edit: 'editieren' }
    end

As a result, we then have the following new paths:

    $
       Prefix Verb   URI Pattern                      Controller#Action
        books GET    /buecher(.:format)               books#index
              POST   /buecher(.:format)               books#create
     new_book GET    /buecher/neu(.:format)           books#new
    edit_book GET    /buecher/:id/editieren(.:format) books#edit
         book GET    /buecher/:id(.:format)           books#show
              PATCH  /buecher/:id(.:format)           books#update
              PUT    /buecher/:id(.:format)           books#update
              DELETE /buecher/:id(.:format)           books#destroy
    $

The brilliant thing with Rails routes is that you do not need to do
anything else. The rest is managed transparently by the routing engine.

Multilingual Rails Application
------------------------------

The approach for multilingual Rails applications is very similar to the
monoligual, all-German Rails application described in ?. But we need to
define YAML language files for all required languages and tell the Rails
application which language it should currently use. We do this via
`I18n.locale`.

### Using I18n.locale for Defining the Default Language

I18n
I18n.locale
Of course, a Rails application has to know in which language a web page
should be represented. `I18n.locale` saves the current language and can
be read by the application. I am going to show you this with a mini web
shop example:

    $
      [...]
    $
    $

This web shop gets a homepage:

    $
      [...]
    $

We still need to enter it as root page in the `config/routes.rb`:

    Webshop::Application.routes.draw do
      get "page/index"
      root 'page#index'
    end

We populate the `app/views/page/index.html.erb` with the following
example:

    <h1>Example Webshop</h1>
    <p>Welcome to this webshop.</p>

    <p>
    <strong>I18n.locale:</strong>
    <%= I18n.locale %>
    </p>

If we start the Rails server with `rails server` and go to
<http://0.0.0.0:3000/> in the browser, then we see the following web
page:

As you can see, the default is set to "en" for English. Stop the Rails
server with CTRL-C and change the setting for the default language to
German in the file `config/application.rb`:

    config.i18n.default_locale = :de

If you then start the Rails server and again go to
<http://0.0.0.0:3000/> in the web browser, you will see the following
web page:

The web page has not changed, but as output of `<%=
      I18n.locale %>` you now get '`de`' for German (Deutsch), not
'`en`' for English as before.

Please stop the Rails server with CTRL-C and change the setting for the
default language to `en` for English in the file
`config/application.rb`:

    # The default locale is :en and all translations from config/locales/*.rb,yml are auto loaded.
    # config.i18n.load_path += Dir[Rails.root.join('my', 'locales', '*.{rb,yml}').to_s]

    config.i18n.default_locale = :en

We now know how to set the default for `I18n.locale` in the entire
application, but that only gets half the job done. A user wants to be
able to choose his own language. There are various ways of achieving
this. To make things clearer, we need a second page that displays a
German text.

Please create the file `app/views/page/index.de.html.erb` with the
following content:

    <h1>Beispiel Webshop</h1>
    <p>Willkommen in diesem Webshop.</p>

    <p>
    <strong>I18n.locale:</strong>
    <%= I18n.locale %>
    </p>

#### Setting I18n.locale via URL Path Prefix

I18n.locale
path prefix
default\_url\_options
The more stylish way of setting the language is to add it as prefix to
the URL. This enables search engines to manage different language
versions better. We want <http://0.0.0.0:3000/de> to display the German
version of our homepage and <http://0.0.0.0:3000/en> the English
version. The first step is adapting the `config/routes.rb`

    Webshop::Application.routes.draw do
      scope ':locale', locale: /en|de/ do
        get "page/index"
        get '/', to: 'page#index'
      end

      root 'page#index'
    end

Next, we need to set a before\_filter in the
`app/controllers/application_controller.rb`. This filter sets the
parameter locale set by the route as `I18n.locale`:

    class ApplicationController < ActionController::Base
      # Prevent CSRF attacks by raising an exception.
      # For APIs, you may want to use :null_session instead.
      protect_from_forgery with: :exception

      before_filter :set_locale

      private
      def set_locale
        I18n.locale = params[:locale] || I18n.default_locale
      end
    end

To test it, start Rails with `rails server` and go to the URL
<http://0.0.0.0:3000/de>.

Of course we can also go to <http://0.0.0.0:3000/de/page/index>:

If we go to <http://0.0.0.0:3000/en> and
<http://0.0.0.0:3000/en/page/index> we get the English version of each
page.

But now we have a problem: by using the prefix, we initially get to a
page with the correct language, but what if we want to link from that
page to another page in our Rails project? Then we would need to
manually insert the prefix into the link. Who wants that? Obviously
there is a clever solution for this problem. We can set global default
parameters for URL generation by defining a method called
default\_url\_options in our controller.

So we just need to add this method in
`app/controllers/application_controller.rb`:

    class ApplicationController < ActionController::Base
      # Prevent CSRF attacks by raising an exception.
      # For APIs, you may want to use :null_session instead.
      protect_from_forgery with: :exception

      before_filter :set_locale

      def default_url_options
        { locale: I18n.locale }
      end

      private
      def set_locale
        I18n.locale = params[:locale] || I18n.default_locale
      end
    end

As a result, all links created with link\_to and url\_for (on which
link\_to is based) are automatically expanded by the parameter `locale`.
You do not need to do anything else. All links generated via the
scaffold generator are automatically changed accordingly.

##### Navigation Example

To give the user the option of switching easily between the different
language versions, it makes sense to offer two links at the top of the
web page. We don't want the current language to be displayed as active
link. This can be achieved as follows for all views in the file
`app/views/layouts/application.html.erb`:

    <!DOCTYPE html>
    <html>
    <head>
      <title>Webshop</title>
      <%= stylesheet_link_tag    "application", media: "all", "data-turbolinks-track" => true %>
      <%= javascript_include_tag "application", "data-turbolinks-track" => true %>
      <%= csrf_meta_tags %>
    </head>
    <body>

    <p>
    <%= link_to_unless I18n.locale == :en, "English", locale: :en %>
    |
    <%= link_to_unless I18n.locale == :de, "Deutsch", locale: :de %>
    </p>

    <%= yield %>

    </body>
    </html>

The navigation is then displayed at the top of the page.

![](screenshots/I18n_locale_url_prefix_navigation.jpg)

#### Setting I18n.locale via Accept Language HTTP Header of Browser

I18n.locale
accept language HTTP header
When a user goes to your web page for the first time, you ideally want
to immediately display the web page in the correct language for that
user. To do this, you can read out the accept language field in the HTTP
header. In every web browser, the user can set his preferred language
(see <http://www.w3.org/International/questions/qa-lang-priorities>).
The browser automatically informs the web server and consequently Ruby
on Rails of this value.

Please edit the `app/controllers/application_controller.rb` as follows:

    class ApplicationController < ActionController::Base
      # Prevent CSRF attacks by raising an exception.
      # For APIs, you may want to use :null_session instead.
      protect_from_forgery with: :exception

      before_filter :set_locale

      private
      def extract_locale_from_accept_language_header
        http_accept_language = request.env['HTTP_ACCEPT_LANGUAGE'].scan(/^[a-z]{2}/).first
        if ['de', 'en'].include? http_accept_language
          http_accept_language
        else
          'en'
        end
      end

      def set_locale
        I18n.locale = extract_locale_from_accept_language_header || I18n.default_locale
      end
    end

And please do not forget to clean the settings in ? out of the
`config/routes.rb`:

    Webshop::Application.routes.draw do
      get "page/index"
      root 'page#index'
    end

Now you always get the output in the language defined in the web
browser. Please note that
`request.env['HTTP_ACCEPT_LANGUAGE'].scan(/^[a-z]{2}/).first` does not
catch all cases. For example, you should make sure that you support the
specified language in your Rails application in the first place. There
are some ready-made gems that can easily do this job for you. Have a
look at
<https://www.ruby-toolbox.com/categories/i18n#http_accept_language> to
find them.

#### Saving I18n.locale in a Session

I18n.locale
session
Often you want to save the value of `I18n.locale` in a session (see ?).

> **Note**
>
> The approach described here for sessions will of course work just the
> same with cookies. See ?.

To set the value, let's create a controller in our web shop as example:
the controller SetLanguage with the two actions english and german:

    $
      [...]
    $

In the file `app/controllers/set_language_controller.rb` we populate the
two actions as follows:

    class SetLanguageController < ApplicationController
      def english
        I18n.locale = :en
        set_session_and_redirect
      end

      def german
        I18n.locale = :de
        set_session_and_redirect
      end

      private
      def set_session_and_redirect
        session[:locale] = I18n.locale
        redirect_to :back
        rescue ActionController::RedirectBackError
          redirect_to :root
      end
    end

Finally, we also want to adapt the set\_locale methods in the file
`app/controllers/application_controller.rb`:

    class ApplicationController < ActionController::Base
      # Prevent CSRF attacks by raising an exception.
      # For APIs, you may want to use :null_session instead.
      protect_from_forgery with: :exception

      before_filter :set_locale

      private
      def set_locale
        I18n.locale = session[:locale] || I18n.default_locale
        session[:locale] = I18n.locale
      end
    end

After starting Rails with `rails server`, you can now set the language
to German by going to the URL <http://0.0.0.0:3000/set_language/german>
and to English by going to <http://0.0.0.0:3000/set_language/english>.

##### Navigation Example

To give the user the option of switching easily between the different
language versions, it makes sense to offer two links at the top of the
web page. We don't want the current language to be displayed as active
link. This can be achieved as follows for all views in the file
`app/views/layouts/application.html.erb`:

    <!DOCTYPE html>
    <html>
    <head>
      <title>Webshop</title>
      <%= stylesheet_link_tag    "application", media: "all", "data-turbolinks-track" => true %>
      <%= javascript_include_tag "application", "data-turbolinks-track" => true %>
      <%= csrf_meta_tags %>
    </head>
    <body>

    <p>
    <%= link_to_unless I18n.locale == :en, "English", set_language_english_path %>
    |
    <%= link_to_unless I18n.locale == :de, "Deutsch", set_language_german_path %>
    </p>

    <%= yield %>

    </body>
    </html>

The navigation is then displayed at the top of the page.

![](screenshots/I18n_locale_url_prefix_navigation.jpg)

#### Setting I18n.locale via Domain Extension

I18n.locale
domain extension
If you have several domains with the extensions typical for the
corresponding languages, you can of course also use these extensions to
set the language. For example, if a user visits the page
<http://www.example.com> he would see the English version, if he goes to
<http://www.example.de> then the German version would be displayed.

To achieve this, we would need to go into the
`app/controllers/application_controller.rb` and insert a before\_filter
that analyses the accessed domain and sets the `I18n.locale` :

    class ApplicationController < ActionController::Base
      # Prevent CSRF attacks by raising an exception.
      # For APIs, you may want to use :null_session instead.
      protect_from_forgery with: :exception

      before_filter :set_locale

      private
      def set_locale
        case request.host.split('.').last
        when 'de'
          I18n.locale = :de
        when 'com'
          I18n.locale = :en
        else
          I18n.locale = I18n.default_locale
        end
      end
    end

> **Tip**
>
> To test this functionality, you can add the following items on your
> Linux or Mac OS X development system in the file `/etc/hosts`:
>
>     0.0.0.0 www.example.com
>     0.0.0.0 www.example.de
>
> Then you can go to the URL <http://www.example.com:3000> and
> <http://www.example.de:3000> and you will see the corresponding
> language versions.

#### Which Approach is the Best?

I believe that a combination of the approaches described above will lead
to the best result. When I first visit a web page I am happy if I find
that the accept language HTTP header of my browser is read and
implemented correctly. But it is also nice to be able to change the
language later on in the user configuration (in particular for badly
translated pages, English language is often better). And ultimately it
has to be said that a page that is easy to represent is worth a lot for
a search engine, and this also goes for the languages. Rails gives you
the option of easily using all variations and even enables you to
combine them together.

### Multilingual Scaffolds

As an example, we use a mini webshop in which we translate a product
scaffold. The aim is to make the application available in German and
English.

The Rails application:

    $
      [...]
    $
    $
      [...]
    $
      [...]
    $

We define the product model in the `app/models/product.rb`

    class Product < ActiveRecord::Base
      validates :name,
                presence: true,
                uniqueness: true,
                length: { within: 2..255 }

      validates :price,
                presence: true,
                numericality: { greater_than: 0 }
    end

When selecting the language for the user, we use the URL prefix
variation described in ?. We use the following
`app/controllers/application_controller.rb`

    class ApplicationController < ActionController::Base
      # Prevent CSRF attacks by raising an exception.
      # For APIs, you may want to use :null_session instead.
      protect_from_forgery with: :exception

      before_filter :set_locale

      def default_url_options
        { locale: I18n.locale }
      end

      private
      def set_locale
        I18n.locale = params[:locale] || I18n.default_locale
      end
    end

This is the `config/routes.rb`

    Webshop::Application.routes.draw do
      scope ':locale', locale: /en|de/ do
        resources :products
        get '/', to: 'products#index'
      end

      root 'products#index'
    end

Then we insert the links for the navigation in the
`app/views/layouts/application.html.erb`:

    <!DOCTYPE html>
    <html>
    <head>
      <title>Webshop</title>
      <%= stylesheet_link_tag    "application", media: "all", "data-turbolinks-track" => true %>
      <%= javascript_include_tag "application", "data-turbolinks-track" => true %>
      <%= csrf_meta_tags %>
    </head>
    <body>

    <p>
    <%= link_to_unless I18n.locale == :en, "English", locale: :en %>
    |
    <%= link_to_unless I18n.locale == :de, "Deutsch", locale: :de %>
    </p>

    <%= yield %>

    </body>
    </html>

Start the Rails server with `rails
      server.`

    $
    => Booting WEBrick
    => Rails 4.0.0 application starting in development on http://0.0.0.0:3000
    => Run `rails server -h` for more startup options
    => Ctrl-C to shutdown server
    [2013-07-17 22:16:19] INFO  WEBrick 1.3.1
    [2013-07-17 22:16:19] INFO  ruby 2.0.0 (2013-06-27) [x86_64-darwin12.4.0]
    [2013-07-17 22:16:19] INFO  WEBrick::HTTPServer#start: pid=42806 port=3000

If we go to <http://0.0.0.0:3000> we see the normal English page.

If we click the option German, nothing changes on the page, apart from
the language navigation right at the top.

Now we still need to find a way of translating the individual elements
of this page appropriately and as generically as possible.

#### Text Blocks in YAML Format

Now we need to define the individual text blocks for I18n.t. The
corresponding directories still have to be created first:

    $
    $
    $

To make sure that the YAML files created there are indeed read in
automatically, you need to insert the following lines in the file
`config/application.rb`:

    # The default locale is :en and all translations from config/locales/*.rb,yml are auto loaded.
    config.i18n.load_path += Dir[Rails.root.join('config', 'locales', 'models', '*', '*.yml').to_s]
    config.i18n.load_path += Dir[Rails.root.join('config', 'locales', 'views', '*', '*.yml').to_s]
    config.i18n.default_locale = :en

##### German

Please create the file `config/locales/models/product/de.yml` with the
following content.

    de:
      activerecord:
        models:
          product: 'Produkt'
        attributes:
          product:
            name: 'Name'
            description: 'Beschreibung'
            price: 'Preis'

In the file `config/locales/views/product/de.yml` we insert a few values
for the scaffold views:

    de:
      views:
        show: Anzeigen
        edit: Editieren
        destroy: Löschen
        are_you_sure: Sind Sie sicher?
        back: Zurück
        edit: Editieren
        product:
          index:
            title: Liste aller Produkte
            new_product: Neues Produkt
          edit:
            title: Produkt editieren
          new:
            title: Neues Produkt
          flash_messages:
            product_was_successfully_created: 'Das Produkt wurde erfolgreich angelegt.'
            product_was_successfully_updated: 'Das Produkt wurde erfolgreich aktualisiert.'

Finally, we copy a ready-made default translation by Sven Fuchs from his
github repository <https://github.com/svenfuchs/rails-i18n>:

    $
    $
      % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                     Dload  Upload   Total   Spent    Left  Speed
    100  4940  100  4940    0     0   9574      0 --:--:-- --:--:-- --:--:-- 11932
    $ cd ../..
    $

> **Note**
>
> If you know how Bundler works you can also insert the line
> `gem 'rails-i18n'` into the file `Gemfile` and then execute `bundle
>             install`. This gives you all language files from the
> repository.

The file `config/locales/de.yml` contains all required formats and
generic phrases for German that we need for a normal Rails application
(for example days of the week, currency symbols, etc). Use your favorite
editor to have a look in there to get an impression.

##### English

As most things are already present in the system for English, we just
need to insert a few values for the scaffold views in the file
`config/locales/views/product/en.yml`:

    en:
      views:
        show: Show
        edit: Edit
        destroy: Delete
        are_you_sure: Are you sure?
        back: Back
        edit: Edit
        product:
          index:
            title: List of all products
            new_product: New product
          edit:
            title: Edit Product
          new:
            title: New product
          flash_messages:
            product_was_successfully_created: 'Product was successfully created.'
            product_was_successfully_updated: 'Product was successfully updated.'

#### Equipping Views with I18n.t

Please edit the listed view files as specified.

##### \_form.html.erb

In the file `app/views/products/_form.html.erb` we need to change the
display of the validation errors in the top section to I18n.t. The names
of form errors are automatically read in from
`activerecord.attributes.product`:

    <%= form_for(@product) do |f| %>
      <% if @product.errors.any? %>
        <div id="error_explanation">
          <h2><%= t 'activerecord.errors.template.header', model: Product.model_name.human, count: @product.errors.count %></h2>

          <ul>
          <% @product.errors.full_messages.each do |msg| %>
            <li><%= msg %></li>
          <% end %>
          </ul>
        </div>
      <% end %>

      <div class="field">
        <%= f.label :name %><br>
        <%= f.text_field :name %>
      </div>
      <div class="field">
        <%= f.label :description %><br>
        <%= f.text_field :description %>
      </div>
      <div class="field">
        <%= f.label :price %><br>
        <%= f.text_field :price %>
      </div>
      <div class="actions">
        <%= f.submit %>
      </div>
    <% end %>

##### edit.html.erb

In the file `app/views/products/edit.html.erb` we need to integrate the
heading and the links at the bottom of the page with I18n.t:

    <h1><%= t 'views.product.edit.title' %></h1>

    <%= render 'form' %>

    <%= link_to I18n.t('views.show'), @product %> |
    <%= link_to I18n.t('views.back'), products_path %>

##### index.html.erb

In the file `app/views/products/index.html.erb` we need to change
practically every line. In the table header I use
human\_attribute\_name(), but you could also do it directly with I18n.t.
The price of the product is specified with the helper
number\_to\_currency. In a real application, we would have to specify a
defined currency at this point as well.

    <h1><%= t 'views.product.index.listing_products' %></h1>

    <table>
      <thead>
        <tr>
          <th><%= Product.human_attribute_name(:name) %></th>
          <th><%= Product.human_attribute_name(:description) %></th>
          <th><%= Product.human_attribute_name(:price) %></th>
          <th></th>
          <th></th>
          <th></th>
        </tr>
      </thead>

      <tbody>
        <% @products.each do |product| %>
          <tr>
            <td><%= product.name %></td>
            <td><%= product.description %></td>
            <td><%= number_to_currency(product.price) %></td>
            <td><%= link_to I18n.t('views.show'), product %></td>
            <td><%= link_to I18n.t('views.edit'), edit_product_path(product) %></td>
            <td><%= link_to I18n.t('views.destroy'), product, method: :delete, data: { confirm: I18n.t('views.are_you_sure')} %></td>
          </tr>
        <% end %>
      </tbody>
    </table>

    <br>

    <%= link_to I18n.t('views.product.index.new_product'), new_product_path %>

##### new.html.erb

In the `app/views/products/new.html.erb` we need to adapt the heading
and the link:

    <h1><%= t 'views.product.new.title' %></h1>

    <%= render 'form' %>

    <%= link_to I18n.t('views.back'), products_path %>

##### show.html.erb

In the `app/views/products/show.html.erb` we again use
human\_attribute\_name() for the attributes. Plus the links need to be
translated with I18n.t. As with the index view, we again use
number\_to\_currency() to show the price in formatted form:

    <p id="notice"><%= notice %></p>

    <p>
      <strong><%= Product.human_attribute_name(:name) %>:</strong>
      <%= @product.name %>
    </p>

    <p>
      <strong><%= Product.human_attribute_name(:description) %>:</strong>
      <%= @product.description %>
    </p>

    <p>
      <strong><%= Product.human_attribute_name(:price) %>:</strong>
      <%= number_to_currency(@product.price) %>
    </p>

    <%= link_to I18n.t('views.edit'), edit_product_path(@product) %> |
    <%= link_to I18n.t('views.back'), products_path %>

#### Translating Flash Messages in the Controller

Finally, we need to translate the two flash messages in the
`app/controllers/products_controller.rb` for creating (create) and
updating (update) records, again via I18n.t:

    class ProductsController < ApplicationController
      before_action :set_product, only: [:show, :edit, :update, :destroy]

      # GET /products
      # GET /products.json
      def index
        @products = Product.all
      end

      # GET /products/1
      # GET /products/1.json
      def show
      end

      # GET /products/new
      def new
        @product = Product.new
      end

      # GET /products/1/edit
      def edit
      end

      # POST /products
      # POST /products.json
      def create
        @product = Product.new(product_params)

        respond_to do |format|
          if @product.save
            format.html { redirect_to @product, notice: I18n.t('views.product.flash_messages.product_was_successfully_created') }
            format.json { render action: 'show', status: :created, location: @product }
          else
            format.html { render action: 'new' }
            format.json { render json: @product.errors, status: :unprocessable_entity }
          end
        end
      end

      # PATCH/PUT /products/1
      # PATCH/PUT /products/1.json
      def update
        respond_to do |format|
          if @product.update(product_params)
            format.html { redirect_to @product, notice: I18n.t('views.product.flash_messages.product_was_successfully_updated') }
            format.json { head :no_content }
          else
            format.html { render action: 'edit' }
            format.json { render json: @product.errors, status: :unprocessable_entity }
          end
        end
      end

      # DELETE /products/1
      # DELETE /products/1.json
      def destroy
        @product.destroy
        respond_to do |format|
          format.html { redirect_to products_url }
          format.json { head :no_content }
        end
      end

      private
        # Use callbacks to share common setup or constraints between actions.
        def set_product
          @product = Product.find(params[:id])
        end

        # Never trust parameters from the scary internet, only allow the white list through.
        def product_params
          params.require(:product).permit(:name, :description, :price)
        end
    end

#### The Result

Now you can use the scaffold products both in German and in English. You
can switch the language via the link at the top of the page.

Further Information
-------------------

The best source of information on this topic can be found in the Rails
documentation at <http://guides.rubyonrails.org/i18n.html>. This also
shows how you can operate other backends for defining the translations.

As so often, [Railscasts.com](Railscasts.com) offers a whole range of
Railscasts on the topic I18n:
<http://railscasts.com/episodes?utf8=%E2%9C%93&search=i18n>
