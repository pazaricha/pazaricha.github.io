---
layout: post
title:  'Difference between "let" and "before each" in Rspec'
categories: tests
comments: true
---
Rspec is a great ruby library for writing tests (or "specs" as they are called in Rspec). It includes lots of great helper methods that can help you save time and write faster more performant tests.

Often times when you're writing a test you need to prepare some data or do some action before that test gets run. 

RSpec provides 2 main ways of achieving this:

1. Via the `let` statement.
2. Via the `before :each` statement.

### But what's the difference between the two?

In short, `let` blocks are "lazy" and only gets evaluated the first time they are called in your tests, and `before :each` blocks are run before *every test* (within the same context/describe block it's written in), regardless of whether you use or need what's done inside them.

### Let's see a *very* simple example in order to understand the difference.

In the following code, we create two user instances and then we do a test on one of them. Let's see the difference between `let` and `before :each` in this case.

Example using `let`:

{% highlight ruby %}
let(:user_without_name) { User.new(name: nil, age: 28, gender: "Male" ) }
let(:user_without_age) { User.new(name: "Paz", age: nil, gender: "Male" ) }

it "fails to save the user if the name is blank" do
  user_without_name.save
  expect(user_without_name.errors.keys).to include(:name)
end
{% endhighlight %}

Example using `before :each`:

{% highlight ruby %}
before :each do
  @user_without_name = User.new(name: nil, age: 28, gender: "Male" )
  @user_without_age = User.new(name: "Paz", age: nil, gender: "Male" )
end

it "fails to save the user if the name is blank" do
  @user_without_name.save
  expect(@user_without_name.errors.keys).to include(:name)
end
{% endhighlight %}

In the `let` example the "user_without_name" is the only instance created when running the test as the "user_without_age" never gets called in the test.

However, In the `before :each` example both the "@user_without_name" and the "@user_without_age" are created when the test is run even though "@user_without_age" never gets called.

### When should you use each one?
When you need to decide if you should use `let` or `before :each` in your test, think if you will need **everything** that runs inside your block to happen before **every** test. If the answer is no - use `let`.

