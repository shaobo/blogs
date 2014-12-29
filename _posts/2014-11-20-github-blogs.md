---
layout: post
title: Blogging with github pages 
comments: true
permalink: "github-blogs"
---
## create github repo 
```bash
mkdir blogs/ 

git init  
#创建一个没有父节点的分支gh-pages,github规定，只有该分支中的页面，才会生成网页文件  
git checkout --orphan gh-pages  
git add .  
git commit -m "initialize blogs"  
git remote add origin https://github.com/shaobo/blogs.git  
git push -u origin gh-pages  
  "To https://github.com/shaobo/blogs.git
  [new branch]      gh-pages -> gh-pages
  Branch gh-pages set up to track remote branch gh-pages from origin. "

#debug locally with jekyll=>http://127.0.0.1:4000/blogs/
jekyll serve --baseurl /blogs --watch
```

## things to note
- use 'gh-pages' branch
- without DNS mapping,setting 'baseurl:  /blogs' in _config.yaml,and add '{ { site.baseurl } }' before '/public/' ,or pubic resources (css/page title image) wouldn't be found by github pages  
- use travis to show github pages building status [travis continous integration](https://travis-ci.org/shaobo/blogs)  
- or use jekyll to debug in local   
- line break with 2 spaces ,or 2 white space lines
- *line breaks cannot be insertted into italic blocks* \* without 2+ consecutive line breaks \*


###References
[markdown](https://guides.github.com/features/mastering-markdown/)
[blogging tutorial with jekyll](http://www.ruanyifeng.com/blog/2012/08/blogging_with_jekyll.html)
