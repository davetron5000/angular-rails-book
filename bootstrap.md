# Creating a Skeleton App

Even the most basic Rails app requires additional configuration beyond simply
running `rails new`.  For example, you may have certain gems you know you'll
need from the start.

An Angular application will need even more than that, and while the setup we're
about to see can (and should) be automated with a [rails app template][rails-app-template],
it's important to see these steps and understand why we're doing them.

[rails-app-template]: http://technology.stitchfix.com/blog/2014/01/06/rails-app-templates/

Our goal here is start from scratch and have an application that renders a
view containing a piece of data provided by Angular.  That will be sufficient
for us to validate that we're moving in the right direction.

The basic steps are:

1. Create an empty Rails app
2. Add some basic gems we know we'll need
3. Set up [Bower][bower] to manage our front-end dependencies
4. Write just enough code to serve content via Angular
5. Deploy to a production server to validate the asset pipeline is working

[bower]: http://bower.io

## An empty Rails app

Our app will be called “Receta”, which is Spanish for “recipe”.  We're going
to use Postgres as our database (although we won't be using anything
Postgres-specific), and we're going to skip `bundle install` for now.  Note if you would prefer to use MySQL, it should work fine
and if you plan on deploying to Heroku, the table we'll need for this app will work fine on both, so don't install Postgres
unless you really want to.

```shell
> rails new receta --skip-bundle --quiet --database=postgresql
> cd receta
```

Now that we have our empty app, let's add a few basic gems, configure the
database, and make sure we have a clean base from which to work.

## Basic gems

We're going to use RSpec and Capybara here, as well as the Selenium driver for
browser-based testing, so let's add those gems to our `Gemfile`.  RSpec comes in via the `rspec-rails` gem, which also makes using RSpec in Rails a bit simpler.  Note that we're also
pinning version 3.2.19 of the `sass` gem. Currently, a bug in sprockets and/or SASS prevents
everything from working as designed, so we need to stay on this version for now.

```diff
diff --git a/Gemfile b/Gemfile
index 90bd53a..75cb4c5 100644
--- a/Gemfile
+++ b/Gemfile
@@ -21,12 +21,18 @@ gem 'coffee-rails', '~> 4.0.0'
 # Use jquery as the JavaScript library
 gem 'jquery-rails'
 
-# Turbolinks makes following links in your web application faster. Read more: https://github.com/rails/turbolinks
-gem 'turbolinks'
-
 # Build JSON APIs with ease. Read more: https://github.com/rails/jbuilder
 gem 'jbuilder', '~> 1.2'
+gem 'sass', '3.2.19' 
+group :test, :development do
+  gem "rspec-rails", "~> 2.0"
+  gem "factory_girl_rails", "~> 4.0"
+  gem "capybara"
+  gem "database_cleaner"
+  gem "selenium-webdriver"
+end
+
 group :doc do
   # bundle exec rake doc:rails generates the API under doc/api.
   gem 'sdoc', require: false
```

