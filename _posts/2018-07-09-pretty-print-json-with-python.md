---
layout: post
title:  "Pretty Printing JSON with Python"
subtitle: ""
date: 2018-07-09 23:05:11
categories: [python, json]
---

# Pretty Printing JSON with Python

Python can be used to pretty-print JSON files locally, by invoking the `json.tool` module. It can turn "ugly" JSON

```shell
➜  roles cat web.json
{"name": "web","description": "Web server role.","json_class": "Chef::Role","default_attributes": {"chef_client": {"interval": 300,"splay": 60}},"override_attributes": {},"chef_type": "role","run_list": ["recipe[chef-client::default]","recipe[chef-client::delete_validation]","recipe[learn_chef_apache2::default]"],"env_run_lists": {}}
```

into pretty-printed JSON:

```shell
➜  roles python -m json.tool web.json
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
    "override_attributes": {},
    "chef_type": "role",
    "run_list": [
        "recipe[chef-client::default]",
        "recipe[chef-client::delete_validation]",
        "recipe[learn_chef_apache2::default]"
    ],
    "env_run_lists": {}
}
```