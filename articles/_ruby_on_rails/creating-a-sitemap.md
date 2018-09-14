---
layout: post
title: Creating a sitemap with Ruby on Rails
section: Recipes
---
One of the easiest ways that you can improve your search engine rankings is by creating a sitemap of your SaaS product and submitting it to search engines. Sitemaps don't replace the crawlers search engines use to index sites, but they can help those crawlers by providing extra metadata on the URLs that make up your site, such as how often those pages are updated.

If your site has content that changes over time, it helps to keep your sitemap updated so that search engines can track those changes to your site.

Let's build a basic sitemap for a Ruby on Rails application. 

### Add a route for the sitemap

Using an "outside in" approach, we'll start with the necessary route for the sitemap by updating the routes file, `routes.rb`.

{% highlight ruby %}
get 'sitemap', to: 'sitemap#index', constraints: { format: 'xml' }
{% endhighlight %}

We only need the index action for our sitemap. We have also defined a constraint on the route so that it only accepts XML requests.

### Add a controller for the sitemap

Now we'll create a controller for our sitemap. 

{% highlight ruby %}
class SitemapController < ApplicationController
end
{% endhighlight %}

A sitemap is an XML document, so we'll need to tell the action that we want to respond to any requests to the controller with XML. We can do that with the `respond_to` method on the controller.

### Add an action that renders the sitemap

Next, we'll add the `index` action for our controller.

{% highlight ruby %}
def index
  respond_to do |format|
    format.xml
  end
end
{% endhighlight %}

A sitemap is an XML document, so we'll need to tell the action that we want to respond to any requests to the action with XML. We can do that with the `respond_to` block on the action.

We'll define the list of pages within the sitemap view (see below) rather than in the controller. We'll set this up with the typical set of pages, but if you have any other pages, feel free to add them similarly.

### Add the sitemap view

Next, we'll add the view to our sitemap.

{% highlight html+erb %}
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.sitemaps.org/schemas/sitemap/0.9 http://www.sitemaps.org/schemas/sitemap/0.9/sitemap.xsd">
  <url>
    <loc><%= root_url %></loc>
    <changefreq>weekly</changefreq>
  </url>
  <url>
    <loc><%= new_signup_url %></loc>
    <changefreq>weekly</changefreq>
  </url>
  <url>
    <loc><%= pages_url %></loc>
    <changefreq>weekly</changefreq>
  </url>
  <url>
    <loc><%= terms_url %></loc>
    <changefreq>weekly</changefreq>
  </url>
  <url>
    <loc><%= privacy_url %></loc>
    <changefreq>weekly</changefreq>
  </url>
</urlset>
{% endhighlight %}

What we have added here is a set of URLs that we want to submit to the search engine. The URL itself is within the `loc` tag, and the frequency with which we want the search engine to check the page is in the `changefreq` tag. 

The valid options for the `changefreq` tag are hourly, daily, weekly, monthly, yearly, or never. If the page in question is unlikely to change very often, set the `changefreq` tag to monthly or even yearly. For other pages that change more frequently, like your key landing page(s), I recommend setting the `changefreq` tag to weekly.

### Updating the sitemap

The sitemap contains some pages that aren't likely to change, but what about content that changes more frequently, like blog posts? Perhaps you have a blog on your SaaS product or even a group of product pages that are frequently updated. How do we include this changing content without having to amend the sitemap file each time manually?

With Ruby on Rails it's simple to pass the pages through that you want to change, and update the sitemap with these changes.

Let's say our SaaS product has a blog with a collection of posts that we use to highlight new features of the product and updates to it. We can amend the controller so that it returns all the posts on our blog.

{% highlight ruby %}
class SitemapController < ApplicationController
  def index
    @posts = Post.all
    respond_to do |format|
      format.xml
    end
  end
end
{% endhighlight %}

Now that we have the posts as an instance variable, we can include it in our sitemap. Since this information changes on a regular basis, we'll set each of our posts to have a `changefreq` of monthly.

In our sitemap, we can now tell search engines about these new posts on our blog.

{% highlight html+erb %}
<urlset xmlns="http://www.sitemaps.org/schemas/sitemap/0.9" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://www.sitemaps.org/schemas/sitemap/0.9 http://www.sitemaps.org/schemas/sitemap/0.9/sitemap.xsd">
  <url>
    <loc><%= root_url %></loc>
    <changefreq>weekly</changefreq>
  </url>
  <url>
    <loc><%= new_signup_url %></loc>
    <changefreq>weekly</changefreq>
  </url>
  <url>
    <loc><%= pages_url %></loc>
    <changefreq>weekly</changefreq>
  </url>
  <url>
    <loc><%= terms_url %></loc>
    <changefreq>weekly</changefreq>
  </url>
  <url>
    <loc><%= privacy_url %></loc>
    <changefreq>weekly</changefreq>
  </url>
  
  <% @posts.each do |post| %>
    <url>
      <loc><%= url_for(post) %></loc>
      <changefreq>monthly</changefreq>
    </url>
  <% end %>
</urlset>
{% endhighlight %}

Now we can keep the sitemap updated with each blog post we publish,  without having to add these new posts manually. Nice!

### Let's deploy it!

Once our sitemap is complete, there's the simple matter of deploying it to our preferred hosting stack and then submitting that sitemap to search engines. [Bing](https://www.bing.com/webmaster/help/how-to-submit-sitemaps-82a15bd4) allows you to submit your sitemap without any other requirements, but Google does require you to have a Google account before you can submit your sitemap to them.

You can also add your sitemap to your robots.txt file so that when a search engine crawls your site for new content, it will redirect to the sitemap from the robots.txt file. You can include something like this in the file so that search engine crawlers can find your sitemap:

{% highlight console %}
Sitemap: https://www.myamazingproduct.com/sitemap.xml
{% endhighlight %}

With a little investment in time and effort, your SaaS product can have a sitemap that updates on its own and requires little in the way of maintenance. Like all SEO recommendations, sitemaps aren't the only solution to improving your search engine rankings, but they are one small change that will help round out your SEO strategy.