Also note that for Capybara and Selenium to work, you must [install Firefox](https://www.mozilla.org/en-US/firefox/new/), as it's required for the browser-based testing to work.

Capybara and
Selenium should be unsurprising choices, as these are common means of doing
integration/acceptance/browser tests.  When creating a rich client
application, browser testing is even more important than normal, so we want to make sure
we easily have the ability to launch a browser and click around.

RSpec is not the default testing framework with Rails, although it *is* popular.
It's not required for Angular development, but I'm recommending it here because
its API is similar to that of Jasmine, which we'll be using for testing our front-end code.

This means the “shape” of your front-end tests will mirror those of your back-end tests, which will decrease your mental load as you
switch back and forth.  If you are philosophically opposed to RSpec, you are free to use MiniTest or whatever you want.

We're also removing Turbolinks, since it's designed for a different type of
application than that one you'd make with Angular.

Now, we can install our gems:

    > bundle install

Once this is done, let's set up our database so we can run the app.

    > vim config/database.yml # set the user/password for your local database
    > rake db:setup
    > rails s

Visit http://localhost:3000 to make sure your app is running, then quit the
server with `Ctrl-C`.

Now that we have the Rails side of things squared away, let's set up Bower,
which we'll  use to manage our front-end library dependencies.

## Front-end dependency management with Bower

Because Rails doesn't provide a way to manage front-end assets, such as JavaScript libraries, fonts, or CSS, the community has
taken to bundling popular packages in RubyGems.  Using the “Engines” feature of Rails, these assets can be placed in the asset
pipeline by installing a RubyGem.  Rails even does this with JQuery.

We're not going to rely on RubyGems for our assets.  We're going to use [Bower][bower]
to manage them instead.  Bower was created by Twitter specifically to manage front-end assets, and
almost every imaginable library—including the Angular modules we'll be using—is available via Bower.

[bower]: http://bower.io

The same cannot be said for RubyGems.  While it's nice that it's at least _possible_ to use
RubyGems to manage front-end assets, doing so has two problems.

First, it creates an abstraction between our `Gemfile` and our
assets that creates confusion.  What version of JQuery is bundled with Rails?  In the Rails 4.0.3 app we just created, I see
version 3.1.0 of the `jquery-rails` gem.  According to the GitHub page for that gem, it bundles JQuery 1.10.2.  This is
unnecessarily confusing.  Further, some gems don't even advertise the version of the asset they bundle.  I've even seen one that
bundles an off-release version of a JavaScript library.

The second reason is actually more important, and that's that not every front-end asset is available as a RubyGem.  This means
that we will need a second system to manage *those*.  And because we'll be using a lot more libraries and assets than in a
“normal” Rails app, we need to keep an eye on the dependencies between these libraries.  With some libraries managed as RubyGems
and some in another system, we lose that ability.

Bower can do all of this for us, and the `bower-rails` gem even provides a clean `Gemfile`-like way to declare our dependencies.

First, we have to install Bower, which is a JavaScript command-line application, that you must install using `npm`, the "Node Package Manager".  I am not
making this up.  

Installing Node and NPM depends on your operating system.  For Mac OS X, using [homebrew], it looks like this:

```shell
> brew install node
```

If you aren't using homebrew, or aren't using OS X, you'll  need to consult the [Node installation instructions][node-install].

[homebrew]: http://brew.sh/
[node-install]: http://nodejs.org/download/

Once you have NPM, install Bower as follows:

```shell
> npm install bower
```

Now that you have Bower installed, we'll install the `bower-rails` gem, which will bridge our Rails application with Bower.  Add `bower-rails` to our `Gemfile`:

    git://receta.git/Gemfile#add-bower^1..add-bower

Now, let's install it.

```shell
> bundle install
```

Bower works similarly to Bundler, and the Gem we just installed gives us a few
handy tasks:

    sh://receta#rake -T bower

Our dependencies go in a file called `Bowerfile`, located in the root directory of your project,
that looks very much like a
`Gemfile`.  The dependencies we want to bring in first will be Angular and
Twitter Bootstrap.  Bootstrap isn't required, but we're going to use it here
to keep the CSS we have to write to a minimum while still resulting in a
pleasant-enough interface.

    git://receta.git/Bowerfile#add-bowerfile

Now, we can install our dependencies via the `bower:install` Rake task:

    > rake bower:install
    bower.js files generated
    /usr/local/share/npm/bin/bower install
    bower bootstrap-sass-official#*       not-cached git://github.com/twbs/bootstrap-sass.git#*
    bower bootstrap-sass-official#*          resolve git://github.com/twbs/bootstrap-sass.git#*
    bower angular#*                           cached git://github.com/angular/bower-angular.git#1.2.13
    bower angular#*                         validate 1.2.13 against git://github.com/angular/bower-angular.git#*
    bower bootstrap-sass-official#*         download https://github.com/twbs/bootstrap-sass/archive/v3.1.1.tar.gz
    bower angular#*                              new version for git://github.com/angular/bower-angular.git#*
    bower angular#*                          resolve git://github.com/angular/bower-angular.git#*
    bower angular#*                         download https://github.com/angular/bower-angular/archive/v1.2.14.tar.gz
    bower bootstrap-sass-official#*          extract archive.tar.gz
    bower angular#*                          extract archive.tar.gz
    bower bootstrap-sass-official#*         resolved git://github.com/twbs/bootstrap-sass.git#3.1.1
    bower angular#*                         resolved git://github.com/angular/bower-angular.git#1.2.14
    bower bootstrap-sass-official#*          install bootstrap-sass-official#3.1.1
    bower angular#*                          install angular#1.2.14

    bootstrap-sass-official#3.1.1 bower_components/bootstrap-sass-official

    angular#1.2.14 bower_components/angular

Bower installs dependencies in `vendor/assets/bower_components`, which you
should check into your repository.

    > git add vendor/assets
    > git commit -m 'angular and bootstrap'

This location may seem strange, but it allows you to separate Bower-managed
third-party libraries from non-Bower-managed ones, if you should find the need
for a library that isn't available via Bower (although that would be highly
unusual).

