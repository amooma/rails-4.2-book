Ruby Basics
===========

Introduction
------------

This book requires basic knowledge of HTML, plus the reader - you, in
other words - should also have a basic understanding of programming.

A couple of years ago I made the mistake of trying to understand Ruby on
Rails without knowing anything about Ruby. Ruby just looked so simple,
so I didn't give it another thought. But because I did not have a basic
understanding of Ruby, I was not able to understand Rails properly. So I
kept shunting it into a virtual corner and carried on working with PHP
instead. One can only feel the magic of Rails after understanding the
basics of Ruby.

The inventor of Ruby, Yukihiro Matsumoto, states in his book “The Ruby
Programming Language” that:

“It is easy to program in Ruby, but Ruby is not a simple language.”

This chapter is a tightrope walk between oversimplification and a degree
of detail that is unnecessary for a Rails newbie. After all, the
objective is not becoming a Ruby guru, but understanding Ruby on Rails.
I am going to elaborate on the most important points. The rest is then
up to you. If you would like to know more about Ruby, then I recommend
the book "The Ruby Programming Language" by David Flanagan and Yukihiro
Matsumoto.

Because Ruby version 2.2 is the way to go we use it. Please take care to
update your current Ruby if you are running older versions. Truth be told: For most part of this book you'll be fine with versions 2.0 and 2.1 too - but we tested everything with version 2.2.

The command `ruby -v` will print the current running Ruby version:

```bash
$ ruby -v
ruby 2.2.1p85 (2015-02-26 revision 49769) [x86_64-darwin14]
$
```

> **Warning**
>
> The beginning of this chapter is going to be a bit boring. Bear with
> me. It is worth it.

Hello World
-----------

Ruby is a scripting language. So it is not compiled and then executed,
but read by an interpreter and then processed line by line.

A simple Ruby program `hello-world.rb` consist of the following line:

```ruby
puts 'Hello World!'
```

Use your favorite editor to open a new file with the filename
`hello-world.rb` and insert the above line into it. You can then execute
this Ruby program in the command line as follows:

```bash
$ ruby hello-world.rb
Hello World!
$
```

A program line in a Ruby program does not have to end with a semicolon.
The Ruby interpreter is even so intelligent that is recognizes if a
program line was split over two or more lines for the sake of
readability. I will spare you the corresponding examples and am only
mentioning this so you don't say or think later, "is it okay like this?"

Indenting code is also not necessary. But it does make it much easier to
read for human beings!

### puts and print

If you go looking for examples on Ruby on the Internet, you will find
two typical ways of printing text on the screen:

-   `puts`

    Prints a string, followed by a newline.

-   `print`

    Prints a string (without newline). If the string itself contains a
    newline, this will be printed as normal.

Example program (an extension of the program `hello-world.rb`):

```ruby
puts 'Hello World!'
puts
puts '------------'
print 'Hello World!'
print
puts '------------'
```

On the screen, you will see this:

```bash
$ ruby hello-world.rb
Hello World!

------------
Hello World!------------
$
```

### Comments

comments
Ruby
A comment in a Ruby program starts with a `#`-sign and ends with a
newline. As an example, I can add a comment to the `hello-world.rb`
above:

```ruby
# Program for displaying "Hello World!"
# by Stefan Wintermeyer

puts 'Hello World!'
```

A comment can also follow a program line:

```ruby
# Program for displaying "Hello World!"
# by Stefan Wintermeyer

puts 'Hello World!'  # output
```

A `#`-sign within strings in inverted commas is not treated as the start
of a comment. Example program:

```ruby
# Example program
# by Stefan Wintermeyer

puts 'Hello World!'
puts '############'
puts
puts '1#2#3#4#5#6#'  # Comment on this
```

On the screen, you will see this:

```bash
$ ruby hello-world.rb
Hello World!
############

1#2#3#4#5#6#
$
```

### Help via ri

ri
Ruby Index
(Ruby Help)
Ruby
ri (integrated Help)
ri
Ruby Index
ri
When programming, you do not always have a Ruby handbook available.
Fortunately, the Ruby developers thought of this and provided a built-in
help feature in form of the program `ri` (of course only if you have
installed the documentation). ri is the abbreviation for “**R**uby
**I**ndex”. The output gets opend in a so called pager (z.B. `less`).
The parameter `-T` sets the output directly to stdout.

This is a typical chicken and egg situation. How can I explain the Ruby
help feature, if we are only just getting started with Ruby? So I am
going to jump ahead a little and show you how you can search for
information on the class String:

    $
    String < Object

    ------------------------------------------------------------------------------
    Includes:
    Comparable (from ruby site)

    (from ruby site)
    ------------------------------------------------------------------------------


    Rake extension methods for String.

    A String object holds and manipulates an arbitrary sequence of bytes,
    typically representing characters. String objects may be created using
    String::new or as literals.

    Because of aliasing issues, users of strings should be aware of the methods
    that modify the contents of a String object.  Typically, methods with
    names ending in ``!'' modify their receiver, while those without a ``!''
    return a new String.  However, there are exceptions, such as
    String#[]=.
    ------------------------------------------------------------------------------
    [...]
    $

If we are looking for information on a specific method (chicken-egg!),
then we can also use `ri`. Let's take gsub as an example. This is a
method for replacing parts of a String (that is useful now and again).

    $
    = String.size

    (from ruby site)
    ------------------------------------------------------------------------------
      str.size     -> integer

    ------------------------------------------------------------------------------

    Returns the character length of str.

    $

