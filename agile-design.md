# Agile design

- First clear thing about your code: it's going to change
- Second clear thing: it's going to rot

If you keep accumulating technical debt, changes become more and more difficult until they become impossible.


## Design smells

* **Rigidity:** Code is difficult to change. A small change cascades through the codebase.

* **Fragility:** The application breaks in many unrelated places when a simple change is made to the code.

* **Immobility:** It is difficult to extract or separate parts of the code that could be useful to reuse.

* **Viscosity:** Of the software, if keeping the design when changing the code is more difficult than doing a fast hack. Of the environment, if it is so slow or inefficient that we change the code so that we avoid dealing with it rather than keeping the design.

* **Needless complexity:** When we anticipate changes that will probably never come, and the app is littered with code that will never be used.

* **Needless repetition:** When the same code appears over and over again, in slightly different forms, we are missing an abstraction. Also, it's a maintenance nightmare.

* **Opacity:** Code can be written in an opaque, unreadable and convoluted manner. To avoid this, code needs to be reviewed by others.

Can you think of examples of these smells in codebases you had worked or are working on?

## Rotting code

Code rots because it doesn't accomodate change. Requirements change constantly and the developers implementing them don't follow the initial inflexible design of the codebase.

Solution: don't do any design up-front, write easy-to-change, flexible code, apply the right design patterns, follow the green-red-refactor mantra to write clean and maintainable code guided by tests that is refactored frequently.


## Resources

* [The half-life of code](https://www.sandimetz.com/blog/2017/6/1/the-half-life-of-code), by Sandi Metz
