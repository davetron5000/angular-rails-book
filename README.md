# Getting Started with Angular on Ruby on Rails

This is the source of http://angular-rails.com.  To build it, you'll need to do a few things:

* `bundle install` or install the `bookingit` gem
* Create the file `git_repos`
* In `git_repos`, check out https://github.com/davetron5000/receta  This contains all the source files needed to build the book.

Once this is done, type `bookingit build`, then open `book/index.html` in your browser.  Whenever you change something, re-run that
command.

## Changes to the source

`bookingit` uses git tags and such to generate the source and diffs in the book.  This allows me to know that everything is working and control what's
being displayed.  Unfortunately, if something needs to change, this breaks all the tags and SHA-1s.  I haven't sorted out a good way to fix this yet.