The program `ri` always prints the output in the pager program defined
by the shell (for example `less`). You can also use the command option
`-T` to output everything directly to STDOUT. In the book I am going to
be using both variations.

irb
---

irb
Interactive Ruby
(Ruby shell, Ruby console)
Ruby shell
irb
Interactive Ruby
irb
irb stands for “**I**nteractive **R**u**b**y” and is a kind of sandbox
where you can play around with Ruby at your leisure. irb is launched by
entering `irb` on the shell and ends if you enter `exit`.

An example is worth a thousand words:

    $
    >>
    Hello World!
    => nil
    >>
    $

> **Note**
>
> I use `irb --simple-prompt` to generate shorter lines which make it
> easier to read this book on mobile devises.

In this chapter, we develop many examples within irb. It is so
wonderfully quick and easy.

### Comments in irb

Having comments within `irb` would obviously rarely make sense because
code developed in `irb` is more or less code for the bin. But within
this book, we occasionally use comments even in `irb` to make things
clearer. You can copy these comments along with everything else and
insert them into the `irb` without any problems, or you can simply omit
them.

Ruby is Object-Oriented
-----------------------

Ruby only knows objects. Everything is an object (sounds almost like
Zen). Every object is an instance of a class. You can find out the class
of an object via the method .class.

An object in Ruby is encapsulated and can only be reached from the
outside via the methods of the corresponding object. What does this
mean? I cannot change any property of an object directly from the
outside. The corresponding object has to offer a method with which I can
do so.

> **Note**
>
> Please do not panic if you have no idea what a class and an object is.
> I won't tell anyone and you can still work with it just fine without
> worrying too much. This topic alone could fill whole volumes. Roughly
> speaking, an object is a container for something and a method changes
> something in that container.
>
> Please go on reading and have a look at the examples. The puzzle will
> gradually get clearer.

### Methods

methods
Ruby
In other programming languages, the terms you would use for Ruby methods
would be: functions, procedures, subroutines and of course methods.

> **Note**
>
> Here we go with the oversimplification. We can not compare non-Object
> oriented programming languages with OO onces. Plus there are two kinds
> of methods (class methods and instance methods). At this point, I do
> not want to make it too complicated and am simply ignoring this "fine"
> distinctions for now.

At this point you start looking for a good example, but all you can
think of are silly ones. The problem is the premisse that we are only
allowed to use knowledge that has already been described previously in
this book.

So let's assume that we use the following code sequence repeatedly - for
whatever reason:

    $
    >>
    Hello World!
    => nil
    >>
    Hello World!
    => nil
    >>
    Hello World!
    => nil
    >>
    $

So we want to output the string “Hello World!” three times in a row. As
this makes our daily work routine much longer, we are now going to
define a method (with the meaningless name three\_times), with which
this can all be done in one go.

> **Important**
>
> Names of methods are always written in lower case.

    $
    >>
    >>
    >>
    >>
    >>
    => nil
    >>
    Hello World!
    Hello World!
    Hello World!
    => nil
    >>
    $

When defining a method, you can define required parameters and use them
within the method. This enables us to create a method to which we pass a
string as parameter and we can then output it three times.

    $
    >>
    >>
    >>
    >>
    >>
    => nil
    >>
    Hello World!
    Hello World!
    Hello World!
    => nil
    >>

Incidentally, you can omit the brackets when calling the method.

    >>
    Hello World!
    Hello World!
    Hello World!
    => nil
    >>

> **Tip**
>
> Ruby gurus and would-be gurus are going to turn up their noses on the
> subject of “unnecessary” brackets in your programs and will probably
> pepper you with more or less stupid comments with comparisons to Java
> and other programming languages.
>
> There is one simple rule in the Ruby community: the fewer brackets,
> the cooler you are! `;-)`
>
> But you won't get a medal for using fewer brackets. Decide for
> yourself what makes you happy.

If you do not specify a parameter with the above method, you will get
the error message: “`wrong number of arguments (0 for
      1)`”:

    >>
    ArgumentError: wrong number of arguments (0 for 1)
     from (irb):1:in `three_times'
     from (irb):7
     from /Users/xyz/.rvm/rubies/ruby-2.0.0-p247/bin/irb:16:in `<main>'
    >>
    $

You can give the variable `value` a default value and then you can also
call the method without parameter:

    $
    >>
    >>
    >>
    >>
    >>
    => nil
    >>
    Hello World!
    Hello World!
    Hello World!
    => nil
    >>
    blue
    blue
    blue
    => nil
    >>
    $

### Classes

classes
Ruby
class
classes in Ruby
A class is a collection of methods. The name of a class always starts
with an upper case letter. Let's assume that the method belongs to the
new class This\_and\_that. It would then be defined as follows in a Ruby
program:

    class This_and_that
      def three_times
        puts 'Hello World!'
        puts 'Hello World!'
        puts 'Hello World!'
      end
    end

Let's play it through in `irb`:

    $
    >>
    >>
    >>
    >>
    >>
    >>
    >>
    => nil
    >>

Now we try to call the method three\_times:

    >>
    NoMethodError: undefined method `three_times' for This_and_that:Class
     from (irb):8
     from /Users/xyz/.rvm/rubies/ruby-2.0.0-p247/bin/irb:16:in `<main>'
    >>

This results in an error message, because This\_and\_that is a class and
not an instance. As we are working with instance methods, it only works
if we have first created a new object (a new instance) of the class
This\_and\_that with the class method new:

    >>
    => #<This_and_that:0x007f819412c768>
    >>
    Hello World!
    Hello World!
    Hello World!
    => nil
    >>
    $

