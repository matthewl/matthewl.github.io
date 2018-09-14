---
layout: post
title: The RSpec refresher
---
I generally use [Minitest](https://github.com/seattlerb/minitest) for testing when I do client work, but recently I've been working on a project that uses [RSpec](http://rspec.info/) as the preferred testing library. Now, to say that my RSpec is rusty would be an understatement, but after a few days, it seems that it isn't much different from the spec version of Minitest.

For this post, we'll take a look at an example of an RSpec spec and all the different parts of it, as a way of familiarising ourselves with how it works.

Let's look at a spec for our Ruby on Rails application, which has a resource for accounts.

### Describing

We begin with the `describe` method. The `describe` method signifies the start of an example group. An example in RSpec is a test, so our `describe` method is indicating that this is the beginning of a group of tests.

{% highlight ruby %}
describe 'Accounts' do
  describe 'GET index' do
  end
end
{% endhighlight %}

In this case, we have a top-level `describe` block for the Accounts resource with a second nested `describe` block for the index action of our accounts controller. 

### Contexts

Contexts are much like `describe` blocks. They can improve the readability of your specs. They can also be helpful to signify we're running the test under certain criteria, like whether we're logged in or out. Let's take a look at our accounts spec and add some context to it.

{% highlight ruby %}
describe 'Accounts' do
  describe 'GET index' do
    context 'when logged in' do
    end

    context 'when logged out' do
    end
  end
end
{% endhighlight %}

We have added two new `context` blocks that show for the first test we are logged in and then for the second test show that we are logged out.

### The Spec

Finally, there's the example itself, the code that we run to assert that our code behaves as intended. To do this, we use the `it` method to define our example. It should look something like this:

{% highlight ruby %}
it 'shows all accounts' do
end
{% endhighlight %}

Next, we want to ensure that the page responds as expected. We'll use an assertion for this:

{% highlight ruby %}
it 'shows all accounts' do
  get accounts_path

  expect(response).to render_template(:index)
end
{% endhighlight %}

A few things are going on here, so let's look at them one line at a time. First, there's the command to get the accounts path. For our controller spec this could be a get, post, put or destroy. In this case, we're testing the response from getting the index action for the accounts resource.

Next, there's the assertion itself.

{% highlight ruby %}
expect(response).to render_template(:index)
{% endhighlight %}

Let's start with the `expect` method.

Previous versions of RSpec would have had an example like this:

{% highlight ruby %}
response.should render_template(:index)
{% endhighlight %}

The `expect` method takes an argument that could be any Ruby object. The assertion comes from the `to` method that we've passed through to response.

In this case, we're asserting that the response rendered the index template.

We should also add a few assertions for any expected accounts we should see and what should happen if we try to access the accounts page when we're not logged in. Let's add those in to see our final spec.

{% highlight ruby %}
describe 'Accounts' do
  describe 'GET index' do
    context 'when logged in' do
      it 'shows all accounts' do
        get accounts_path

        expect(response).to render_template(:index)
        expect(response.body).to match /ACC0001/
        expect(response.body).to match /ACC0002/
      end
    end

    context 'when logged out' do
      it 'redirects to the login page' do
        get accounts_path
            
        expect(response).to redirect_to(accounts_path)
      end
    end
  end
end
{% endhighlight %}

So what we have now is a set of tests for our accounts index action. It's simple, and there are areas where we could improve our specs, but let's save that for another day.

It's easy to see the final description for each example by concatenating the arguments passed into each `describe`, `context` and `it` method.

{% highlight console %}
Accounts GET index when logged in shows the accounts
Accounts GET index when logged out redirects to the login page
{% endhighlight %}

In other words, we expect the accounts GET index action to show a list of accounts when we're logged in, and we expect the GET index action to redirect to the login page when we're not logged in.

With the use of `describe`, `context` and `it` we have a spec the describes the expected behaviour accurately.

### Just A Different Syntax

RSpec isn't that much different from any other test framework out there once you get to the essential parts you need for your test. RSpec has a different syntax.

My return to RSpec has meant learning its new syntax, but I am finding that the new syntax is winning me over and I'm wondering if I should use RSpec for future client work.

Look out for the follow-up post to this one in the next couple of weeks as we look at reusing code in our specs and a few other goodies I've picked up from using RSpec again.