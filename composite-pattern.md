# The composite pattern

When the action you need to perform is a tree or a series of steps. For example, making a coffee or a cake.

Sometimes you have a list of tasks where some of those tasks are also a list of tasks.

If a task is a list of tasks or not, the consumers of the API don't need to know: all tasks should have the same API.

## Parts

The composite pattern has three moving parts:

* **The component:** The common interface for all objects. Represents a generic task.

The component is not mean to be used, is an abstract class:

```ruby
class Task
  def operation
    raise NotImplementedError
  end
end
```

* **The leaf classes:** Simple task, implements the interface. A single specialized task.

```ruby
class SpecificTask < Task
  def operation
    # Do something specific
  end
end
```

* **The composite:** is both a component and build of components. A task that is a list of tasks.

```ruby
class TaskList < Task
  def initialize
    @tasks = []
  end

  def operation
    @tasks.each(&:operation)
  end

  def add(task)
    @tasks << task
  end

  def remove(task)
    @tasks.delete(task)
  end
end

task = SpecificTask.new
task_list = TaskList.new
task_list.add(task)
task_list.operation
```


## Ruby-specific magic

We can make the composite act as an array:

```ruby
def <<(task)
  @tasks << task
end

task_list << SpecificTask.new
```

```ruby
def [](index)
  @tasks[index]
end

puts(task_list[0].operation)
```

```ruby
def []=(index, value)
  @tasks[index] = value
end

task_list[0] = AnotherTask.new
```

## Problems

* The leaf and the composite are duck types, but they are not equal. One handles children, the other doesn't
* You have to decide how to handle calls to add or remove children on leaf objects.


## Bidirectional trees

When children need to know who is their parent.

```ruby
class Task
  attr_accessor :parent

  def initialize
    @parent = nil
  end

  def operation
    raise NotImplementedError
  end
end
```

Given that the composite class is the place where the parent–child relationships are managed, it is also the logical place to set the parent attribute:

```ruby
class TaskList < Task
  def initialize
    super
    @tasks = []
  end

  def operation
    @tasks.each(&:operation)
  end

  def add(task)
    @tasks << task
    task.parent = self
  end

  def remove(task)
    @tasks.delete(task)
    task.parent = nil
  end
end
```

## Don't forget that composites are tasks AND task lists

```ruby
# wrong

def total_tasks
  @tasks.length
end

# right

# composite calculation
def total_tasks
  @tasks.reduce(0) { |sum, task| sum += task.total_tasks }
end

# leaf calculation
def total_tasks
  1
end
```

## Resources

* [Composite pattern at the Source Making website](https://sourcemaking.com/design_patterns/composite)