I will explain the difference between instance and class methods in more
detail in ?. Another chicken and egg problem.

#### Private Methods

methods
private, Ruby
Quite often it makes sense to only call a method within its own class or
own instance. Such methods are referred to as private methods (as
opposed to public methods), and they are listed below the keyword
`private` within a class.

irb example:

    $
    >>
    >>
    >>
    >>
    >>
    >>
    >>
    >>
    >>
    => nil
    >>
    => #<Example:0x007ff1e10756b8>
    >>
    a
    => nil
    >>
    NoMethodError: private method `b' called for #<Example:0x007ff1e10756b8>
     from (irb):12
     from /Users/xyz/.rvm/rubies/ruby-2.0.0-p247/bin/irb:16:in `<main>'
    >>
    $

#### Method initialize()

initialize()
classes in Ruby
new()
classes in Ruby
initialize()
constructor
classes in Ruby
initialize()
If a new instance is created (by calling the method new), the method
that is processed first and automatically is the method initialize. The
method is automatically a private method, even if it not listed
explicitly under `private`.

irb example:

    $
    >>
    >>
    >>
    >>
    >>
    => nil
    >>
    abc
    => #<Room:0x007faec50402d8>
    >>
    $

The instance `kitchen` is created with `Room.new` and the method
initialize is processed automatically.

The method new accepts the parameters specified for the method
initialize:

    $
    >>
    >>
    >>
    >>
    >>
    => nil
    >>
    Hello World!
    => #<Example:0x007fa9f98ba240>
    >>
    $

#### return

return
puts is nice to demonstrate an example in this book but normally you
need a way to return the result of something. The `return` statement can
be used for that:

    $
    >>
    >>
    >>
    >>
    >>
    => nil
    >>
    => 314.0
    >>
    $

But it wouldn't be Ruby if you couldn't do it shorter. You can simply
skip return:

    $
    >>
    >>
    >>
    >>
    >>
    => nil
    >>
    => 314.0
    >>
    $

You can actually even skip the last line because Ruby returns the value
of the last expression as a default:

    $
    >>
    >>
    >>
    >>
    => nil
    >>
    => 314.0
    >>
    $

`return` is sometimes useful to make a method easier to read. But you
don't have to use it in case you feel more comfortable with out.

#### Inheritance

inheritance
classes in Ruby
A class can inherit from another class. When defining the class, the
parent class must be added with a `<` (smaller than) sign:

    class Example < ParentClass

Rails makes use of this approach very frequently (otherwise I would not
be bothering you with it).

In the following example, we define the class Abc and which contains the
methods a, b and c. Then we define a class Abcd and let it inherit the
class Abc and add a new method d. The new instances `example1` and
`example2` are created with the Class-Methods new and show that
`example2` has access to the methods a, b, c and d but `example1` only
to a, b and c.

    $
    >>
    >>
    >>
    >>
    >>
    >>
    >>
    >>
    >>
    >>
    >>
    => nil
    >>
    >>
    >>
    >>
    >>
    => nil
    >>
    => #<Abc:0x007fb463023928>
    >>
    => #<Abcd:0x007fb46302f020>
    >>
    => "d"
    >>
    => "a"
    >>
    NoMethodError: undefined method `d' for #<Abc:0x007fb463023928>
     from (irb):21
     from /Users/xyz/.rvm/rubies/ruby-2.0.0-p247/bin/irb:16:in `<main>'
    >>
    => "a"
    >>
    $

