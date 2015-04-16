ActiveRecord
============

`ActiveRecord` is a level of abstraction that offers access to a SQL database. 
`ActiveRecord` implements the architectural pattern *Active Record*.

> **Note**
>
> This is referred to as *object-relational-mapping*, *ORM*. I find it rather dry
> and boring, but in case you have trouble going to sleep tonight, have
> a look at <http://en.wikipedia.org/wiki/Object_relational_mapping>.

One of the recipes for the success of Rails is surely the fact that is
uses `ActiveRecord`. The programming and use “feels Ruby like” and it is
much less susceptible to errors than pure SQL. When working with this
chapter, it helps if you have some knowledge of SQL, but this is not
required and also not essential for working with `ActiveRecord`.

Just as an aside, let me mention that you are not obliged to work with
ActiveRecord when working with Rails. You can also use other ORMs.
ActiveRecord is the default and is used by the majority of all Rails
developers.

Howto for this Chapter
----------------------

This chapter is a self-contained unit. But the knowledge provided in 
[Chapter 2, Ruby basics](chapter02-ruby-basics.html) and [Chapter 3, First steps with rails](chapter03-first-steps-with-rails.html) is required. 
Without these basics, you will not have any fun with this chapter!

Rails newbies should read this chapter once from beginning to end.
Please take your time. This chapter is important!

> **Note**
>
> This chapter is only about ActiveRecord. So I am not going to
> integrate any tests (see [Chapter 7, Tests](chapter07-test-driven-development.html)), to keep the examples as simple as
> possible.

### Not in the Mood for SQL?

Occasionally, I will discuss SQL code created by ActiveRecord methods.
If you are not interested in SQL: just read over it and don't worry. The
beauty of ActiveRecord is that you do not need to think about it. For
everyone else, my comments provide better understanding of the
optimization processes going on in the background.

Creating Database/“Model”
-------------------------

> **Note**
>
> Model in this context refers to the data model of Model-View-Controller (MVC).

As a first example, let's take a list of countries in Europe. First, we
create a new Rails project:

```bash
$ rails new europe
  [...]
$ cd europe
$
```

Next, let's have a look at the help page for `rails generate model`:

```bash
$ rails generate model
Usage:
  rails generate model NAME [field[:type][:index] field[:type][:index]] [options]

Options:
      [--skip-namespace], [--no-skip-namespace]  # Skip namespace (affects only isolated applications)
      [--force-plural], [--no-force-plural]      # Forces the use of the given model name
  -o, --orm=NAME                                 # Orm to be invoked
                                                 # Default: active_record

ActiveRecord options:
      [--migration], [--no-migration]    # Indicates when to generate migration
                                         # Default: true
      [--timestamps], [--no-timestamps]  # Indicates when to generate timestamps
                                         # Default: true
      [--parent=PARENT]                  # The parent class for the generated model
      [--indexes], [--no-indexes]        # Add indexes for references and belongs_to columns
                                         # Default: true
  -t, [--test-framework=NAME]            # Test framework to be invoked
                                         # Default: test_unit

TestUnit options:
      [--fixture], [--no-fixture]   # Indicates when to generate fixture
                                    # Default: true
  -r, [--fixture-replacement=NAME]  # Fixture replacement to be invoked

Runtime options:
  -f, [--force]                    # Overwrite files that already exist
  -p, [--pretend], [--no-pretend]  # Run but do not make any changes
  -q, [--quiet], [--no-quiet]      # Suppress status output
  -s, [--skip], [--no-skip]        # Skip files that already exist

Description:
    Stubs out a new model. Pass the model name, either CamelCased or
    under_scored, and an optional list of attribute pairs as arguments.

    Attribute pairs are field:type arguments specifying the
    model's attributes. Timestamps are added by default, so you don't have to
    specify them by hand as 'created_at:datetime updated_at:datetime'.

    As a special case, specifying 'password:digest' will generate a
    password_digest field of string type, and configure your generated model and
    tests for use with ActiveModel has_secure_password (assuming the default ORM
    and test framework are being used).

    You don't have to think up every attribute up front, but it helps to
    sketch out a few so you can start working with the model immediately.

    This generator invokes your configured ORM and test framework, which
    defaults to ActiveRecord and TestUnit.

    Finally, if --parent option is given, it's used as superclass of the
    created model. This allows you create Single Table Inheritance models.

    If you pass a namespaced model name (e.g. admin/account or Admin::Account)
    then the generator will create a module with a table_name_prefix method
    to prefix the model's table name with the module name (e.g. admin_accounts)

Available field types:

    Just after the field name you can specify a type like text or boolean.
    It will generate the column with the associated SQL type. For instance:

        `rails generate model post title:string body:text`

    will generate a title column with a varchar type and a body column with a text
    type. If no type is specified the string type will be used by default.
    You can use the following types:

        integer
        primary_key
        decimal
        float
        boolean
        binary
        string
        text
        date
        time
        datetime

    You can also consider `references` as a kind of type. For instance, if you run:

        `rails generate model photo title:string album:references`

    It will generate an `album_id` column. You should generate these kinds of fields when
    you will use a `belongs_to` association, for instance. `references` also supports
    polymorphism, you can enable polymorphism like this:

        `rails generate model product supplier:references{polymorphic}`

    For integer, string, text and binary fields, an integer in curly braces will
    be set as the limit:

        `rails generate model user pseudo:string{30}`

    For decimal, two integers separated by a comma in curly braces will be used
    for precision and scale:

        `rails generate model product 'price:decimal{10,2}'`

    You can add a `:uniq` or `:index` suffix for unique or standard indexes
    respectively:

        `rails generate model user pseudo:string:uniq`
        `rails generate model user pseudo:string:index`

    You can combine any single curly brace option with the index options:

        `rails generate model user username:string{30}:uniq`
        `rails generate model product supplier:references{polymorphic}:index`

    If you require a `password_digest` string column for use with
    has_secure_password, you should specify `password:digest`:

        `rails generate model user password:digest`

Examples:
    `rails generate model account`

        For ActiveRecord and TestUnit it creates:

            Model:      app/models/account.rb
            Test:       test/models/account_test.rb
            Fixtures:   test/fixtures/accounts.yml
            Migration:  db/migrate/XXX_create_accounts.rb

    `rails generate model post title:string body:text published:boolean`

        Creates a Post model with a string title, text body, and published flag.

    `rails generate model admin/account`

        For ActiveRecord and TestUnit it creates:

            Module:     app/models/admin.rb
            Model:      app/models/admin/account.rb
            Test:       test/models/admin/account_test.rb
            Fixtures:   test/fixtures/admin/accounts.yml
            Migration:  db/migrate/XXX_create_admin_accounts.rb
```

The usage description `rails generate model NAME
  [field[:type][:index] field[:type][:index]] [options]` tells us that
after `rails generate model` comes the name of the model and then the
table fields. If you do not put `:type` after a table field name, it is
assumed to be a string by default. Let's create the *model* `country`:

```bash
$ rails generate model Country name population:integer
      invoke  active_record
      create    db/migrate/20150415194714_create_countries.rb
      create    app/models/country.rb
      invoke    test_unit
      create      test/models/country_test.rb
      create      test/fixtures/countries.yml
      $
```

The generator has created a database migration file with the name
`db/migrate/20150415194714_create_countries.rb`. It provides the
following code:

```ruby
class CreateCountries < ActiveRecord::Migration
  def change
    create_table :countries do |t|
      t.string :name
      t.integer :population

      t.timestamps null: false
    end
  end
end
```

A migration contains database changes. In this migration, a class
CreateCountries is defined as a child of ActiveRecord::Migration. The
class method change is used to define a migration and the associated
roll-back.

With `rake db:migrate` we can apply the migrations, in other words,
create the corresponding database table:

```bash
$ rake db:migrate
== 20150415194714 CreateCountries: migrating ==================================
-- create_table(:countries)
   -> 0.0013s
== 20150415194714 CreateCountries: migrated (0.0014s) =========================

$
```

> **Note**
>
> You will find more details on migrations in [the section Migrations](#migrations).

Let's have a look at the file `app/models/country.rb`:

```ruby
class Country < ActiveRecord::Base
end
```

Hmmm … the class Country is a child of ActiveRecord::Base. Makes sense,
as we are discussing ActiveRecord in this chapter. ;-)

### The Attributes id, created\_at and updated\_at

Even if you cannot see it in the migration, we also get the attributes
`id`, `created_at` und `updated_at` by default for each ActiveRecord model.
In the Rails console, we can output the attributes of the class `Country`
by entering the class name:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Country
=> Country(id: integer, name: string, population: integer, created_at: datetime, updated_at: datetime)
>> exit
$
```

The attribute created\_at stores the time when the record was initially
created. updated\_at shows the time of the last update for this record.

id is used a central identification of the record (primary key). The id
is automatically incremented by 1 for each record.

### Getters and Setters

To read and write values of a SQL table row you can use by ActiveRecord
provided getters and setters [the section called "Getters and Setters"](#getters-and-setters). 
These attr\_accessors are automatically created. The getter of the field 
`updated_at` for a given `Country` with the name `germany` would 
be `germany.updated_at`.

### Possible Data Types in ActiveRecord

ActiveRecord is a *layer* between Ruby and various relational databases.
Unfortunately, many SQL databases have different perspectives regarding
the definition of columns and their content. But you do not need to
worry about this, because ActiveRecord solves this problem transparently
for you.

-   Advantage:

    We can replace the database behind a Rails application without
    having to touch the program code.

-   Disadvantage:

    We cannot use all the features of the database concerned. We have to
    use the least common denominator, so to speak.

To generate a *model*, you can use the following field types:

-   `binary`

    This is a BLOB (*Binary Large Object*) in the classical sense. Never
    heard of it? Then you probably won't need it.

    See also <http://en.wikipedia.org/wiki/Binary_large_object>

-   `boolean`

    A Boolean value. Can be either `true` or `false`.

    See also <http://en.wikipedia.org/wiki/Boolean_data_type>

-   `date`

    You can store a date here.

-   `datetime`

    Here you can store a date including a time.

-   `float`

    For storing a floating point number.

    See also <http://en.wikipedia.org/wiki/Floating_point>

-   `integer`

    For storing an integer.

    See also <http://en.wikipedia.org/wiki/Integer_(computer_science)>

-   `decimal`

    For storing a decimal number.

    > **Tip**
    >
    > You can also enter a decimal directly with the model generator.
    > But you need to observe the special syntax. Example for creating a
    > price with a decimal:
    >
    >     $ rails generate model product name 'price:decimal{7,2}'
    >          invoke  active_record
    >          create    db/migrate/20150416120946_create_products.rb
    >          create    app/models/product.rb
    >          invoke    test_unit
    >          create      test/models/product_test.rb
    >          create      test/fixtures/products.yml
    >      $
    >
    > That would generate the following migration
    > (`db/migrate/20121114110808_create_products.rb`):
    >
    >     class CreateProducts < ActiveRecord::Migration
    >       def change
    >         create_table :products do |t|
    >           t.string :name
    >           t.decimal :price, :precision => 7, :scale => 2
    >
    >           t.timestamps
    >         end
    >       end
    >     end

-   `primary_key`

    This is an integer that is automatically incremented by 1 by the
    database for each new entry. This field type is often used as key
    for linking different database tables or *models*.

    See also <http://en.wikipedia.org/wiki/Unique_key>

-   `string`

    A string, in other words a sequence of any characters, up to a
    maximum of 2^8 -1 (= 255) characters.

    See also <http://en.wikipedia.org/wiki/String_(computer_science)>

-   `text`

    Also a string - but considerably bigger. By default, up to 2^16^ (=
    65536) characters can be saved here.

-   `time`

    A time.

-   `timestamp`

    A time with date, filled in automatically by the database.

In [the section "Migrations"](#migrations) we will provide more information on the individual data types and
discuss available options. Don't forget, this is a book for beginners,
so this section just gives a brief overview.

### Naming Conventions (Country vs. country vs. countries)

Rails newbies often find it hard to figure out when to use upper and
lower case, for example, `Country` or `country` (one is a class, the
other one a model). The problem is usually not the class itself, but
purely the spelling or wording. For now, let's just say: it's all very
logical and you will quickly get the hang of it. The important thing is
that you keep using English words, even if you would normally be
programming in another language (see [the section called "Why is it 
all in english?"](chapter03-first-steps-with-rails.html#why-is-it-all-in-english)).

Originally, my plan was to now start philosophizing at great length on
naming conventions. But then I thought: “Jeez, the readers want to get
going and not sit here for ages reading about theory.” So I am now going
to introduce the methods with which you can find out the naming
conventions yourself in the Rails *console*:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> 'country'.classify
=> "Country"
>> 'country'.tableize
=> "countries"
>> 'country'.foreign_key
=> "country_id"
>>
```

ActiveRecord automatically uses the English plural forms. So for the
class Country, it's countries. If you are not sure about a term, you can
also work with the class and method name.

```bash
>> Country.name.tableize
=> "countries"
>> Country.name.foreign_key
=> "country_id"
>> exit
$
```

You will find a complete list of the corresponding methods at
<http://rails.rubyonrails.org/classes/ActiveSupport/CoreExtensions/String/Inflections.html>.
But I would recommend that, for now, you just go with the flow. If you
are not sure, you can find out the correct notation with the methods
shown above.

### Database Configuration

Which database is used by default? Let's have a quick look at the
configuration file for the database (`config/database.yml`):

```yml
# SQLite version 3.x
#   gem install sqlite3
#
#   Ensure the SQLite 3 gem is defined in your Gemfile
#   gem 'sqlite3'
#
default: &default
  adapter: sqlite3
  pool: 5
  timeout: 5000

development:
  <<: *default
  database: db/development.sqlite3

# Warning: The database defined as "test" will be erased and
# re-generated from your development database when you run "rake".
# Do not set this db to the same as development or production.
test:
  <<: *default
  database: db/test.sqlite3

production:
  <<: *default
  database: db/production.sqlite3
```

As we are working in `development` mode, Rails has created a new SQLite3
database `db/development.sqlite3` as a result of `rake db:migrate` and
saved all data there.

Fans of command line clients can use `sqlite3` for viewing this
database:

```bash
$ sqlite3 db/development.sqlite3
SQLite version 3.8.5 2014-08-15 22:37:57
Enter ".help" for usage hints.
sqlite> .tables
countries          schema_migrations
sqlite> .schema countries
CREATE TABLE "countries" ("id" INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL, "name" varchar, 
"population" integer, "created_at" datetime NOT NULL, "updated_at" datetime NOT NULL);
sqlite> .exit
$
```

Adding Records
--------------

Actually, I would like to show you first how to view records, but there
we have another chicken and egg problem. So first, here is how you can
create a new record with `ActiveRecord`.

### create

The most frequently used method for creating a new record is `create`.
Let's try creating a country in the console with the command
`Country.create(name: 'Germany', population: 81831000)`

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Country.create(name: 'Germany', population: 81831000)
   (0.3ms)  begin transaction
  SQL (1.3ms)  INSERT INTO "countries" ("name", "population",
  "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["name", "Germany"], 
  ["population", 81831000], ["created_at", "2015-04-16 13:32:37.748459"], 
  ["updated_at", "2015-04-16 13:32:37.748459"]]
   (0.7ms)  commit transaction
=> #<Country id: 1, name: "Germany", population: 81831000, 
created_at: "2015-04-16 13:32:37", updated_at: "2015-04-16 13:32:37">
>> exit
$
```

ActiveRecord saves the new record and outputs the executed SQL command
in the development environment. But to make absolutely sure it works,
let's have a quick look with the command line client `sqlite3`:

```bash
$ sqlite3 db/development.sqlite3
SQLite version 3.8.5 2014-08-15 22:37:57
Enter ".help" for usage hints.
sqlite> SELECT * FROM countries;
1|Germany|81831000|2015-04-16 13:32:37.748459|2015-04-16 13:32:37.748459
sqlite> .exit
$
```

#### Syntax

The method `create` can handle a number of different syntax constructs. If
you want to create a single record, you can do this with or without
{}-brackets within the the ()-brackets:

-   `Country.create(name: 'Germany', population:
                81831000)`

-   `Country.create({name: 'Germany', population:
                81831000})`

Similarly, you can describe the attributes differently:

-   `Country.create(:name => 'Germany', :population
                => 81831000)`

-   `Country.create('name' => 'Germany', 'population'
                => 81831000)`

-   `Country.create( name: 'Germany', population: 81831000
                )`

You can also pass an array of hashes to create and use this approach to
create several records at once:

```bash
Country.create([{name: 'Germany'}, {name: 'France'}])
```

### new

In addition to create there is also new. But you have to use save to
save an object created with new (which has both advantages and
disadvantages):

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> france = Country.new
=> #<Country id: nil, name: nil, population: nil, created_at: nil, updated_at: nil>
>> france.name = 'France'
=> "France"
>> france.population = 65447374
=> 65447374
>> france.save
   (0.2ms)  begin transaction
  SQL (0.9ms)  INSERT INTO "countries" ("name", "population", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["name", "France"], ["population", 65447374], ["created_at", "2015-04-16 13:40:07.608858"], ["updated_at", "2015-04-16 13:40:07.608858"]]
   (9.4ms)  commit transaction
=> true
>> france
=> #<Country id: 2, name: "France", population: 65447374, created_at: "2015-04-16 13:40:07", updated_at: "2015-04-16 13:40:07">
>>
```

You can also pass parameters for the new record directly to the method
`new`, just as with `create`:

```bash
>> belgium = Country.new(name: 'Belgium', population: 10839905)
=> #<Country id: nil, name: "Belgium", population: 10839905, created_at: nil, updated_at: nil>
>> belgium.save
   (0.2ms)  begin transaction
  SQL (0.4ms)  INSERT INTO "countries" ("name", "population", "created_at", "updated_at") VALUES (?, ?, ?, ?)  [["name", "Belgium"], ["population", 10839905], ["created_at", "2015-04-16 13:42:04.580377"], ["updated_at", "2015-04-16 13:42:04.580377"]]
   (9.3ms)  commit transaction
=> true
>> exit
$
```

### new\_record?

With the method `new_record?` you can find out if a record has already
been saved or not. If a `new` object has been created with new and not yet
been saved, then the result of `new_record?` is `true`. After a `save` it
is `false`.

Example:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> netherlands = Country.new(name: 'Netherlands')
=> #<Country id: nil, name: "Netherlands", population: nil, created_at: nil, updated_at: nil>
>> netherlands.new_record?
=> true
>> netherlands.save
   (0.2ms)  begin transaction
  SQL (0.5ms)  INSERT INTO "countries" ("name", "created_at", "updated_at") VALUES (?, ?, ?)  [["name", "Netherlands"], ["created_at", "2015-04-16 13:48:03.114012"], ["updated_at", "2015-04-16 13:48:03.114012"]]
   (0.8ms)  commit transaction
=> true
>> netherlands.new_record?
=> false
>> exit
$
```

> **Tip**
>
> For already existing records, you can also check for changes with the
> method `changed?` (see [the section called "changed?"](#changed)).

first, last and all
-------------------

In certain cases, you may need the first record, or the last one, or
perhaps even all records. Conveniently, there is a ready-made method for
each case. Let's start with the easiest ones: `first` and `last`.

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Country.first
  Country Load (0.8ms)  SELECT  "countries".* FROM "countries"  ORDER BY "countries"."id" ASC LIMIT 1
=> #<Country id: 1, name: "Germany", population: 81831000, created_at: "2015-04-16 13:32:37", updated_at: "2015-04-16 13:32:37">
>> Country.last
  Country Load (0.4ms)  SELECT  "countries".* FROM "countries"  ORDER BY "countries"."id" DESC LIMIT 1
=> #<Country id: 4, name: "Netherlands", population: nil, created_at: "2015-04-16 13:48:03", updated_at: "2015-04-16 13:48:03">
>>
```

And now all at once with `all`:

