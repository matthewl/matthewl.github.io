---
layout: post
title: How to Build a JSON Feed for Your Rails Application
---
RSS is a stalwart and one of the early technologies of the Internet. [JSON Feed](https://jsonfeed.org/), however, is a new take on the existing RSS format. While RSS uses XML to build a feed, JSON Feed uses the simpler and more readable JSON, short for JavaScript Object Notation.

20 years ago XML was a perfectly suitable choice for creating RSS feeds, but over the years developers have gradually started using alternatives to XML. One such alternative is JSON. Its simple syntax and structure make it easy for developers to work with and is commonly used in APIs all over the Internet. JSON Feed was developed by Manton Reece, and Brent Simmons as a more developer-friendly way of building RSS feeds. 

JSON feeds serve the same purpose as RSS feeds: They provide a file that lists the most recent changes to a website or collection of articles. JSON Feed is only a few months old, but RSS feed readers like Feedbin have already begun adding JSON Feed compatibility.

How do you add a JSON feed to a Ruby on Rails application, then?

_In this example we’re using the same route, controller and action as the RSS feed we used in the previous example. For this article, we’re going to have just one feed in our Rails application, the JSON feed._

### Define the route

Like RSS feeds, we need to define a route for our JSON feed. In our Rails application’s `routes.rb` file, we’re going to define the route to our JSON feed.

{% highlight ruby %}
get '/default.json', to: 'feeds#index'
{% endhighlight %}

### Create a controller for your feed

Right, we have our route defined. Next, we want to connect the route to our ActiveRecord model that represents the changes in the website. Let’s create a controller to do that.

Just like our RSS feed, we’re going to create a _feeds_ controller and give it an _index_ action that serves the content for our feed.

{% highlight ruby %}
class FeedsController < ApplicationController
  def index
    @posts = Post.published

    respond_to do |format|
      format.json { render layout: false }
    end
  end
end
{% endhighlight %}

We’re responding to the index action’s request with a JSON file, so the `respond_to` block uses the `.json` method.

Just like an RSS feed, we’re rendering the response without a layout. There’s nothing to the JSON feed file other than its content. We don’t need a layout for this.

### Create the view to return the feed

Last but not least is the view file that contains the content of the feed in a format that meets the requirements of the JSON Feed standard. The file uses a _json_ extension and differs slightly in the syntax used. 

The view file contains some elements similar to those of an RSS feed.

* a unique identifier for the article
* the body of the article
* a link to the article

Your view file will look something like this:

{% highlight ruby %}
json.version 'https://jsonfeed.org/version/1'
json.title 'ACME Content'
json.home_page_url root_url
json.feed_url 'https://www.acmecontent.net/feed.json'

json.items @posts do |post|
  json.id post.id
  json.context_text post.title
  json.url post_url(post)
end
{% endhighlight %}

You’ll notice that the JSON feed has a lot less in it than the RSS feed example. For this article, we are using a simple version, but even a more complete JSON feed would still be quite simple.

Where your RSS feed view file has a `.rss.builder` extension, this view file will use a `.json.jbuilder` file extension. We’re using the [JBuilder gem](https://github.com/rails/jbuilder) to generate our view file contents. 

Finally, let’s make our JSON discoverable.

{% highlight html+erb %}
<%= auto_discovery_link_tag(:json, { controller: 'feeds/default', action: 'index' }, { title: 'Recent prompts', type: 'application/json' }) %>
{% endhighlight %}

The discovery tag differs slightly from an RSS feed in that we’re defining the response type because it’s going to be a JSON response.

### What are the benefits?

While the adoption of the JSON Feed standard is still growing, you might question the benefit of adding a JSON feed to your Rails application.

There are many arguments for and against all standards used on the Internet, but I lean towards open standards that make accessibility of websites and applications easy. It’s for that reason that while I would recommend adding a JSON feed to your website, it’s not an urgent thing to do. It’s just more of a nice thing to have.

If you’re building a web-based product, though, that uses feeds as a delivery method for content, then I would put this nearer the top of your product's next feature list.

The JSON Feed standard is still young, but as a more developer-friendly version of RSS, it’s worth looking into, especially if you know the consumers of your feeds are using JSON Feed compatible feed readers.

With new technology, it’s always a case of ensuring there is high enough adoption before you start learning and using that technology yourself. However, the ease with which you can add a JSON feed into your Rails application means that it’s a low-risk feature that can be added to most Rails applications in little time.