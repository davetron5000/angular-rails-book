# Contributing

This book is licensed under a Creative Commons Attribute-NonCommerical-ShareAlike license, and the source for the book can be found
in [its repo][repo].  Feel free to submit pull requests to correct any mistakes you find.

[repo]: https://github.com/davetron5000/angular-rails-book/tree/master

## Changes to code

*Most* of the code in this tutorial comes from the [receta Rails app][receta-repo].  If you look at the Markdown source of this site, you'll see much of
the code is pulled in like so:

    We'll do this in `config/application.rb`, like so:

        git: //receta.git/config/application.rb#angular-and-bootstrap..deal-with-glyphicons

[bookingit][bookingit] is a custom application I've written that interprets this URL and produces diffs or shows code based on branches in the git
repository.
 
[receta-repo]: https://github.com/davetron5000/receta
[bookingit]: https://github.com/davetron5000/bookingit

So, the best way to submit a PR for code changes is to do so against receta's repo, **creating your PR from the branch in question**.  So, if you wanted
to change something about the above code, you would create your branch off of the `deal-with-glyphicons` branch.

When in doubt, just ask, and if you submit a PR from master, that's OK, I can sort out merging it. I'd rather get your contribution than require you to
learn advanced Git jockeying.

## Changes to everything else

I'm so sorry for any typos and grammar issues—I've tried to proof this, but if you find something, I'm happy to accept your changes.

I'm **not** looking for alternate ways to do things, or large changes in the narrative (though the license allows you to do that on your own, if you like!)

When in doubt, open a PR with a question and we can start from there.