> **Tip**
>
> It is important to read the Error-Messages. They tell you what happend
> and where to search for the problem. In this example Ruby said that
> there is an `` undefined method `d' for
>             #<Abc:0x007fb463023928> ``. With that information you know
> that the Class Abc is missing the method d which you were trying to
> use.

#### Class Methods and Instance Methods

class methods and instance methods
Ruby
There are two important kinds of methods: class methods and instance
methods.

You now already know what a class it. And an instance of such a class is
created via the class method new. A class method can only be called in
connection with the class (for example, the method new is a class
method). An instance method is a method that only works with an
instance. So you cannot apply the method new to an instance.

Let's first try to call an instance method as class method:

    $
    >>
    >>
    >>
    >>
    >>
    => nil
    >>
    NoMethodError: undefined method `pi' for Knowledge:Class
     from (irb):6
     from /Users/xyz/.rvm/rubies/ruby-2.0.0-p247/bin/irb:16:in `<main>'
    >>

So that does not work. Well, then let's create a new instance of the
class and try again:

    >>
    => #<Knowledge:0x007fd81b0866f0>
    >>
    => 3.14
    >>
    $

Now we just need to find out how to define a class method. Hardcore
Rails gurus would now whisk you away into the depths of the source code
and pick out examples from the ActiveRecord. I will spare you this and
show an abstract example:

    $
    >>
    >>
    >>
    >>
    >>
    => nil
    >>
    => 3.14
    >>

And the proof to the contrary:

    >>
    => #<Knowledge:0x007fb339078d90>
    >>
    NoMethodError: undefined method `pi' for #<Knowledge:0x007fb339078d90>
     from (irb):8
     from /Users/xyz/.rvm/rubies/ruby-2.0.0-p247/bin/irb:16:in `<main>'
    >>
    $

There are different notations for defining class methods. The two most
common ones are:

-   `self.xyz`

        # Variant 1
        # with self.xyz
        #
        class Knowledge
          def self.pi
            3.14
          end
        end

-   `class << self`

        # Variant 2
        # with class << self
        #
        class Knowledge
          class << self
            def pi
              3.14
            end
          end
        end

The result is always the same.

Of course you can use the same methodname for a class and an instance
method. Obviously that doesn't make any code easier to understand. Here
is an example with pi as a class and an instance method:

    $
    >>
    >>
    >>
    >>
    >>
    >>
    >>
    >>
    => nil
    >>
    => 3.14159265359
    >>
    => #<Knowledge:0x007f8ccc8786a0>
    >>
    => 3.14
    >>
    $

##### List of All Instance Methods

instance methods
Ruby
instance\_methods()
instance methods in Ruby
You can read out all defined methods for a class with the method
instance\_methods. We try it out with the class This\_and\_that (first
we create it once again in the irb):

    $
    >>
    >>
    >>
    >>
    >>
    => nil
    >>
    => [:pi, :nil?, :===, :=~, :!~, :eql?, :hash, :<=>, :class, :singleton_class, :clone, :dup, :initialize_dup, :initialize_clone, :taint, :tainted?, :untaint, :untrust, :untrusted?, :trust, :freeze, :frozen?, :to_s, :inspect, :methods, :singleton_methods, :protected_methods, :private_methods, :public_methods, :instance_variables, :instance_variable_get, :instance_variable_set, :instance_variable_defined?, :instance_of?, :kind_of?, :is_a?, :tap, :send, :public_send, :respond_to?, :respond_to_missing?, :extend, :display, :method, :public_method, :define_singleton_method, :object_id, :to_enum, :enum_for, :==, :equal?, :!, :!=, :instance_eval, :instance_exec, :__send__, :__id__]
    >>

But that is much more than we have defined! Why? It's because Ruby gives
every new class a basic set of methods by default. If we only want to
list the methods that we have defined, then we can do it like this:

    >>
    => [:pi]
    >>
    $

Variables
---------

variables
Ruby
You already know that everything in Ruby is an object. So a variable
must also be an object.

### Naming Conventions

Normal variables are written in lower case. constants RubyConstants
start with an upper case letter.

> **Important**
>
> A constant can also be overwritten with a new value in Ruby (but you
> will get a warning message). So please do not rely on the constancy of
> a constant.

You are on the safe side if you are using only ASCII symbols. But with
Ruby 1.9 and the right encoding, you could also use special characters
(for example German umlauts) more or less without any problems in a
variable name. But if you want to be polite towards other programmers
who probably do not have those characters directly available on their
keyboards, it is better to use only pure ASCII.

### Strings

strings
Variables
Let's experiment a little bit in the `irb`. The method .class tells us
which class we are dealing with.

    $
    >>
    => "First test"
    >>
    => String

That was easy. As you can see, Ruby “automagically” creates an object of
the class String. We could also do this by explicitly calling the method
new:

    >>
    => "Second test"
    >>
    => String

If we call `String.new` without a parameter, this also creates an object
of the class String. But it is an empty String:

    >>
    => ""
    >>
    => String
    >>
    $

#### Single and Double Quotations Marks

quotation marks
for strings in Ruby
quotes
for strings in Ruby
quotation marks
> **Note**
>
> Please note: if we mention single or double quotation marks in the
> context of strings, we do not mean typographically correct curly
> quotation marks (see <http://en.wikipedia.org/wiki/Quotation_mark>),
> but the ASCII symbols referred to as *apostrophe* (`'`) or *quotation
> mark* (`"`).

Strings can be defined either in single quotes or double quotes. There
is a special feature for the double quotes: you can integrate
expressions with the construct `#{}`. The result is then automatically
inserted in the corresponding place in the string.

Example:

    $
    >>
    => "blue"
    >>
    => "Color: blue"
    >>
    => String
    >>
    $

If the result of the expression is not a string, Ruby tries to apply the
method to\_s in order to convert the value of the object into a string.

### Integers

integer
Variables
#### Fixnum and Bignum

fixnum
Variables
bignum
Variables
Fixnum and Bignum are Integer classes. A Fixnum is an Integer that can
be saved in a “Word”.[^2]If a Fixnum gets bigger, it automatically
becomes a Bignum. Here is an example where a becomes larger and by that
becomes a Bignum.

    $
    >>
    => Fixnum
    >>
    => 20
    >>
    => Fixnum
    >>
    => 111111111100
    >>
    => Fixnum
    >>
    => 617283950493827160500
    >>
    => Bignum
    >>
    $

#### Floats

float
Variables
Float is a class for real numbers (“floating point numbers”). The
decimal separator is a point (not a comma, as in some European
countries).

    $
    >>
    => 20.424
    >>
    => Float
    >>
    => Float
    >>
    $

#### Simple Calculations

Calculating with integers and floats is so easy that you can describe
everything with just a few lines of code in the `irb`:

    $
    >>
    => 10
    >>
    => 23
    >>
    => 33
    >>
    => Fixnum
    >>
    => 33
    >>
    => Fixnum
    >>
    => 3.14
    >>
    => 13.14
    >>
    => Float
    >>
    => 230
    >>
    => Fixnum
    >>
    => Float
    >>
    $

### Boolean Values and nil

boolean
Variables
true
Variables
false
Variables
nil
Variables
null
Variables
nil
For boolean values (`true` and `false`) and for `nil` (no value) there
are separate classes:

    $
    >>
    => TrueClass
    >>
    => FalseClass
    >>
    => NilClass
    >>
    $

