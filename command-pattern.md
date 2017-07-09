# The command pattern

Imagine you have to implement several payment gateways that process a payment in different ways:

```ruby
class PaymentGateway
  def process
    raise NotImplementedError
  end

  # Other code related with payments
end
```

You may use inheritance:

```ruby
class StripeGateway < PaymentGateway
  def process
    # talks to Stripe API
  end

  # Other code related with payments
end

class PaypalGateway < PaymentGateway
  def process
    # talks to PayPal API
  end

  # Other code related with payments
end
```

Problems with this approach:
* This creates an explosion of subclasses.
* Inheritance is too permanent: Changes in the parent cascade to all subclasses

### Solution

Identify what changes, name it, dependency-inject it.

What we need is a "command object".

```ruby
class PaymentGateway
  def initialize(command)
    @command = command
  end

  def process
    @command.execute if @command
  end

  # Other code related with payments
end

class StripeProcess
  def execute
    # talks to Stripe API
  end
end

class PaypalProcess
  def execute
    # talks to Paypal API
  end
end

stripe_gateway = PaymentGateway.new(StripeProcess.new)
paypal_gateway = PaymentGateway.new(PayPalProcess.new)
```

* More flexibility
* We don't have to know what we need until runtime


## Lists of Commands

Now we want to define what happens afterwards:

```ruby
def after_process
  # wrap up payment
end
```

Several classes want to send a thank you email, several want to talk to Salesforce. Back to inheritance, you would create two children:

```ruby
class ThankYouEmailGateway < PaymentGateway
  def process
    raise NotImplementedError
  end

  def after_process
    # Send thank you email
  end
end

class SalesforceGateway < PaymentGateway
  def process
    raise NotImplementedError
  end

  def after_process
    # Talk to Salesforce API
  end
end
```

Some classes now inherit from `ThankYouEmailGateway`, some inherit from `SalesforceGateway`. Now you want to both send an email and talk to Salesforce...

You are in trouble.

Problems with this approach:
* Complexity increases
* You can not share behaviour (from [Sandi Metz's talk](https://www.youtube.com/watch?v=OMPfEXIlTVE): `RandomHouse`, `EchoHouse`, `RandomEchoHouse`)


### Solution

```ruby
class PaymentGateway
  def initialize(process_command, after_process_list)
    @process_command = process_command
    @after_process_list = after_process_list
  end

  def process
    @process_command.execute if @process_command
  end

  def after_process
    @after_process_list.each(&:execute)
  end
end

stripe_gateway = PaymentGateway.new(StripeProcess.new, [ThankYouEmail.new])
paypal_gateway = PaymentGateway.new(PayPalProcess.new, [ThankYouEmail.new, Salesforce.new])
```

* More flexibility
* We don't need to know what we need until runtime

## Commands as task lists

* Example: installation program
* Tasks: create, copy, move, delete files
* User wants to know beforehand what the installer will do
* User wants to know afterwards what the installer did

You can use the Composite pattern combined with the Command pattern to keep track of what has been done or to undo steps.

```ruby
class PostCreditCardData
  def execute
    # Post to the gateway endpoint
  end
end

class HandleGatewayToken
  def execute
    # Handle payer data persistance
  end
end

class SendThankYouEmail
  def execute
    # send email
  end
end

class CompositeCommand
  def initialize
    @commands = []
  end

  def execute
    @commands.each(&:execute)
  end

  def add(command)
    @commands << command
  end

  def remove(command)
    @commands.delete(command)
  end
end
```

Now we can pass commands that are a list of commands:

```ruby
class PaymentGateway
  def initialize(process_commands, after_process_commands)
    @process_commands = process_commands
    @after_process_commands = after_process_commands
  end

  def process
    @process_commands.execute
  end

  def after_process
    @after_process_commands.execute
  end
end

stripe_commands = CompositeCommand.new
stripe_commands.add(PostCreditCardData.new)
stripe_commands.add(HandleGatewayToken.new)
# ...etc.

persist_and_thank = CompositeCommand.new
persist_and_thank.add(SendThankYouEmail.new)
persist_and_thank.add(PersistOnSalesforce.new)
# ...etc.

stripe_gateway = PaymentGateway.new(stripe_commands, ThankYouEmail.new)
paypal_gateway = PaymentGateway.new(PaypalProcess.new, persist_and_thank)
```

# Other uses

Id this was an installation program, the wizard would ask us if we want to install X feature, and if we reply "_yes_", it would call `add` on the composite, creating a sort of "to-do" list on which to call the command method that will install all. Before this we have a chance to change our mind and `remove` features.

It can also be used for costy operations, for example doing a list of things on a databse, where opening a connection is expensive. You can collect the tasks and run them later.

Active record also uses commands for migrations:

```ruby
class CreateBookTable < ActiveRecord::Migration
  def self.up
    create_table :books do |t|
      t.column :title, :string
      t.column :author, :string
    end
  end

  def self.down
    drop_table :books
  end
end
```


## Resources

* [Command pattern at Source Making page](https://sourcemaking.com/design_patterns/command)
* [Sandi Metz's talk "Nothing is something"](https://www.youtube.com/watch?v=OMPfEXIlTVE)
