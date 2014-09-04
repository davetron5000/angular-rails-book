# Running Through Remaining Features

In the last section, we worked “inside out”, starting with `RecipeController`
and integrating everything at the end, driven by a browser-based test.

This time, let's work “outside in” by starting with a browser test that will mimic user behavior.  We'll make a test that creates
a recipe, edits it, and then deletes it, so we can test everything at once.

    git://receta.git/spec/features/edit_spec.rb#edit-recipe-test

The test, of course, fails:

    git://receta.git/#edit-recipe-test!rspec spec/features/edit_spec.rb!nonzero

To make it work, we'll need to:

* add functionatliy to our back-end controller
* add functionatliy to our Angular controllers
* create views

## Rails controller

First, we'll add some tests for our controller to get started:

    git://receta.git/spec/controllers/recipes_controller_spec.rb#..back-end-crud

This is nothing but the happy path, mostly to speed us along here.

Now, let's make these tests pass:

    git://receta.git/app/controllers/recipes_controller.rb#..back-end-crud

You'll notice that we are skipping the authenticity token verification.  While
there is a way to insert the one that Rails generates into all HTTP requests,
this is a one-time use token.  If we make more than one HTTP POST to our
app we'll need a new token each time.  There currently isn't a good way
to generate one, so we have to skip the check for this.

Don't forget to add the routes for the new actions:

    git://receta.git/config/routes.rb#..back-end-crud

Back to our test, everything seems to be working:

    git://receta.git/#back-end-crud!rspec spec/controllers/recipes_controller_spec.rb

With the back-end working, let's turn our attention to the front end, which will call this code.

## Angular controllers

We'll have `RecipeController` handle these operations.  It has the `Recipe` resource we created using Angular's `$resource`
service, so it should be pretty straightforward to add new functions.

First, we'll create a test for creating, saving, and deleting.  One wrinkle in our design is that for the case of creating a new
recipe, there won't be an id in the URL, so we don't want to fetch anything from the backend.  At this point, it just means we
need to setup the controller so that there's no `recipeId` in the URL, and no expectation of doing a `GET` to the backend.

Once we've changed `setupController()` accordingly, all we need to do is setup expectations of the HTTP calls to our backend, and
then call the functions on `scope` that we'll need.

    git://receta.git/spec/javascripts/controllers/RecipeController_spec.coffee#..front-end-crud

You'll notice that `save()` is handling both creating a new recipe and updating an existing one.  This will make it easier to
re-use our view for both purposes, since the “Save” button we'll create can just call `save()`, allowing the controller to work
out just how to talk to the back-end.

To make this test pass, we'll need to:

* Check `$routeParams` for a recipe id and, if we don't find one, create an empty recipe
* Override Angular's defaults for saving a resource to use the Rails-standard “PUT”
* Implement `save()` and `delete()`.
* Add a few methods to help with navigating

Let's do it:

    git://receta.git/app/assets/javascripts/controllers/RecipeController.coffee#front-end-crud

You'll notice we made a `create()` method when setting up our `Recipe` resource.  By default, Angular allows both
`recipe.$save()` and `Recipe.save(recipe)`, the later being what we'd use to create a new recipe.  Since Rails wants a `POST` for
create and a `PUT` for update, we have to change the defaults.

You'll also notice that we added a few functions for navigation.  `edit()` takes the user to the edit form, and `cancel()` takes
the user back to wherever makes sense based on the current operation.

This implementation also shows a deviation from what we get with Rails.  With Angular, there's no built-in way to route our code
based on the which “CRUD” operation is being performed.  We have to examine
the inputs and figure it out for ourselves.

Now, let's see if our tests pass:

    git://receta.git/#front-end-crud!rake teaspoon

So far, so good.  The only thing left is to create and wire up the views.

## Views

As you could see from our test, we're going to create two new routes: `/recipes/new` and `/recipes/:recipeId/edit`, both handled
by `RecipeController` and a new view, `form.html`.

First, let's set up the routes in `app.coffee`:

    git://receta.git/app/assets/javascripts/app.coffee#..crud-views

Next, we'll create `form.html`:

    git://receta.git/app/assets/javascripts/templates/form.html#crud-views

Now, we'll need to add a link to create a recipe as well as one to edit in `index.html`:

    git://receta.git/app/assets/javascripts/templates/index.html#..crud-views

These links require new functions called `newRecipe()` and `edit()`, which we'll add to `RecipesController.coffee`:

    git://receta.git/app/assets/javascripts/controllers/RecipesController.coffee#..b788ed96

And lastly, we'll need links to edit and delete a recipe in `show.html`:

    git://receta.git/app/assets/javascripts/templates/show.html#..crud-views

Whew!  Let's see if it all works by re-running our browser-based test:

    git://receta.git/#crud-views!rspec spec/features/edit_spec.rb

Voila! It works!

## Wrapping Up

We went quite quickly through this part of the app, mostly to just see what an entire “CRUD” app would look like as well as some
differences between what Rails gives us and what Angular doesn't.

It may seem like we've written a lot of extra code and tests to do something that would far simpler in Rails.  In a sense, this
is true, but what Angular lacks in creating CRUD applications, it more than
makes up for when creating a richer user experience.

The last section is going to be some reflection on what we've just learned and
where we could go from here.
