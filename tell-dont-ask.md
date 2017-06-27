# Feature envy and tell don't ask

Feature envy is a term used to describe a situation in which one object gets at the fields of another object in order to perform some sort of computation or make a decision, rather than asking the object to do the computation itself.

**Examples**

```ruby
# bad
stream = payable.is_a?(Payment) ? payable.paid_for.stream : payable.recurring_donation.stream

# good
stream = payable.stream

class Payment
  def stream
    paid_for.stream
  end
end

class Mandate
  def stream
    recurring_donation.stream
  end
end
```

* Difficult to test and to use mocks because of the type check
* Violates encapsulation
* Knows too much about the chain so unrelated changes cascade through the codebase breaking unrelated classes

**More conditionals**

```ruby
# bad
class Post
  def send_to_feed
    if user.is_a?(TwitterUser)
      twitter_client.post_to_feed(contents)
    else
      gmail_client.send_email(contents)
    end
  end
end

# good: Polymorphism
class Post
  def send_to_feed
    user.send_to_feed(contents)
  end
end

class TwitterUser
  def send_to_feed(contents)
    twitter_client.post_to_feed(contents)
  end
end

class EmailUser
  def send_to_feed(contents)
    gmail_client.send_email(contents)
  end
end
```

**Views should be dumb and have no logic**

```erb
# bad
<% if current_user.admin? %>
  <%= current_user.admin_welcome_message %>
<% else %>
  <%= current_user.user_welcome_message %>
<% end %>

# good

<%= current_user.welcome_message %>
```

**Use a null object**

```ruby
# bad
def street_name(user)
  if user.address
    user.address.street_name
  else
    'No street name on file'
  end
end

# good
def street_name(user)
  user.address.street_name
end

class User
  def address
    @address || NullAddress.new
  end
end

class NullAddress
  def street_name
    'No street name on file'
  end
end
```

The end result of this style ("tell don't ask") is that it is easy to swap one object for another that can respond to the same commands but in a different way. This is because the behaviour of one object is not tied to the internal structure of the objects that it talks to.


## Resources

* [Martin Fowler "Tell, don't ask"](https://martinfowler.com/bliki/TellDontAsk.html)
* [Very interesting point regarding mock objects and tell don't ask](http://www.mockobjects.com/2006/10/tell-dont-ask-and-mock-objects.html)