`nil` (no value [^3]) is, by the way, the contraction of the Latin word
*nihil* (nothing [^4]) or, if you look at it in terms of programming
history, the term derives from “*not in list*” from the legacy of the
programming language Lisp (the name is an acronym of *List Processing*).

### Scope of Variables

scope
Variables
Variables have a different scope (or “reach”) within the Ruby
application and therefore also within a Ruby on Rails application.

> **Important**
>
> You need to keep this scope in mind while programming. Otherwise you
> can end up with odd effects.

#### Local Variables (aaa or \_aaa)

local variables
Ruby
variables
Ruby
local
Local variables either start with a lower case letter or an underscore
(`_`). Their scope is limited to the current environment (for example
the current method). The follwing example defines two methods which use
the same local variable radius. Because they are local they don't
interact with each other:

    $
    >>
    >>
    >>
    => nil
    >>
    >>
    >>
    => nil
    >>
    => 314.0
    >>
    => 6.28
    >>
    $

#### Global Variables (\$aaa)

global variables
Ruby
variables
Ruby
global
A global variable starts with a `$`-sign and is accessible in the entire
programm. Example:

    $
    >>
    => 10
    >>
    10
    => nil
    >>
    >>
    >>
    => nil
    >>
    10
    => nil
    >>
    => 20
    >>
    20
    => nil
    >>
    $

Global variables are used very rarely.

#### Instance Variables (@aaa)

instance variables
Ruby
variables
Ruby
instance variables
Instance variables (“**A**ttributes”, hence the `@`) only apply within a
class, but everywhere in it – a mini version of global variables, so to
speak. Unlike global variables, you will find instance variables all
over the place in a Rails application. Let's tackle them in form of an
example program with the name `color.rb`:

    class Wall
      def initialize
        @color = 'white'
      end

      def color
        @color
      end

      def paint_it(value)
        @color = value
      end
    end

    my_wall = Wall.new
    puts my_wall.color

    my_wall.paint_it('red')
    puts my_wall.color

If you start this program, the following output will appear:

    $  
    white
    red
    $

In the method initialize()initialize we set the instance variable
`@color` to the value “white”. The method paint\_it(value) changes this
instance variable.

With the method color we can access the value of `@color` outside of the
instance. This kind of method is called a setter method.

Methods Once Again
------------------

In order to keep the amount of chicken and egg problems in this chapter
at a manageable level, we need to go back to the topic Methods and
combine what we have learned so far.

### Getters and Setters

getter
Ruby
setter
Ruby
As instance variables Rubyinstance variables (“attributes”) only exist
within the relevant instance, you always need to write a “getter” method
for exporting such a variable. If we define a class Room that has the
instance variables `@doors` and `@windows` (for the number of doors and
windows in the room), then we can create the getter methods doors und
windows (example program `room.rb`):

    class Room
      def initialize
        @doors  = 1
        @windows = 1
      end

      def doors
        @doors
      end

      def windows
        @windows
      end
    end

    kitchen = Room.new

    puts "D: #{kitchen.doors}"
    puts "W: #{kitchen.windows}"

The execution of the program:

    $  
    D: 1
    W: 1
    $

As this scenario – wanting to simply return a value in identical form –
is so common, there is already a ready-made getter method for it with
the name attr\_reader()attr\_reader, which you would apply as follows in
the program `room.rb`:

    class Room
      def initialize
        @doors  = 1
        @windows = 1
      end

      attr_reader :doors, :windows
    end

    kitchen = Room.new

    puts "D: #{kitchen.doors}"
    puts "W: #{kitchen.windows}"

attr\_reader is a method which is called on the Room class. That is the
reason why we use Symbols (e.g. :doors and :windows) instead of
variables (e.g. @doors and @windows) as parameter.

> **Note**
>
> attr\_reader is a good example for meta programming in Ruby. When
> working with Rails, you will frequently come across meta programming
> and be grateful for how it works automagically.

If you want to change the number of doors or windows from the outside,
you need a “setter” method. It can be implemented as follows:

    class Room
      def initialize
        @doors  = 1
        @windows = 1
      end

      attr_reader :doors, :windows

      def doors=(value)
        @doors = value
      end

      def windows=(value)
        @windows = value
      end
    end

    kitchen = Room.new

    kitchen.windows = 2

    puts "D: #{kitchen.doors}"
    puts "W: #{kitchen.windows}"

The corresponding output is this:

    $  
    D: 1
    W: 2
    $

As you can probably imagine, there is of course also a ready-made and
easier way of doing this. Via the setter method
attr\_writer()attr\_writer you can simplify the code of `room.rb`
further:

    class Room
      def initialize
        @doors  = 1
        @windows = 1
      end

      attr_reader :doors, :windows
      attr_writer :doors, :windows
    end

    kitchen = Room.new

    kitchen.windows = 2

    puts "D: #{kitchen.doors}"
    puts "W: #{kitchen.windows}"

And (who would have thought!) there is even a method
attr\_accessor()attr\_accessor that combines getters and setters. The
code for `room.rb` would then look like this:

    class Room
      def initialize
        @doors  = 1
        @windows = 1
      end

      attr_accessor :doors, :windows
    end


    kitchen = Room.new

    kitchen.windows = 2

    puts "D: #{kitchen.doors}"
    puts "W: #{kitchen.windows}"

### Built-In Methods for String

Most classes already come with a bundle of very useful methods. These
methods are always written after the relevant object, separated by a
point.

