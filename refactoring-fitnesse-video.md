# Refactoring Fitnesse
## (video by Uncle Bob)

Play at 1.25 speed:
* https://www.youtube.com/watch?v=y4_SJzNJnXU

### Notes

* [Extract a private class](https://sourcemaking.com/refactoring/extract-class) when there are different responsibilities in place

* [Extract method contents](https://refactoring.com/catalog/extractMethod.html) contents of if statements into a method. If there are several outputs, make them class attributes first.

* [Extract the contents of for loops into a method](http://www.integralist.co.uk/posts/refactoring-techniques#17). Looping is one responsibility!
For example:

```ruby
managers = []
employees.each do |e|
  managers << e if e.manager?
end

# becomes:

managers = employees.select { |e| e.manager? }
```

* [Extract long conditions into boolean methods with descriptive names](https://refactoring.guru/decompose-conditional). For example:

```ruby
  if game_is_over?
    puts 'Game Over'

# later on...

  def game_is_over?
    board.has_win? || board.is_full?
  end
```
