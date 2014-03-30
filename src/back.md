# In Conclusion

This concludes our whirlwind tour of what to do to set up the basics of an
Angular app, powered by Rails.  With the setup we've gone through, you can now
crank out features quickly and easily—the hard work has been done.

We saw a few different workflows in this tutorial, but I want to leave with a
workflow I've found effective:

1. Create a browser-based test to exercise the expected paths of the UI.  This
   is largely the “happy path”, but might also include validations and other
   exceptional flows that the user will likely experience. I'd also recommend
   testing navigation, since a front-end JavaScript application will
   complicate this slightly (as compared to a plain web app).
2. Write your Angular controller (test-first, of course), which will lead you to fleshing out
   what the back-end needs to look like.
3. Implement the back-end.
4. Implement the view in a basic way to get your browser-based test to pass
5. Add whatever bells-and-whistles or other UI stuff you want, using your
   browser-based test to make sure you've broken something.

With that, go forth and make awesomely rich applications, powered by the best
web framework around!

