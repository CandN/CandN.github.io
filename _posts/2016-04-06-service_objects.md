---
layout: post
title: Service Objects
category: Ruby
author: dmp0x7c5
---

Imagine you have really complex legacy code to work with and you run git blame just to append more and more names to your personal blacklist. Service objects will certainly help you. If you haven't heard about them before, here comes the shortest possible definition: Service objects are just a plain old ruby objects (PORO) responsible for ONE thing. Some people call them just objects. That's it. It doesn't matter how you implement it or where you place it, the ONE thing is the key.


## Code

I like this scheme:

{% highlight ruby %}
module RebelServices
  class EmpireDestroyer
    def initialize(jedi:)
      @jedi = jedi 
    end

    def call
      jedi.use_the_force
    end
    
    private
    
    attr_reader :jedi
  end
end

# Execution:
RebelServices::EmpireDestroyer.new(jedi: luke).call
{% endhighlight %}

Some developers like to just use `self.call` without instantiating in the execution as in most cases you don't need to use the instance outside:

{% highlight ruby %}
module RebelServices
  class EmpireDestroyer
    def initialize(jedi:)
      @jedi = jedi 
    end

    def self.call(jedi:)
      self.new(jedi: jedi).call
    end
    
    def call
      jedi.use_the_force
    end
    
    private
    
    attr_reader :jedi
  end
end

# Execution:
RebelServices::EmpireDestroyer.call(jedi: luke)
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

## Handling legacy

In the foreword I've said that service objects will help you deal with legacy stuff. Just decouple logic from the code into smaller services (objects). In general, as a human, developer tends to understand small pieces much better than really complex stuff with 100 lines in a single method. We like to see everything important in just a few lines. I'm not saying that service objects will solve all your complexity problems, but it's a good start.


## Naming

The most important thing is how to name a service object properly. Make name short but include context, environment and its behaviour. Keep it unambiguous. Ask yourself what is's doing and ask your coworked what does he think after just hearing its name. I like to keep service objects separated in modules, just to indicate proper context.

I've noticed that developers have a tendency to stick to a class name given when the class was born. We're not afraid to rename methods and variables inside a class (probably because we're seeing all occurences in one buffer, one file, in sight). It may be a few minutes work to replace all occurences and check if everything is working fine. Keep calm and rename your classes from time to time, it's worth it. Don't threat naming like: 'I have to figure out the best name now, because its forever. I'll show it to my grandson and tell him it's from 2015, when they killed Solo'.

## Gems

In the foreword I said that it should just be just a PORO, of course there are gems for everything (even asking about [starwars](https://github.com/moski/starwars)). In case of service objects there are few interesting ones at github to consider, especially when it comes to control flow between objects.

#### interactor [collectiveidea/interactor](https://github.com/collectiveidea/interactor)

They called service objects - interactors, like I said call them whatever you want, even one-thing-doers is fine as long they do ONE thing. Check their github page for more but in short: interactors are doing just ONE thing, are equipped with: error handling, afer/before/around hooks, shared context, rollbacks and organizers (helping you with execution multiple interactions in a single flow).

Short example:


#### waterfall [apneadiving/waterfall](https://github.com/apneadiving/waterfall)

Waterfall is to control the flow / chain block execution / share context (outflow) and handle errors. Each waterfall contains context (`outflow`), `error_pool` and to controll the flow we're using `chain`s with blocks.

To show how can it be used with one-thing-doers I've prepared small example in the context of well known long long time ago story:


#### interactor vs waterfall

I found interactor easier to just jump in and solve problems, waterfall requires more reading and some ideas might not be easy to understand at first sight, understanding basic glossary of chains, outflows and dam is the key. With this a bit more compliated looking it might be used to solve more complex flows than with interactor.


## Sidenote

Let me know what do you think about service objects: How you use them? And why you love/hate them?


