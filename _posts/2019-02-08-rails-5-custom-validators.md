---
layout: post
title:  "Rails 5 Custom Validators"
date:   2019-02-08 12:00:00 -0500
tags: ["Ruby on Rails", "Testing"]
---

One of my recent projects required me to give users a form that asked them to enter a date. Unfortunately, they had to enter that date in a very specific format: FullMonth, FullYear. But, it gave me an excuse to try out the new Rails 5 custom validators class. Check it out:

```ruby
# app/models/concerns/custom_date_validator.rb
class CustomDateValidator < ActiveModel::EachValidator
  DATE_FORMAT = '%B, %Y'

  def validate_each(record, attribute, value)
    if correct_format?(value)
      record.send("#{attribute}=", formatted_time(value))
    else
      record.errors[attribute] << (options[:message] || 'is not a valid format')
    end
  end

  def correct_format?(value)
    /(Jan(uary)?|Feb(ruary)?|Mar(ch)?|Apr(il)?|May|Jun(e)?|Jul(y)?|Aug(ust)?|Sep(tember)?|Oct(ober)?|Nov(ember)?|Dec(ember)?),\s+\d{4}/.match?(value)
  end

  def formatted_time(value)
    DateTime.strptime(value, DATE_FORMAT).strftime(DATE_FORMAT)
  end
end
```

`correct_format?` uses regexp to check if the input string is in the correct format. If it doesn't work, the attribute validator is triggered and the correct error is given to the model.

If it does match the regexp statement, `formatted_time` with make sure values that are close enough (i.e. "February" vs. "Feb") are all normalized. Implementing it is **super** easy. Rails will use the validator class name to give you a way of calling it from models:

```ruby
# app/models/post.rb
class Post < ApplicationRecord
  # Notice how this is close to CustomDateValidator. Rails just removed
  # "Validator" from the class name and snake_cased the rest.
  validates :start_date, custom_date: true
end
```

Writing tests using the test suite built into Rails (MiniTest) is super easy too:

```ruby
# test/models/concerns/custom_date_validator_test.rb
require 'test_helper'

class Validatable
  include ActiveModel::Model
  include ActiveModel::Validations

  attr_accessor :start_date

  validates :start_date, custom_date: true
end

class CustonDateValidatorTest < ActiveSupport::TestCase
  test 'accepts the correct format' do
    assert Validatable.new(start_date: 'February, 2019').valid?

    variation = Validatable.new(start_date: 'Feb, 2019')
    assert variation.valid?
    assert_equal 'February, 2019', variation.start_date
  end

  test 'rejects random inputs' do
    assert_not Validatable.new(start_date: 'February, 19').valid?
    assert_not Validatable.new(start_date: '2-2019').valid?
    assert_not Validatable.new(start_date: '02-2019').valid?
    assert_not Validatable.new(start_date: '02-19').valid?
    assert_not Validatable.new(start_date: '02-01-2019').valid?
    assert_not Validatable.new(start_date: '02-01-19').valid?
    assert_not Validatable.new(start_date: '2-1-19').valid?
    assert_not Validatable.new(start_date: 'random').valid?
  end
end
```

Hope this helps!
