---
layout: post
title: Grep and replace
category: Unix
author: matDobek
---

## tl;dr

Simple way of replacing text across multiple files:

    grep -lr "oldText" ./ |
      xargs sed -i "" "s/oldText/newText/g" Sample

## Sample
Let's assume that we have:
{% highlight ruby %}
# ./app/models/some_model.rb
require "god_object"
class SomeModel
 # lots of smart stuff here!
end

# ./app/views/some_view.rb
require "god_object"
class SomeView
 # lots of smart stuff here!
end

# ./app/controllers/some_controller.rb
require "god_object"
class SomeController
 # lots of smart stuff here!
end
{% endhighlight %}

As you may know, `god object` is not the best practice. We probably don't want to use them in our code.
So let's rename all occurrences of `god_object` to `good_object`, inside our `./app` directory!

    grep -lr "god_object" ./app |
      xargs sed -i "" "s/god_object/good_object/g"

Yay!

## Explanation
Here's the command breakdown:

Return all occurrences of `god_object`, found in `sample_file`:

    grep "god_object" sample_file

Return all occurrences of `god_object`, found in `sample_directory`, and its subdirectories:

    grep -r "god_object" ./sample_directory

Return all filepaths of `god_object` found in `sample_directory`, and its subdirectories:

    grep -rl "god_object" ./sample_directory

Change all occurrences of substring `god_object` to `good_object`, found inside `sample_file_path`, and return the changes (so they could be passed somewhere else):

    sed "s/god_object/good_object/g" sample_file_path

Change all occurrences of substring `god_object` to `good_object`, found inside `sample_file_path`, and change them inplace:

    sed -i "" "s/god_object/good_object/g" sample_file_path

Take `result` and pass it as argument to `sed`:

    result | xargs sed

Aaaand that's it.
