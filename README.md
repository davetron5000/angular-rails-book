# Getting Started with Angular on Ruby on Rails

This is the source of http://angular-rails.com.  To build it, you'll need to you'll need to do a few things:

1. `bundle install` or `gem install bookingit` to install the bookingit gem
1. `mkdir git_repos`
1. `git clone https://github.com/davetron5000/receta git_repos/receta` to clone the repository containing the source code examples in the book
1. `bookingit build` to generate the HTML and CSS for the book 

Once this is done, open `book/index.html` in your browser.  Whenever you change something, re-run `bookingit build`.

## Changes to the source

`bookingit` uses git tags and such to generate the source and diffs in the book.  This allows me to know that everything is working and control what's
being displayed.  Unfortunately, if something needs to change, this breaks all the tags and SHA-1s.  I haven't sorted out a good way to fix this yet.
