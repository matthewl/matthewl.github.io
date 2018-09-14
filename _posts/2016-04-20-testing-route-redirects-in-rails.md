---
layout: post
title: Testing route redirects in Rails
---
If it’s a line of code that I have written in a Rails application, then I’ll have a test written for it. That’s the general rule to test-driven development, and I try to stick with it as much as possible. There is, however, a part of Rails applications that I frequently see missed when it comes to testing, and that’s the routes file.

Yes, there are integration tests and controller tests for Rails, but as an added failsafe, I like to include routing tests in my Rails applications where I can.

I use the `assert_routing` method to assert that a path and the options for it both match. It’s a two-way assertion as it tests both the routes and the options for the route. I’ve never had to use anything else for testing my routes.

This week though, a client request came in to redirect a couple of old routes that were recently removed to a new route for a sign-up page. That’s easy to do in the routes file with the use of the `redirect` method. 

{% highlight ruby %}
Rails.application.routes.draw do

  get '/sign_up', to: redirect('/start')

  # … the rest of the routes … 
end
{% endhighlight %}

However, the use of the `assert_routing` method won’t work with the redirect as we only want to test that we can redirect to the right path.

If you look at a routes test file and an integration test file, you’ll see that they both inherit from the same class, `ActionDispatch::IntegrationTest`. With that in mind, I can now write a test that visits a path and asserts that the redirection is to the correct path. A mini-integration test if you like. I ended up with something like this:

{% highlight ruby %}
require 'test_helper'

class SignUpRoutesTest < ActionDispatchIntegrationTest

  test “redirect old sign up path to new sign up path” do
    get “/sign_up”

    assert_redirected_to “/start”
  end
end
{% endhighlight %}

Writing tests for your routes are just as important as writing tests for any other part of your Rails application. Also, that includes testing routes that redirect. Given that my test is just four lines long, it’s just as simple to test routes that redirect as it is to test the resource routes in your Rails applications.