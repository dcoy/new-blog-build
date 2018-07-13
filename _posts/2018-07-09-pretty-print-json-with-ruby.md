---
layout: post
title:  "Pretty Printing JSON with Ruby"
subtitle: ""
date: 2018-07-09 23:05:35
categories: [ruby, json]
---

Ruby can be used to pretty-print JSON files, by invoking the `JSON` module from the Ruby standard library. It can turn "ugly" JSON:

{% highlight json %}

➜ cat web.json
{"name": "web","description": "Web server role.","json_class": "Chef::Role","default_attributes": {"chef_client": {"interval": 300,"splay": 60}},"override_attributes": {},"chef_type": "role","run_list": ["recipe[chef-client::default]","recipe[chef-client::delete_validation]","recipe[learn_chef_apache2::default]"],"env_run_lists": {}}

{% endhighlight %}

into pretty-printed JSON:

{% highlight json %}

➜ ruby -rjson -e 'puts JSON.pretty_generate(JSON.parse(ARGF.read))' web.json
{
  "name": "web",
  "description": "Web server role.",
  "json_class": "Chef::Role",
  "default_attributes": {
    "chef_client": {
      "interval": 300,
      "splay": 60
    }
  },
  "override_attributes": {
  },
  "chef_type": "role",
  "run_list": [
    "recipe[chef-client::default]",
    "recipe[chef-client::delete_validation]",
    "recipe[learn_chef_apache2::default]"
  ],
  "env_run_lists": {
  }
}

{% endhighlight %}