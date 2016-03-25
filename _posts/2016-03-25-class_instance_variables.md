---
title: Class instance variables
author: matDobek
---

It's hot summer Sunday, cloudless sky, you lie on the beach. Sun bathes your body, and you lie there smiling to yourself, that you don't have anything to do. That's a good day. You lazily pull out your hand for the phone, and start reading twitter. Rspec 3.3 is released, Matz ate spaghetti for dinner, Matt is wondering about class instance variables, Grieg bought the new bike.

Wait what? `Class instance variables`? What the hell is that? Can I eat it? Why don't I know anything about it?

Calm down, clamn down.

Start thinking about sea. Yiiiis, beautiful, wide sea. You hear seagulls, not that far screaming something. You start listening more carefully.

    Class instance variables!
    Class instance variables!
    Class instance variables!

Dammit! You're doomed. Your break is over. Ruby needs you.
You take your car, and drive to your home as fast as you can. Even faster than that. As soon as you arrive, you park the car, enter the house, open your laptop, and start typing: `Class instance variables`.

## Class variables

Probably most of you already know what class variables are, but lets hit refresh.
Class variable is shared across all instances of class + all instances of subclasses.

{% highlight ruby %}
class Animal
  def self.animals_created
    @@animals_created ||= []
  end

  def self.add_animal(instance)
    @@animals_created << instance
  end

  def initialize
    self.class.animals_created
    self.class.add_animal(self)
  end
end

class Shark < Animal
end

class Seagull < Animal
end

shark = Shark.new
seagull1 = Seagull.new
seagull2= Seagull.new

puts Animal.animals_created.count #=> 3
puts Shark.animals_created.count #=> 3
puts Seagull.animals_created.count #=> 3
{% endhighlight %}

`Animal` , `Shark` and `Seagull` share the same `animals_created variable`, among all instances.

## Class instance variables

Class instance variable, is shared across all instances of class, but each subclass, has it's own copy of class instance variable.

{% highlight ruby %}
class Animal
  def self.animals_created
    @animals_created ||= []
  end

  def self.add_animal(instance)
    @animals_created << instance
  end

  def initialize
    self.class.animals_created
    self.class.add_animal(self)
  end
end

class Shark < Animal
end

class Seagull < Animal
end

shark = Shark.new
seagull1 = Seagull.new
seagull2= Seagull.new

puts Animal.animals_created.count #=> 0
puts Shark.animals_created.count #=> 1
puts Seagull.animals_created.count #=> 2
{% endhighlight %}

Class instance varialbe is different for `Animal`, `Shark` and `Seagull` class, but

- all `Animal` instances share the same `class_instance_variable`,
- all `Shark` instances share the same `class_instance_variable`, and
- all `Seagull` instances share the same `class_instance_variable`.
