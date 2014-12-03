Julian's Chef Style Guide
=========================

This project contains the Chef Style Guide that I use, and was based on the work we did at [SecondMarket](http://www.secondmarket.com/). Some of these rules are enforced by FoodCritic; some others are not.

I have a talk entitled "What Makes a Good Cookbook?" based on this material.

<iframe width="853" height="480" src="//www.youtube.com/embed/ILOW0Dm_ILQ?rel=0&t=1m58s" frameborder="0" allowfullscreen></iframe>

Git Etiquette
-------------

Although not strictly a Chef style thing, please always ensure your ``user.name`` and ``user.email`` are set properly in your ``.gitconfig``.

By "properly", I mean that ``user.name`` is your given name (e.g., "Julian Dunn") and ``user.email`` is an actual, working e-mail address for you. It's annoying to see commit log entries from things like "guestuser &lt;login@Bobs-Macbook-Pro.local&gt;".

Cookbook Naming
---------------

* Avoid dashes in cookbook names. This is because any LWRPs you create will use the cookbook name as part of the LWRP name, so the methods become very awkward. In particular, since '-' can't be part of a symbol in Ruby, you won't be able to use LWRPs in any cookbooks with '-' in them.
* All organization application cookbooks should be prefixed with a short organizational prefix, such as 'sm' for 'SecondMarket' (e.g. 'smpostgresql')

Cookbook Versioning
-------------------

* Use [semantic versioning](http://semver.org/) when numbering cookbooks.
* Only upload stable cookbooks from master.
* Only upload unstable cookbooks from the dev branch. Merge to master and bump the version when stable.
* Always update CHANGELOG.md with any changes, with the JIRA ticket and a brief description.

System and Component Naming
---------------------------

Name things uniformly for their system and component. For the ganglia master,

* attributes: `node['ganglia']['master']`
* recipe: `ganglia::master`
* role: `ganglia-master`
* directories: `ganglia/master` (if specific to component), `ganglia` (if not). For example: `/var/log/ganglia/master`

(The foregoing was shamelessly cribbed from Ironfan)

Name attributes after the recipe in which they are primarily used. e.g. `node['postgresql']['server']`

Default Recipe
--------------

Don't use the default recipe (leave it blank). Instead, create recipes called `server` or `client` (or other).

Resource Parameter Order
------------------------

Follow this order for information in each resource declaration:

*    Source
*    Cookbook
*    Resource ownership
*    Permissions
*    Notifications
*    Action

Example:

    template '/tmp/foobar.txt' do
      source 'foobar.txt.erb'
      owner  'someuser'
      group  'somegroup'
      mode   00644
      variables(
        :foo => 'bar'
      )
      notifies :reload, 'service[whatever]'
      action :create
    end

File Modes
----------

Always specify all five digits of the file mode, and not as a string.

Wrong:

    mode "644"

Right:

    mode 00644

Always Specify Action
---------------------

In each resource declarations always specify the action to be taken:

Wrong:

    package 'monit'

Right:

    package 'monit' do
      action :install
    end

FoodCritic Linting
------------------

It goes without saying that all cookbooks should pass FoodCritic rules before being uploaded.

    $ foodcritic -f all your-cookbook

should return nothing.

Symbols or Strings?
-------------------

Prefer strings over symbols, because they're easier to read and you don't need to explain to non-Rubyists what a symbol is. Please retrofit old cookbooks as you come across them.

Wrong:

    default[:foo][:bar] = 'baz'

Right:

    default['foo']['bar'] = 'baz'

String Quoting
--------------

Use single-quoted strings in all situations where the string doesn't need interpolation.

Shelling Out
------------

Always use `mixlib-shellout` to shell out. Never use backticks, Process.spawn, popen4,
or anything else!

As of Chef Client 12 you can use `shell_out`, `shell_out!` and `shell_out_with_system_locale`
directly in recipe DSL.

Constructs to Avoid
-------------------

* `node.set` / `normal_attributes` - Avoid using attributes at normal precedence since they are set directly on the node object itself, rather than implied (computed) at runtime.
* `node.set_unless` - Can lead to weird behavior if the node object had something set. Avoid unless altogether necessary (one example where it's necessary is in `node['postgresql']['server']['password']`)
* `if node.run_list.include?('foo')` i.e. branching in recipes based on what's in the node's run list. Better and more readable to use a feature flag and set its precedence appropriately.
* `node['foo']['bar']` i.e. setting normal attributes without specifying precedence. This is deprecated in Chef 11, so either use `node.set['foo']['bar']` to replace its precedence in-place or choose the precedence to suit.

Useful Links
------------

* [Ironfan Style Guide](https://github.com/infochimps-labs/ironfan/wiki/style_guide)
* [FoodCritic](http://acrmp.github.com/foodcritic/)

License and Authors
-------------------

* Author:: Julian C. Dunn (<jdunn@aquezada.com>)
* Copyright:: 2012-2013, SecondMarket Labs, LLC.
* Copyright:: 2013-2014, Chef Software, Inc.

```text
Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
