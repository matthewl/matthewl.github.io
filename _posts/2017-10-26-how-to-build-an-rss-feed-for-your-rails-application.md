---
layout: post
title: How to Build an RSS Feed for Your Rails Application
---
The rise of social media platforms has seen RSS uptake fall, but that doesn't mean it's not something to leave out of your Ruby on Rails application.

An RSS feed is just a file that lists the most recent changes to a website or a collection of articles. Many websites still utilise RSS feeds, and a lot of CMS platforms have this baked right into the system.

How do you add an RSS feed to a Ruby on Rails application?

### Define the route

The first thing we need to do is define a route for it. The route is the endpoint for the RSS feed, and it's what others look for when subscribing to your site. In our application’s `routes.rb` file, we define a route to our feed.

{% highlight ruby %}
get '/default.rss', to: 'feeds#index'
{% endhighlight %}

### Create a controller for your feed

The next step in the process is to connect the endpoint to the ActiveRecord model whos changes we want to see. 

From our route we need to define a feeds controller and add to it an index action that will provide the content for our feed.

{% highlight ruby %}
class FeedsController < ApplicationController
  def index
    @posts = Post.published

    respond_to do |format|
      format.rss { render layout: false }
    end
  end
end
{% endhighlight %}

Nothing here that you haven’t done before, but one thing to note is that we don’t want to render a layout with our feed, so we pass `false` to the layout option when rendering our feed.

You don’t have to use a new controller for this. If you already have a controller that is accessible from the Internet and delivers the same content but to the browser, then you can use the existing controller also to deliver the feed.

{% highlight ruby %}
class PostsController < ApplicationController
  def index
    @posts = Post.published

    respond_to do |format|
      format.html { render :index }
      format.rss { render layout: false }
    end
  end
end
{% endhighlight %}

### Create the view to return the feed

Finally, there's the creation of the view. The view is typically what the end-user sees in a Rails application, but in this case, it's going to be what the RSS reader sees.

The view is going to be an XML file that with elements typically found in an RSS feed. These are:

* A unique identifier for the article
* A title for the article
* The body of the article
* Timestamps for the article

The view will look something like this:

{% highlight ruby %}
xml.instruct! :xml, :version =\> '1.0' 
xml.rss :version =\> '2.0' do
  xml.channel do
    xml.title 'ACME Content'
    xml.description 'Our latest posts'
    xml.link root_url

    for post in @posts
      xml.item do
        xml.title post.title
        xml.description post.body
        xml.pubDate post.published_at.to_s(:rfc822)
        xml.link post_url(post)
        xml.guid post.id
      end
    end
  end
end
{% endhighlight %}

There’s a couple of things to note here.

The view uses a _.builder_ extension rather than an _.erb_ extension. The reason for this is we’re using the [Builder gem](https://github.com/tenderlove/builder) to format our feed into XML.

We’re using an RFC format of the date to help validate the feed. 

We haven’t included this in the view file, but you can cache your RSS feed for a period so that feed readers aren’t continually hitting the endpoint and in turn hitting your database.

### Auto-discovery feeds

Have you noticed when you subscribe to a website using an RSS reader that for some sites you can pass in the website URL, and you get the URL of the  RSS feed?

Well, the reason this works is that the website contains a link that tells the RSS reader where the sites RSS feed is.

So how do we do this in Rails?

Well, it’s quite simple. By using the `auto_discovery_link_tag` we can add our site’s RSS feed to the HTML markup so that RSS readers can get it.

{% highlight html+erb %}
<%= auto_discovery_link_tag(:rss, { controller: 'feeds/default', action: 'index' }, { title: 'Recent articles' }) %>
{% endhighlight %}

### Testing strategy

Ideally, we would write some tests to ensure that the endpoint is responding with a success code and that the response itself contains the right elements for the RSS subscriber to see.

### What are the benefits?

Not only is RSS a subscription channel to your website, but RSS can also be used as an integration endpoint for subscribers and other platforms.

Two platforms that spring to mind are [IFTTT](https://ifttt.com/) and [Zapier](https://zapier.com). Both of these platforms have channels that can act on new items published to an RSS feed.

Social media automation is one way of utilising this, and it gives you more control over what to filter through to your social media channels as well.

RSS is an early Internet technology, but its usefulness is often overlooked. As a way for people to subscribe to your website, it's the first step in attracting people to your website, and the easiest one for people to engage with.