```bash
>> Country.all
  Country Load (0.3ms)  SELECT "countries".* FROM "countries"
=> #<ActiveRecord::Relation [#<Country id: 1, name: "Germany", population: 81831000, created_at: "2015-04-16 13:32:37", updated_at: "2015-04-16 13:32:37">, #<Country id: 2, name: "France", population: 65447374, created_at: "2015-04-16 13:40:07", updated_at: "2015-04-16 13:40:07">, #<Country id: 3, name: "Belgium", population: 10839905, created_at: "2015-04-16 13:42:04", updated_at: "2015-04-16 13:42:04">, #<Country id: 4, name: "Netherlands", population: nil, created_at: "2015-04-16 13:48:03", updated_at: "2015-04-16 13:48:03">]>
>>
```

But the objects created by `first`, `last` and `all` are different. `first` and
`last` return an object of the class `Country` and `all` of course returns an
array of such objects:

```bash
>> Country.first.class
  Country Load (0.2ms)  SELECT  "countries".* FROM "countries"  ORDER BY "countries"."id" ASC LIMIT 1
=> Country(id: integer, name: string, population: integer, created_at: datetime, updated_at: datetime)
>> Country.all.class
=> Country::ActiveRecord_Relation
>>
```

So `Country.first` is a Country which makes sense. But `Country.all` is
something we haven't had yet. Let's use the console to get a better idea
of it:

```bash
>> puts Country.all.to_yaml
  Country Load (0.4ms)  SELECT "countries".* FROM "countries"
---
- !ruby/object:Country
  attributes:
    id: 1
    name: Germany
    population: 81831000
    created_at: 2015-04-16 13:32:37.748459 Z
    updated_at: 2015-04-16 13:32:37.748459 Z
- !ruby/object:Country
  attributes:
    id: 2
    name: France
    population: 65447374
    created_at: 2015-04-16 13:40:07.608858 Z
    updated_at: 2015-04-16 13:40:07.608858 Z
- !ruby/object:Country
  attributes:
    id: 3
    name: Belgium
    population: 10839905
    created_at: 2015-04-16 13:42:04.580377 Z
    updated_at: 2015-04-16 13:42:04.580377 Z
- !ruby/object:Country
  attributes:
    id: 4
    name: Netherlands
    population: 
    created_at: 2015-04-16 13:48:03.114012 Z
    updated_at: 2015-04-16 13:48:03.114012 Z
=> nil
>>
```

hmmm... by using the `to_yaml` method suddenly the database has work to
do. The reason for this behavior is optimization. Let's assume that you
want to chain a couple of methods. Than it might be better for
ActiveRecord to wait till the very last second which it does. It only
requests the data from the SQL database when it has to do it. Until than
it stores the request in a `ActiveRecord::Relation`.

The result of `Country.all` is actually an `Array` of `Country`.

