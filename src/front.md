# Intro: Angular and Rails in Production

The latest wave of JavaScript frameworks present an interesting problem to experienced developers.  On their own, these
frameworks can only do but so much, since they are designed to run on the client-side, in a browser.  This means that for
anything interesting—any real application you might want to create—you need a back-end.  That back-end is likely a web framework
that was designed without a JavaScript framework in mind.  This leaves a gap between understanding your backend and using the
JavaScript framework.

Rails is a terrific back-end.  Between Active Record, the controller layer and the huge ecosystem built around creating and
deploying Rails apps, you'd be hard-pressed to find a better back-end for your ambituous AngularJS application.  Of course, Rails
wasn't designed to work with a front-end JavaScript framework.

Angular, of course, wasn't designed with Rails in mind either, since it is entirely back-end agnostic. Because of this,
there is no clear path to using Angular in your Rails app.  Further, because Rails wasn't designed around heavy use of
JavaScript, there's no “Rails Way” for managing front-end assets and using them with the asset pipeline.

This mini-book is going to change that.  We're going to see step-by-step instructions for building a Rails app with Angular, and
getting it deployed to production.  That means we'll properly configure the asset pipeline, provide a solution for managing
front-end assets, and see how to integrate testing the front-end code into your app.

This is not a Rails or Angular tutorial.  If you'd like to learn Angular, I would suggest completing their [tutorial].  Type out
the code.  Run the code.  Tweak the code.  It only takes an hour or so, and you'll feel confident you can start using Angular.
That's what I did, but I was left with a lot of unanswered questions:

[tutorial]: http://docs.angularjs.org/tutorial/step_00

* How do I serve Angular assets via the Rails Asset Pipeline?
* How does Angular's templating system work with Rails?
* How do I connect Angular to my Rails controllers?
* How do I test my Angular code within a Rails app?
* How do I manage all these JavaScript dependencies?
* What challenges will there be in the production environment not present in the development environment?

I was able to find answers by piecing together information from Stack Overflow and various blog posts.  You shouldn't have to do
that.

This mini-book is broken up into four major parts:

* What the app will do, along with a basic skeleton
* Step-by-step walkthrough of a simple search feature
* A very fast runthrough of CRUD operations
* A brief retrospective about what we've learned and the way forward.

We'll work test first, and keep dependencies at a minimum.  We'll stick to the Rails “out of box” experience as much as we can.
But, the point of this is that the Rails “out of box” experience is missing a lot when it comes to creating an Angular-based app.

The technology we'll be using:

* CoffeeScript
* Back-end tests use RSpec
* Front-end tests use Jasmine
* Browser-based acceptance tests using RSpec and Capybara
* Front-end dependencies managed with Bower

We'll also be using Twitter Bootstrap for our front-end, and Heroku for deployment (neither of these are required or related, but
we need *something* and both are free and easy to use).

Finally, we'll be working test-first as much as possible.

When we're done, you'll know exactly what you need to do to get Angular into your Rails app in a way that supports sustained
development and deployment.  You'll be able to get to your code more quickly and confidently.

## What You'll Need

The code in this book is pulled from a [git repository][repo] containing the Rails app
we'll be building.  The console output we'll see is generated directly from
that code, so you should be able to follow along easily.

[repo]: http://github.com/davetron5000/receta

That being said, it might be better to create the app from scratch and
actually make the changes in question.  It will get your fingers used to
typing the commands and get you familiar with the files you'll need to edit as
you work.

Other than that, you'll need:

* Ruby 2 or later
* Rails 4.0.x
* Some sort of database installed.  I'd recommend Postgres, but MySQL should work fine
* An account on [Heroku], where we'll deploy the app

This was book and the app we'll see were developed on OS X, so some terminal
commands might be different if you are on Windows.  Please [let me know][contact]
what those differences are.

[Heroku]: http://heroku.com
[contact]: http://github.com/davetron5000

With that out of the way, let's get started!
