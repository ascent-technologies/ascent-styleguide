# Rails

*   [ActiveRecord](#activerecord)
*   [Time](#time)
*   [Concerns](#concerns)
    *   [Models](#models)
    *   [Controllers](#controllers)
*   [Service Objects](#service-objects)
*   [Presenters](#presenters)

Inspired by [https://github.com/bbatsov/rails-style-guide](https://github.com/bbatsov/rails-style-guide).

## ActiveRecord

*   When using `enum`, explicitly define the value of each key. Avoid using `0` as a value, because `0`'s semantic
    meaning can be ambiguous.

    Reasons why `0` is ambigous:

    *   Sometimes `0` means `false`, but it's presence is actually considered "truthy".

    *   The default value for an integer column in relational databases is `0`. Is a `0` in the DB an intentional write
        or a result of accidentally applied default?

    ```ruby
    # bad
    enum colors: [ "red", "green", "blue" ]

    # avoid using 0
    enum colors: { red: 0, green: 1, blue: 2 }

    # good
    enum colors: { red: 1, green: 2, blue: 3 }
    ```

*   Group macro-style methods (`has_many`, `validates`, etc) in the beginning of the class definition.

    ```ruby
    class User < ActiveRecord::Base
      # Includes and extends
      include Indexable

      # constants come up next
      COLORS = %w[ red green blue ]

      # afterwards we put attr related macros
      attr_accessor :formatted_date_of_birth

      attr_accessible :login, :first_name, :last_name, :email, :password

      # enums after attr macros, prefer the hash syntax
      enum eye_color: { blue: 1, brown: 2, hazel: 3, other: 4 }

      # followed by association macros
      belongs_to :country

      has_one :address

      has_many :authentications, dependent: :destroy

      # and validation macros
      validates :email, presence: true
      validates :username, presence: true
      validates :username, uniqueness: { case_sensitive: false }
      validates :username, format: { with: /\A[A-Za-z][A-Za-z0-9._-]{2,19}\z/ }
      validates :password, format: { with: /\A\S{8,128}\z/, allow_nil: true }

      # next we have callbacks
      before_save :cook
      before_save :update_username_lower

      # other macros (like devise's) should be placed after the callbacks

      # ...
    end
    ```

*   Always use `has_many :through` over `has_and_belongs_to_many`. Using `has_many :through` allows additional
    attributes and validations on the join model.

    ```ruby
    # bad - using has_and_belongs_to_many
    class User < ActiveRecord::Base
      has_and_belongs_to_many :groups
    end

    class Group < ActiveRecord::Base
      has_and_belongs_to_many :users
    end

    # good - using has_many :through
    class User < ActiveRecord::Base
      has_many :memberships
      has_many :groups, through: :memberships
    end

    class Membership < ActiveRecord::Base
      belongs_to :user
      belongs_to :group
    end

    class Group < ActiveRecord::Base
      has_many :memberships
      has_many :users, through: :memberships
    end
    ```

*   Use `find_each` to iterate over a collection of AR objects. Looping through a collection of records from the
    database (using the `all` method, for example) is very inefficient since it will try to instantiate all the objects
    at once. In that case, batch processing methods allow you to work with the records in batches, thereby greatly
    reducing memory consumption.

    ```ruby
    # bad
    Person.where('age >= 21').each do |person|
      person.party_all_night!
    end

    # good
    Person.where('age >- 21').find_each do |person|
      person.party_all_night!
    end
    ```

## Time

*   Don't use `Time.parse`.

    ```ruby
    # bad
    Time.parse('2015-03-02 19:05:37') # => Will assume time string given is in the system's time zone.

    # good
    Time.zone.parse('2015-03-02 19:05:37') # => Mon, 02 Mar 2015 19:05:37 EET +02:00
    ```

*   Don't use `Time.now`.

    ```ruby
    # bad
    Time.now # => Returns system time and ignores your configured time zone.

    # good
    Time.zone.now # => Fri, 12 Mar 2014 22:04:47 EET +02:00
    Time.current # Same thing but shorter.
    ```

## Concerns

*   Consider using a concern module to group similar functionality together as a way to avoid monolithic classes.

### Models

*   Model concerns that are included in multiple classes should live in `app/models/concerns`.

*   Model concerns that are only included in a single class should live in `app/models/concerns/{my_model}_concerns`.

    *   Concerns should be named for the model that are intended to modify.

    *   Given a `User` model, a concern that deals with authorization might be named `UserConcerns::Authorization`.

#### Example

```ruby
# app/models/user.rb
class User < ApplicationRecord
  include UserConcerns::Authentication
end
```

```ruby
# app/models/concerns/user_concerns/authentication.rb
module UserConcerns
  module Authentication
    extend ActiveSupport::Concern

    included do
      has_one :user_authenticator, dependent: :destroy
    end

    def authenticate
      # ... do something authentic
    end
  end
end
```

### Controllers

*   Controller concerns that are included in multiple classes should live in `app/controllers/concerns`.

*   Before creating a controller concern that is included in a single class, consider if a service object or a presenter
    would be more appropriate.

    *   Controller concerns that are only included in a single class should live in `app/controllers/concerns/{my_controller}_concerns`.

    *   Concerns should be named for the controller that are intended to modify.

    *   Given a `UsersController` class, a concern that deals with authorization might be named `UsersControllerConcerns::Authorization`.

#### Example

```ruby
# app/controllers/concerns/presentable.rb
module Presentable
  extend ActiveSupport::Concern

  # Creates a `@presenter` instance variable for use in your action/view.
  def use_presenter(name, *args)
    @presenter = "Presenters::#{ name.to_s.camelize }".constantize.new(self, view_context, params, *args)
  end
end
```

## Service Objects

A service object is a less structured way to extract action functionality from a controller. It provides a more explicit
(and therefore potentially easier to follow) method of extracting functionality than controller concerns, and is not
typically used in cases where knowledge of the view is necessary (as with presenters).

This provides a means of letting the controller action retain the overall flow of logic that it will perform, while
abstracting away the details to a class that can provide additional meaning and be tested independently of the
controller (e.g. in tests, but also in the rails console).

Service objects can also be well suited to code that happens in multiple actions in a single controller - or even
multiple different controllers.

Services should live in `app/lib/services`.

#### Example

```ruby
class UserRegistrationService
  def initialize(user)
    @user = user
  end

  def should_activate?
    # something based on @user
  end

  def activate!
    # do something
  end
end
```

```ruby
class UsersController < ApplicationController
  def activate
    service = UserRegistrationService.new(user_to_activate)

    if service.should_activate?
      if service.activate!
        # show user something great

      else
        # tell user something dissapointing
      end

    else
      redirect_to root_path, flash: { error: "sorry bud" }
    end
  end
end
```

## Presenters

The concept of a presenter is like a combination of service objects and helper modules. They act as an intermediary
between the controller and the view.

If you have a long (lines-of-code wise) action that sets a lot of member variables that are later used in a view, a
presenter can help you extract the functionality of that action and move the coupling with the view out of the
controller.

Another use of a presenter would be to extract complex calculations/logic that are tied to the view from the controller
into a discreet class.

Presenters should live in `app/lib/presenters` and inherit from `Presenters::Base`.

#### Example

```ruby
class Presenters::Activities < Presenters::Base
  def initialize(controller, view_context, params)
    super

    @per_page = params[:limit].presence || 20
  end

  def activities
    @activities ||= elasticsearch_results.records.with_read_marks_for(current_user)
  end

  # ... more public methods

  private

  def elasticsearch_results
    # ...
  end

  # ... more private methods
end
```
