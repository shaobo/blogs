---
layout: post
title: Odoo module dependency overview
comments: true
permalink: "odoo-module-dependency"
---

*even though I want to be much dependent as I can in life,I like the word Depency,the first thing come to me in odoo is to visualize its module dependency*

----
## module dependency configuration

according to the depency configuration in odoo database,so the simple query could fetch it out:

``` sql
select '"'|| m.name || '" -> "'|| d.name || '";' FROM ir_module_module m 
JOIN ir_module_module_dependency d ON (m.id = d.module_id) 
where m.state = 'installed'
```

*The module dependcy is structured in  directed graph, and there is python process code load_marked_graph() in loading.py*

## generate a picture by Graphviz to show the dependency

*from the picture,you can tell most of the modules are webs*

<img src="{{ site.baseurl }}/images/module_dep.svg" class="enlarge" />
