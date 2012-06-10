---
layout: post
title: No-Op is the New Go(-Op?)
---

A while back I added some no-op support to [Moonshine](https://github.com/railsmachine/moonshine) for deploys. For those of you unfamiliar with [Puppet](http://www.puppetlabs.com/), applying a manifest with no-op will show all sorts of useful information, including:

* Creation of new Puppet resources
* Modifications to existing Puppet resources
* Refreshes of any Puppet resources

What this means is that if your changes would change the content of a config file, install a new gem, or restart Apache **Puppet will print all that out**, including diffs.

This support is available in the newer version of Moonshine, so you can call it in your deploy:

``` shell
cap production noop deploy
```

But there is a catch!

If you have any cap callbacks that are called as part of the deploy process, you'll need to make them no-op friendly. You can do that by doing something like so:

``` ruby
after 'deploy:restart', 'god:restart'
    
namespace :god do
  task :restart do
    if fetch(:noop)
      puts "Not restarting god since this is a no-op!"
    else
      sudo 'restart god'
    end
  end
end
```

Now you're ready to start doing a safe run of your deploys before shipping it!

You can read more about no-op deploys by checking out the [wiki docs](https://github.com/railsmachine/moonshine/wiki/No-Op-Deploys-with-Moonshine) on it.
