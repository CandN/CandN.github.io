---
layout: post
title: Cron meets rails
category: Unix
author: matDobek
---

## Cron introduction

*Cron* is a UNIX tool that executes commands on given schedule. Thanks of it, we can backup database every day at 00:00, or ping server, every 1 minute, to check if it’s up and running smoothly.

All we need to know is two commands, and get some understanding how *crontabs* files are stored.



## Crontab first look

*Crontab* is an utility program which manages *crontab* files. Those files contain instructions for the *cron* demon.  *Cron* wakes up every minute, reads the *crontab* files located in `/usr/lib/cron/tabs/` and executes proper task, if the time is right.

There is NO need to restart *cron*, after updating *crontab* files.

To handle *crontab* files, we just need to know two commands:

{% highlight bash %}
crontab -e # create/edit crontab file for current user
crontab -l # display the crontab file for current user
{% endhighlight %}

So when user named `jon_snow` will for the first time run the `crontab -e`, the `/usr/lib/cron/tabs/jon_snow` file will be created, and opened in specified editor.

***
Note, that you can set `EDITOR` environment variable, to pick editor of your choice. It will become default one, for opening bash programs, like *crontab*. In my case it looks like:

{% highlight bash %}
# ~/.bashrc
export EDITOR=vim
{% endhighlight %}

If you are experiencing `crontab: temp file must be edited in place` error, during  `crontab -e` - it is caused by vim policy on backup files, which collides with *crontab* one. To fix, update your vim config:

{% highlight bash %}
# ~/.vimrc
set backupskip=/tmp/*,/private/tmp/* " skip backup, it's fix for crontab
{% endhighlight %}

***

*Crontab* files follow specyfic format:

{% highlight bash %}
*     *     *     *     *     command
-     -     -     -     -
|     |     |     |     |
|     |     |     |     +--- day of the week (0 - 7)
|     |     |     |
|     |     |     +--------- month (1 - 12)
|     |     |
|     |     +--------------- day (1 - 31)
|     |
|     +--------------------- hour (0 - 23)
|
+--------------------------- minute (0 - 59)
{% endhighlight %}

Notice that days of the week has 8 possible values. That is because sunday is assigned to 0 and 7.

Sample `crontab` file, for `jon_snow` user, can look like:

{% highlight bash %}
# /usr/lib/crone/tabs/jon_snow

# Run every 1st day of the month, at 00:00
0 0 1 * *         echo "Brace yourself, spoilers are coming!"

# Run every monday, at 06:00
0 6 * * 1         echo "kidding"

# Run every monday, and wednesday at 06:00
0 6 * * 1,3       echo "No, spoilers"

# Run every weekday at 06:00
0 6 * * 1-5       echo "because"

# Run every 10 minutes
*/10 * * * *      echo "I'm Jon Snow"

# Run everyday at 0:00, 3:00, 6:00, 9:00 and 12:00
0 0-12/3 * * *    echo "and I know nothing"
{% endhighlight %}



## Debugging and failures

If executed task is producing output (logs some info / throws error), *cron* will try to send an email, to owner of the *crontab* file. If user has no email specified, it will save the output under `/var/mail/USERNAME`. For example: `/var/mail/jon_snow`.

To specify the email, just add to your *crontab* file:

{% highlight bash %}
# /usr/lib/crone/tabs/jon_snow

MAILTO=jon_snow@nightwatch.org # remember that there cannot be spaces around the "="

# Run every 1st day of the month, at 00:00
0 0 1 * *     echo "Brace yourself, spoilers are coming!"

# ...
{% endhighlight %}


## Permissions

If the /usr/lib/cron/cron.allow file exists, then you must be listed there in order to be allowed to use this command.
If the /usr/lib/cron/cron.allow file does not exist but the /usr/lib/cron/cron.deny file does exist, then you must not be listed in the /usr/lib/cron/cron.deny file in order to use this command.

The format of these files is one user-name per line, with no leading or trailing whitespace.  Lines of other formats will be ignored, and so can be used for comments.



## Cron on rails

And now it’s the time for Jon, to build his own Ruby on Rails app. He wants to remind every monday, his fellow lords, that winter is coming.

He prepared *rake task* `send_email:winter_is_coming`. Let's put it inside our *crontab* file:

{% highlight bash %}
# cron itself won't have loaded any PATHs, or ENVs so we need to do this VERY explict

# The task should:
# go to root of the rails app
# provide RAIL_ENV
# provide bundlers path - you can check it via "which bundler"
# replace ../bin/bundler with ../wrappers/bundler - to load environtment needed by bundler

* * * * 1 cd /path/to/your/rails/app && RAILS_ENV=production /usr/local/rvm/gems/ruby-2.0.0-p647/wrappers/bundle exec rake send_email:winter_is_coming
{% endhighlight %}

That's it!