Here are a few examples for methods of the class String.

    $
    >>
    => "A dog"
    >>
    => String
    >>
    => 5
    >>
    => "a dog"
    >>
    => "A DOG"
    >>
    => "god A"
    >>
    $

With instance\_methods(false) you can get a list of the build in
methods:

    $
    >>
    => [:<=>, :==, :===, :eql?, :hash, :casecmp, :+, :*, :%, :[], :[]=, :insert, :length, :size, :bytesize, :empty?, :=~, :match, :succ, :succ!, :next, :next!, :upto, :index, :rindex, :replace, :clear, :chr, :getbyte, :setbyte, :byteslice, :to_i, :to_f, :to_s, :to_str, :inspect, :dump, :upcase, :downcase, :capitalize, :swapcase, :upcase!, :downcase!, :capitalize!, :swapcase!, :hex, :oct, :split, :lines, :bytes, :chars, :codepoints, :reverse, :reverse!, :concat, :<<, :prepend, :crypt, :intern, :to_sym, :ord, :include?, :start_with?, :end_with?, :scan, :ljust, :rjust, :center, :sub, :gsub, :chop, :chomp, :strip, :lstrip, :rstrip, :sub!, :gsub!, :chop!, :chomp!, :strip!, :lstrip!, :rstrip!, :tr, :tr_s, :delete, :squeeze, :count, :tr!, :tr_s!, :delete!, :squeeze!, :each_line, :each_byte, :each_char, :each_codepoint, :sum, :slice, :slice!, :partition, :rpartition, :encoding, :force_encoding, :valid_encoding?, :ascii_only?, :unpack, :encode, :encode!, :to_r, :to_c]
    >>
    $

If you are not sure what one of these methods does you can use `ri` to
look it up:

    $
    String.size

    (from ruby site)
    ------------------------------------------------------------------------------
      str.length   -> integer
      str.size     -> integer


    ------------------------------------------------------------------------------

    Returns the character length of str.


    $

### Method Chaining

methods
in Ruby chaining
method chaining
Ruby
You may not think of it straight away, but once you have got used to
working with Ruby, then it makes perfect sense (and is perfectly
logical) to chain different methods.

    $
    >>
    => "A dog"
    >>
    => "GOD A"
    >>
    $

### Converting from One to the Other: Casting

casting
of objects in Ruby
There is a whole range of useful instance methods for converting
(“casting”) objects from one class to another. First, let's use the
method to\_s() Ruby.to\_s to convert a Fixnum to a String.

    $
    >>
    => 10
    >>
    => Fixnum
    >>
    => "10"
    >>
    => String
    >>
    $

> **Note**
>
> Incidentally, that is exactly what `puts` does if you use `puts` to
> output a Fixnum or a Float (for non-strings, it simply implicitly adds
> the method .to\_s and outputs the result).

Now we use the method to\_i() Ruby.to\_i to change a Float to a Fixnum.

    $
    >>
    => 10.0
    >>
    => Float
    >>
    => 10
    >>
    => Fixnum
    >>
    $

### Method to\_s for Your Own Classes

to\_s()
Ruby
You should always integrate a method to\_sfor your own custom classes,
even if it is just for the sake of easier debugging. Then you can simply
output a corresponding object via `puts` (`puts` automatically outputs
an object via the method to\_s).

Here is an example:

    $
    >>
    >>
    >>
    >>
    >>
    >>
    >>
    >>
    >>
    => nil
    >>
    => Stefan Wintermeyer
    >>
    Stefan Wintermeyer
    => nil
    >>
    $

### Is “+” a Method?

Why is there also a plus symbol in the list of methods for String? Let's
find out by looking it up in `ri`:

    $
    String.+

    (from ruby site)
    ------------------------------------------------------------------------------
      str + other_str   -> new_str


    ------------------------------------------------------------------------------

    Concatenation---Returns a new String containing other_str
    concatenated to str.

      "Hello from " + self.to_s   #=> "Hello from main"


    $

hmmm … Let's see what it says for Fixnum:

    $
    Fixnum.+

    (from ruby site)
    ------------------------------------------------------------------------------
      fix + numeric  ->  numeric_result


    ------------------------------------------------------------------------------

    Performs addition: the class of the resulting object depends on the class of
    numeric and on the magnitude of the result.


    $

Let's have a go and play around with this in `irb`. So we should be able
to add the + to an object, just as any other method, separated by a dot
and add the second number in brackets as parameter:

    $
    >>
    => 20
    >>
    => 20
    >>
    => 20
    >>
    => 20
    >>
    $

Aha! The plus symbol is indeed a method, and this method takes the next
value as parameter. Really we should put this value in brackets, but
thanks to Ruby's well thought-out syntax this is not necessary.

#### Can I Overwrite the Method +?

Yes, you can overwrite any method. Logically, this does not make much
sense for methods such as +, unless you want to drive your fellow
programmers mad. I am going to show you a little demo in `irb` so you
will believe me.

The aim is overwriting the method + for Fixnum. We want the result of
every addition to be the number 42.

    $
    >>
    => 20
    >>
    >>
    >>
    >>
    >>
    => nil
    >>
    => 42
    >>
    $

First we perform a normal addition. Than we redefine the method + for
the class Fixnum, and after that we do the calculation again. But this
time, with different results.

if-Condition
------------

if
condition in Ruby
conditional statements
Ruby
An abstract `if`-condition looks like this:

    if expression
      program
    end

