---
layout: post
title: Service Objects
category: Ruby
author: dmp0x7c5
---

Imagine you have really complex legacy code to work with and you run git blame just to append more and more names to your personal blacklist... Service objects will certainly help you. If you haven't heard about them before, here comes the shortest possible definition: Service object is just a plain old ruby object (PORO) responsible for ONE thing. Some people call them just object. That's it. It doesn't matter how you implement it or where you place it, the ONE thing is the key.


## Code

I like this scheme:

{% highlight ruby %}
module CandidateServices
  class ExperienceUpdater
    def initialize(candidate:)
      @candidate = candidate
    end

    def call
      release_the_kraken
    end
    
    private
    
    attr_reader :candidate
  end
end
{% endhighlight %}

Execution:

{% highlight ruby %}
CandidateServices::ExperienceUpdater.new(candidate: candidate).call
{% endhighlight %}

As we should focus on the only ONE thing, just one method defines its public interface. I've seen few different appraches but `call` seems to be a standard. If you need more probaly you should split logic into two separate objects. Probably because of lambdas and procs - no suprise, `call` rules here too:

{% highlight ruby %}
action = lambda { puts "I'm your service" }
action.call
#=> I'm your serivice
named_action = proc { |name| puts "#{name}, I'm your father" }
named_action.call("Luke")
#=> Luke, I'm your father
{% endhighlight %}


## Naming

The most important thing is how to name a service object properly. Make name short but include context, environment and its behaviour. Keep it unambiguous. Ask yourself what is's doing and ask your coworked what does he think after just hearing its name. I like to keep service objects separated in modules, just to indicate proper context.

I've noticed that developers have a tendency to stick to a class name given when the class was born. We're not afraid to rename method and variables inside a class (probably because we're seeing all occurences in one buffer, one file, in sight). It may be a few minutes work to replace all occurences and check if everything is working fine. Keep calm and rename your classes from time to time, it's worth it. Don't threat naming like: 'I have to figure out the best name now, because its forever. I'll show it to my grandson and tell him it's from 2015. when they killed Solo'.


## Handling legacy


## Gems

In the foreword I said that it should just be just a PORO. There is a few interesting gems at github to consider, especially when it comes to control flow between objects.


