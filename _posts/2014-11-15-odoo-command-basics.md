---
layout: post
title: Odoo commands 
comments: true
permalink: "odoo commands"
---
##Docker
```bash
#list containers  
sudo docker ps
#to login to the container 
sudo docker exec -it odoo8 bash
#in the container,find the running odoo process and kill it or by sudo /etc/init.d/openerp stop followed by `ps -Al,but the odoo process will be restarted soon
ps -ef |grep python

#stop the specified container odoo8
sudo docker stop odoo8  
```

## Ubuntu
```bash
#find files under current diretory recursively for which contains word 'public'
find . -type f -exec grep -l 'public' {} +
```

##PostgreSQL truncate
sudo dropdb -h localhost -U odoo testpg


###reference
[yingliu4203 odoo8nightly on docker](https://registry.hub.docker.com/u/yingliu4203/odoo8nightly/)