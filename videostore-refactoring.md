# Videostore refactoring

Exercise from the Refactoring book by Martin Fowler.

Refactoring is improving the design of the code without changing the behaviour. Because of that, if we refactor, we have to be covered by tests, to ensure that we don't break the original behavior.

Remember the TDD mantra: Red - Green - Refactor. We can not refactor if we are not green, to be green we first have to be red, and we can't be red if there are no tests.

## Notes

These notes are from the Clean Code video by Uncle Bob where he does the refactorting. You can follow along [with the code sample here](https://github.com/unclebob/videostore):

### Problems with the code sample

The program works, so what?

* A poorly designed system is hard to change, and then it's easy to make mistakes and introduce bugs.
* It is not object oriented.
* The `statement` method does far too much.
* If we wanted to print an HTML statement, there is no way to reuse the code, you have to duplicate the statement method.
* Duplication means maintenance and synchronisation burden, hence bugs.
* If more requirements come in, it's going to be harder to figure out where to make the changes and harder to make them without making a mistake.

> _When you find you have to add a feature to a program, and the program's code is not
structured in a convenient way to add the feature, first refactor the program to make it
easy to add the feature, then add the feature._ <br>
&mdash; Martin Fowler

<br>

[Make the change easy, then make the easy change](https://twitter.com/kentbeck/status/250733358307500032?lang=en).<br>&mdash; Kent Beck

### Tests

* Testing the views is brittle, what you want to test is behahvior or values
* Avoid testing through strings
* Tests trump encapsulation. Actually tests trumps everything
* Use descriptive test names
* Always use an updated version of the test framework

### Code

* Run the tests constantly
* Start refactoring from the tests
* Wrap long lines to make them more readable
* Use names that express intention
* Break long methods into smaller ones
* Update obsolete syntax to its most recent version
* Extract the body of a loop into its own method (SRP of the loop!)
* Check what calls are being used and analyze where they belong
* Whenever you have types and a switch, replace with polymorfism

## Exercise

Do the refactoring yourself and add the functionality to print an html statement.
