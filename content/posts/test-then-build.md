---
layout: post
title: "Test then build or, Setting a goal"
date: 2018-08-05
---

## How do you know something works?
If a tree falls in the forest does anyone hear it?  Likewise if you create a function do you know it works?  I'm not even talking about edge/corner cases in this hypothetical instance.  I'm talking about plain, vanilla instance where the data you're taking in is pristine.  No weird characters, no bad data, evertyhing goes as expected.

To know that everything works as intended you have to test it.  You have to walk it through the ring of fire that is an integrations or behavior test.  Unit testing is great for when you're developing but when we're talking about behavior (our function working well with the turbulent world around us) then *all unit tests are lies*.

More testing == more declarative code so don't stop your tests.  Just be realistic about what each type of test will do.  The _du jour_ methodology I've seen is unit test locally (as in make sure each little piece of a what you're building works), then mock a behavioral test, then add a full integrations test for a staging/mirror environment.  Who knows maybe that'll change as new tools/frameworks come out, do whatever works at the end of the day.

The point is: *prove it works*.

## Check for the shape you want, not the right shape
In this little picture below we've got our magic function that outputs tetris style shapes.  In this magical land we want the function to output a block of two widths.  This is our _design_ stage, we have talked about what we want and written it down somewhere.

![Magic Shape Function](/images/magic-shape-func.jpg)


So we go ahead an make the function righ?  Eeasy enough, stick a couple blocks together and write a test checking that we've got our 2 blocks.  Wrong!  This is the danger of writing your function, then testing based on what you _think_ should happen.  If we're only checking that we've got two blocks together in a horizontal fashion we'll potentially let shapes like below through.


![Magic Shape Test Func - False Positive](/images/magic-shape-false-positive-test.jpg)

Technically this satisfies our test and it would get greenlighted.  To avoid any false positives like this one we could have written the test first and explicitly described the output we wanted.  This pattern of describing what you want then satisfying those requirments means you build within a box you've defined.  There will be fewer suprsises if you create limits as you are setting out to make all those tests go from red to green.

## Documentation through testing
A nice side benefit of writing all these tests is that you can just open up the relevant testing directory and know right away what each piece of code should do.  If you fully embrace behavioral testing you can even know what a feature is meant to do, or not meant to do.  If your test names are descriptive enough you may not even need to run the tests, simply reading through for the behavior you want can be enough to outline what each piece of your program is supposed to do.

Integration tests will also give you a small clue, or a big one, as to how to configure and setup different pieces of your whole application.  Maybe a redis cluster or database is required for the piece of code you're working on.  What better way to self-document than keep tests up to date?