Since `vendor/assets/bower_components` *isn't* Rails standard, you'll need to
add it to the asset path so these files get picked up.   While we're here, we'll also
add a few lines of configuration to get the glyphicons working while we're at it.

We'll do this in `config/application.rb`, like so:

    git://receta.git/config/application.rb#angular-and-bootstrap..deal-with-glyphicons

Lastly, you'll need to reference these files in `application.js` and
`application.css.scss`, respectively (we'll also remove the reference to Turbolinks
while we're here).  First, rename `application.css` to `application.css.scss`, because we'll need to use SASS directives to get
everything to work for Bootstrap's latest version (note that they frequently break things on minor versions, so please let me
know if this no longer works).

```diff
diff --git a/app/assets/javascripts/application.js b/app/assets/javascripts/application.js
index d6925fa..29f41b7 100644
--- a/app/assets/javascripts/application.js
+++ b/app/assets/javascripts/application.js
@@ -12,5 +12,5 @@
 //
 //= require jquery
 //= require jquery_ujs
-//= require turbolinks
+//= require angular/angular
 //= require_tree .
diff --git a/app/assets/stylesheets/application.css b/app/assets/stylesheets/application.css
index 3192ec8..2cac3ad 100644
--- a/app/assets/stylesheets/application.css.scss
+++ b/app/assets/stylesheets/application.css.scss
@@ -9,5 +9,6 @@
  * compiled file, but it's generally better to create a new file per style scope.
  *
  *= require_self
  *= require_tree .
  */
+
+@import "bootstrap-sass-official/assets/stylesheets/bootstrap-sprockets";
+@import "bootstrap-sass-official/assets/stylesheets/bootstrap";

```

*(Note that previous version of `bootstrap-sass-official` would've required you to required `bootstrap.css` and not
 `_bootstrap.css`.  Seems that the maintainers are flip-flopping on their naming conventions).*

The reason these `require` lines are so long is due to an “impedance mismatch”
between Bower and the Rails asset pipeline.  Bower isn't much more than a
simple way to download a Git repository, and there isn't much standardization
across various front-end components.

So, even though everything's installed in `vendor/assets/bower_components/ASSET_NAME`,
what's underneath could be anything.
To bring those into our app so they are served by the asset pipeline, we
have to specify the exact file or files within the package.  This often
requires hunting around for what was downloaded to find the right path.
Hopefully, a future version of Rails will provide some help here, but until
then, Bower is a fairly clean solution.

