---
layout: post
title: Custom controller actions
section: Recipes
---
When I first started writing Rails applications, I made tonnes of coding mistakes. I cut and pasted code, my model layer usually ended up being a set of god objects, and I treated my helpers like a dumping ground for anything that didn't quite fit anywhere else. Thankfully, my code has got better over the years, but I'm still learning and not a week goes by where I don't question a bit of code that I've written.

A couple of weeks ago I came across a controller I had written a year ago for a client. It contained the seven resource actions common to all Rails controllers as well as three additional custom actions. Not a huge problem, if I'm honest, but the additional actions in the controller make the controller bigger than it should be. The controller already contained enough code for the resourceful actions. I asked myself if the custom actions should be kept in this controller or should they be moved elsewhere.

When you have a RESTful resource in a Rails application that already has the seven default actions, it can be tempting to add on additional controller actions. What you'll end up with, though, is a large controller class and an equally large test class for it. For an extra one or two actions this is okay, but when does the controller class become too big?

Some programmers would rightly argue that if the custom action shares the same resource as the present Rails actions, then that custom action should be included in the same controller. There's nothing to stop us from doing that. After all, the Rails guides encourage custom actions, if needed.

> You are not limited to the seven routes that RESTful routing creates by default. If you like, you may add additional routes that apply to the collection or individual members of the collection.
>
> &mdash; [Rails Guides - Rails Routing from the Outside In](http://guides.rubyonrails.org/routing.html)

My concern was that these controllers were becoming bigger and bigger. One controller contained three custom actions on top of the seven default actions. The file wasn't huge when compared to classes in the model layer, but I wondered if it would be better to extract these custom actions into separate controllers.

Over the last few weeks, I've been working on a Rails application with some custom controller actions. I've been moving some of these out into separate controllers using the following method.

First, we create a folder in our controllers' folder. So if you have a controller called `Tasks` then we create a folder in the controllers' folder called *"tasks"*. You'll end up with something like this:

{% highlight console %}
~/project_root/app/controllers/tasks
{% endhighlight %}

Next, we create a controller in this folder for our custom action. So let's say we have a custom action for tasks called "complete". We create a controller which is namespaced under our tasks folder and contains a single action. What we end up with is something like this:

{% highlight ruby %}
class Tasks::CompleteController < ApplicationController
  
  def complete
    # ...
  end

end
{% endhighlight %}

The benefits of this are quite clear. A smaller code file that encapsulates the single custom action is easy to locate within our application, and by removing it from our resourceful controller, we also reduce the size of the original controller.

Finally, as one last change, we'll add a route for custom action.

{% highlight ruby %}
# routes.rb

put '/tasks/:id/complete' => 'tasks/repeat#complete', as: 'complete_task'
{% endhighlight %}

Now we have a custom action for our resource, and while it follows the same URL format as the tasks resource, it's separated from the resourceful controller to keep my code organised.

Is this the best way to handle custom actions?

Maybe, maybe not, but my argument for it is that if the controller already contains a full set of original actions as well as two or more custom actions, then it's time to move these custom actions elsewhere.

We should also remember that if we have some custom actions mixed in with resourceful controllers we could, in fact, have a resource there already. We would need to identify the similar action for that action and determine if we can use an existing action.