The program between the expression and `end` is executed if the result
of the expression is not `false` and not `nil`.

> **Note**
>
> You can also use a `then` after the expression:
>
>     if expression then
>       program
>     end

The construct for a simple `if`-branch in a Ruby program looks like the
following example program:

    a = 10

    if a == 10
      puts 'a is 10'
    end

> **Note**
>
> The '==' is used to compare two values. Please don't mix it up with
> the single '='.

You can try an *expression* really well in `irb`:

    $
    >>
    => 10
    >>
    => true
    >>
    $

### Shorthand

if
condition in Ruby (shorthand)
A frequently used shorthand notation of an `if`-condition can be found
in the following code:

    a = 10

    # long version
    #
    if a == 10 then
      puts 'a is 10'
    end

    # short version
    #
    puts 'a is 10' if a == 10

### else

else
condition in Ruby (shorthand)
You can probably imagine how this works, but for the sake of
completeness, here is a little example:

    a = 10

    if a == 10 then
      puts 'a is 10'
    else
      puts 'a is not 10'
    end

### elsif

elsif
conditon in Ruby (shorthand)
Again, most programmers will know what this is all about. Example:

    a = 10

    if a == 10 then
      puts 'a is 10'
    elsif a == 20 then
      puts 'a is 20'
    end

Loops
-----

loops
Ruby
There are different ways of implementing loops in Ruby. The iterator
variation is used particularly often in the Rails environment.

### while and until

loops
Ruby
while
loops
Ruby
until
while
loops in Ruby
until
loops in Ruby
An abstract while loop looks like this:

    while expression do
      program
    end

> **Note**
>
> The `do` that follows the `expression` is optional. Often you will
> also see this:
>
>     while expression
>       program
>     end

Here is a practical `irb` example:

    $
    >>
    => 0
    >>
    ?>
    >>
    >>
    0
    1
    2
    => nil
    >>
    $

Until loops are built similarly:

    until expression
      program
    end

Again, here is the corresponding `irb` example:

    $
    >>
    => 5
    >>
    >>
    >>
    >>
    4
    3
    2
    1
    0
    => nil
    >>
    $

### Blocks and Iterators

blocks
Ruby
iterators
Ruby
“Block” and “iterator” are some of the favorite words of many Ruby
programmers. Now I am going to show you why.

In the loop

    5.times { |i| puts i }

`i` is the iterator and `puts i` is the block.

You can also express the whole thing in the following syntax:

    5.times do |i|
      puts i
    end

#### Iterators

iterators
Ruby
Iterators are just a specific type of method. As you probably know, the
word “*iterate*” means to repeat something. For example, the class
Fixnum has the iterator times() Rubytimes. Let's see what help `ri`
offers us:

    $
    Fixnum.times

    (from ruby site)
    Implementation from Integer
    ------------------------------------------------------------------------------
      int.times {|i| block }  ->  self
      int.times               ->  an_enumerator


    ------------------------------------------------------------------------------

    Iterates block int times, passing in values from zero to int -
    1.

    If no block is given, an enumerator is returned instead.

      5.times do |i|
        print i, " "
      end

    produces:

      0 1 2 3 4


    $

And it also gives a nice example that we are going to try out in irb:

    $
    >>
    ?>
    >>
    0
    1
    2
    3
    4
    => 5
    >>
    $

There is also a single-line notation for small blocks:

    $
    >>
    0
    1
    2
    3
    4
    => 5
    >>
    $

By the way, an iterator does not necessarily have to pass a variable to
the block:

    $
    >>
    example
    example
    example
    example
    example
    => 5
    >>
    $

#### Blocks

blocks
Ruby
A block is the code that is triggered by an iterator. In the block, you
have access to the local variable(s) passed by the iterator.

#### Method upto

Apart from times there is also the method upto() Rubyupto, for easily
implementing a loop. `ri` offers a nice example for this, too:

    $
    Fixnum.upto

    (from ruby site)
    Implementation from Integer
    ------------------------------------------------------------------------------
      int.upto(limit) {|i| block }  ->  self
      int.upto(limit)               ->  an_enumerator


    ------------------------------------------------------------------------------

    Iterates block, passing in integer values from int up to and
    including limit.

    If no block is given, an enumerator is returned instead.

      5.upto(10) { |i| print i, " " }

    produces:

      5 6 7 8 9 10


    $

Arrays and Hashes
-----------------

arrays
Ruby
hashes
Ruby
As in many programming languages, *arrays* and *hashes* are popular
structures in Ruby for storing data.

### Arrays

arrays
Ruby
An array is a list of objects. Let's play around in `irb:`

    $
    >>
    => [1, 2, 3, 4, 5]
    >>
    => Array
    >>
    $

That is simple and easy to understand.

Let's see if it also works with strings in the array:

    $
    >>
    => ["Test", "Banana", "blue"]
    >>
    => Array
    >>
    => "Banana"
    >>
    => String
    >>
    $

That also works.

So all that's missing now is an array with a mixture of both. Obviously
that will work, too, because the array stores objects and it does not
matter which kind of objects they are (i.e. String, Fixnum, Float, …).
But a little test can't hurt:

    $
    >>
    => [1, 2.2, "House", nil]
    >>
    => Array
    >>
    => 1
    >>
    => Fixnum
    >>
    => "House"
    >>
    => String
    >>
    $

