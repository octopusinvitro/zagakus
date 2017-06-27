# Variable naming

Naming is hard.

The cost of software is maintenance. You spend 90% of your time reading code. Make it readable. Clarity is king.

Every language has their own conventions, this is meant to be a generic list of good practices.



### Variable names should communicate intent. Avoid comments:

> Every comment is a failure:
> it's clutter + lies
>
> -- Uncle Bob

```ruby
# bad
d = 20 # days the workshop lasts
rds = 31.7 # relativistic Doppler shift
mon = 'March'

# good
workshop_duration_in_days = 20
relativistic_doppler_shift = 31.7
month = 'March'
```

* Those comments are not going to help you when you are far away from the declaration!
* One letter variables are difficult to search in a document.
* Avoid names that can not be pronounced out loud.
* Avoid words that don't exist in the English language.



### Try to avoid letters that look like numbers

Like the letter l or the letter O:

```ruby
a = l
if ( O == l )
  a = O1
else
  l = 01
end
```



### Think hard about what your variables represent

```ruby
# bad
def copy(x, y)
  x.member1 = y.member1
  x.member2 = y.member2
end

# good
def copy(from, to)
  to.name = from.name
  to.address = from.address
end
```


### Classes and variables should be nouns but not too generic

```ruby
# bad
class Parser
class Data
sorted

# good
class DocumentParser
class WeatherResponse
sorted_name
sorted_list
```


### Avoid brain overhead, be explicit


```ruby
# bad
def get_them
  list1 = [];
  the_list.each do |x|
    if (x.first == 4)
      list1 << x
    end
  end
  list1
end
```

What is saved in `the_list`? Why is the first element so important? What does `4` represent? What exactly is being returned?

```ruby
# good
FLAGGED = 4

def flagged_cells
  game_board.select { |cell| cell.status_value == FLAGGED }
end
```

* Magic numbers should be turn into well-named constants
* Don't depend on positions of items on an array, rather use a well-named class attribute
* Learn the Enumerable class well, so you don't need extra temporary variables



### Don't be cute

```ruby
# bad
class HolyHandGrenade

# good
class DeleteItems
```



### Be consistent with your domain language

Don't mix names through your application classes or methods if they do the same:

```ruby
# bad
dog_api.retrieve(url)
cat_api.fetch(url)
rat_api.get(url)

# good
dog_api.get(url)
cat_api.get(url)
rat_api.get(url)
```

## Scope

### For variables

It's ok to give your variables short names in short scopes

Give them long names if they are used along long scopes so that you don't ahve to scroll

### For methods

It's the opposite. Methods available across a big scope will be called from a lot of places.

Long scope, short method name, for example `File.open()`.

Short scope or private functions, long method name, for example: `raise_error_when_empty_and_no_available_chips`


## Command query separation

Your methods can be of two types:

* command methods (change state)
* query methods (return a value)

Never mix them

For example: a method that returns a boolean and sets a variable to some value.

Solution: Separate in two methods. It would be weird if you used it in a conditional. You would have to remember that it also changes the state of your system.


## Resources
* Clean Code, by Uncle Bob Martin
