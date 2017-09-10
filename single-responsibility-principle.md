# Single-Responsibility Principle

> "*A class should have only one reason to change.*"

A responsibility is a reason for change. If a class has more than one responsibility, it will have more than one reason to change.

Responsibilities become coupled, and coupling leads to fragile designs that break in unexpected ways when changed.

Responsibilities should be separated based on the way that they might change.

## Identify the responsibilities

```ruby
class Modem
  def dial(phone_number)
  end

  def hangup
  end

  def send(message)
  end

  def receive
  end
end
```

Two responsibilities:
* connection (dial, hangup)
* data communication (send, receive).

```ruby
class Connection
  def dial(phone_number)
  end

  def hangup
  end
end

class DataChannel
  def send(message)
  end

  def receive
  end
end

class Modem
  def initialize(connection, data_channel)
    @connection = connection
    @data_channel = data_channel
  end
end
```

**Rigidity:** The application changes in ways that cause the two responsibilities to
change at different times. If that is the case, separate them.

**Needless complexity:** If this is not happening, then separating the two responsibilities can increase complexity.


## Different reasons to change

```ruby
class Employee
  def calculate_pay
  end

  def store
  end
end
```

* `calculate_pay` = business rules, they change often
* `store` = persistence, doesn't change much

Both change for different reasons.

> "*Gather together the things that change for the same reasons (cohesion). Separate those things that change for different reasons (decouple).*"

Increase cohesion, decrease coupling

Clases that follow SRP are also more likely to be more reusable.

## SRP applied to methods

A method should do one thing and do it well. Methods should ideally be small, with no more that four or five lines, according to Sandi Metz. In case you need an `if`-`else`.

```ruby
def use_spare_time
  if temperature > 30
    go_to(the_beach)
  else
    watch(movie)
  end
end
```

Indications of a method getting too long:
* Number of lines
* Different responsibilities
* Local variables used all over the place (probably asking to be class fields)
* There is an `and` or an `or` in the method name, or you need to use them to describe what the method does

> "*Long methods are the place where all your classes went to hide*" &mdash; Uncle Bob

Identify the parts that could change in the future and separate them into small methods with informative names that are in charge of one thing.

As a result, we have a method that is easy to understand and maintain.


## Resources

* [The Single-Responsibility Principle](https://8thlight.com/blog/uncle-bob/2014/05/08/SingleReponsibilityPrinciple.html), by Uncle Bob
* [Single Responsibility Principle: Why Does it Matter?](https://8thlight.com/blog/elizabeth-engelman/2015/01/22/single-responsibility-principle-why-does-it-matter.html)
* [Thinking differently about SRP](https://8thlight.com/blog/cory-foy/2012/08/07/thinking-differently-about-srp.html)
