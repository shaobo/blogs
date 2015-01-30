---
layout: post
title: Graphviz your idea
comments: true
permalink: "graphviz"
---

1  install graphviz     sudo apt-get install graphviz

2  Generate your graph 
`dot -Tsvg input.dot -o output_picture.svg`
![node alias]({{ site.baseurl }}/images/soft/graphviz/node_alias.svg)

```bash
#save following blocks as input.dot text file
digraph agr{
   node[fillcolor=red];
   barn;

   house;
   node[fillcolor=green];

   farm;
   edge[color=red];

   barn -> house;
   edge[color=green];

   barn -> farm;
   house -> farm;
}

#another example dot input file
digraph unix {
        size="6,6";
        node [color=lightblue2, style=filled];
        "5th Edition" -> "6th Edition";
        "5th Edition" -> "PWB 1.0";
        "6th Edition" -> "LSX";
}
```
[advance programing graph](http://blog.csdn.net/tao_627/article/details/26128001)
[On-line Reference Pages](http://www.graphviz.org/Documentation.php)



