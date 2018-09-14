---
layout: post
title: Custom assertions with Minitest
section: Testing
---
[Minitest](https://github.com/seattlerb/minitest) is a great testing framework for new and experienced programmers. With assertions limited to a number that makes them easier to recall it levels the playing field for new programmers allowing them to get up to speed in no time.

I very rarely have to use anything other than Minitest's own set of assertions in my day to day work, but every once in a while there's an exception to this. It might be a collection of assertions that could be refactored into one assertion, or it could be a part of your code that requires a specific assertion that can't be easily handled by Minitest's assertions.

Let's look at such a case.

I'm using assertions to test the contents of an email I send. The email has templates for both plain text and HTML. At the moment, I'm testing each assertion on its own.

{% highlight ruby %}
assert_match(/Hi there #{person.first_name}/, mail.text_part.to_s)
assert_match(/Hi there #{person.first_name}/, mail.html_part.to_s)
{% endhighlight %}

I have a few of these tests around my code for different emails that I am sending. The duplication of testing different templates of the email isn't a significant concern, but it could certainly be tidied up. Wouldn't it be nice to wrap these two assertions into one?

Minitest allows you to create custom assertions, but up until this time I made do with Minitest and Rails' assertions. Adding custom assertions to Minitest is quickly done.

I included the following assertion in my *test_helper.rb* file but you can include your custom assertions in a separate file if you want to and then include that in your *test_helper.rb* file using a *require*.

{% highlight ruby %}
module Minitest::Assertions
  def assert_mail_match(expected, actual)
    ['text','html'].each do |mail_part|
      assert_match(expected, actual.send("#{mail_part}_part").to_s)
    end
  end
end
{% endhighlight %}

What we're doing is adding a wrapper around Mintest's *assert_match* method and pass in our expected value and the actual value. The method will test the two mail parts (in this case plain text and HTML) in the mailer and assert that each format has the correct corresponding content.

We can now call the new assertion in our tests:

{% highlight ruby %}
assert_mail_match(/Hi there #{person.first_name}/, mail)
{% endhighlight %}

Wrapping these two assertions into one makes sense. The template formats for the mailers are unlikely to change, and the two different formats have the same content albeit with different formatting.

After a couple of years of using RSpec, Minitest has been a nice change to how I test the Rails applications I'm working on. Its syntax and single way of doing things means that I find it much easier to write tests with Minitest than I do with RSpec.
