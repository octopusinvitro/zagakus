# Four rules of simple design
## by Corey Haines

**Simple system:**
does not guess the future, but is arranged in a way that adapts to future changes

* Don't be afraid of deleting code
* Don't get attached to the code
* You are not your code

## Four rules of simple design

Originally codified by Kent Beck in the late 90’s.

* **Tests Pass:** Tests should be automated and run fast
* **Expresses Intent:** Good names! So watch out for SRP violations and extract classes with good names.
* **No Duplication (DRY):** Not only code, but also duplicated knowledge
* **Small:** Delete code that is not used, extract behaviour

They are iterative, as fixing one may uncover some of the others.

## Examples

* Test names are important because tests are documentation

```ruby
# bad
def test_a_new_world_is_empty
  world = World.new
  assert_equal 0, world.living_cells.count
end

# good
def test_a_new_world_is_empty
  world = World.new
  assert_true world.empty?
end
```

```ruby
# bad
def test_a_cell_can_be_added_to_the_world
  world = World.new
  world.set_living_at(1, 1)
  assert_equal 1, world.living_cells.count
end

# good
def test_a_cell_can_be_added_to_the_world
  world = World.new
  world.set_living_at(1, 1)
  assert_true world.alive_at?(1, 1)
end
```

* Duplication of knowledge. How many places does the code need to be changed?

```ruby
#bad
class World
  def set_living_at(x, y)
  #...
  end

  def alive_at?(x, y)
  #...
  end
end

class LivingCell
  attr_reader :x, :y
end

class DeadCell
  attr_reader :x, :y
end
```

```ruby
# good
class Location
  attr_reader :x, :y
end

class World
  def set_living_at(location)
  #...
  end

  def alive_at?(location)
  #...
  end
end

class LivingCell
  attr_reader :location
end

class DeadCell
  attr_reader :location
end
```

* Behavior attractors

```ruby
class Cell
  attr_reader :location
  def alive_in_next_generation?
  # run rules
  end
end

# Where to put this method?
def neighbors_of(location)
  # calculate the coordinates of neighbors
end
```

```ruby
class Location
  attr_reader :x, :y
  def neighbors
    # calculate a list of locations
    # that are considered neighbors
  end
end
```

* Testing state vs testing behaviour

```ruby
def test_a_world_starts_out_empty
  world = World.new
  assert_true world.empty?
end

def test_world_is_not_empty_after_setting_a_living_cell
  world = World.new
  location = Location.random
  world.set_living_at(location)
  assert_false world.empty?
end
```

Identify the fundamental behaviour: to calculate the next generation

```ruby
def test_a_new_world_is_empty
  assert_true World.new.empty?
end

def test_an_empty_world_stays_empty_after_a_tick
  world = World.new
  next_world = world.tick
  assert_true next_world.empty?
end
```

* Don't Have Tests Depend on Previous Tests

```ruby
def test_an_empty_world_stays_empty_after_a_tick
  world = World.new
  next_world = world.tick
  assert_true next_world.empty?
end
```

What if we don't want the new world to be empty anymore?
Be explicit:

```ruby
def test_an_empty_world_stays_empty_after_a_tick
  world = World.empty
  next_world = world.tick
  assert_true next_world.empty?
end
```
