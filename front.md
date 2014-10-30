# AngularJS, Rails, and Production

The latest wave of JavaScript frameworks present an interesting problem to experienced Rails developers.  On their own, these
frameworks can only do so much, since they are designed to run on the client-side, in a browser.  This means that for
any real applicaiton you might create, you need a back-end.

Rails is a terrific back-end, however it wasn't designed with a JavaScript framework like AngularJS in mind.  Rails' view of the world is serving up pages and HTML.  Rich Javascript-based single-page applications aren't something Rails encourages.  

[AngularJS][angular] is a terrific front-end.  It supports testability, clean code organization, and has a simple object model.  It's supremely powerful but, of
course, wasn't designed with Rails in mind—it's back-end agnostic.

[angular]: http://angularjs.org

The result is that there is no “Rails Way” for managing the front-end assets needed to create a rich JavaScript application using AngularJS.

This mini-book is going to change that.  We're going to see step-by-step instructions for building a Rails app with Angular, and
getting it deployed to production.  That means we'll properly configure the asset pipeline, provide a solution for managing
front-end assets, and see how to integrate testing the front-end code into your app.

This is not a Rails or Angular tutorial.  If you'd like to learn Angular, I would suggest completing their [tutorial].  Don't just read it, type out the code.  Run the code.  Tweak the code.  It only takes an hour or so, and it'll give the confidence you  need to start using Angular (that's what I did).  

Once you're familiar enough with Angular to start building an app, you'll find there are a lot of unanswered questions:

[tutorial]: http://docs.angularjs.org/tutorial/step_00

* How do I serve Angular assets via the Rails Asset Pipeline?
* How does Angular's templating system work with Rails?
* How do I connect Angular to my Rails controllers?
* How do I test my Angular code within a Rails app?
* How do I manage all these JavaScript dependencies?
* What challenges will there be in the production environment not present in the development environment?

I was able to find answers by piecing together information from Stack Overflow and various blog posts.  You shouldn't have to do that.

## How this book is organized

This mini-book is broken up into four major parts:

* Creating a basic skeleton to validate Angular is setup and configured
* A step-by-step walkthrough of a simple feature
* A more brisk walkthrough of using TDD to implement a feature
* A very fast runthrough of building a few more features

The technologies we'll be using:

* CoffeeScript
* RSpec (for back-end tests)
* Jasmine (for front-end tests)
* Capybara & Selenium (for browser-based acceptance tests)
* Bower (to manage front-end dependencies)
* Twitter Bootstrap (for styling–not required, but simplifies this tutorial)
* Heroku (for deployment–not required, but is an easy way to validate production behavior)

When we're done, you'll know exactly what you need to do to get Angular into your Rails app in a way that supports sustained
development and deployment.  You'll be able to get to your code more quickly and confidently.

## What you'll need

The code in this book is pulled from a [git repository][repo] containing the Rails app
we'll be building.  The console output we'll see is generated directly from
that code, so you should be able to follow along easily.

[repo]: http://github.com/davetron5000/receta

I'd still recommend you follow along at your terminal and editor.  Even though you will
just be typing in what you see, it'll get your fingers used to the workflow
and code involved in a a Rails-powered Angular app.

All you'll need:

* Ruby 2 or later
* Rails 4.0.x
* Some sort of database installed (I'd recommend Postgres, but MySQL should work fine)
* An account on [Heroku], where we'll deploy the app

The app we'll see in this book was originally developed on OS X, so some terminal
commands might be different if you are on Windows.  Please [let me know][contact]
what those differences are.

[Heroku]: http://heroku.com
[contact]: http://github.com/davetron5000

With that out of the way, let's get started!
