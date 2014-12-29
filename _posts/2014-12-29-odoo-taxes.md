---
layout: post
title: Odoo taxes 
comments: true
permalink: "odoo-taxes"
---

*Calculating the correct sales tax for an order can be quite a complicated task. Sales Taxes not only differ from state to state but can also be different within the zip code.Also sales taxes can be different for different types of products.[>Manage your sales Taxes better in OpenERP](http://bistasolutions.com/blogs/2014/01/16/sales-taxes-in-openerp/)*

## Taxes Applicability
odoo Taxes有适用性定制一项，即Accounting>Configuration>Taxes>Taxes>Special Computation>
Applicability:python code  
Applicable Code (if type=code)  
*以下为odoo接口调用处hooking代码,_applicable() in account.py*

``` python
if tax.applicable_type=='code':
	localdict = {'price_unit':price_unit, 'product':product, 'partner':partner}
	exec tax.python_applicable in localdict
	if localdict.get('result', False):
	    res.append(tax)
```

怪异的是，按理说应该可以编写形如下述的代码段,可实际执行（使用python的exec）是result变量并没有增加到系统中（没有任何报错），也就是不能获取判断结果

``` python
"""
if partner.zip in here:
    result = 8.0
else:
    result = 8.5
"""
```
如果不使用代码段，直接写result = 8.5是没有问题的。

看来还是直接增加tax处理合理，如采用TaxCloud服务，这里配置项仅仅适用于简单的条件匹配，正如选项名称Applicability option所示,过于复杂的逻辑对于维护也是个问题。
另外，除了定制Tax外，目前也可以从价格方面进行定制，比如为不同的客户创建不同的价格

##Pricelist
- settings>users>administrator>Sales Pricelists checked
- settings>configuration>sales>Customer Features:Use pricelists to adapt your price per customers

##Reference
[TaxCloud is a sales tax management service for online retailers](http://www.programmableweb.com/api/taxcloud)  
[Odoo/OpenERP Pricelists](https://apagen.wordpress.com/2013/05/25/openerp-pricelists/)  
[Introduction to pricelist on youtube](https://www.youtube.com/watch?v=gotUoKrMHT0)  
[tax structure](https://doc.odoo.com/6.1/book/3/3_9/tax_structure/)  


