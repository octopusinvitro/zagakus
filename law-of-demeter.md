# Law of Demeter

The Law of Demeter or principle of least knowledge is a design guideline for developing software. It tries to restrict class interaction in order to minimize coupling among classes.

* Don't leak implementation details. Put the knowledge in the right place.
* Encapsulation: the state of the object can only be changed via its public API
* state = data. Be careful with anything that produces a mutation. Only the class should know how to change its state (its data)


```ruby
# bad
car.wheels[0].steering_knuckle.move_right
car.wheels[1].steering_knuckle.move_right
car.wheels[2].steering_knuckle.move_right
car.wheels[3].steering_knuckle.move_right

# good
car.move_right

class Car
  def move_right
    wheels.each { |wheel| wheel.move_right }
  end
  # ...
end

class Wheel
  def move_right
    steering_knuckle.move_right
  end
  # ...
end
```

The longer the method chaining, the longer the coupling to more objects. Any method of an object should only call methods belonging to:

* itself

```ruby
class User
  attr_reader :first_name, :last_name

  def full_name
    "#{first_name} #{last_name}"
  end
end
```

* any parameters that were passed in to the method

```ruby
def tax(amount)
  amount.to_dollars * tax_rate
end
```

* any objects it created

```ruby
class Wheel
  def move_right
    steering_knuckle.move_right
  end

  def steering_knuckle
    SteeringKnuckle.new
  end
end
```

Basically restrict it to one level of inception.

## Exceptions

Immutable data:

```ruby
x = cell.position.x
```

Functional programming: There is no state, everything is immutable, works with data structures mainly. Everything can be public because there is no way to mutate anything.

> "_OO makes code understandable by encapsulating moving parts. Functional programming makes code understandable by minimizing moving parts._" &mdash; Michael Feathers
