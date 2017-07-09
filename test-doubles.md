# Test doubles

People call them mocks, but they are really test doubles and there are many.

There are mocking libraries out there, but most of the time you can very easily roll your own and it's much simpler.

Also, use dependency injection every time you can. It makes testing your code easier, and your code more decoupled.

## Dummies

They do nothing. They are used when you need to pass something as an argument but you are not going to use it for the test at hand.

Code:

```ruby
class Authorizer
  def authorize(username, password)
    # implements authorization logic
  end
end

class System
  def initialize(authorizer)
    @authorizer = autorizer
  end

  def login_count
    # implements number of logged in users
  end
end
```

Tests:

```ruby
class DummyAuthorizer < Authorizer
  def authorize(username, password)
    nil
  end
end

describe 'newly created system' do
  it 'has no logged-in users' do
    system = System.new(DummyAuthorizer.new)
    expect(system.login_count).to be(0)
  end
end
```

Or using a mocking library:

```ruby
describe 'newly created system' do
  let(:dummy_authorizer) { instance_double(Authorizer) }
  before { allow(dummy_authorizer).to receive(:authorize).and_return(nil) }

  it 'has no logged-in users' do
    system = System.new(dummy_authorizer)
    expect(system.login_count).to be(0)
  end
end
```


## Stubs

Return harcoded values. They are used to test parts of the system that require certain values to be set. For example, when you need to test some feature that requires you to be logged-in:

```ruby
before { allow(dummy_authorizer).to receive(:authorize).and_return(true) }
```

For a logged-out user:

```ruby
before { allow(dummy_authorizer).to receive(:authorize).and_return(false) }
```


## Spies

They literally spy if some method was called. They can also count the number of times it was called, or keep a list of the arguments passed. But be careful! The more you spy, the more coupled you get to the class you are mocking.

```ruby
class AcceptingAuthorizerSpy < Authorizer
  def initialize
    @authorize_was_called = false
  end

  def authorize(username, password)
    @authorize_was_called = true
  end
end
```


## Mocks

Like spies, but you are not so interested in the return values of functions as you are in what function was called, with what arguments, when, and how often. They are mostly used in mocking libraries, not manually coded.

```ruby
class AcceptingAuthorizerVerificationMock < Authorizer
  def initialize
    @authorize_was_called = false
  end

  def authorize(username, password)
    @authorize_was_called = true
  end

  def verify
    authorize_was_called
  end
end
```


## Fakes

Fakes are the only test double that is different to the others. It has actual business behaviour hardcoded into it. It's like a simulator.

```ruby
before { allow(dummy_authorizer).to receive(:authorize).and_return(username == 'Bob') }
```


## Summary

* Use **dependency injection** as much as possible. It makes testing easier and decouples the code.
* Prefer simpler test doubles like dummies, stubs and spies. Theya are also easy to write.
* Sometimes is easier to write your own mocks and can make the test more readable.
* If you can use the real object without causing much trouble, it's better. Sometimes mocks and their related objects can get out of sync and you don't want to be in a paralel universe where everything works but your code is actually broken.


## Resources

* [The Little Mocker](https://8thlight.com/blog/uncle-bob/2014/05/14/TheLittleMocker.html) by Uncle Bob
* [Mocks aren't stubs](https://martinfowler.com/articles/mocksArentStubs.html) by Martin Fowler
* [When to mock](https://8thlight.com/blog/uncle-bob/2014/05/10/WhenToMock.html) by Uncle Bob