If `Country.all` returns an array, then we should also be able to use
iterators (see [the section called "Iterators"](chapter02.ruby-basics.html#iterators) and [the section called "Iterator each"](chapter02-ruby-basics.html#iterator-each)), 
right? Yes, of course! That is the beauty of it. Here is a little experiment with each:

```bash
>> Country.all.each do |country|
?>   puts country.name
>> end
  Country Load (0.3ms)  SELECT "countries".* FROM "countries"
Germany
France
Belgium
Netherlands
=> [#<Country id: 1, name: "Germany", population: 81831000, created_at: "2015-04-16 13:32:37", updated_at: "2015-04-16 13:32:37">, #<Country id: 2, name: "France", population: 65447374, created_at: "2015-04-16 13:40:07", updated_at: "2015-04-16 13:40:07">, #<Country id: 3, name: "Belgium", population: 10839905, created_at: "2015-04-16 13:42:04", updated_at: "2015-04-16 13:42:04">, #<Country id: 4, name: "Netherlands", population: nil, created_at: "2015-04-16 13:48:03", updated_at: "2015-04-16 13:48:03">]
>>
```

So can we also use `.all.first` as an alternative for `.first`? Yes, but
it does not make much sense. Have a look for yourself:

```bash
>> Country.first
  Country Load (0.3ms)  SELECT  "countries".* FROM "countries"  ORDER BY "countries"."id" ASC LIMIT 1
=> #<Country id: 1, name: "Germany", population: 81831000, created_at: "2015-04-16 13:32:37", updated_at: "2015-04-16 13:32:37">
>> Country.all.first
  Country Load (0.2ms)  SELECT  "countries".* FROM "countries"  ORDER BY "countries"."id" ASC LIMIT 1
=> #<Country id: 1, name: "Germany", population: 81831000, created_at: "2015-04-16 13:32:37", updated_at: "2015-04-16 13:32:37">
>>
```

`Country.first` and `Country.all.first` result in exact the same SQL
query.

Populating the Database with seeds.rb
-------------------------------------

With the file `db/seeds.rb`, the Rails gods have given us a way of
feeding default values easily and quickly to a fresh installation. This
is a normal Ruby program within the Rails environment. You have full
access to all classes and methods of your application.

So you do not need to enter everything manually with `rails
  console` in order to make the records created in [the section called "create"](#create) available in a new
Rails application, but you can simply use the following file
`db/seeds.rb`:

```ruby
Country.create(name: 'Germany', population: 81831000)
Country.create(name: 'France', population: 65447374)
Country.create(name: 'Belgium', population: 10839905)
Country.create(name: 'Netherlands', population: 16680000)
```

You then populate it with data via `rake db:seed`. To be on the safe
side, you should always set up the database from scratch with
`rake db:setup` in the context of this book and then automatically
populate it with the file `db/seeds.rb`. Here is what is looks like:

```bash
$ rake db:setup
db/development.sqlite3 already exists
db/test.sqlite3 already exists
-- create_table("countries", {:force=>:cascade})
   -> 0.0148s
-- create_table("products", {:force=>:cascade})
   -> 0.0041s
-- initialize_schema_migrations_table()
   -> 0.0203s
-- create_table("countries", {:force=>:cascade})
   -> 0.0036s
-- create_table("products", {:force=>:cascade})
   -> 0.0036s
-- initialize_schema_migrations_table()
   -> 0.0008s
$
```

I use the file `db/seeds.rb` at this point because it offers a simple
mechanism for filling an empty database with default values. In the
course of this book, this will make it easier for us to set up quick
example scenarios.

### It's all just Ruby code

The `db/seeds.rb` is a Ruby program. Correspondingly, we can also use
the following approach as an alternative:

```ruby
country_list = [
  [ "Germany", 81831000 ],
  [ "France", 65447374 ],
  [ "Belgium", 10839905 ],
  [ "Netherlands", 16680000 ]
]

country_list.each do |name, population|
  Country.create( name: name, population: population )
end
```

The result is the same. I am showing you this example to make it clear
that you can program completely normally within the file `db/seeds.rb`.

### Generating seeds.rb From Existing Data

Sometimes it can be useful to export the current data pool of a Rails
application into a `db/seeds.rb`. While writing this book, I encountered
this problem in almost every chapter. Unfortunately, there is no
standard approach for this. I am showing you what you can do in this
case. There are other, more complex scenarios that can be derived from
my approach.

We create our own little rake task for that. That can be done by
creating the file `lib/tasks/export.rake` with the following content:

```ruby
namespace :export do
  desc "Prints Country.all in a seeds.rb way."
  task :seeds_format => :environment do
    Country.order(:id).all.each do |country|
      puts "Country.create(#{country.serializable_hash.delete_if {|key, value| ['created_at','updated_at','id'].include?(key)}.to_s.gsub(/[{}]/,'')})"
    end
  end
end
```

Then you can call the corresponding rake task with the command
`rake export:seeds_format`:

```bash
$ rake export:seeds_format
Country.create("name"=>"Germany", "population"=>81831000)
Country.create("name"=>"France", "population"=>65447374)
Country.create("name"=>"Belgium", "population"=>10839905)
Country.create("name"=>"Netherlands", "population"=>16680000)
$
```

You can either expand this program so that the output is written
directly into the `db/seeds.rb` or you can simply use the shell:

```bash
$ rake export:seeds_format > db/seeds.rb
$
```

Searching and Finding with Queries
----------------------------------

The methods `first` and `all` are already quite nice, but usually you want
to search for something specific with a query.

For describing queries, we create a new Rails project:

```bash
$ rails new jukebox
  [...]
$  cd jukebox
$ rails generate model Album name release_year:integer
  [...]
$ rake db:migrate
  [...]
$
```

For the examples uses here, use a `db/seeds.rb` with the following
content:

```ruby
Album.create(name: "Sgt. Pepper's Lonely Hearts Club Band", release_year: 1967)
Album.create(name: "Pet Sounds", release_year: 1966)
Album.create(name: "Revolver", release_year: 1966)
Album.create(name: "Highway 61 Revisited", release_year: 1965)
Album.create(name: "Rubber Soul", release_year: 1965)
Album.create(name: "What's Going On", release_year: 1971)
Album.create(name: "Exile on Main St.", release_year: 1972)
Album.create(name: "London Calling", release_year: 1979)
Album.create(name: "Blonde on Blonde", release_year: 1966)
Album.create(name: "The Beatles", release_year: 1968)
```

Then, set up the new database with `rake db:setup`:

```bash
$ rake db:setup
db/development.sqlite3 already exists
-- create_table("albums", {:force=>:cascade})
   -> 0.0135s
-- initialize_schema_migrations_table()
   -> 0.0226s
-- create_table("albums", {:force=>:cascade})
   -> 0.0022s
-- initialize_schema_migrations_table()
   -> 0.0037s
$
```

### find

The simplest case is searching for a record via a primary key (by
default, the `id` field in the database table). If I know the ID of an
object (here: a record line), then I can search for the individual
object or several objects at once via the ID:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.find(2)
  Album Load (0.3ms)  SELECT  "albums".* FROM "albums" WHERE "albums"."id" = ? LIMIT 1  [["id", 2]]
=> #<Album id: 2, name: "Pet Sounds", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">
>> Album.find([1,3,7])
  Album Load (0.4ms)  SELECT "albums".* FROM "albums" WHERE "albums"."id" IN (1, 3, 7)
=> [#<Album id: 1, name: "Sgt. Pepper's Lonely Hearts Club Band", release_year: 1967, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 3, name: "Revolver", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 7, name: "Exile on Main St.", release_year: 1972, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]
>>
```

If you always want to have an array as result, you also always have to
pass an array as parameter:

```bash
>> Album.find(5).class
  Album Load (0.2ms)  SELECT  "albums".* FROM "albums" WHERE "albums"."id" = ? LIMIT 1  [["id", 5]]
=> Album(id: integer, name: string, release_year: integer, created_at: datetime, updated_at: datetime)
>> Album.find([5]).class
  Album Load (0.2ms)  SELECT  "albums".* FROM "albums" WHERE "albums"."id" = ? LIMIT 1  [["id", 5]]
=> Array
>> exit
$
```

> **Warning**
>
> The method `find` generates an exception if the ID you are searching for
> does not have a record in the database. If in doubt, you should use
> `where` (see [the section called "where"](#where)).

### where

With the method `where`, you can search for specific values in the
database. Let's search for all albums from the year 1966:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.where(release_year: 1966)
  Album Load (0.2ms)  SELECT "albums".* FROM "albums" WHERE "albums"."release_year" = ?  [["release_year", 1966]]
=> #<ActiveRecord::Relation [#<Album id: 2, name: "Pet Sounds", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 3, name: "Revolver", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 9, name: "Blonde on Blonde", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]>
>> Album.where(release_year: 1966).count
   (0.3ms)  SELECT COUNT(*) FROM "albums" WHERE "albums"."release_year" = ?  [["release_year", 1966]]
=> 3
>>
```

You can also use where to search for *ranges* (see [the section called "Range"](chapter02-ruby-basics.html#range)):

```bash
>> Album.where(release_year: 1960..1966)
  Album Load (0.3ms)  SELECT "albums".* FROM "albums" WHERE ("albums"."release_year" BETWEEN 1960 AND 1966)
=> #<ActiveRecord::Relation [#<Album id: 2, name: "Pet Sounds", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 3, name: "Revolver", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 4, name: "Highway 61 Revisited", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 5, name: "Rubber Soul", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 9, name: "Blonde on Blonde", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]>
>> Album.where(release_year: 1960..1966).count
   (0.2ms)  SELECT COUNT(*) FROM "albums" WHERE ("albums"."release_year" BETWEEN 1960 AND 1966)
=> 5
>>
```

And you can also specify several search factors simultaneously,
separated by commas:

```bash
>> Album.where(release_year: 1960..1966, id: 1..5)
  Album Load (0.3ms)  SELECT "albums".* FROM "albums" WHERE ("albums"."release_year" BETWEEN 1960 AND 1966) AND ("albums"."id" BETWEEN 1 AND 5)
=> #<ActiveRecord::Relation [#<Album id: 2, name: "Pet Sounds", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 3, name: "Revolver", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 4, name: "Highway 61 Revisited", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 5, name: "Rubber Soul", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]>
>>
```

Or an array of parameters:

```bash
>> Album.where(release_year: [1966, 1968])
  Album Load (0.4ms)  SELECT "albums".* FROM "albums" WHERE "albums"."release_year" IN (1966, 1968)
=> #<ActiveRecord::Relation [#<Album id: 2, name: "Pet Sounds", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 3, name: "Revolver", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 9, name: "Blonde on Blonde", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 10, name: "The Beatles", release_year: 1968, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]>
>>
```

The result of `where` is always an array. Even if it only contains one hit
or if no hits are returned. If you are looking for the first hit, you
need to combine the method `where` with the method `first`:

```bash
>> Album.where(release_year: [1966, 1968]).first
  Album Load (0.4ms)  SELECT  "albums".* FROM "albums" WHERE "albums"."release_year" IN (1966, 1968)  ORDER BY "albums"."id" ASC LIMIT 1
=> #<Album id: 2, name: "Pet Sounds", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">
>> Album.where(release_year: [1966, 1968]).first.class
  Album Load (0.4ms)  SELECT  "albums".* FROM "albums" WHERE "albums"."release_year" IN (1966, 1968)  ORDER BY "albums"."id" ASC LIMIT 1
=> Album(id: integer, name: string, release_year: integer, created_at: datetime, updated_at: datetime)
>> exit
$
```

#### SQL Queries with where

Sometimes there is no other way and you just have to define and execute
your own SQL query. In ActiveRecord, there are two different ways of
doing this. One *sanitizes* each query before executing it and the other
passes the query on to the SQL database 1 to 1 as it is. Normally, you
should always use the sanitized version because otherwise you can easily
fall victim to an *SQL injection* attack (see
<http://en.wikipedia.org/wiki/Sql_injection>).

If you do not know much about SQL, you can safely skip this section. The
SQL commands used here are not explained further.

##### Sanitized Queries

In this variant, all dynamic search parts are replaced by a question
mark as placeholder and only listed as parameters after the SQL string.

In this example, we are searching for all albums whose name contains the
string “on”:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.where( 'name like ?', '%on%')
  Album Load (1.1ms)  SELECT "albums".* FROM "albums" WHERE (name like '%on%')
=> #<ActiveRecord::Relation [#<Album id: 1, name: "Sgt. Pepper's Lonely Hearts Club Band", release_year: 1967, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 6, name: "What's Going On", release_year: 1971, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 7, name: "Exile on Main St.", release_year: 1972, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 8, name: "London Calling", release_year: 1979, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 9, name: "Blonde on Blonde", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]>
>>
```
Now the number of albums that were published from 1965 onwards:

```bash
>> Album.where( 'release_year > ?', 1964 ).count
   (0.2ms)  SELECT COUNT(*) FROM "albums" WHERE (release_year > 1964)
=> 10
>>
```

The number of albums that are more recent than 1970 and whose name
contains the string “on”:

```bash
>> Album.where( 'name like ? AND release_year > ?', '%on%', 1970 ).count
   (0.3ms)  SELECT COUNT(*) FROM "albums" WHERE (name like '%on%' AND release_year > 1970)
=> 3
>>
```

If the variable `search_string` contains the desired string, you can
search for it as follows:

```bash
>> search_string = 'ing'
=> "ing"
>> Album.where( 'name like ?', "%#{search_string}%").count
   (0.2ms)  SELECT COUNT(*) FROM "albums" WHERE (name like '%ing%')
=> 2
>> exit
$
```

##### “Dangerous” SQL Queries

If you really know what you are doing, you can of course also define the
SQL query completely and forego the *sanitizing* of the query.

Let's count all albums whose name contain the string “on”:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.where( "name like '%on%'" ).count
   (0.2ms)  SELECT COUNT(*) FROM "albums" WHERE (name like '%on%')
=> 5
>> exit
$
```

Please only use this variation if you know exactly what you are doing
and once you have familiarized yourself with the topic SQL injections
(see <http://en.wikipedia.org/wiki/Sql_injection>).

#### Lazy Loading

Lazy Loading is a mechanism that only carries out a database query if
the program flow cannot be realised without the result of this query.
Until then, the query is saved as `ActiveRecord::Relation`. (Incidentally,
the opposite of *lazy loading* is referred to as *eager loading*.)

Does it make sense in principle, but you are not sure what the point of
it all is? Then let's cobble together a query where we nest several
methods. In the following example, `a` is defined more and more closely
and only at the end (when calling the method `all`) the database query
would really be executed in a production system. With the method
ActiveRecord methods `to_sql` you can display the current SQL
query.

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> a = Album.where(release_year: 1965..1968)
  Album Load (0.2ms)  SELECT "albums".* FROM "albums" WHERE ("albums"."release_year" BETWEEN 1965 AND 1968)
=> #<ActiveRecord::Relation [#<Album id: 1, name: "Sgt. Pepper's Lonely Hearts Club Band", release_year: 1967, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 2, name: "Pet Sounds", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 3, name: "Revolver", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 4, name: "Highway 61 Revisited", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 5, name: "Rubber Soul", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 9, name: "Blonde on Blonde", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 10, name: "The Beatles", release_year: 1968, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]>
>> a.class
=> Album::ActiveRecord_Relation
>> a = a.order(:release_year)
  Album Load (0.3ms)  SELECT "albums".* FROM "albums" WHERE ("albums"."release_year" BETWEEN 1965 AND 1968)  ORDER BY "albums"."release_year" ASC
=> #<ActiveRecord::Relation [#<Album id: 4, name: "Highway 61 Revisited", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 5, name: "Rubber Soul", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 2, name: "Pet Sounds", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 3, name: "Revolver", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 9, name: "Blonde on Blonde", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 1, name: "Sgt. Pepper's Lonely Hearts Club Band", release_year: 1967, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 10, name: "The Beatles", release_year: 1968, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]>
>> a = a.limit(3)
  Album Load (0.4ms)  SELECT  "albums".* FROM "albums" WHERE ("albums"."release_year" BETWEEN 1965 AND 1968)  ORDER BY "albums"."release_year" ASC LIMIT 3
=> #<ActiveRecord::Relation [#<Album id: 4, name: "Highway 61 Revisited", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 5, name: "Rubber Soul", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 2, name: "Pet Sounds", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]>
>> exit
$
```

The console can be a bit tricky about this. It tries to help the
developer by actually showing the result but in a non-console
environment this would would only happen at the very last time.

##### Automatic Optimization

One of the great advantages of *lazy loading* is the automatic
optimization of the SQL query through ActiveRecord.

Let's take the sum of all release years of the albums that came out in
the 70s. Then we sort the albums alphabetically and then calculate the
sum.

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.where(release_year: 1970..1979).sum(:release_year)
   (1.5ms)  SELECT SUM("albums"."release_year") FROM "albums" WHERE ("albums"."release_year" BETWEEN 1970 AND 1979)
=> 5922
>> Album.where(release_year: 1970..1979).order(:name).sum(:release_year)
   (0.3ms)  SELECT SUM("albums"."release_year") FROM "albums" WHERE ("albums"."release_year" BETWEEN 1970 AND 1979)
=> 5922
>> exit
$
```

Logically, the result is the same for both queries. But the interesting
thing is that ActiveRecord uses the same SQL code for both queries. It
has detected that `order` is completely irrelevant for `sum` and therefore
taken it out altogether.

> **Note**
>
> In case you are asking yourself why the first query took 1.5ms and the
> second 0.3ms: ActiveRecord cached the results of the first SQL
> request.

### order and reverse\_order

To sort a database query, you can use the method `order`. Example: all
albums from the 60s, sorted by name:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.where(release_year: 1960..1969).order(:name)
  Album Load (0.2ms)  SELECT "albums".* FROM "albums" WHERE ("albums"."release_year" BETWEEN 1960 AND 1969)  ORDER BY "albums"."name" ASC
=> #<ActiveRecord::Relation [#<Album id: 9, name: "Blonde on Blonde", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 4, name: "Highway 61 Revisited", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 2, name: "Pet Sounds", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 3, name: "Revolver", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 5, name: "Rubber Soul", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 1, name: "Sgt. Pepper's Lonely Hearts Club Band", release_year: 1967, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 10, name: "The Beatles", release_year: 1968, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]>
>>
```

With the method reverse\_order you can reverse an order previously
defined via `order`:

```bash
>> Album.where(release_year: 1960..1969).order(:name).reverse_order
  Album Load (0.3ms)  SELECT "albums".* FROM "albums" WHERE ("albums"."release_year" BETWEEN 1960 AND 1969)  ORDER BY "albums"."name" DESC
=> #<ActiveRecord::Relation [#<Album id: 10, name: "The Beatles", release_year: 1968, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 1, name: "Sgt. Pepper's Lonely Hearts Club Band", release_year: 1967, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 5, name: "Rubber Soul", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 3, name: "Revolver", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 2, name: "Pet Sounds", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 4, name: "Highway 61 Revisited", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 9, name: "Blonde on Blonde", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]>
>> exit
$
```

### limit

The result of any search can be limited to a certain range via the
method `limit`.

The first 5 albums from the 60s:

```bash
>> Album.where(release_year: 1960..1969).limit(5)
  Album Load (0.3ms)  SELECT  "albums".* FROM "albums" WHERE ("albums"."release_year" BETWEEN 1960 AND 1969) LIMIT 5
=> #<ActiveRecord::Relation [#<Album id: 1, name: "Sgt. Pepper's Lonely Hearts Club Band", release_year: 1967, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 2, name: "Pet Sounds", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 3, name: "Revolver", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 4, name: "Highway 61 Revisited", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 5, name: "Rubber Soul", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]>
>>
```

All albums sorted by name, then the first 5 of those:

```bash
>> Album.order(:name).limit(5)
  Album Load (0.4ms)  SELECT  "albums".* FROM "albums"  ORDER BY "albums"."name" ASC LIMIT 5
=> #<ActiveRecord::Relation [#<Album id: 9, name: "Blonde on Blonde", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 7, name: "Exile on Main St.", release_year: 1972, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 4, name: "Highway 61 Revisited", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 8, name: "London Calling", release_year: 1979, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 2, name: "Pet Sounds", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]>
>> exit
$
```

#### offset

With the method `offset`, you can define the starting position of the
method limit.

First, we return the first two records and then the first two records
with an offset of 5:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.limit(2)
  Album Load (1.0ms)  SELECT  "albums".* FROM "albums" LIMIT 2
=> #<ActiveRecord::Relation [#<Album id: 1, name: "Sgt. Pepper's Lonely Hearts Club Band", release_year: 1967, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 2, name: "Pet Sounds", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]>
>> Album.limit(2).offset(5)
  Album Load (0.3ms)  SELECT  "albums".* FROM "albums" LIMIT 2 OFFSET 5
=> #<ActiveRecord::Relation [#<Album id: 6, name: "What's Going On", release_year: 1971, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 7, name: "Exile on Main St.", release_year: 1972, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]>
>> exit
$
```

### group

With the method `group`, you can return the result of a query in grouped
form.

Let's return all albums, grouped by their release year:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.group(:release_year)
  Album Load (0.3ms)  SELECT "albums".* FROM "albums" GROUP BY "albums"."release_year"
=> #<ActiveRecord::Relation [#<Album id: 5, name: "Rubber Soul", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 9, name: "Blonde on Blonde", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 1, name: "Sgt. Pepper's Lonely Hearts Club Band", release_year: 1967, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 10, name: "The Beatles", release_year: 1968, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 6, name: "What's Going On", release_year: 1971, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 7, name: "Exile on Main St.", release_year: 1972, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 8, name: "London Calling", release_year: 1979, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]>
>> exit
$
```

### pluck

Normally, ActiveRecord pulls all table columns from the database and
leaves it up to the programmer to later pick out the components he is
interested in. But in case of large amounts of data, it can be useful
and above all much quicker to define a specific database field directly
for the query. You can do this via the method `pluck`.

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.where(release_year: 1960..1969).pluck(:name)
   (0.1ms)  SELECT "albums"."name" FROM "albums" WHERE ("albums"."release_year" BETWEEN 1960 AND 1969)
=> ["Sgt. Pepper's Lonely Hearts Club Band", "Pet Sounds", "Revolver", "Highway 61 Revisited", "Rubber Soul", "Blonde on Blonde", "The Beatles"]
>> Album.where(release_year: 1960..1969).pluck(:name, :release_year)
   (0.3ms)  SELECT "albums"."name", "albums"."release_year" FROM "albums" WHERE ("albums"."release_year" BETWEEN 1960 AND 1969)
=> [["Sgt. Pepper's Lonely Hearts Club Band", 1967], ["Pet Sounds", 1966], ["Revolver", 1966], ["Highway 61 Revisited", 1965], ["Rubber Soul", 1965], ["Blonde on Blonde", 1966], ["The Beatles", 1968]]
>> exit
$
```

As a result, `pluck` returns an array.

### first\_or\_create and first\_or\_initialize

The methods `first_or_create` and `first_or_initialize` are create ways
to search for a specific entry in your database or create one if the
entry doesn't exist already. Both can be chained to a where search.

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.where(name: 'Test')
  Album Load (0.2ms)  SELECT "albums".* FROM "albums" WHERE "albums"."name" = ?  [["name", "Test"]]
=> #<ActiveRecord::Relation []>
>> test = Album.where(name: 'Test').first_or_create
  Album Load (0.3ms)  SELECT  "albums".* FROM "albums" WHERE "albums"."name" = ?  ORDER BY "albums"."id" ASC LIMIT 1  [["name", "Test"]]
   (0.1ms)  begin transaction
  SQL (0.4ms)  INSERT INTO "albums" ("name", "created_at", "updated_at") VALUES (?, ?, ?)  [["name", "Test"], ["created_at", "2015-04-16 18:34:35.775645"], ["updated_at", "2015-04-16 18:34:35.775645"]]
   (9.2ms)  commit transaction
=> #<Album id: 11, name: "Test", release_year: nil, created_at: "2015-04-16 18:34:35", updated_at: "2015-04-16 18:34:35">
>> exit
$
```

### Calculations

#### average

With the method `average`, you can calculate the average of the values in
a particular column of the table. Our data material is of course not
really suited to this. But as an example, let's calculate the average
release year of all albums and then the same for albums from the 60s:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.average(:release_year)
   (0.3ms)  SELECT AVG("albums"."release_year") FROM "albums"
=> #<BigDecimal:7fd76fd027a0,'0.19685E4',18(36)>
>> Album.average(:release_year).to_s
   (0.2ms)  SELECT AVG("albums"."release_year") FROM "albums"
=> "1968.5"
>> Album.where( :release_year => 1960..1969 ).average(:release_year)
   (0.1ms)  SELECT AVG("albums"."release_year") FROM "albums" WHERE ("albums"."release_year" BETWEEN 1960 AND 1969)
=> #<BigDecimal:7fd76fc908d0,'0.1966142857 14286E4',27(36)>
>> Album.where( :release_year => 1960..1969 ).average(:release_year).to_s
   (0.3ms)  SELECT AVG("albums"."release_year") FROM "albums" WHERE ("albums"."release_year" BETWEEN 1960 AND 1969)
=> "1966.14285714286"
>> exit
$
```

#### count

The name says it all: the method `count` counts the number of records.

First, we return the number of all albums in the database and then the
number of albums from the 60s:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.count
   (0.1ms)  SELECT COUNT(*) FROM "albums"
=> 11
>> exit
$
```

#### maximum

With the method `maximum`, you can output the item with the highest value
within a query.

Let's look for the highest release year:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.maximum(:release_year)
   (0.2ms)  SELECT MAX("albums"."release_year") FROM "albums"
=> 1979
>> exit
$
```

#### minimum

With the method `minimum`, you can output the item with the lowest value
within a query.

Let's find the lowest release year:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.minimum(:release_year)
   (0.2ms)  SELECT MIN("albums"."release_year") FROM "albums"
=> 1965
>> exit
$
```

#### sum

With the method `sum`, you can calculate the sum of all items in a
specific column of the database query.

Let's find the sum of all release years:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.sum(:release_year)
   (0.2ms)  SELECT SUM("albums"."release_year") FROM "albums"
=> 19685
>> exit
$
```

### SQL EXPLAIN

Most SQL databases can provide detailled information on a SQL query with
the command EXPLAIN. This does not make much sense for our mini
application, but if you are working with a large database one day, then
EXPLAIN is a good debugging method, for example to find out where to
place an index. SQL EXPLAIN can be called with the method `explain` (it
will be displayed in prettier form if you add a `puts`):

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.where(release_year: 1960..1969)
  Album Load (0.2ms)  SELECT "albums".* FROM "albums" WHERE ("albums"."release_year" BETWEEN 1960 AND 1969)
=> #<ActiveRecord::Relation [#<Album id: 1, name: "Sgt. Pepper's Lonely Hearts Club Band", release_year: 1967, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 2, name: "Pet Sounds", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 3, name: "Revolver", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 4, name: "Highway 61 Revisited", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 5, name: "Rubber Soul", release_year: 1965, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 9, name: "Blonde on Blonde", release_year: 1966, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">, #<Album id: 10, name: "The Beatles", release_year: 1968, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">]>
>> Album.where(release_year: 1960..1969).explain
  Album Load (0.3ms)  SELECT "albums".* FROM "albums" WHERE ("albums"."release_year" BETWEEN 1960 AND 1969)
=> EXPLAIN for: SELECT "albums".* FROM "albums" WHERE ("albums"."release_year" BETWEEN 1960 AND 1969)
0|0|0|SCAN TABLE albums

>> exit
$
```

### Batches

ActiveRecord stores the results of a query in Memory. With very large
tables and results that can become a performance issue. To address this
you can use the find\_each method which splits up the query into batches
with the size of 1,000 (can be configured with the `:batch_size`
option). Our example Album table is too small to show the effect but the
method would be used like this:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Album.where(release_year: 1960..1969).find_each do |album|
?>   puts album.name.upcase
>> end
  Album Load (0.2ms)  SELECT  "albums".* FROM "albums" WHERE ("albums"."release_year" BETWEEN 1960 AND 1969)  ORDER BY "albums"."id" ASC LIMIT 1000
SGT. PEPPER'S LONELY HEARTS CLUB BAND
PET SOUNDS
REVOLVER
HIGHWAY 61 REVISITED
RUBBER SOUL
BLONDE ON BLONDE
THE BEATLES
=> nil
>> exit
$
```

Editing a Record
----------------

Adding data is quite nice, but often you want to edit a record. To show
how that's done I use the album database from [Section "Searching and Finding with Queries"](#searching-and-finding-with-queries).

### Simple Editing

Simple editing of a record takes place in the following steps:

1.  Finding the record and creating a corresponding instance

2.  Changing the attribute

3.  Saving the record via the method ActiveRecord methods `save`

We are now searching for the album “The Beatles” and changing its name
to “A Test”:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> beatles_album = Album.where(name: 'The Beatles').first
  Album Load (0.2ms)  SELECT  "albums".* FROM "albums" WHERE "albums"."name" = ?  ORDER BY "albums"."id" ASC LIMIT 1  [["name", "The Beatles"]]
=> #<Album id: 10, name: "The Beatles", release_year: 1968, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">
>> beatles_album.name
=> "The Beatles"
>> beatles_album.name = 'A Test'
=> "A Test"
>> beatles_album.save
   (0.1ms)  begin transaction
  SQL (0.6ms)  UPDATE "albums" SET "name" = ?, "updated_at" = ? WHERE "albums"."id" = ?  [["name", "A Test"], ["updated_at", "2015-04-16 18:46:03.851575"], ["id", 10]]
   (9.2ms)  commit transaction
=> true
>> exit
$
```

### changed?

If you are not sure if a record has been changed and not yet saved, you
can check via the method `changed?`:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> beatles_album = Album.where(id: 10).first
  Album Load (0.4ms)  SELECT  "albums".* FROM "albums" WHERE "albums"."id" = ?  ORDER BY "albums"."id" ASC LIMIT 1  [["id", 10]]
=> #<Album id: 10, name: "A Test", release_year: 1968, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 18:46:03">
>> beatles_album.changed?
=> false
>> beatles_album.name = 'The Beatles'
=> "The Beatles"
>> beatles_album.changed?
=> true
>> beatles_album.save
   (0.1ms)  begin transaction
  SQL (0.6ms)  UPDATE "albums" SET "name" = ?, "updated_at" = ? WHERE "albums"."id" = ?  [["name", "The Beatles"], ["updated_at", "2015-04-16 18:47:26.794527"], ["id", 10]]
   (9.2ms)  commit transaction
=> true
>> beatles_album.changed?
=> false
>> exit
$
```

### update\_attributes

With the method `update_attributes` you can change several attributes of
an object in one go and then immediately save them automatically.

Let's use this method within the example used in [the section called "Simple Editing"](#simple-editing):

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> first_album = Album.first
  Album Load (0.2ms)  SELECT  "albums".* FROM "albums"  ORDER BY "albums"."id" ASC LIMIT 1
=> #<Album id: 1, name: "Sgt. Pepper's Lonely Hearts Club Band", release_year: 1967, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 17:45:34">
>> first_album.changed?
=> false
>> first_album.update_attributes(name: 'Another Test')
   (0.1ms)  begin transaction
  SQL (0.5ms)  UPDATE "albums" SET "name" = ?, "updated_at" = ? WHERE "albums"."id" = ?  [["name", "Another Test"], ["updated_at", "2015-04-16 18:57:08.054247"], ["id", 1]]
   (9.2ms)  commit transaction
=> true
>> first_album.changed?
=> false
>> Album.first
  Album Load (0.3ms)  SELECT  "albums".* FROM "albums"  ORDER BY "albums"."id" ASC LIMIT 1
=> #<Album id: 1, name: "Another Test", release_year: 1967, created_at: "2015-04-16 17:45:34", updated_at: "2015-04-16 18:57:08">
>>
```

This kind of update can also be chained with a `where` method:

```bash
>> Album.where(name: 'Another Test').first.update_attributes(name: "Sgt. Pepper's Lonely Hearts Club Band")
  Album Load (0.3ms)  SELECT  "albums".* FROM "albums" WHERE "albums"."name" = ?  ORDER BY "albums"."id" ASC LIMIT 1  [["name", "Another Test"]]
   (0.1ms)  begin transaction
  SQL (0.4ms)  UPDATE "albums" SET "name" = ?, "updated_at" = ? WHERE "albums"."id" = ?  [["name", "Sgt. Pepper's Lonely Hearts Club Band"], ["updated_at", "2015-04-16 18:58:00.268375"], ["id", 1]]
   (9.0ms)  commit transaction
=> true
>> exit
```

### Locking

There are many ways of locking a database. By default, Rails uses
“optimistic locking” of records. To activate locking you need to have an
attribute with the name `lock_version` which has to be an integer. To
show how it works I'll create a new Rails project with a `Product` model.
Than I'll try to change the price of the first `Product` on two different
instances. The second change will raise an
ActiveRecord::StaleObjectError.

Example setup:

```bash
$ rails new shop
  [...]
$ cd shop
$ rails generate model Product name 'price:decimal{8,2}' lock_version:integer
  [...]
$ rake db:migrate
  [...]
$
```

Raising an ActiveRecord::StaleObjectError:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Product.create(name: 'Orange', price: 0.5)
   (0.1ms)  begin transaction
  SQL (0.7ms)  INSERT INTO "products" ("name", "price", "created_at", "updated_at", "lock_version") VALUES (?, ?, ?, ?, ?)  [["name", "Orange"], ["price", 0.5], ["created_at", "2015-04-16 19:02:17.338531"], ["updated_at", "2015-04-16 19:02:17.338531"], ["lock_version", 0]]
   (1.0ms)  commit transaction
=> #<Product id: 1, name: "Orange", price: #<BigDecimal:7feb59231198,'0.5E0',9(27)>, lock_version: 0, created_at: "2015-04-16 19:02:17", updated_at: "2015-04-16 19:02:17">
>> a = Product.first
  Product Load (0.4ms)  SELECT  "products".* FROM "products"  ORDER BY "products"."id" ASC LIMIT 1
=> #<Product id: 1, name: "Orange", price: #<BigDecimal:7feb5918a870,'0.5E0',9(27)>, lock_version: 0, created_at: "2015-04-16 19:02:17", updated_at: "2015-04-16 19:02:17">
>> b = Product.first
  Product Load (0.3ms)  SELECT  "products".* FROM "products"  ORDER BY "products"."id" ASC LIMIT 1
=> #<Product id: 1, name: "Orange", price: #<BigDecimal:7feb59172d60,'0.5E0',9(27)>, lock_version: 0, created_at: "2015-04-16 19:02:17", updated_at: "2015-04-16 19:02:17">
>> a.price = 0.6
=> 0.6
>> a.save
   (0.1ms)  begin transaction
  SQL (0.4ms)  UPDATE "products" SET "price" = 0.6, "updated_at" = '2015-04-16 19:02:59.514736', "lock_version" = 1 WHERE "products"."id" = ? AND "products"."lock_version" = ?  [["id", 1], ["lock_version", 0]]
   (9.1ms)  commit transaction
=> true
>> b.price = 0.7
=> 0.7
>> b.save
   (0.1ms)  begin transaction
  SQL (0.3ms)  UPDATE "products" SET "price" = 0.7, "updated_at" = '2015-04-16 19:03:08.408511', "lock_version" = 1 WHERE "products"."id" = ? AND "products"."lock_version" = ?  [["id", 1], ["lock_version", 0]]
   (0.1ms)  rollback transaction
ActiveRecord::StaleObjectError: Attempted to update a stale object: Product
[...]
>> exit
$
```

You have to deal with the conflict by rescuing the exception and fix the
conflict depending on your business logic. Please make sure to add a
`lock_version` hidden field in your forms while using this mechanism
with a WebGUI.

has\_many – 1:n Association
---------------------------

In order to explain `has_many`, let's create a bookshelf application. In
this database, there is a model with books and a model with authors. As
a book can have multiple authors, we need a 1:n association
(*one-to-many association*) to represent it.

> **Note**
>
> Associations are also sometimes referred to as *relations* or
> *relationships*.

First, we create a Rails application:

```bash
$ rails new bookshelf
  [...]
$ cd bookshelf
$
```

Now we create the model for the books:

```bash
$ rails generate model book title
  [...]
$
```

And finally, we create the database table for the authors. In this, we
need an assignment field to the books table. This *foreign key* is
always set by default as name of the referenced object (here: `book`)
with an attached `_id`:

```bash
$ rails generate model author book_id:integer first_name last_name
  [...]
$
```

Then execute a `rake db:migrate` so that the database tables are
actually created:

```bash
$ rake db:migrate
  [...]
$
```

Let's have a look at this on the *console*:

```bash
$ rails console
Loading development environment (Rails 4.2.1)
>> Book
=> Book(id: integer, title: string, created_at: datetime, updated_at: datetime)
>> Author
=> Author(id: integer, book_id: integer, first_name: string, last_name: string, created_at: datetime, updated_at: datetime)
>> exit
$
```

The two database tables are set up and can be used with ActiveRecord.
But ActiveRecord does not yet know anything of the 1:n relation between
them. But this can be done in two small steps.

First we add the line `has_many :authors` in the `app/models/book.rb`
file to set the 1:n relationship:

    class Book < ActiveRecord::Base
      has_many :authors
    end

Than we add `belongs_to :book` in the `app/models/author.rb` file to get
the other way around configured (this is not always needed but often
comes in handy):

    class Author < ActiveRecord::Base
      belongs_to :book
    end

These two simple definitions form the basis for a good deal of
ActiveRecord magic. It will generate a bunch of cool new methods for us
to link both models.

### Creating Records

In this example, we want to save a record for the book "Homo faber" by
Max Frisch.

#### Manually

We drop the database with `rake db:reset`

    $
      [...]
    $  

Befor using the magic we'll insert a book with an author manually. For
that we have to use the book's id in the `book_id` attribute to create
the author.

    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  begin transaction
      SQL (2.3ms)  INSERT INTO "books" ("created_at", "title", "updated_at") VALUES (?, ?, ?)  [["created_at", Tue, 16 Jul 2013 11:58:17 UTC +00:00], ["title", "Homo faber"], ["updated_at", Tue, 16 Jul 2013 11:58:17 UTC +00:00]]
       (3.0ms)  commit transaction
    => #<Book id: 1, title: "Homo faber", created_at: "2013-07-16 11:58:17", updated_at: "2013-07-16 11:58:17">
    >>
       (0.1ms)  begin transaction
      SQL (0.5ms)  INSERT INTO "authors" ("book_id", "created_at", "first_name", "last_name", "updated_at") VALUES (?, ?, ?, ?, ?)  [["book_id", 1], ["created_at", Tue, 16 Jul 2013 11:58:21 UTC +00:00], ["first_name", "Max"], ["last_name", "Frisch"], ["updated_at", Tue, 16 Jul 2013 11:58:21 UTC +00:00]]
       (3.1ms)  commit transaction
    => #<Author id: 1, book_id: 1, first_name: "Max", last_name: "Frisch", created_at: "2013-07-16 11:58:21", updated_at: "2013-07-16 11:58:21">
    >>
    $

ActiveRecord
methods
id()
Entering the `book_id` manually in this way is of course not very
practical and susceptible to errors. That's why there is the method ?.

#### create

ActiveRecord
methods
create()
Now we try doing the same as in ?, but this time we use a bit of
ActiveRecord magic. We can use the method create of `authors` to add new
authors to each Book object. These automatically get the correct
`book_id`:

    $
      [...]
    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  begin transaction
      SQL (2.2ms)  INSERT INTO "books" ("created_at", "title", "updated_at") VALUES (?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:01:01 UTC +00:00], ["title", "Homo faber"], ["updated_at", Tue, 16 Jul 2013 12:01:01 UTC +00:00]]
       (3.1ms)  commit transaction
    => #<Book id: 1, title: "Homo faber", created_at: "2013-07-16 12:01:01", updated_at: "2013-07-16 12:01:01">
    >>
       (0.0ms)  begin transaction
      SQL (0.4ms)  INSERT INTO "authors" ("book_id", "created_at", "first_name", "last_name", "updated_at") VALUES (?, ?, ?, ?, ?)  [["book_id", 1], ["created_at", Tue, 16 Jul 2013 12:01:23 UTC +00:00], ["first_name", "Max"], ["last_name", "Frisch"], ["updated_at", Tue, 16 Jul 2013 12:01:23 UTC +00:00]]
       (0.8ms)  commit transaction
    => #<Author id: 1, book_id: 1, first_name: "Max", last_name: "Frisch", created_at: "2013-07-16 12:01:23", updated_at: "2013-07-16 12:01:23">
    >>
    $

You could also place the authors.create() directly behind the
Book.create():

    $
      [...]
    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  begin transaction
      SQL (2.2ms)  INSERT INTO "books" ("created_at", "title", "updated_at") VALUES (?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:02:36 UTC +00:00], ["title", "Homo faber"], ["updated_at", Tue, 16 Jul 2013 12:02:36 UTC +00:00]]
       (2.6ms)  commit transaction
       (0.0ms)  begin transaction
      SQL (0.4ms)  INSERT INTO "authors" ("book_id", "created_at", "first_name", "last_name", "updated_at") VALUES (?, ?, ?, ?, ?)  [["book_id", 1], ["created_at", Tue, 16 Jul 2013 12:02:36 UTC +00:00], ["first_name", "Max"], ["last_name", "Frisch"], ["updated_at", Tue, 16 Jul 2013 12:02:36 UTC +00:00]]
       (0.9ms)  commit transaction
    => #<Author id: 1, book_id: 1, first_name: "Max", last_name: "Frisch", created_at: "2013-07-16 12:02:36", updated_at: "2013-07-16 12:02:36">
    >>
    $

As create also accepts an array of hashes as an alternative to a single
hash, you can also create multiple authors for a book in one go:

    $
      [...]
    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  begin transaction
      SQL (2.1ms)  INSERT INTO "books" ("created_at", "title", "updated_at") VALUES (?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:03:30 UTC +00:00], ["title", "Example"], ["updated_at", Tue, 16 Jul 2013 12:03:30 UTC +00:00]]
       (3.0ms)  commit transaction
       (0.0ms)  begin transaction
      SQL (0.4ms)  INSERT INTO "authors" ("book_id", "created_at", "last_name", "updated_at") VALUES (?, ?, ?, ?)  [["book_id", 1], ["created_at", Tue, 16 Jul 2013 12:03:30 UTC +00:00], ["last_name", "A"], ["updated_at", Tue, 16 Jul 2013 12:03:30 UTC +00:00]]
       (0.8ms)  commit transaction
       (0.0ms)  begin transaction
      SQL (0.3ms)  INSERT INTO "authors" ("book_id", "created_at", "last_name", "updated_at") VALUES (?, ?, ?, ?)  [["book_id", 1], ["created_at", Tue, 16 Jul 2013 12:03:30 UTC +00:00], ["last_name", "B"], ["updated_at", Tue, 16 Jul 2013 12:03:30 UTC +00:00]]
       (0.8ms)  commit transaction
    => [#<Author id: 1, book_id: 1, first_name: nil, last_name: "A", created_at: "2013-07-16 12:03:30", updated_at: "2013-07-16 12:03:30">, #<Author id: 2, book_id: 1, first_name: nil, last_name: "B", created_at: "2013-07-16 12:03:30", updated_at: "2013-07-16 12:03:30">]
    >>
    $

#### build

ActiveRecord
methods
build()
The method build resembles create. But the record is not saved. This
only happens after a save:

    $
      [...]
    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  begin transaction
      SQL (24.5ms)  INSERT INTO "books" ("created_at", "title", "updated_at") VALUES (?, ?, ?)  [["created_at", Sun, 18 Nov 2012 11:35:35 UTC +00:00], ["title", "Homo faber"], ["updated_at", Sun, 18 Nov 2012 11:35:35 UTC +00:00]]
       (3.0ms)  commit transaction
    => #<Book id: 1, title: "Homo faber", created_at: "2012-11-18 11:35:35", updated_at: "2012-11-18 11:35:35">
    >>
    => #<Author id: nil, book_id: 1, first_name: "Max", last_name: "Frisch", created_at: nil, updated_at: nil>
    >>
    => true
    >>
       (0.1ms)  begin transaction
      SQL (0.7ms)  INSERT INTO "authors" ("book_id", "created_at", "first_name", "last_name", "updated_at") VALUES (?, ?, ?, ?, ?)  [["book_id", 1], ["created_at", Sun, 18 Nov 2012 11:36:12 UTC +00:00], ["first_name", "Max"], ["last_name", "Frisch"], ["updated_at", Sun, 18 Nov 2012 11:36:12 UTC +00:00]]
       (2.5ms)  commit transaction
    => true
    >>
    => false
    >>
    $

> **Warning**
>
> When using create and build, you of course have to observe logical
> dependencies, otherwise there will be an error. For example, you
> cannot chain two build methods. Example:
>
>     $
>     Loading development environment (Rails 4.0.0)
>     >>
>     NoMethodError: undefined method `build' for #<Class:0x007fcc6ce71ab8>
>     [...]
>     >>
>     $

### Accessing Records

First we need example data. Please populate the file `db/seeds.rb` with
the following content:

    Book.create(title: 'Homo faber').authors.create(first_name: 'Max', last_name: 'Frisch')
    Book.create(title: 'Der Besuch der alten Dame').authors.create(first_name: 'Friedrich', last_name: 'Dürrenmatt')
    Book.create(title: 'Julius Shulman: The Last Decade').authors.create([
      {first_name: 'Thomas', last_name: 'Schirmbock'},
      {first_name: 'Julius', last_name: 'Shulman'},
      {first_name: 'Jürgen', last_name: 'Nogai'}
      ])
    Book.create(title: 'Julius Shulman: Palm Springs').authors.create([
      {first_name: 'Michael', last_name: 'Stern'},
      {first_name: 'Alan', last_name: 'Hess'}
      ])
    Book.create(title: 'Photographing Architecture and Interiors').authors.create([
      {first_name: 'Julius', last_name: 'Shulman'},
      {first_name: 'Richard', last_name: 'Neutra'}
      ])
    Book.create(title: 'Der Zauberberg').authors.create(first_name: 'Thomas', last_name: 'Mann')
    Book.create(title: 'In einer Familie').authors.create(first_name: 'Heinrich', last_name: 'Mann')

Now drop the database and refill it with the `db/seeds.rb`:

    $
      [...]
    $

The convenient feature of the 1:n assignment in ActiveRecord is the
particularly easy access to the n instances. Let's look at the first
record:

    $
    Loading development environment (Rails 4.0.0)
    >>
      Book Load (0.1ms)  SELECT "books".* FROM "books" ORDER BY "books"."id" ASC LIMIT 1
    => #<Book id: 1, title: "Homo faber", created_at: "2013-07-16 12:05:49", updated_at: "2013-07-16 12:05:49">
    >>
      Book Load (0.3ms)  SELECT "books".* FROM "books" ORDER BY "books"."id" ASC LIMIT 1
      Author Load (1.4ms)  SELECT "authors".* FROM "authors" WHERE "authors"."book_id" = ?  [["book_id", 1]]
    => #<ActiveRecord::Associations::CollectionProxy [#<Author id: 1, book_id: 1, first_name: "Max", last_name: "Frisch", created_at: "2013-07-16 12:05:50", updated_at: "2013-07-16 12:05:50">]>
    >>

Isn't that cool?! You can access the records simply via the plural form
of the n model. The result is returned as array. Hm, maybe it also works
the other way round?

    >>
      Author Load (0.4ms)  SELECT "authors".* FROM "authors" ORDER BY "authors"."id" ASC LIMIT 1
      Book Load (0.2ms)  SELECT "books".* FROM "books" WHERE "books"."id" = ? ORDER BY "books"."id" ASC LIMIT 1  [["id", 1]]
    => #<Book id: 1, title: "Homo faber", created_at: "2013-07-16 12:05:49", updated_at: "2013-07-16 12:05:49">
    >>
    $

Bingo! Accessing the associated Book class is also very easy. And as
it's only a single record (belongs\_to), the singular form is used in
this case.

> **Note**
>
> If there was no author for this book, the result would be an empty
> array. If no book is associated with an author, then ActiveRecord
> outputs the value `nil` as Book.

### Searching For Records

Before we can start searching, we again need defined example data.
Please fill the file `db/seeds.rb` with the following content (its the
same as we used in ?):

    Book.create(title: 'Homo faber').authors.create(first_name: 'Max', last_name: 'Frisch')
    Book.create(title: 'Der Besuch der alten Dame').authors.create(first_name: 'Friedrich', last_name: 'Dürrenmatt')
    Book.create(title: 'Julius Shulman: The Last Decade').authors.create([
      {first_name: 'Thomas', last_name: 'Schirmbock'},
      {first_name: 'Julius', last_name: 'Shulman'},
      {first_name: 'Jürgen', last_name: 'Nogai'}
      ])
    Book.create(title: 'Julius Shulman: Palm Springs').authors.create([
      {first_name: 'Michael', last_name: 'Stern'},
      {first_name: 'Alan', last_name: 'Hess'}
      ])
    Book.create(title: 'Photographing Architecture and Interiors').authors.create([
      {first_name: 'Julius', last_name: 'Shulman'},
      {first_name: 'Richard', last_name: 'Neutra'}
      ])
    Book.create(title: 'Der Zauberberg').authors.create(first_name: 'Thomas', last_name: 'Mann')
    Book.create(title: 'In einer Familie').authors.create(first_name: 'Heinrich', last_name: 'Mann')

Now drop the database and refill it with the `db/seeds.rb`:

    $
      [...]
    $

And off we go. First we check how many books are in the database:

    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  SELECT COUNT(*) FROM "books"
    => 7
    >>

And how many authors?

    >>
       (0.1ms)  SELECT COUNT(*) FROM "authors"
    => 11
    >>
    $

#### joins

ActiveRecord
methods
joins()
To find all books that have at least one author with the surname 'Mann'
we use a *join*.

[^10]

    $
    Loading development environment (Rails 4.0.0)
    >>
      Book Load (0.5ms)  SELECT "books".* FROM "books" INNER JOIN "authors" ON "authors"."book_id" = "books"."id" WHERE "authors"."last_name" = 'Mann'
    => #<ActiveRecord::Relation [#<Book id: 6, title: "Der Zauberberg", created_at: "2013-07-16 12:05:50", updated_at: "2013-07-16 12:05:50">, #<Book id: 7, title: "In einer Familie", created_at: "2013-07-16 12:05:50", updated_at: "2013-07-16 12:05:50">]>
    >>

The database contains two books with the author 'Mann'. In the SQL, you
can see that the method joins executes an `INNER JOIN`.

Of course, we can also do it the other way round. We could search for
the author of the book 'Homo faber':

    >>
      Author Load (0.3ms)  SELECT "authors".* FROM "authors" INNER JOIN "books" ON "books"."id" = "authors"."book_id" WHERE "books"."title" = 'Homo faber'
    => #<ActiveRecord::Relation [#<Author id: 1, book_id: 1, first_name: "Max", last_name: "Frisch", created_at: "2013-07-16 12:05:50", updated_at: "2013-07-16 12:05:50">]>
    >>
    $

#### includes

ActiveRecord
methods
includes()
ActiveRecord
methods
joins()
includes is very similar to the method joins (see ?). Again, you can use
it to search within a 1:n association. Let's once more search for all
books with an author whose surname is 'Mann':

    $
    Loading development environment (Rails 4.0.0)
    >>
      SQL (0.3ms)  SELECT "books"."id" AS t0_r0, "books"."title" AS t0_r1, "books"."created_at" AS t0_r2, "books"."updated_at" AS t0_r3, "authors"."id" AS t1_r0, "authors"."book_id" AS t1_r1, "authors"."first_name" AS t1_r2, "authors"."last_name" AS t1_r3, "authors"."created_at" AS t1_r4, "authors"."updated_at" AS t1_r5 FROM "books" LEFT OUTER JOIN "authors" ON "authors"."book_id" = "books"."id" WHERE "authors"."last_name" = 'Mann'
    => #<ActiveRecord::Relation [#<Book id: 6, title: "Der Zauberberg", created_at: "2013-07-16 12:05:50", updated_at: "2013-07-16 12:05:50">, #<Book id: 7, title: "In einer Familie", created_at: "2013-07-16 12:05:50", updated_at: "2013-07-16 12:05:50">]>
    >>
    $

In the console output, you can see that the SQL code is different from
the joins query.

joins only reads in the `Book` records and includes also reads the
associated `Authors`. As you can see even in our little example, this
obviously takes longer (0.2 ms vs. 0.3 ms).

#### join vs. includes

Why would you want to use includes at all? Well, if you already know
before the query that you will later need all author data, then it makes
sense to use includes, because then you only need one database query.
That is a lot faster than starting a seperate query for each n.

In that case, would it not be better to always work with includes? No,
it depends on the specific case. When you are using includes, a lot more
data is transported initially. This has to be cached and processed by
ActiveRecord, which takes longer and requires more resources.

### delete and destroy

ActiveRecord
methods
delete()
ActiveRecord
methods
delete\_all()
ActiveRecord
methods
destroy()
ActiveRecord
methods
destroy\_all()
With the methods destroy, destroy\_all, delete and delete\_all you can
delete records, as described in ?. In the context of has\_many, this
means that you can delete the Author records associated with a Book in
one go:

    $
    Loading development environment (Rails 4.0.0)
    >>
      Book Load (0.1ms)  SELECT "books".* FROM "books" WHERE "books"."title" = 'Julius Shulman: The Last Decade' ORDER BY "books"."id" ASC LIMIT 1
    => #<Book id: 3, title: "Julius Shulman: The Last Decade", created_at: "2013-07-16 12:05:50", updated_at: "2013-07-16 12:05:50">
    >>
       (1.7ms)  SELECT COUNT(*) FROM "authors" WHERE "authors"."book_id" = ?  [["book_id", 3]]
    => 3
    >>
      Author Load (0.4ms)  SELECT "authors".* FROM "authors" WHERE "authors"."book_id" = ?  [["book_id", 3]]
       (0.1ms)  begin transaction
      SQL (0.5ms)  DELETE FROM "authors" WHERE "authors"."id" = ?  [["id", 3]]
      SQL (0.1ms)  DELETE FROM "authors" WHERE "authors"."id" = ?  [["id", 4]]
      SQL (0.0ms)  DELETE FROM "authors" WHERE "authors"."id" = ?  [["id", 5]]
       (2.4ms)  commit transaction
    => [#<Author id: 3, book_id: 3, first_name: "Thomas", last_name: "Schirmbock", created_at: "2013-07-16 12:05:50", updated_at: "2013-07-16 12:05:50">, #<Author id: 4, book_id: 3, first_name: "Julius", last_name: "Shulman", created_at: "2013-07-16 12:05:50", updated_at: "2013-07-16 12:05:50">, #<Author id: 5, book_id: 3, first_name: "Jürgen", last_name: "Nogai", created_at: "2013-07-16 12:05:50", updated_at: "2013-07-16 12:05:50">]
    >>
       (0.2ms)  SELECT COUNT(*) FROM "authors" WHERE "authors"."book_id" = ?  [["book_id", 3]]
    => 0
    >>
    $

### Options

I can't comment on all possible options at this point. But I'd like to
show you the most often used ones. For all others, please refer to the
Ruby on Rails documentation that you can find on the Internet at
<http://rails.rubyonrails.org/classes/ActiveRecord/Associations/ClassMethods.html>.

#### belongs\_to

ActiveRecord
relations
belongs\_to()
The most important option for belongs\_to is.

##### touch: true

It automatically sets the field `updated_at` of the entry in the table
Book to the current time when an Author is edited. In the
`app/models/author.rb`, it would look like this:

    class Author < ActiveRecord::Base
      belongs_to :book, touch: true
    end

#### has\_many

ActiveRecord
relations
has\_many()
The most important options for `has_many are`.

##### order: :last\_name

If you want to sort the authors by surname, you can do this via the
following `app/models/book.rb`:

    class Book < ActiveRecord::Base
      has_many :authors, order: :last_name
    end

As an example, let's create a new book with new authors and see how
ActiveRecord sorts them:

    $
    Loading development environment (Rails 4.0.0)
    >>  
       (0.1ms)  begin transaction
      SQL (23.5ms)  INSERT INTO "books" ("created_at", "title", "updated_at") VALUES (?, ?, ?)  [["created_at", Sun, 18 Nov 2012 12:04:31 UTC +00:00], ["title", "Test"], ["updated_at", Sun, 18 Nov 2012 12:04:31 UTC +00:00]]
       (2.6ms)  commit transaction
       (0.1ms)  begin transaction
      SQL (0.6ms)  INSERT INTO "authors" ("book_id", "created_at", "first_name", "last_name", "updated_at") VALUES (?, ?, ?, ?, ?)  [["book_id", 8], ["created_at", Sun, 18 Nov 2012 12:04:31 UTC +00:00], ["first_name", nil], ["last_name", "Z"], ["updated_at", Sun, 18 Nov 2012 12:04:31 UTC +00:00]]
       (0.8ms)  commit transaction
       (0.1ms)  begin transaction
      SQL (0.4ms)  INSERT INTO "authors" ("book_id", "created_at", "first_name", "last_name", "updated_at") VALUES (?, ?, ?, ?, ?)  [["book_id", 8], ["created_at", Sun, 18 Nov 2012 12:04:31 UTC +00:00], ["first_name", nil], ["last_name", "A"], ["updated_at", Sun, 18 Nov 2012 12:04:31 UTC +00:00]]
       (0.8ms)  commit transaction
    => [#<Author id: 12, book_id: 8, first_name: nil, last_name: "Z", created_at: "2012-11-18 12:04:31", updated_at: "2012-11-18 12:04:31">, #<Author id: 13, book_id: 8, first_name: nil, last_name: "A", created_at: "2012-11-18 12:04:31", updated_at: "2012-11-18 12:04:31">]
    >>
      Book Load (0.3ms)  SELECT "books".* FROM "books" ORDER BY "books"."id" DESC LIMIT 1
      Author Load (0.2ms)  SELECT "authors".* FROM "authors" WHERE "authors"."book_id" = 8 ORDER BY last_name
    => [#<Author id: 13, book_id: 8, first_name: nil, last_name: "A", created_at: "2012-11-18 12:04:31", updated_at: "2012-11-18 12:04:31">, #<Author id: 12, book_id: 8, first_name: nil, last_name: "Z", created_at: "2012-11-18 12:04:31", updated_at: "2012-11-18 12:04:31">]
    >>
    $

And if we want to sort in descending order for a change:

    has_many :authors, :order => 'title DESC'

##### dependent: :destroy

If a book is removed, then it usually makes sense to also automatically
remove all authors dependent on this book. This can be done via
`:dependent => :destroy` in the `app/models/book.rb`:

    class Book < ActiveRecord::Base
      has_many :authors, dependent: :destroy
    end

In the following example, we destroy the first book in the database
table. All authors of this book are also automatically destroyed:

    $
    Loading development environment (Rails 4.0.0)
    >>
      Book Load (0.1ms)  SELECT "books".* FROM "books" LIMIT 1
    => #<Book id: 1, title: "Homo faber", created_at: "2012-11-18 11:46:29", updated_at: "2012-11-18 11:46:29">
    >>
      Book Load (0.3ms)  SELECT "books".* FROM "books" LIMIT 1
      Author Load (0.1ms)  SELECT "authors".* FROM "authors" WHERE "authors"."book_id" = 1
    => [#<Author id: 1, book_id: 1, first_name: "Max", last_name: "Frisch", created_at: "2012-11-18 11:46:29", updated_at: "2012-11-18 11:46:29">]
    >>
      Book Load (0.3ms)  SELECT "books".* FROM "books" LIMIT 1
       (0.1ms)  begin transaction
      Author Load (0.2ms)  SELECT "authors".* FROM "authors" WHERE "authors"."book_id" = 1
      SQL (4.8ms)  DELETE FROM "authors" WHERE "authors"."id" = ?  [["id", 1]]
      SQL (0.1ms)  DELETE FROM "books" WHERE "books"."id" = ?  [["id", 1]]
       (3.0ms)  commit transaction
    => #<Book id: 1, title: "Homo faber", created_at: "2012-11-18 11:46:29", updated_at: "2012-11-18 11:46:29">
    >>
      Author Exists (0.2ms)  SELECT 1 AS one FROM "authors" WHERE "authors"."id" = 1 LIMIT 1
    => false
    >>
    $

> **Important**
>
> ActiveRecord
> methods
> destroy()
> ActiveRecord
> methods
> delete()
> Please always remember the difference between the methods destroy (see
> ?) and delete (see ?). This association only works with the method
> destroy.

##### has\_many .., through: ..

Here I need to elaborate a bit: you will probably have noticed that in
our book-author example we have sometimes been entering authors several
times in the `authors` table. Normally, you would of course not do this.
It would be better to enter each author only once in the authors table
and take care of the association with the books via an intermediary
table. For this purpose, there is `has_many ,
        through: => `.

This kind of association is called Many-to-Many (n:n) and we'll discuss
it in detail in ?.

Many-to-Many – n:n Association
------------------------------

ActiveRecord
relations
has\_many()
ActiveRecord
relations
belongs\_to()
ActiveRecord
associations
ActiveRecord, relations
Up to now, we have always associated a database table directly with
another table. For many-to-many, we will associate two tables via a
third table. As example for this kind of relation, we use an order in a
very basic online shop. In this type of shop system, a Product can
appear in several orders (Order) and at the same time an order can
contain several products. This is referred to as many-to-many. Let's
recreate this scenario with code.

### Preparation

Create the shop application:

    $
      [...]
    $

A model for products:

    $
      [...]
    $

A model for an order:

    $
      [...]
    $

And a model for individual items of an order:

    $
      [...]
    $

Then, create the database:

    $
      [...]
    $

### The Association

An order (Order) consists of one or several items (LineItem). This
LineItem consists of the `order_id`, a `product_id` and the number of
items ordered (`quantity`). The individual product is defined in the
product database (Product).

Associating the models happens as always in the directory `app/models`.
First, in the file `app/models/order.rb:`

    class Order < ActiveRecord::Base
      has_many :line_items
      has_many :products, :through => :line_items
    end

Then in the counterpart in the file `app/models/product.rb:`

    class Product < ActiveRecord::Base
      has_many :line_items
      has_many :orders, :through => :line_items
    end

Finally, the file `app/models/line_item.rb:`

    class LineItem < ActiveRecord::Base
      belongs_to :order
      belongs_to :product
    end

### The Association Works Transparent

As we implement the associations via has\_many, most things will already
be familiar to you from ?. I am going to discuss a few examples. For
more details, see ?.

First we populate our product database with the following values:

    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  begin transaction
      SQL (2.2ms)  INSERT INTO "products" ("created_at", "name", "price", "updated_at") VALUES (?, ?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:30:29 UTC +00:00], ["name", "Milk (1 liter)"], ["price", #<BigDecimal:7fb985120d18,'0.45E0',9(45)>], ["updated_at", Tue, 16 Jul 2013 12:30:29 UTC +00:00]]
       (2.5ms)  commit transaction
    => #<Product id: 1, name: "Milk (1 liter)", price: #<BigDecimal:7fb985120d18,'0.45E0',9(45)>, created_at: "2013-07-16 12:30:29", updated_at: "2013-07-16 12:30:29">
    >>
       (0.1ms)  begin transaction
      SQL (0.9ms)  INSERT INTO "products" ("created_at", "name", "price", "updated_at") VALUES (?, ?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:30:36 UTC +00:00], ["name", "Butter (250 gr)"], ["price", #<BigDecimal:7fb98281fe28,'0.75E0',9(45)>], ["updated_at", Tue, 16 Jul 2013 12:30:36 UTC +00:00]]
       (2.3ms)  commit transaction
    => #<Product id: 2, name: "Butter (250 gr)", price: #<BigDecimal:7fb98281fe28,'0.75E0',9(45)>, created_at: "2013-07-16 12:30:36", updated_at: "2013-07-16 12:30:36">
    >>
       (0.1ms)  begin transaction
      SQL (0.5ms)  INSERT INTO "products" ("created_at", "name", "price", "updated_at") VALUES (?, ?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:30:43 UTC +00:00], ["name", "Flour (1 kg)"], ["price", #<BigDecimal:7fb98520b9f8,'0.45E0',9(45)>], ["updated_at", Tue, 16 Jul 2013 12:30:43 UTC +00:00]]
       (2.0ms)  commit transaction
    => #<Product id: 3, name: "Flour (1 kg)", price: #<BigDecimal:7fb98520b9f8,'0.45E0',9(45)>, created_at: "2013-07-16 12:30:43", updated_at: "2013-07-16 12:30:43">
    >>  

Now we create a new Order object with the name `order`:

    >>
    => #<Order id: nil, delivery_address: "123 Acme Street, ACME STATE 12345", created_at: nil, updated_at: nil>
    >>

Logically, this new order does not yet contain any products:

    >>
    => 0
    >>

As often, there are several ways of adding products to the order. The
simplest way: as the products are integrated as array, you can simply
insert them as elements of an array:

    >>
    => #<ActiveRecord::Associations::CollectionProxy [#<Product id: 1, name: "Milk (1 liter)", price: #<BigDecimal:7fb985120d18,'0.45E0',9(45)>, created_at: "2013-07-16 12:30:29", updated_at: "2013-07-16 12:30:29">]>
    >>

But if the customer wants to buy three liters of milk instead of one
liter, we need to enter it in the LineItem (in the linking element)
table. ActiveRecord already build an object for us:

    >>
    => #<ActiveRecord::Associations::CollectionProxy [#<LineItem id: nil, order_id: nil, product_id: 1, quantity: nil, created_at: nil, updated_at: nil>]>
    >>

But the object is not yet saved in the database. After we do this via
save, we can change the quantity in the LineItem object:

    >>
       (0.1ms)  begin transaction
      SQL (0.9ms)  INSERT INTO "orders" ("created_at", "delivery_address", "updated_at") VALUES (?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:34:08 UTC +00:00], ["delivery_address", "123 Acme Street, ACME STATE 12345"], ["updated_at", Tue, 16 Jul 2013 12:34:08 UTC +00:00]]
      SQL (0.3ms)  INSERT INTO "line_items" ("created_at", "order_id", "product_id", "updated_at") VALUES (?, ?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:34:08 UTC +00:00], ["order_id", 1], ["product_id", 1], ["updated_at", Tue, 16 Jul 2013 12:34:08 UTC +00:00]]
       (2.6ms)  commit transaction
    => true
    >>
       (0.2ms)  begin transaction
      SQL (1.0ms)  UPDATE "line_items" SET "quantity" = ?, "updated_at" = ? WHERE "line_items"."id" = 1  [["quantity", 3], ["updated_at", Tue, 16 Jul 2013 12:34:49 UTC +00:00]]
       (3.0ms)  commit transaction
    => true
    >>

Alternatively, we can also buy butter twice directly by adding a
LineItem:

    >>
       (0.1ms)  begin transaction
      SQL (0.6ms)  INSERT INTO "line_items" ("created_at", "order_id", "product_id", "quantity", "updated_at") VALUES (?, ?, ?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:35:38 UTC +00:00], ["order_id", 1], ["product_id", 2], ["quantity", 2], ["updated_at", Tue, 16 Jul 2013 12:35:38 UTC +00:00]]
       (2.3ms)  commit transaction
    => #<LineItem id: 2, order_id: 1, product_id: 2, quantity: 2, created_at: "2013-07-16 12:35:38", updated_at: "2013-07-16 12:35:38">
    >>

> **Warning**
>
> When creating a line\_item we bypass the has\_many: ... :through ..
> logic. The database table contains all the correct information but
> order hasn't been updated:
>
>     >>
>     => #<ActiveRecord::Associations::CollectionProxy [#<Product id: 1, name: "Milk (1 liter)", price: #<BigDecimal:7fb985120d18,'0.45E0',9(45)>, created_at: "2013-07-16 12:30:29", updated_at: "2013-07-16 12:30:29">]>
>     >>
>
> But in the database table, it is of course correct:
>
>     >>
>       Order Load (0.3ms)  SELECT "orders".* FROM "orders" ORDER BY "orders"."id" ASC LIMIT 1
>       Product Load (0.2ms)  SELECT "products".* FROM "products" INNER JOIN "line_items" ON "products"."id" = "line_items"."product_id" WHERE "line_items"."order_id" = ?  [["order_id", 1]]
>     => #<ActiveRecord::Associations::CollectionProxy [#<Product id: 1, name: "Milk (1 liter)", price: #<BigDecimal:7fb985148ac0,'0.45E0',9(45)>, created_at: "2013-07-16 12:30:29", updated_at: "2013-07-16 12:30:29">, #<Product id: 2, name: "Butter (250 gr)", price: #<BigDecimal:7fb985153c90,'0.75E0',9(45)>, created_at: "2013-07-16 12:30:36", updated_at: "2013-07-16 12:30:36">]>
>     >>
>
> In this specific case, you would need to reload the object from the
> database via the method reload:
>
>     >>
>       Order Load (0.4ms)  SELECT "orders".* FROM "orders" WHERE "orders"."id" = ? LIMIT 1  [["id", 1]]
>     => #<Order id: 1, delivery_address: "123 Acme Street, ACME STATE 12345", created_at: "2013-07-16 12:34:08", updated_at: "2013-07-16 12:34:08">
>     >>
>       Product Load (0.3ms)  SELECT "products".* FROM "products" INNER JOIN "line_items" ON "products"."id" = "line_items"."product_id" WHERE "line_items"."order_id" = ?  [["order_id", 1]]
>     => #<ActiveRecord::Associations::CollectionProxy [#<Product id: 1, name: "Milk (1 liter)", price: #<BigDecimal:7fb98516a2d8,'0.45E0',9(45)>, created_at: "2013-07-16 12:30:29", updated_at: "2013-07-16 12:30:29">, #<Product id: 2, name: "Butter (250 gr)", price: #<BigDecimal:7fb985169540,'0.75E0',9(45)>, created_at: "2013-07-16 12:30:36", updated_at: "2013-07-16 12:30:36">]>
>     >>

Let's enter a second order with all available products into the system:

    >>
       (0.1ms)  begin transaction
      SQL (0.6ms)  INSERT INTO "orders" ("created_at", "delivery_address", "updated_at") VALUES (?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:39:27 UTC +00:00], ["delivery_address", "2, Test Road"], ["updated_at", Tue, 16 Jul 2013 12:39:27 UTC +00:00]]
       (2.7ms)  commit transaction
    => #<Order id: 2, delivery_address: "2, Test Road", created_at: "2013-07-16 12:39:27", updated_at: "2013-07-16 12:39:27">
    >>
      Product Load (0.3ms)  SELECT "products".* FROM "products"
       (0.1ms)  begin transaction
      SQL (0.5ms)  INSERT INTO "line_items" ("created_at", "order_id", "product_id", "updated_at") VALUES (?, ?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:39:33 UTC +00:00], ["order_id", 2], ["product_id", 1], ["updated_at", Tue, 16 Jul 2013 12:39:33 UTC +00:00]]
      SQL (0.1ms)  INSERT INTO "line_items" ("created_at", "order_id", "product_id", "updated_at") VALUES (?, ?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:39:33 UTC +00:00], ["order_id", 2], ["product_id", 2], ["updated_at", Tue, 16 Jul 2013 12:39:33 UTC +00:00]]
      SQL (0.1ms)  INSERT INTO "line_items" ("created_at", "order_id", "product_id", "updated_at") VALUES (?, ?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:39:33 UTC +00:00], ["order_id", 2], ["product_id", 3], ["updated_at", Tue, 16 Jul 2013 12:39:33 UTC +00:00]]
       (2.6ms)  commit transaction
      Product Load (0.1ms)  SELECT "products".* FROM "products" INNER JOIN "line_items" ON "products"."id" = "line_items"."product_id" WHERE "line_items"."order_id" = ?  [["order_id", 2]]
    => #<ActiveRecord::Associations::CollectionProxy [#<Product id: 1, name: "Milk (1 liter)", price: #<BigDecimal:7fb9851c32c0,'0.45E0',9(45)>, created_at: "2013-07-16 12:30:29", updated_at: "2013-07-16 12:30:29">, #<Product id: 2, name: "Butter (250 gr)", price: #<BigDecimal:7fb9851c1538,'0.75E0',9(45)>, created_at: "2013-07-16 12:30:36", updated_at: "2013-07-16 12:30:36">, #<Product id: 3, name: "Flour (1 kg)", price: #<BigDecimal:7fb9851cafe8,'0.45E0',9(45)>, created_at: "2013-07-16 12:30:43", updated_at: "2013-07-16 12:30:43">]>
    >>
       (0.1ms)  begin transaction
       (0.1ms)  commit transaction
    => true
    >>

Now we can try out the oposite direction of this many-to-many
association. Let's search for all orders that contain the first product:

    >>
      Product Load (0.3ms)  SELECT "products".* FROM "products" ORDER BY "products"."id" ASC LIMIT 1
      Order Load (0.2ms)  SELECT "orders".* FROM "orders" INNER JOIN "line_items" ON "orders"."id" = "line_items"."order_id" WHERE "line_items"."product_id" = ?  [["product_id", 1]]
    => #<ActiveRecord::Associations::CollectionProxy [#<Order id: 1, delivery_address: "123 Acme Street, ACME STATE 12345", created_at: "2013-07-16 12:34:08", updated_at: "2013-07-16 12:34:08">, #<Order id: 2, delivery_address: "2, Test Road", created_at: "2013-07-16 12:39:27", updated_at: "2013-07-16 12:39:27">]>
    >>

Of course, we can also work with a joins (see ?) and search for all
orders that contain the product "Milk (1 liter)":

    >>
      Order Load (0.2ms)  SELECT "orders".* FROM "orders" INNER JOIN "line_items" ON "line_items"."order_id" = "orders"."id" INNER JOIN "products" ON "products"."id" = "line_items"."product_id" WHERE "products"."name" = 'Milk (1 liter)'
    => #<ActiveRecord::Relation [#<Order id: 1, delivery_address: "123 Acme Street, ACME STATE 12345", created_at: "2013-07-16 12:34:08", updated_at: "2013-07-16 12:34:08">, #<Order id: 2, delivery_address: "2, Test Road", created_at: "2013-07-16 12:39:27", updated_at: "2013-07-16 12:39:27">]>
    >>
    $

has\_one – 1:1 Association
--------------------------

ActiveRecord
relations
has\_one()
ActiveRecord
relations
belongs\_to()
ActiveRecord
associations
ActiveRecord, relations
Similar to has\_many (see ?), the method has\_one also creates a logical
relation between two models. But in contrast to has\_many, one record is
only ever associated with exactly one other record in has\_one. In most
practical cases of application, it logically makes sense to put both
into the same model and therefore the same database table, but for the
sake of completeness I also want to discuss has\_one here.

> **Tip**
>
> You can probably safely skip has\_one without losing any sleep.

In the examples, I assume that you have already read and understood ?. I
am not going to explain methods like build (?) again but assume that you
already know the basics.

### Preparation

We use the example from the Rails documentation (see
<http://api.rubyonrails.org/classes/ActiveRecord/Associations/ClassMethods.html>)
and create an application containing employees and offices. Each
employee has an office. First the application:

    $
      [...]
    $
    $

And now the two models:

    $
      [...]
    $
      [...]
    $
      [...]
    $

### Association

The association in the file `app/model/employee.rb:`

    class Employee < ActiveRecord::Base
      has_one :office
    end

And its counterpart in the file `app/model/office.rb:`

    class Office < ActiveRecord::Base
      belongs_to :employee
    end

#### Options

The options of has\_one are similar to those of has\_many. So for
details, please refer to ? or
<http://api.rubyonrails.org/classes/ActiveRecord/Associations/ClassMethods.html#method-i-has_one>.

### Console Examples

Let's start the console and create two employees:

    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  begin transaction
      SQL (2.3ms)  INSERT INTO "employees" ("created_at", "last_name", "updated_at") VALUES (?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:44:24 UTC +00:00], ["last_name", "Udelhoven"], ["updated_at", Tue, 16 Jul 2013 12:44:24 UTC +00:00]]
       (1.0ms)  commit transaction
    => #<Employee id: 1, last_name: "Udelhoven", created_at: "2013-07-16 12:44:24", updated_at: "2013-07-16 12:44:24">
    >>
       (0.2ms)  begin transaction
      SQL (0.7ms)  INSERT INTO "employees" ("created_at", "last_name", "updated_at") VALUES (?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:44:32 UTC +00:00], ["last_name", "Meier"], ["updated_at", Tue, 16 Jul 2013 12:44:32 UTC +00:00]]
       (2.2ms)  commit transaction
    => #<Employee id: 2, last_name: "Meier", created_at: "2013-07-16 12:44:32", updated_at: "2013-07-16 12:44:32">
    >>

Now the first employee gets his own office:

    >>
      Employee Load (0.2ms)  SELECT "employees".* FROM "employees" ORDER BY "employees"."id" ASC LIMIT 1
       (0.1ms)  begin transaction
      SQL (0.4ms)  INSERT INTO "offices" ("created_at", "employee_id", "location", "updated_at") VALUES (?, ?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:45:13 UTC +00:00], ["employee_id", 1], ["location", "2nd floor"], ["updated_at", Tue, 16 Jul 2013 12:45:13 UTC +00:00]]
       (2.2ms)  commit transaction
    => #<Office id: 1, location: "2nd floor", employee_id: 1, created_at: "2013-07-16 12:45:13", updated_at: "2013-07-16 12:45:13">
    >>

Both directions can be accessed the normal way:

    >>
      Employee Load (0.4ms)  SELECT "employees".* FROM "employees" ORDER BY "employees"."id" ASC LIMIT 1
      Office Load (0.2ms)  SELECT "offices".* FROM "offices" WHERE "offices"."employee_id" = ? ORDER BY "offices"."id" ASC LIMIT 1  [["employee_id", 1]]
    => #<Office id: 1, location: "2nd floor", employee_id: 1, created_at: "2013-07-16 12:45:13", updated_at: "2013-07-16 12:45:13">
    >>
      Office Load (0.3ms)  SELECT "offices".* FROM "offices" ORDER BY "offices"."id" ASC LIMIT 1
      Employee Load (0.2ms)  SELECT "employees".* FROM "employees" WHERE "employees"."id" = ? ORDER BY "employees"."id" ASC LIMIT 1  [["id", 1]]
    => #<Employee id: 1, last_name: "Udelhoven", created_at: "2013-07-16 12:44:24", updated_at: "2013-07-16 12:44:24">
    >>

For the second employee, we use the automatically generated method
create\_office (with has\_many, we would use offices.create here):

    >>
      Employee Load (0.2ms)  SELECT "employees".* FROM "employees" ORDER BY "employees"."id" DESC LIMIT 1
       (0.1ms)  begin transaction
      SQL (0.5ms)  INSERT INTO "offices" ("created_at", "employee_id", "location", "updated_at") VALUES (?, ?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:46:58 UTC +00:00], ["employee_id", 2], ["location", "1st floor"], ["updated_at", Tue, 16 Jul 2013 12:46:58 UTC +00:00]]
       (2.3ms)  commit transaction
      Office Load (0.1ms)  SELECT "offices".* FROM "offices" WHERE "offices"."employee_id" = ? ORDER BY "offices"."id" ASC LIMIT 1  [["employee_id", 2]]
    => #<Office id: 2, location: "1st floor", employee_id: 2, created_at: "2013-07-16 12:46:58", updated_at: "2013-07-16 12:46:58">
    >>

Removing is intuitively done via destroy:

    >>
      Employee Load (0.3ms)  SELECT "employees".* FROM "employees" ORDER BY "employees"."id" ASC LIMIT 1
      Office Load (0.1ms)  SELECT "offices".* FROM "offices" WHERE "offices"."employee_id" = ? ORDER BY "offices"."id" ASC LIMIT 1  [["employee_id", 1]]
       (0.1ms)  begin transaction
      SQL (0.3ms)  DELETE FROM "offices" WHERE "offices"."id" = ?  [["id", 1]]
       (2.4ms)  commit transaction
    => #<Office id: 1, location: "2nd floor", employee_id: 1, created_at: "2013-07-16 12:45:13", updated_at: "2013-07-16 12:45:13">
    >>>>
      Employee Load (0.4ms)  SELECT "employees".* FROM "employees" ORDER BY "employees"."id" ASC LIMIT 1
      Office Load (0.1ms)  SELECT "offices".* FROM "offices" WHERE "offices"."employee_id" = ? ORDER BY "offices"."id" ASC LIMIT 1  [["employee_id", 1]]
    => nil
    >>>>

> **Warning**
>
> If you create a new Office for an Employee with an existing Office
> then you will not get an error message:
>
>     >>
>       Employee Load (0.3ms)  SELECT "employees".* FROM "employees" ORDER BY "employees"."id" DESC LIMIT 1
>        (0.1ms)  begin transaction
>       SQL (0.4ms)  INSERT INTO "offices" ("created_at", "employee_id", "location", "updated_at") VALUES (?, ?, ?, ?)  [["created_at", Tue, 16 Jul 2013 12:48:44 UTC +00:00], ["employee_id", 2], ["location", "Basement"], ["updated_at", Tue, 16 Jul 2013 12:48:44 UTC +00:00]]
>        (1.9ms)  commit transaction
>       Office Load (0.1ms)  SELECT "offices".* FROM "offices" WHERE "offices"."employee_id" = ? ORDER BY "offices"."id" ASC LIMIT 1  [["employee_id", 2]]
>        (0.1ms)  begin transaction
>       SQL (0.4ms)  UPDATE "offices" SET "employee_id" = ?, "updated_at" = ? WHERE "offices"."id" = 2  [["employee_id", nil], ["updated_at", Tue, 16 Jul 2013 12:48:44 UTC +00:00]]
>        (0.8ms)  commit transaction
>     => #<Office id: 3, location: "Basement", employee_id: 2, created_at: "2013-07-16 12:48:44", updated_at: "2013-07-16 12:48:44">
>     >>
>       Employee Load (0.2ms)  SELECT "employees".* FROM "employees" ORDER BY "employees"."id" DESC LIMIT 1
>       Office Load (0.2ms)  SELECT "offices".* FROM "offices" WHERE "offices"."employee_id" = ? ORDER BY "offices"."id" ASC LIMIT 1  [["employee_id", 2]]
>     => #<Office id: 3, location: "Basement", employee_id: 2, created_at: "2013-07-16 12:48:44", updated_at: "2013-07-16 12:48:44">
>     >>
>
> The old Office is even still in the database (the `employee_id` was
> automatically set to `nil`):
>
>     >>
>       Office Load (0.3ms)  SELECT "offices".* FROM "offices"
>     => #<ActiveRecord::Relation [#<Office id: 2, location: "1st floor", employee_id: nil, created_at: "2013-07-16 12:46:58", updated_at: "2013-07-16 12:48:44">, #<Office id: 3, location: "Basement", employee_id: 2, created_at: "2013-07-16 12:48:44", updated_at: "2013-07-16 12:48:44">]>
>     >>
>     $

### has\_one vs. belongs\_to

Both has\_one and belongs\_to offer the option of representing a 1:1
relationship. The difference in practice is in the programmer's personal
preference and the location of the foreign key. In general, has\_one
tends to be used very rarely and depends on the degree of normalization
of the data schema.

Polymorphic Associations
------------------------

ActiveRecord
polymorphic associations
Already the word "polymorphic" will probably make you tense up. What can
it mean? Here is what the website
<http://api.rubyonrails.org/classes/ActiveRecord/Associations/ClassMethods.html>
tells us: “Polymorphic associations on models are not restricted on what
types of models they can be associated with.” Well, there you go - as
clear as mud! ;-)

I am showing you an example in which we create a model for cars (Car)
and a model for bicycles (Bike). To describe a car or bike, we use a
model to tag it (Tag). A car and a bike can have any number of tags. The
application:

    $
      [...]
    $  
    $

Now the three required models:

    $
      [...]
    $
      [...]
    $
      [...]
    $
      [...]
    $

Car and Bike are clear. For Tag we use the migration shortcut
`taggable:references{polymorphic}` to generate the fields taggable\_type
and taggable\_id, to give ActiveRecord an opportunity to save the
assignment for the polymorphic association. We have to enter it
accordingly in the model.

The model generator already filed the `app/models/tag.rb` file with the
configuration for the polymorphic association:

`app/models/tag.rb`

    class Tag < ActiveRecord::Base
      belongs_to :taggable, polymorphic: true
    end

For the other models we have to add the polymorphic association
manually:

`app/models/car.rb`

    class Car < ActiveRecord::Base
      has_many :tags, as: :taggable
    end

`app/models/bike.rb`

    class Bike < ActiveRecord::Base
      has_many :tags, as: :taggable
    end

For Car and Bike we use an additional `:as: :taggable` when defining
has\_many. For Tag we use `belongs_to
  :taggable, polymorphic: true` to indicate the polymorphic association
to ActiveRecord.

> **Tip**
>
> The suffix “*able*” in the name “*taggable*” is commonly used in
> Rails, but not obligatory. For creating the association we now not
> only need the ID of the entry, but also need to know which *model* it
> actually is. So the term “*taggable\_type*” makes sense.

Let's go into the *console* and create a car and a bike:

    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  begin transaction
      SQL (2.2ms)  INSERT INTO "cars" ("created_at", "name", "updated_at") VALUES (?, ?, ?)  [["created_at", Tue, 16 Jul 2013 13:04:50 UTC +00:00], ["name", "Beetle"], ["updated_at", Tue, 16 Jul 2013 13:04:50 UTC +00:00]]
       (0.9ms)  commit transaction
    => #<Car id: 1, name: "Beetle", created_at: "2013-07-16 13:04:50", updated_at: "2013-07-16 13:04:50">
    >>
       (0.1ms)  begin transaction
      SQL (0.4ms)  INSERT INTO "bikes" ("created_at", "name", "updated_at") VALUES (?, ?, ?)  [["created_at", Tue, 16 Jul 2013 13:04:57 UTC +00:00], ["name", "Mountainbike"], ["updated_at", Tue, 16 Jul 2013 13:04:57 UTC +00:00]]
       (2.5ms)  commit transaction
    => #<Bike id: 1, name: "Mountainbike", created_at: "2013-07-16 13:04:57", updated_at: "2013-07-16 13:04:57">
    >>

Now we define for each a tag with the color of the corresponding object:

    >>
       (0.0ms)  begin transaction
      SQL (0.5ms)  INSERT INTO "tags" ("created_at", "name", "taggable_id", "taggable_type", "updated_at") VALUES (?, ?, ?, ?, ?)  [["created_at", Tue, 16 Jul 2013 13:05:19 UTC +00:00], ["name", "blue"], ["taggable_id", 1], ["taggable_type", "Car"], ["updated_at", Tue, 16 Jul 2013 13:05:19 UTC +00:00]]
       (3.0ms)  commit transaction
    => #<Tag id: 1, name: "blue", taggable_id: 1, taggable_type: "Car", created_at: "2013-07-16 13:05:19", updated_at: "2013-07-16 13:05:19">
    >>
       (0.1ms)  begin transaction
      SQL (0.4ms)  INSERT INTO "tags" ("created_at", "name", "taggable_id", "taggable_type", "updated_at") VALUES (?, ?, ?, ?, ?)  [["created_at", Tue, 16 Jul 2013 13:05:27 UTC +00:00], ["name", "black"], ["taggable_id", 1], ["taggable_type", "Bike"], ["updated_at", Tue, 16 Jul 2013 13:05:27 UTC +00:00]]
       (2.3ms)  commit transaction
    => #<Tag id: 2, name: "black", taggable_id: 1, taggable_type: "Bike", created_at: "2013-07-16 13:05:27", updated_at: "2013-07-16 13:05:27">
    >>

For the `beetle`, we add another Tag:

    >>
       (0.1ms)  begin transaction
      SQL (0.6ms)  INSERT INTO "tags" ("created_at", "name", "taggable_id", "taggable_type", "updated_at") VALUES (?, ?, ?, ?, ?)  [["created_at", Tue, 16 Jul 2013 13:05:56 UTC +00:00], ["name", "Automatic"], ["taggable_id", 1], ["taggable_type", "Car"], ["updated_at", Tue, 16 Jul 2013 13:05:56 UTC +00:00]]
       (2.1ms)  commit transaction
    => #<Tag id: 3, name: "Automatic", taggable_id: 1, taggable_type: "Car", created_at: "2013-07-16 13:05:56", updated_at: "2013-07-16 13:05:56">
    >>

Let's have a look at all Tag items:

    >>
      Tag Load (0.3ms)  SELECT "tags".* FROM "tags"
    => #<ActiveRecord::Relation [#<Tag id: 1, name: "blue", taggable_id: 1, taggable_type: "Car", created_at: "2013-07-16 13:05:19", updated_at: "2013-07-16 13:05:19">, #<Tag id: 2, name: "black", taggable_id: 1, taggable_type: "Bike", created_at: "2013-07-16 13:05:27", updated_at: "2013-07-16 13:05:27">, #<Tag id: 3, name: "Automatic", taggable_id: 1, taggable_type: "Car", created_at: "2013-07-16 13:05:56", updated_at: "2013-07-16 13:05:56">]>
    >>

And now all tags of the beetle:

    >>
      Tag Load (0.4ms)  SELECT "tags".* FROM "tags" WHERE "tags"."taggable_id" = ? AND "tags"."taggable_type" = ?  [["taggable_id", 1], ["taggable_type", "Car"]]
    => #<ActiveRecord::Associations::CollectionProxy [#<Tag id: 1, name: "blue", taggable_id: 1, taggable_type: "Car", created_at: "2013-07-16 13:05:19", updated_at: "2013-07-16 13:05:19">, #<Tag id: 3, name: "Automatic", taggable_id: 1, taggable_type: "Car", created_at: "2013-07-16 13:05:56", updated_at: "2013-07-16 13:05:56">]>
    >>

Of course you can also check which object the last Tag belongs to:

    >>
      Tag Load (0.3ms)  SELECT "tags".* FROM "tags" ORDER BY "tags"."id" DESC LIMIT 1
      Car Load (0.2ms)  SELECT "cars".* FROM "cars" WHERE "cars"."id" = ? ORDER BY "cars"."id" ASC LIMIT 1  [["id", 1]]
    => #<Car id: 1, name: "Beetle", created_at: "2013-07-16 13:04:50", updated_at: "2013-07-16 13:04:50">
    >>
    $

Polymorphic associations are always useful if you want to normalize the
database structure. In this example, we could also have defined a model
CarTag and BikeTag, but as Tag is the same for both, a polymorphic
association makes more sense in this case.

### Options

Polymorphic associations can be configured with the same options as a
normal ? model.

Delete/Destroy a Record
-----------------------

To remove a database record, you can use the methods destroy and delete.
It's quite easy to confuse these two terms, but they are different and
after a while you get used to it.

As an example, we use the following Rails application:

    $
      [...]
    $
    $
      [...]
    $
      [...]
    $
      [...]
    $

`app/models/book.rb`

    class Book < ActiveRecord::Base
      has_many :authors, dependent: :destroy
    end

`app/models/author.rb`

    class Author < ActiveRecord::Base
      belongs_to :book
    end

### destroy

ActiveRecord
methods
destroy()
With destroy you can remove a record and any existing dependencies are
also taken into account (see for example `:dependent => :destroy` in ?).
Simply put: to be on the safe side, it's better to use destroy because
then the Rails system does more for you.

Let's create a record and then destroy it again:

    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  begin transaction
      SQL (2.2ms)  INSERT INTO "books" ("created_at", "title", "updated_at") VALUES (?, ?, ?)  [["created_at", Tue, 16 Jul 2013 13:30:24 UTC +00:00], ["title", "Homo faber"], ["updated_at", Tue, 16 Jul 2013 13:30:24 UTC +00:00]]
       (2.2ms)  commit transaction
    => #<Book id: 1, title: "Homo faber", created_at: "2013-07-16 13:30:24", updated_at: "2013-07-16 13:30:24">
    >>
       (0.3ms)  SELECT COUNT(*) FROM "books"
    => 1
    >>
       (0.2ms)  begin transaction
      Author Load (0.2ms)  SELECT "authors".* FROM "authors" WHERE "authors"."book_id" = ?  [["book_id", 1]]
      SQL (0.3ms)  DELETE FROM "books" WHERE "books"."id" = ?  [["id", 1]]
       (0.6ms)  commit transaction
    => #<Book id: 1, title: "Homo faber", created_at: "2013-07-16 13:30:24", updated_at: "2013-07-16 13:30:24">
    >>
       (0.4ms)  SELECT COUNT(*) FROM "books"
    => 0
    >>

As we are using the option `dependent: :destroy` in the Book model, we
can also automatically remove all authors:

    >>
       (0.1ms)  begin transaction
      SQL (0.6ms)  INSERT INTO "books" ("created_at", "title", "updated_at") VALUES (?, ?, ?)  [["created_at", Tue, 16 Jul 2013 13:31:11 UTC +00:00], ["title", "Homo faber"], ["updated_at", Tue, 16 Jul 2013 13:31:11 UTC +00:00]]
       (1.9ms)  commit transaction
       (0.1ms)  begin transaction
      SQL (0.5ms)  INSERT INTO "authors" ("book_id", "created_at", "first_name", "last_name", "updated_at") VALUES (?, ?, ?, ?, ?)  [["book_id", 2], ["created_at", Tue, 16 Jul 2013 13:31:11 UTC +00:00], ["first_name", "Max"], ["last_name", "Frisch"], ["updated_at", Tue, 16 Jul 2013 13:31:11 UTC +00:00]]
       (1.0ms)  commit transaction
    => #<Author id: 1, book_id: 2, first_name: "Max", last_name: "Frisch", created_at: "2013-07-16 13:31:11", updated_at: "2013-07-16 13:31:11">
    >>
       (0.4ms)  SELECT COUNT(*) FROM "authors"
    => 1
    >>
      Book Load (0.3ms)  SELECT "books".* FROM "books" ORDER BY "books"."id" ASC LIMIT 1
       (0.1ms)  begin transaction
      Author Load (0.1ms)  SELECT "authors".* FROM "authors" WHERE "authors"."book_id" = ?  [["book_id", 2]]
      SQL (0.3ms)  DELETE FROM "authors" WHERE "authors"."id" = ?  [["id", 1]]
      SQL (0.1ms)  DELETE FROM "books" WHERE "books"."id" = ?  [["id", 2]]
       (2.1ms)  commit transaction
    => #<Book id: 2, title: "Homo faber", created_at: "2013-07-16 13:31:11", updated_at: "2013-07-16 13:31:11">
    >>
       (0.4ms)  SELECT COUNT(*) FROM "authors"
    => 0
    >>

When removing records, please always consider the difference between the
content of the database table and the value of the currently removed
object. The instance is *frozen* after removing the database field. So
it is no longer in the database, but still present in the program, yet
it can no longer be modified there. It is read-only. To check, you can
use the method frozen?()frozen?:

    >>
       (0.1ms)  begin transaction
      SQL (0.6ms)  INSERT INTO "books" ("created_at", "title", "updated_at") VALUES (?, ?, ?)  [["created_at", Tue, 16 Jul 2013 13:32:30 UTC +00:00], ["title", "Homo faber"], ["updated_at", Tue, 16 Jul 2013 13:32:30 UTC +00:00]]
       (2.0ms)  commit transaction
    => #<Book id: 3, title: "Homo faber", created_at: "2013-07-16 13:32:30", updated_at: "2013-07-16 13:32:30">
    >>
       (0.1ms)  begin transaction
      Author Load (0.2ms)  SELECT "authors".* FROM "authors" WHERE "authors"."book_id" = ?  [["book_id", 3]]
      SQL (0.4ms)  DELETE FROM "books" WHERE "books"."id" = ?  [["id", 3]]
       (1.9ms)  commit transaction
    => #<Book id: 3, title: "Homo faber", created_at: "2013-07-16 13:32:30", updated_at: "2013-07-16 13:32:30">
    >>
       (0.2ms)  SELECT COUNT(*) FROM "books"
    => 0
    >>
    => #<Book id: 3, title: "Homo faber", created_at: "2013-07-16 13:32:30", updated_at: "2013-07-16 13:32:30">
    >>
    => true
    >>

The record has been removed from the database, but the object with all
its data is still present in the running Ruby program. So could we then
revive the entire record? The answer is yes, but it will then be a new
record:

    >>
       (0.1ms)  begin transaction
      SQL (0.5ms)  INSERT INTO "books" ("created_at", "title", "updated_at") VALUES (?, ?, ?)  [["created_at", Tue, 16 Jul 2013 13:33:31 UTC +00:00], ["title", "Homo faber"], ["updated_at", Tue, 16 Jul 2013 13:33:31 UTC +00:00]]
       (1.7ms)  commit transaction
    => #<Book id: 4, title: "Homo faber", created_at: "2013-07-16 13:33:31", updated_at: "2013-07-16 13:33:31">
    >>
    $

### delete

ActiveRecord
methods
delete()
With delete you can remove a record directly from the database. Any
dependencies to other records in the *model* are not taken into account.
The method delete only deletes that one row in the database and nothing
else.

Let's create a book with one author and then remove the book with
delete:

    $
      [...]
    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  begin transaction
      SQL (2.2ms)  INSERT INTO "books" ("created_at", "title", "updated_at") VALUES (?, ?, ?)  [["created_at", Tue, 16 Jul 2013 13:35:49 UTC +00:00], ["title", "Homo faber"], ["updated_at", Tue, 16 Jul 2013 13:35:49 UTC +00:00]]
       (2.5ms)  commit transaction
       (0.0ms)  begin transaction
      SQL (0.4ms)  INSERT INTO "authors" ("book_id", "created_at", "first_name", "last_name", "updated_at") VALUES (?, ?, ?, ?, ?)  [["book_id", 1], ["created_at", Tue, 16 Jul 2013 13:35:49 UTC +00:00], ["first_name", "Max"], ["last_name", "Frisch"], ["updated_at", Tue, 16 Jul 2013 13:35:49 UTC +00:00]]
       (0.9ms)  commit transaction
    => #<Author id: 1, book_id: 1, first_name: "Max", last_name: "Frisch", created_at: "2013-07-16 13:35:49", updated_at: "2013-07-16 13:35:49">
    >>
       (0.3ms)  SELECT COUNT(*) FROM "authors"
    => 1
    >>
      Book Load (0.2ms)  SELECT "books".* FROM "books" ORDER BY "books"."id" DESC LIMIT 1
      SQL (2.9ms)  DELETE FROM "books" WHERE "books"."id" = 1
    => #<Book id: 1, title: "Homo faber", created_at: "2013-07-16 13:35:49", updated_at: "2013-07-16 13:35:49">
    >>
       (0.4ms)  SELECT COUNT(*) FROM "authors"
    => 1
    >>
       (0.4ms)  SELECT COUNT(*) FROM "books"
    => 0
    >>
    $

The record of the book 'Homo faber' is deleted, but the author is still
in the database.

As with destroy, an object also gets frozen when you use delete (see ?).
The record is already removed from the database, but the object itself
is still there.

Transactions
------------

ActiveRecord
transactions
transactions
ActiveRecord, transactions
database
transactions
ActiveRecord, transactions
ActiveRecord
methods
transaction()
In the world of databases, the term transaction refers to a block of SQL
statements that must be executed together and without interruption. If
an error should occur within the transaction, the database is reset to
the state before the start of the transaction.

Now and again, there are areas of application where you need to carry
out a database transaction. The classic example is transferring money
from one account to another. That only makes sense if both actions
(debiting one account and crediting the recipient's account) are
executed.

A transaction follows this pattern:

    ActiveRecord::Base.transaction do
      Book.create(:title => 'A')
      Book.create(:title => 'B')
      Book.create(:title => 'C').authors.create(:last_name => 'Z')
    end

Transactions are a complex topic. If you want to find out more, you can
consult the ri help on the shell via `ri
  ActiveRecord::Transactions::ClassMethods`.

> **Important**
>
> The methods save and destroy are automatically executed within the
> transaction *wrapper*. That way, Rails ensures that no undefined state
> can arise for these two methods.

> **Warning**
>
> Transactions are not natively supported by all databases. In that
> case, the code will still work, but you no longer have the security of
> the transaction.

Scopes
------

ActiveRecord
NamedScopes
When programming Rails applications, it is sometimes clearer and simpler
to define frequent searches as separate methods. In Rails speak, these
are referred to as *NamedScope*. These NamedScopes can be chained, just
like other methods.

### Preparation

We are building our own little online shop:

    $
      [...]
    $
    $
      [...]
    $
      [...]
    $

Please populate the file `db/seeds.rb` with the following content:

    Product.create(name: 'Milk (1 liter)', weight: 1000, in_stock: true, price: 0.45, expiration_date: Date.today + 14.days)
    Product.create(name: 'Butter (250 g)', weight: 250, in_stock: true, price: 0.75, expiration_date: Date.today + 14.days)
    Product.create(name: 'Flour (1 kg)', weight: 1000, in_stock: false, price: 0.45, expiration_date: Date.today + 100.days)
    Product.create(name: 'Jelly Babies (6 x 300 g)', weight: 1500, in_stock: true, price: 4.96, expiration_date: Date.today + 1.year)
    Product.create(name: 'Super-Duper Cake Mix', in_stock: true, price: 11.12, expiration_date: Date.today + 1.year)
    Product.create(name: 'Eggs (12)', in_stock: true, price: 2, expiration_date: Date.today + 7.days)
    Product.create(name: 'Peanuts (8 x 200 g bag)', in_stock: false, weight: 1600, price: 17.49, expiration_date: Date.today + 1.year)

Now drop the database and repopulate it with the `db/seeds.rb`:

    $
      [...]
    $

### Defining a Scope

If we want to count products that are in stock in our online shop, then
we can use the following query each time:

    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  SELECT COUNT(*) FROM "products" WHERE "products"."in_stock" = 't'
    => 5
    >>
    $

But we could also define a NamedScope `available` in the
`app/models/product.rb`:

    class Product < ActiveRecord::Base
      scope :available, -> { where(in_stock: true) }
    end

And then use it:

    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.2ms)  SELECT COUNT(*) FROM "products" WHERE "products"."in_stock" = 't'
    => 5
    >>
    $

Let's define a second NamedScope for this example in the
`app/models/product.rb`:

    class Product < ActiveRecord::Base
      scope :available, -> { where(in_stock: true) }
      scope :cheap, -> { where(price: 0..1) }
    end

Now we can chain both named scopes to output all cheap products that are
in stock:

    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  SELECT COUNT(*) FROM "products" WHERE ("products"."price" BETWEEN 0 AND 1)
    => 3
    >>
       (0.2ms)  SELECT COUNT(*) FROM "products" WHERE "products"."in_stock" = 't' AND ("products"."price" BETWEEN 0 AND 1)
    => 2
    >>
    $

### Passing in Arguments

If you need a NamedScope that can also process parameters, then that is
no problem either. The following example outputs products that are
cheaper than the specified value. The `app/models/product.rb` looks like
this:

    class Product < ActiveRecord::Base
      scope :cheaper_than, ->(price) { where("price < ?", price) }
    end

Now we can count all products that cost less than 50 cent:

    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.2ms)  SELECT COUNT(*) FROM "products" WHERE (price < 0.5)
    => 2
    >>
    $

### Creating New Records with Scopes

Let's use the following `app/models/product.rb`:

    class Product < ActiveRecord::Base
      scope :available, -> { where(in_stock: true) }
    end

With this NamedScope we can not only find all products that are in
stock, but also create new products that contain the value `true` in the
field `in_stock`:

    $
    Loading development environment (Rails 4.0.0)
    >>
    => #<Product id: nil, name: nil, price: nil, weight: nil, in_stock: true, expiration_date: nil, created_at: nil, updated_at: nil>
    >>
    => true
    >>
    $

This works with the method build (see ?) and create (see ?).

Validation
----------

ActiveRecord
validation
Non-valid records are frequently a source of errors in programs. With
validates, Rails offers a quick and easy way of validating them. That
way you can be sure that only meaningful records will find their way
into your database.

### Preparation

Let's create a new application for this chapter:

    $
      [...]
    $
    $
      [...]
    $
      [...]
    $

### The Basic Idea

For each model, there is a matching model file in the directory
`app/models/`. In this Ruby code, we can not only define database
dependencies, but also implement all validations. The advantage: Every
programmer knows where to find it.

Without any validation, we can create an empty record in a model without
a problem:

    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  begin transaction
      SQL (2.6ms)  INSERT INTO "products" ("created_at", "updated_at") VALUES (?, ?)  [["created_at", Tue, 16 Jul 2013 17:50:34 UTC +00:00], ["updated_at", Tue, 16 Jul 2013 17:50:34 UTC +00:00]]
       (3.4ms)  commit transaction
    => #<Product id: 1, name: nil, price: nil, weight: nil, in_stock: nil, expiration_date: nil, created_at: "2013-07-16 17:50:34", updated_at: "2013-07-16 17:50:34">
    >>
      Product Load (0.3ms)  SELECT "products".* FROM "products" ORDER BY "products"."id" ASC LIMIT 1
    --- !ruby/object:Product
    attributes:
      id: 1
      name:
      price:
      weight:
      in_stock:
      expiration_date:
      created_at: 2013-07-16 17:50:34.791368000 Z
      updated_at: 2013-07-16 17:50:34.791368000 Z
    => nil
    >>
    $

But in practice, this record with no content doesn't make any sense. A
Product needs to have a `name` and a price. That's why we can define
validations in ActiveRecord. Then you can ensure as programmer that only
records that are valid for you are saved in your database.

To make the mechanism easier to understand, I am going to jump ahead a
bit and use the `presence` helper. Please fill your
`app/model/product.rb` with the following content:

    class Product < ActiveRecord::Base
      validates :name,
                presence: true

      validates :price,
                presence: true
    end

Now we try again to create an empty record in the console:

    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  begin transaction
       (0.1ms)  rollback transaction
    => #<Product id: nil, name: nil, price: nil, weight: nil, in_stock: nil, expiration_date: nil, created_at: nil, updated_at: nil>
    >>

Watch out for the `rollback transaction` part and the misssing `id` of
the Product object! Rails began the transaction of creating a new record
but for some reason it couldn't do it. So it had to rollback the
transaction. The validation method intervened before the record was
saved. So validating happens before saving.

Can we access the errors? Yes, via the method errors or with
errors.messages we can look at the errors that occurred:

    >>
    => #<ActiveModel::Errors:0x007fec75067a20 @base=#<Product id: nil, name: nil, price: nil, weight: nil, in_stock: nil, expiration_date: nil, created_at: nil, updated_at: nil>, @messages={:name=>["can't be blank"], :price=>["can't be blank"]}>
    >>
    => {:name=>["can't be blank"], :price=>["can't be blank"]}
    >>

This error message was defined for a human and English-speaking user
(more on this and how the errors can be translated into another language
in ?).

Only once we assign a value to the attributes `name` and price, we can
save the object:

    >>
    => "Milk (1 liter)"
    >>
    => 0.45
    >>
       (0.2ms)  begin transaction
      SQL (2.9ms)  INSERT INTO "products" ("created_at", "name", "price", "updated_at") VALUES (?, ?, ?, ?)  [["created_at", Tue, 16 Jul 2013 17:52:50 UTC +00:00], ["name", "Milk (1 liter)"], ["price", #<BigDecimal:7fec739394c0,'0.45E0',9(45)>], ["updated_at", Tue, 16 Jul 2013 17:52:50 UTC +00:00]]
       (2.9ms)  commit transaction
    => true
    >>

#### valid?

ActiveRecord
methods
valid?()
The method valid? indicates in boolean form if an object is valid. So
you can check the validity already before you save:

    >>
    => #<Product id: nil, name: nil, price: nil, weight: nil, in_stock: nil, expiration_date: nil, created_at: nil, updated_at: nil>
    >>
    => false
    >>

#### save( validate: false )

ActiveRecord
methods
save()
As so often in life, you can find a way around everything. If you pass
the parameter `:validate => false` to the method save, the data of
`Validation` is saved:

    >>
    => #<Product id: nil, name: nil, price: nil, weight: nil, in_stock: nil, expiration_date: nil, created_at: nil, updated_at: nil>
    >>
    => false
    >>
       (0.1ms)  begin transaction
       (0.1ms)  rollback transaction
    => false
    >>
       (0.1ms)  begin transaction
      SQL (0.8ms)  INSERT INTO "products" ("created_at", "expiration_date", "in_stock", "name", "price", "updated_at", "weight") VALUES (?, ?, ?, ?, ?, ?, ?)  [["created_at", Mon, 19 Nov 2012 09:28:29 UTC +00:00], ["expiration_date", nil], ["in_stock", nil], ["name", nil], ["price", nil], ["updated_at", Mon, 19 Nov 2012 09:28:29 UTC +00:00], ["weight", nil]]
       (2.3ms)  commit transaction
    => true
    >>
    $

> **Warning**
>
> I assume that you understand the problems involved here. Please only
> use this option if there is a good reason to do so. Otherwise you
> might as well do without the whole validation process.

### presence

ActiveRecord
methods
validates\_presence\_of()
ActiveRecord
validates
presence
In our model `product` there are a few fields that must be filled in in
any case. We can achieve this via presence.

`app/models/product.rb`

    class Product < ActiveRecord::Base
      validates :name,
                presence: true

      validates :price,
                presence: true
    end

If we try to create an empty user record with this, we get lots of
validation errors:

    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  begin transaction
       (0.1ms)  rollback transaction
    => #<Product id: nil, name: nil, price: nil, weight: nil, in_stock: nil, expiration_date: nil, created_at: nil, updated_at: nil>
    >>
    => {:name=>["can't be blank"], :price=>["can't be blank"]}
    >>

Only once we have entered all the data, the record can be saved:

    >>
    => "Milk (1 liter)"
    >>
    => 0.45
    >>
       (0.2ms)  begin transaction
      SQL (6.3ms)  INSERT INTO "products" ("created_at", "expiration_date", "in_stock", "name", "price", "updated_at", "weight") VALUES (?, ?, ?, ?, ?, ?, ?)  [["created_at", Mon, 19 Nov 2012 09:30:21 UTC +00:00], ["expiration_date", nil], ["in_stock", nil], ["name", "Milk (1 liter)"], ["price", #<BigDecimal:7fc7044fad08,'0.45E0',9(45)>], ["updated_at", Mon, 19 Nov 2012 09:30:21 UTC +00:00], ["weight", nil]]
       (2.5ms)  commit transaction
    => true
    >>
    $

### length

ActiveRecord
methods
validates\_length\_of()
ActiveRecord
validates
length
With length you can limit the length of a specific attribute. It's
easiest to explain using an example. Let us limit the maximum length of
the name to 20 and the minimum to 2.

`app/models/product.rb`

    class Product < ActiveRecord::Base
      validates :name,
                presence: true,
                length: { in: 2..20 }

      validates :price,
                :presence => true
    end

If we now try to save a Product with a name that consists in one letter,
we get an error message:

    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  begin transaction
       (0.1ms)  rollback transaction
    => #<Product id: nil, name: "M", price: #<BigDecimal:7f9f3d0943c0,'0.45E0',9(45)>, weight: nil, in_stock: nil, expiration_date: nil, created_at: nil, updated_at: nil>
    >>
    => {:name=>["is too short (minimum is 2 characters)"]}
    >>
    $

#### Options

length can be called with the following options.

##### minimum

The minimum length of an attribute. Example:

    validates :name,
              presence: true,
              length: { minimum: 2 }

###### too\_short

Defines the error message of :minimum. Default: "is too short (min is %d
characters)". Example:

    validates :name,
              presence: true,
              length: { minimum: 5 ,
              too_short: "must have at least %{count} characters"}

> **Note**
>
> For all error messages, please note ?.

##### maximum

The maximum length of an attribute. Example:

    validates :name,
              presence: true,
              length: { maximum: 20 }

###### too\_long

Defines the error message of :maximum. Default: "is too long (maximum is
%d characters)". Example:

    validates :name,
              presence: true,
              length: { maximum: 20 ,
              too_long: "must have at most %{count} characters" }

> **Note**
>
> For all error messages, please note ?.

##### is

Is exactly the specified number of characters long. Example:

    validates :name,
              presence: true,
              length: { is: 8 }

##### :in or :within

Defines a length interval. The first number specifies the minimum number
of the range and the second the maximum. Example:

    validates :name,
              presence: true,
              length: { in: 2..20 }

##### tokenizer

You can use this to define how the attribute should be split for
counting. Default: `lambda{ |value| value.split(//) }` (individual
characters are counted). Example (for counting words):

    validates :content,
              presence: true,
              length: { in: 2..20 },
              tokenizer: lambda {|str| str.scan(/\w+/)}

### numericality

ActiveRecord
methods
validates\_numericality\_of()
ActiveRecord
validates
numericality
With numericality you can check if an attribute is a number. It's easier
to explain if we use an example.

`app/models/product.rb`

    class Product < ActiveRecord::Base
      validates :name,
                presence: true,
                length: { in: 2..20 }

      validates :price,
                presence: true

      validates :weight,
                numericality: true
    end

If we now use a `weight` that consists of letters or contains letters
instead of numbers, we will get an error message:

    $
    Loading development environment (Rails 4.0.0)
    >>  
       (0.1ms)  begin transaction
       (0.1ms)  rollback transaction
    => #<Product id: nil, name: "Milk (1 liter)", price: #<BigDecimal:7ff4a4380b30,'0.45E0',9(45)>, weight: 0, in_stock: nil, expiration_date: nil, created_at: nil, updated_at: nil>
    >>
    => {:weight=>["is not a number"]}
    >>
    $

> **Tip**
>
> You can use numericality to define the content as number even if an
> attribute is saved as string in the database.

#### Options

numericality can be called with the following options.

##### only\_integer

The attribute can only contain an integer. Default: false. Example:

    validates :weight,
              numericality: { only_integer: true }

##### greater\_than

The number saved in the attribute must be greater than the specified
value. Example:

    validates :weight,
              numericality: { greater_than: 100 }

##### greater\_than\_or\_equal\_to

The number saved in the attribute must be greater than or equal to the
specified value. Example:

    validates :weight,
              numericality: { greater_than_or_equal_to: 100 }

##### equal\_to

Defines a specific value that the attribute must have. Example:

    validates :weight,
              numericality: { equal_to: 100 }

##### less\_than

The number saved in the attribute must be less than the specified value.
Example:

    validates :weight,
              numericality: { less_than: 100 }

##### less\_than\_or\_equal\_to

The number saved in the attribute must be less than or equal to the
specified value. Example:

    validates :weight,
              numericality: { less_than_or_equal_to: 100 }

##### odd

The number saved in the attribute must be an odd number. Example:

    validates :weight,
              numericality: { odd: true }

##### even

The number saved in the attribute must be an even number. Example:

    validates :weight,
              numericality: { even: true }

### uniqueness

ActiveRecord
methods
validates\_uniqueness\_of()
ActiveRecord
validates
uniqueness
With uniqueness you can define that the value of this attribute must be
unique in the database. If you want a product in the database to have a
unique name that appears nowhere else, then you can use this validation:

`app/models/product.rb`

    class Product < ActiveRecord::Base
      validates :name,
                presence: true,
                uniqueness: true
    end

If we now try to create a new Product with a name that already exists,
then we get an error message:

    $
    Loading development environment (Rails 4.0.0)
    >>
      Product Load (1.9ms)  SELECT "products".* FROM "products" ORDER BY "products"."id" DESC LIMIT 1
    => #<Product id: 4, name: "Milk (1 liter)", price: #<BigDecimal:7f90649840a8,'0.45E0',9(45)>, weight: nil, in_stock: nil, expiration_date: nil, created_at: "2012-11-19 09:30:21", updated_at: "2012-11-19 09:30:21">
    >>
       (0.1ms)  begin transaction
      Product Exists (0.1ms)  SELECT 1 AS one FROM "products" WHERE "products"."name" = 'Milk (1 liter)' LIMIT 1
       (0.1ms)  rollback transaction
    => #<Product id: nil, name: "Milk (1 liter)", price: nil, weight: nil, in_stock: nil, expiration_date: nil, created_at: nil, updated_at: nil>
    >>
    => {:name=>["has already been taken"]}
    >>
    $

> **Warning**
>
> The validation via uniqueness is no absolute guarantee that the
> attribute is unique in the database. A race condition could occur (see
> <http://en.wikipedia.org/wiki/Race_condition>). A detailled discussion
> of this effect would go beyond the scope of book aimed at beginners
> (this phenomenon is extremely rare).

#### Options

uniqueness can be called with the following options.

##### scope

Defines a scope for the uniqueness. If we had a differently structured
phone number database (with just one field for the phone number), then
we could use this option to specify that a phone number must only be
saved once per user. Here is what it would look like:

    validates :name,
              presence: true,
              uniqueness: { scope: :user_id }

##### case\_sensitive

Checks for uniqueness of upper and lower case as well. Default: false.
Example:

    validates :name,
              presence: true,
              uniqueness: { case_sensitive: true }

### inclusion

ActiveRecord
methods
validates\_inclusion\_of()
ActiveRecord
validates
inclusion
With inclusion you can define from which values the content of this
attribute can be created. For our example, we can demonstrate it using
the attribute in\_stock.

`app/models/product.rb`

    class Product < ActiveRecord::Base
      attr_accessible :expiration_date, :in_stock, :name, :price, :weight

      validates :name,
                presence: true

      validates :in_stock,
                inclusion: { in: [true, false] }
    end

In our data model, a Product must be either `true` or `false` for
in\_stock (there must not be a nil). If we enter a different value than
`true` or `false`, a validation error is returned:

    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  begin transaction
       (0.1ms)  rollback transaction
    => #<Product id: nil, name: "Milk low-fat (1 liter)", price: nil, weight: nil, in_stock: nil, expiration_date: nil, created_at: nil, updated_at: nil>
    >>
    => {:in_stock=>["is not included in the list"]}
    >>
    $

> **Tip**
>
> Always remember the power of Ruby! For example, you can generate the
> enumerable object always live from another database. In other words,
> the validation is not defined statically.

#### Options

inclusion can be called with the following option.

##### message

For outputting custom error messages. Default: "is not included in the
list". Example:

    validates :in_stock,
              inclusion: { in: [true, false],
                              message: 'this one is not allowed' }

> **Note**
>
> For all error messages, please note ?.

### exclusion

ActiveRecord
methods
validates\_exclusion\_of()
ActiveRecord
validates
exclusion
exclusion is the inversion of ?. You can define from which values the
content of this attribute must not be created.

`app/models/product.rb`

    class Product < ActiveRecord::Base
      attr_accessible :expiration_date, :in_stock, :name, :price, :weight

      validates :name,
                presence: true

      validates :in_stock,
                exclusion: { in: [nil] }
    end

> **Tip**
>
> Always remember the power of Ruby! For example, you can generate the
> enumerable object always live from another database. In other words,
> the validation does not have to be defined statically.

#### Options

exclusion can be called with the following option.

##### message

For outputting custom error messages. Example:

    validates :in_stock,
              inclusion: { in: [nil],
                              message: 'this one is not allowed' }

> **Note**
>
> For all error messages, please note ?.

### format

ActiveRecord
methods
validates\_format\_of()
ActiveRecord
validates
format
With format you can define via a regular expression (see
<http://en.wikipedia.org/wiki/Regular_expression>) how the content of an
attribute can be structured.

With format you can for example carry out a simple validation of the
syntax of an e-mail address:

    validates :email,
              format: { with: /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\Z/i }

> **Warning**
>
> It should be obvious that the e-mail address validation shown here is
> not complete. It is just meant to be an example. You can only use it
> to check the syntactic correctness of an e-mail address.

#### Options

validates\_format\_of can be called with the following options:

-   `:message`

    For outputting a custom error message. Default: "is invalid".
    Example:

        validates :email,
                  format: { with: /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\Z/i,
                               message: 'is not a valid email address' }

    > **Note**
    >
    > For all error messages, please note ?.

### General Validation Options

ActiveRecord
validates
allow\_nil
ActiveRecord
validates
allow\_blank
ActiveRecord
validates
on
ActiveRecord
validates
if
ActiveRecord
validates
unless
ActiveRecord
validates
proc
There are some options that can be used for all validations.

#### allow\_nil

Allows the value `nil`. Example:

    validates :email,
              format: { with: /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\Z/i },
              allow_nil: true

#### allow\_blank

As `allow_nil`, but additionally with an empty string. Example:

    validates :email,
              format: { with: /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\Z/i },
              allow_blank: true

#### on

With `on`, a validation can be limited to the events `create`, `update`
or `safe`. In the following example, the validation only takes effect
when the record is initially created (during the `create`):

    validates :email,
              format: { with: /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\Z/i },
              on: :create

#### `if` and `unless`

`if` or `unless` call the specified method and only execute the
validation if the result of the method is true:

    validates :name,
              presence: true,
              if: :today_is_monday?

    def today_is_monday?
      Date.today.monday?
    end

##### proc

`:``proc` calls a Proc object.

    validates :name,
              presence: true,
              if: Proc.new { |a| a.email == 'test@test.com' }

### Writing Custom Validations

ActiveRecord
custom validations
ActiveRecord
validate
ActiveRecord
errors
add
ActiveRecord
errors
add\_to\_base
Now and then, you want to do a validation where you need custom program
logic. For such cases, you can define custom validations.

#### Defining Validations with Your Own Methods

Let's assume you are a big shot hotel mogul and need a reservation
system.

    $
      [...]
    $  
    $
      [...]
    $
      [...]
    $

Then we specify in the `app/model/reservation.rb` that the attributes
`start_date` and `end_date` must be present in any case, plus we use the
method reservation\_dates\_must\_make\_sense to make sure that the
`start_date` is before the `end_date`:

    class Reservation < ActiveRecord::Base
      validates :start_date,
                presence: true

      validates :end_date,
                presence: true

      validate :reservation_dates_must_make_sense

      private
      def reservation_dates_must_make_sense
        if end_date <= start_date
          errors.add(:start_date, 'has to be before the end date')
        end
      end
    end

With errors.add we can add error messages for individual attributes.
With errors.add\_to\_base you can add error messages for the whole
object.

Let's test the validation in the console:

    $
    Loading development environment (Rails 4.0.0)
    >>
    => #<Reservation id: nil, start_date: "2012-11-19", end_date: "2012-11-19", room_type: nil, created_at: nil, updated_at: nil>
    >>
    => false
    >>
    => {:start_date=>["has to be before the end date"]}
    >>
    => Tue, 20 Nov 2012
    >>
    => true
    >>
       (0.1ms)  begin transaction
      SQL (8.7ms)  INSERT INTO "reservations" ("created_at", "end_date", "room_type", "start_date", "updated_at") VALUES (?, ?, ?, ?, ?)  [["created_at", Mon, 19 Nov 2012 14:00:50 UTC +00:00], ["end_date", Tue, 20 Nov 2012], ["room_type", nil], ["start_date", Mon, 19 Nov 2012], ["updated_at", Mon, 19 Nov 2012 14:00:50 UTC +00:00]]
       (3.4ms)  commit transaction
    => true
    >>
    $

### Further Documentation

The topic validations is described very well in the official Rails
documentation at
<http://guides.rubyonrails.org/active_record_validations.html>.

Migrations
----------

ActiveRecord
migrations
migrations
ActiveRecord, migrations
SQL database tables are generated in Rails with *migrations* and they
should also be changed with *migrations*. If you create a model with
`rails generate model`, a corresponding migration file is automatically
created in the directory `db/migrate/`. I am going to show you the
principle using the example of a shop application. Let's create one
first:

    $
      [...]
    $
    $

Then we create a Product model:

    $
          invoke  active_record
          create    db/migrate/20121119143522_create_products.rb
          create    app/models/product.rb
          invoke    test_unit
          create      test/unit/product_test.rb
          create      test/fixtures/products.yml
    $

The migrations file `db/migrate/20121119143522_create_products.rb` was
created. Let's have a closer look at it:

    class CreateProducts < ActiveRecord::Migration
      def change
        create_table :products do |t|
          t.string :name
          t.decimal :price, :precision => 7, :scale => 2
          t.integer :weight
          t.boolean :in_stock
          t.date :expiration_date

          t.timestamps
        end
      end
    end

The method change creates and deletes the database table in case of a
rollback. The migration files have embedded the current time in the file
name and are processed in chronological order during a migration (in
other words, when you call `rake
  db:migrate`).

    $
    ==  CreateProducts: migrating =================================================
    -- create_table(:products)
       -> 0.0017s
    ==  CreateProducts: migrated (0.0018s) ========================================

    $

Only those migrations that have not been executed yet are processed. If
we call `rake db:migrate` again, nothing happens, because the
corresponding migration has already been executed:

    $
    $

But if we manually delete the database with `rm` and then call
`rake db:migrate` again, the migration is repeated:

    $  
    $
    ==  CreateProducts: migrating =================================================
    -- create_table(:products)
       -> 0.0016s
    ==  CreateProducts: migrated (0.0017s) ========================================

    $  

After a while we realise that we want to save not just the weight for
some products, but also the height. So we need another database field.
There is an easy to remember syntax for this, `rails generate migration
  add_*`:

    $
          invoke  active_record
          create    db/migrate/20121119143758_add_height_to_product.rb
    $

In the migration file
`db/migrate/20121119143758_add_height_to_product.rb` we once again find
a change method:

    class AddHeightToProduct < ActiveRecord::Migration
      def change
        add_column :products, :height, :integer
      end
    end

With `rake db:migrate` we can start in the new migration:

    $
    ==  AddHeightToProduct: migrating =============================================
    -- add_column(:products, :height, :integer)
       -> 0.0007s
    ==  AddHeightToProduct: migrated (0.0008s) ====================================

    $

In the *console* we can look at the new field. It was added after the
field `updated_at`:

    $
    Loading development environment (Rails 4.0.0)
    >>
    => Product(id: integer, name: string, price: decimal, weight: integer, in_stock: boolean, expiration_date: date, created_at: datetime, updated_at: datetime, height: integer)
    >>
    $

> **Warning**
>
> Please note that you need to add the new field in attr\_accessible in
> `app/models/product.rb`, otherwise you will not have access to the
> `height` attribute.

What if you want to look at the previous state of things? No problem.
You can easily go back to the previous version with `rake
  db:rollback`:

    $
    ==  AddHeightToProduct: reverting =============================================
    -- remove_column("products", :height)
       -> 0.0151s
    ==  AddHeightToProduct: reverted (0.0152s) ====================================

    $

Each migration has its own version number. You can find out the version
number of the current status via `rake
  db:version`:

    $
    Current version: 20121119143522
    $

> **Important**
>
> Please note that all version numbers and timestamps only apply to the
> example printed here. If you recreate the example, you will of course
> get a different timestamp for your own example.

You will find the corresponding version in the directory `db/migrate`:

    $
    20121119143522_create_products.rb
    20121119143758_add_height_to_product.rb
    $

You can go to a specific migration via `rake db:migrate
  VERSION=` and add the appropriate version number after the equals
sign. The zero represents the version zero, in other words the start.
Let's try it out:

    $
    ==  CreateProducts: reverting =================================================
    -- drop_table("products")
       -> 0.0005s
    ==  CreateProducts: reverted (0.0006s) ========================================

    $

The table was deleted with all data. We are back to square one.

### Which Database is Used?

The database table is created through the migration. As you can see, the
table names automatically get the plural of the *model*s (Person vs.
`people`). But in which database are the tables created? This is defined
in the configuration file `config/database.yml`:

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

    production:
      adapter: sqlite3
      database: db/production.sqlite3
      pool: 5
      timeout: 5000

Three different databases are defined there in YAML format (see
<http://www.yaml.org/> or <http://en.wikipedia.org/wiki/YAML>). For us,
only the `development` database is relevant for now (first item). By
default, Rails usesSQLite3 there. SQLite3 may not be the correct choice
for the analysis of the weather data collected worldwide, but for a
quick and straightforward development of Rails applications you will
quickly learn to appreciate it. In the production environment, you can
later still switch to "big" databases such as MySQL or PostgreSQL.[^11]

To satisfy your curiosity, we have a quick look at the database with the
command line tool `sqlite3`:

    $  
    SQLite version 3.7.12 2012-04-03 19:43:07
    Enter ".help" for instructions
    Enter SQL statements terminated with a ";"
    sqlite>
    schema_migrations
    sqlite>
    $

Nothing in it. Of course not, as we have not yet run the migration:

    $
    ==  CreateProducts: migrating =================================================
    -- create_table(:products)
       -> 0.0142s
    ==  CreateProducts: migrated (0.0143s) ========================================

    ==  AddHeightToProduct: migrating =============================================
    -- add_column(:products, :height, :integer)
       -> 0.0011s
    ==  AddHeightToProduct: migrated (0.0012s) ====================================

    $  
    SQLite version 3.7.12 2012-04-03 19:43:07
    Enter ".help" for instructions
    Enter SQL statements terminated with a ";"
    sqlite>
    products           schema_migrations
    sqlite>
    CREATE TABLE "products" ("id" INTEGER PRIMARY KEY AUTOINCREMENT NOT NULL, "name" varchar(255), "price" decimal(7,2), "weight" integer, "in_stock" boolean, "expiration_date" date, "created_at" datetime NOT NULL, "updated_at" datetime NOT NULL, "height" integer);
    sqlite>
    $

The table `schema_migrations` is used for the versioning of the
migrations. This table is created during the first migration carried out
by Rails, if it does not yet exist.

### Creating Index

I assume that you know what a database index is. If not, you will find a
brief introduction at <http://en.wikipedia.org/wiki/Database_index>. In
brief: you can use it to quickly search for a specific table column.

In our production database, we should index the field `name` in the
`products` table. We create a new migration for that purpose:

    $
          invoke  active_record
          create    db/migrate/20121120142002_create_index.rb
    $

In the file `db/migrate/20121120142002_create_index.rb` we create the
index with add\_index in the method self.up, and in the method self.down
we delete it again with remove\_index:

    class CreateIndex < ActiveRecord::Migration
      def up
        add_index :products, :name
      end

      def down
        remove_index :products, :name
      end
    end

With `rake db:migrate` we create the index:

    $
    ==  CreateIndex: migrating ====================================================
    -- add_index(:products, :name)
       -> 0.0010s
    ==  CreateIndex: migrated (0.0011s) ===========================================

    $

Of course we don't have to use the up and down method. We can use change
too. The migration for the new index would look like this:

    class CreateIndex < ActiveRecord::Migration
      def change
        add_index :products, :name
      end
    end

> **Tip**
>
> You can also create an index directly when you generate the model. In
> our case (an index for the attribute `name`) the command would look
> like this:
>
>     $
>           invoke  active_record
>           create    db/migrate/20121120142344_create_products.rb
>           create    app/models/product.rb
>           invoke    test_unit
>           create      test/unit/product_test.rb
>           create      test/fixtures/products.yml
>     $
>     class CreateProducts < ActiveRecord::Migration
>       def change
>         create_table :products do |t|
>           t.string :name
>           t.decimal :price, :precision => 7, :scale => 2
>           t.integer :weight
>           t.boolean :in_stock
>           t.date :expiration_date
>
>           t.timestamps
>         end
>         add_index :products, :name
>       end
>     end
>     $

### Miscellaneous

This book is aimed at beginners, so I cannot discuss the topic
migrations in great depth. The main focus is on understanding the
mechanics in principle. But there are a few details that are so
important that I want to mention them here.

#### Automatically Added Fields (id, created\_at and updated\_at)

Rails kindly adds the following fields automatically in the default
migration:

-   `id:integer`

    This is the unique ID of the record. The field is automatically
    incremented by the database. For all SQL fans:
    `NOT NULL AUTO_INCREMENT`

-   `created_at:datetime`

    The field is filled automatically by ActiveRecord when a record is
    created.

-   `updated_at:datetime`

    The field is automatically updated to the current time whenever the
    record is edited.

So you don't have to enter these fields yourself when generating the
model.

At first you may ask yourself: "Is that really necessary? Does it make
sense?". But after a while you will learn to appreciate these automatic
fields. Omitting them would usually be false economy.

### Further Documentation

The following webpages provide excellent further information on the
topic migration:

-   <http://api.rubyonrails.org/classes/ActiveRecord/Migration.html>

-   <http://api.rubyonrails.org/classes/ActiveRecord/ConnectionAdapters/TableDefinition.html>

-   <http://railscasts.com/episodes/107-migrations-in-rails-2-1>

    This screencast is a bit dated (Rails version 2.1), but still good
    if you are trying to understand the basics.

-   <http://www.dizzy.co.uk/ruby_on_rails/cheatsheets/rails-migrations>

Miscellaneous
-------------

In this section, I am going to show you some examples of topics and
questions that are important for your everyday work, but as a whole go
beyond the scope of this book aimed at beginners. They provide recipes
for solving specific ActiveRecord problems.

### Callbacks

ActiveRecord
callback
ActiveRecord
callback
before\_validation
ActiveRecord
callback
after\_validation
ActiveRecord
callback
before\_save
ActiveRecord
callback
before\_create
ActiveRecord
callback
after\_save
ActiveRecord
callback
after\_create
Callbacks are defined programming hooks in the life of an ActiveRecord
object. You can find a list of all callbacks at
<http://api.rubyonrails.org/classes/ActiveRecord/Callbacks.html>. Here
are the most frequently used callbacks:

-   `before_validation`

    Executed before the validation.

-   `after_validation`

    Executed after the validation.

-   `before_save`

    Executed before each save.

-   `before_create`

    Executed before the first save.

-   `after_save`

    Executed after every save.

-   `after_create`

    Executed after the first save.

A callback is always executed in the model. Let's assume you always want
to save an e-mail address in a User model in lower case, but also give
the user of the web interface the option to enter upper case letters.
You could use a before\_save callback to convert the attribute `email`
to lower case via the method downcase.

The Rails application:

    $
      [...]
    $
    $
      [...]
    $
      [...]
    $

Here is what the model `app/models/user.rb` would look like. The
interesting stuff is the `before_save` part:

    class User < ActiveRecord::Base
      validates :login,
                presence: true

      validates :email,
                presence: true,
                format: { :with => /\A([^@\s]+)@((?:[-a-z0-9]+\.)+[a-z]{2,})\Z/i }

      before_save :downcase_email

      private

      def downcase_email
        self.email = self.email.downcase
      end

    end

Let's see in the console if it really works as we want it to:

    $
    Loading development environment (Rails 4.0.0)
    >>
       (0.1ms)  begin transaction
      SQL (29.9ms)  INSERT INTO "users" ("created_at", "email", "login", "updated_at") VALUES (?, ?, ?, ?)  [["created_at", Wed, 21 Nov 2012 09:14:47 UTC +00:00], ["email", "smith@example.com"], ["login", "smith"], ["updated_at", Wed, 21 Nov 2012 09:14:47 UTC +00:00]]
       (0.7ms)  commit transaction
    => #<User id: 1, email: "smith@example.com", login: "smith", created_at: "2012-11-21 09:14:47", updated_at: "2012-11-21 09:14:47">
    >>
    $

Even though the e-mail address was entered partly with a capital
letters, ActiveRecord has indeed converted all letters automatically to
lower case via the before\_save callback.

In ? you will find an example for the same model where we use an
after\_create callback to automatically send an e-mail to a newly
created user. In ? you will find an example for defining a default value
for a new object via an after\_initialize callback.

### Default Values

ActiveRecord
callback
after\_initialize
If you need specific default values for an ActiveRecord object, you can
easily implement this with the after\_initialize callback. This method
is called by ActiveRecord when a new object is created. Let's assume we
have a modelOrder and the minimum order quantity is always 1, so we can
enter 1 directly as default value when creating a new record.

Let's set up a quick example:

    $
      [...]
    $
    $
      [...]
    $
      [...]
    $

We write an after\_initialize callback into the file
`app/models/order.rb`:

    class Order < ActiveRecord::Base
      after_initialize :set_defaults

      private
      def set_defaults
        self.quantity ||= 1
      end
    end

And now we check in the console if a new order object automatically
contains the quantity 1:

    $
    Loading development environment (Rails 4.0.0)
    >>
    => #<Order id: nil, product_id: nil, quantity: 1, created_at: nil, updated_at: nil>
    >>
    => 1
    >>
    $

That's working fine.