Next, let's have a look at what the `ri` help page says for Array:

    $
    Array < Object

    ------------------------------------------------------------------------------
    Includes:
    Enumerable (from ruby site)

    (from ruby site)
    ------------------------------------------------------------------------------
    Arrays are ordered, integer-indexed collections of any object. Array indexing
    starts at 0, as in C or Java.  A negative index is assumed to be relative to
    the end of the array---that is, an index of -1 indicates the last element of
    the array, -2 is the next to last element in the array, and so on.
    ------------------------------------------------------------------------------
    Class methods:

      []
      new
      try_convert

    Instance methods:

      &
      *
      +
      -
      <<

    [...]

    $

As you can see, arrays can also be created via the method new (like any
class). Individual new elements can then be added at the end of an array
via the method \<\<. Here is the corresponding example:

    $
    >>
    => []
    >>
    => ["first item"]
    >>
    => ["first item", "second item"]
    >>
    $

#### Iterator each

iterators
Ruby
each()
You can work your way through an array piece by piece via the method
each() Rubyeach. Example:

    $
    >>
    => ["eggs", "butter"]
    >>
    ?>
    >>
    eggs
    butter
    => ["eggs", "butter"]
    >>
    $

Once more, `ri` provides help and an example in case you forget how to
use each:

    $
    Array.each

    (from ruby site)
    ------------------------------------------------------------------------------
      ary.each {|item| block }   -> ary
      ary.each                   -> an_enumerator


    ------------------------------------------------------------------------------

    Calls block once for each element in self, passing that element
    as a parameter.

    If no block is given, an enumerator is returned instead.

      a = [ "a", "b", "c" ]
      a.each {|x| print x, " -- " }

    produces:

      a -- b -- c --


    $

### Hashes

hashes
Ruby
A “*Hash*” is a list of *key/value pairs*. Here is an example with
strings as keys:

    $
    >>
    => {"egg"=>0.1, "butter"=>0.99}
    >>
    => 0.1
    >>
    => 2
    >>
    $

Of course, hashes can store not just strings as objects in the values,
but - as with arrays - also classes that you define yourself (see ?).

#### Symbols

symbols
Ruby
*Symbols* are a strange concept and difficult to explain. But they are
very useful and used frequently, amongst others with hashes. Normally,
variables always create new objects:

    $
    >>
    => "Example 1"
    >>
    => 70260036330560
    >>
    => "Example 2"
    >>
    => 70260036391520
    >>
    $

In both cases, we have the variable `a`, but object ID is different. We
could carry on in this way indefinitely. Each time, it would generate a
different object ID and therefore a new object. In principle, this is no
big deal and entirely logical in terms of object orientation. But it is
also rather a waste of memory space.

A symbol is defined by a colon before the name and cannot store any
values itself, but it always has the same object ID, so it is very well
suited to be a *key*:

    $
    >>
    => Symbol
    >>
    => 413928
    >>
    $

Let's do another little experiment to make the difference clearer. We
use a string object with the content “`white`” three times in a row and
then the symbol `:white` three times in a row. For `"white"`, a new
object is created each time. For the symbol `:white`, only the first
time:

    $
    >>
    => 70209583052020
    >>
    => 70209583082240
    >>
    => 70209583088920
    >>
    => 413928
    >>
    => 413928
    >>
    => 413928
    >>
    $

Using symbols as key for hashes is much more memory efficient:

    $
    >>
    => {:black=>"#000000", :white=>"#FFFFFFFF"}
    >>
    #FFFFFFFF
    => nil
    >> exit
    $

You will frequently see symbols in Rails. If you want to find out more
about symbols, go to the help page about the class Symbol via
`ri Symbol`.

#### Iterator each

iterators
Ruby
each()
With the method each() Rubyeach you can work your way through a Hash
step by step. Example:

    $
    >>
    => {:black=>"#000000", :white=>"#FFFFFFFF"}
    >>
    ?>
    >>
    black #000000
    white #FFFFFFFF
    => {:black=>"#000000", :white=>"#FFFFFFFF"}
    >>
    $

Again, `ri` offers help and an example, in case you cannot remember one
day how to use each:

    $
    Hash.each

    (from ruby site)
    ------------------------------------------------------------------------------
      hsh.each      {| key, value | block } -> hsh
      hsh.each_pair {| key, value | block } -> hsh
      hsh.each                              -> an_enumerator
      hsh.each_pair                         -> an_enumerator


    ------------------------------------------------------------------------------

    Calls block once for each key in hsh, passing the key-value pair
    as parameters.

    If no block is given, an enumerator is returned instead.

      h = { "a" => 100, "b" => 200 }
      h.each {|key, value| puts "#{key} is #{value}" }

    produces:

      a is 100
      b is 200


    $

Miscellaneous
-------------

### Range

range
The class Range represents an interval. The start and end points of the
interval are defined enclosed in normal brackets and separated by two
dots in between them. Here is an example in which we use a range like an
iterator with each:

    $
    >>
    => 0..3
    >>
    => Range
    >>
    ?>
    >>
    0
    1
    2
    3
    => 0..3
    >>

Via the method to\_a you can generate an array from a Range:

    >>
    => [0, 1, 2, 3]
    >>

A range can be generated from objects of any type. Important is only
that the objects can be compared via \<=\> and use the method succ for
counting on to the next value. So you can also use Range to represent
letters:

    >>
    => ["a", "b", "c", "d", "e", "f", "g", "h"]
    >>

As alternative notation, you may sometimes come across Range.new(). In
this case, the start and end points are not separated by two dots, but
by a comma. This is what it looks like:

    >>
    => true
    >>
    $