There is an alternative solution called [Rails Assets](https://rails-assets.org/), which 
converts any Bower package to a Rubygem on the fly, and integrates with your `Gemfile`.  The
reason we're not using that for this book is that Rails Assets does not handle assets packaged in a private repository,
whereas Bower (and thus `bower-rails`) does.  While our demo app doesn't use private
assets, we want to present a complete solution that can handle any needs you're likely to have.  At the time of this writing, Bower
and `bower-rails` is it.

Now that we have our initial set of front-end dependencies downloaded, let's
write *just* enough code to use them, so we can be sure all the moving parts
are working together.

## Tiniest Angular app ever

We'll need a basic controller to render a view that will get Angular
bootstrapped, and we'll need a tiny bit of JavaScript to create our Angular
application.

First, we'll add a new route to `routes.rb` to send our root route to a
controller.

    git://receta.git/config/routes.rb#basic-setup-verified^^..basic-setup-verified

We'll then create a basic controller called `HomeController`

    git://receta.git/app/controllers/home_controller.rb#basic-setup-verified

We'll also need a barebones Angular app, which we'll put in `app.coffee`:

    git://receta.git/app/assets/javascripts/app.coffee#basic-setup-verified

Finally, we'll create the view that uses some Bootstrap CSS as well as just
enough Angular to demonstrate that it's working.  In this case, we'll create a
text field that, as we type, will update the heading of our panel.

    git://receta.git/app/views/home/index.html.erb#basic-setup-verified

Now, when we run our app and visit the root URL, we should see our basic
Angular app working.

    > rails s

If you the Rails error page with a message like so:

    couldn't find file 'bootstrap/glyphicons-halflings-regular.eot'

This is because later versions of the bootstrap package include sprockets directives that bake in an assumption about how you've
installed Bootstrap.  Namely, the directives tell Rails to find the glyphicons fonts in `bootstrap/`, which doesn't exist in any of the asset paths we have configured. So, we must add it.

Open `config/application.rb` and add this line:

```ruby
config.assets.paths << Rails.root.join("vendor","assets","bower_components","bootstrap-sass-official","assets","fonts")
```

This may seem like more annoying configuration, but this is a one-time only activity that enables better project management for
the life our application.  It's a tradeoff, but a worthwhile one.

Before we get too far into development, we need to deploy this to production.  Since
we've been making heavier-than-normal changes to asset-related aspects of our
app, we should verify now that everything works in production mode.  This way,
as we do more and more front-end work and bring in more and more libraries
and assets, we can can have a better idea of what went wrong if something
*does* go wrong.

## Production deployment

We're going to use [Heroku][heroku] to deploy.  Heroku is a cloud-based “platform as a service” vendor that is free and easy to
use.  We can get a Rails app deployed with a small database for free just be doing a `git push`.  The Heroku environment is also
fairly unforgiving.  If our application works on Heroku, it'll work anywhere.

If you don't have a Heroku account, sign up for one (it's free).  You should also install
the [Heroku Tool Belt][heroku-cli], which will allow management of your app
from the command line.

[heroku]: http://heroku.com
[heroku-cli]: https://toolbelt.heroku.com/

Once you're signed up and logged in, create a new application and follow the instructions to add the
Heroku remote Git repository to your local git configuration.

Before deploying, however, we need to add a few more gems to our `Gemfile`.
These are mostly Heroku-specific, although they aren't hurting anything if you
don't want to use Heroku.

    git://receta.git/Gemfile#..heroku-gems

Install those gems, commit `Gemfile` and `Gemfile.lock`, and push to the
Heroku repo

    > bundle install
    > git add Gemfile Gemfile.lock
    > git commit -m 'heroku deployment'
    > git push heroku master

Since this is the first push to a new app, it will take a while. Once it's done, open your app in your browser.

    > heroku open

You should see your app and it should work the same way it did locally.

We've now completely validated that Bower, along with our configuration, will correctly bring in the assets we need to get
started, in both development and production.

At this point, we can start to actually build our application.  The first
thing we'll do is create the search screen that allows us to find and browse
recipes.  This will drive the vast majority of the configuration we need to get our app running with Angular.

