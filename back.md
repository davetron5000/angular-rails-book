# In Conclusion

This concludes our whirlwind tour of what to do to set up the basics of an
Angular app, powered by Rails.  With the setup we've gone through, you can now
crank out features quickly and easily—the hard work has been done.

With that said, I want to leave you with a few design tips, and a basic workflow I've found useful when building Angular apps with Rails.

## Design Tips

Because we are “off the Rails”, we have to make more design decisions that we might normally.

* Complexity in JavaScript is far more painful than in Ruby, so if you can make your Angular code simpler by doing a bit more work in Rails, do it (for example, using JBuilder to produce JSON with camel-cased keys).
* While Angular is supremely flexible, its components do have happy paths - try to stick to those where you can.
* Test your navigation.  Since you're using JavaScript to capture user actions and transitioning between views, it's more likely to break.
* Enforce business rules at the Rails layer always.  You don't have to do so in a nice way, but do do it.

It's also useful to have a solid workflow in place.

## Decent Workflow

We saw a few different workflows in this tutorial, but I want to leave with a
workflow I've found effective:

1. Create a browser-based test to exercise the expected paths of the UI.  This
   is largely the “happy path”, but might also include validations and other
   exceptional flows that the user will likely experience. 
2. Write your Angular controller (test-first, of course), which will lead you to fleshing out
   what the back-end needs to look like.
3. Implement the back-end.
4. Implement the view in a basic way to get your browser-based test to pass.
5. Add whatever bells-and-whistles or other UI stuff you want, using your
   browser-based test to make sure you've broken something.

With that, go forth and make awesomely rich applications, powered by the best
web framework around!
