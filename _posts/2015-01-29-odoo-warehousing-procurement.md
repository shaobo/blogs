---
layout: post
title: Odoo warehousing procurement
comments: true
permalink: "odoo-warehousing-procurement"
---

Reordering rules (underlying stock\_warehouse\_orderpoint model) defines Minimum stock rules,can trigger procurement order creation.
procurement order needs to find a suitable procurement rule (A rule describes what a procurement should do: produce, buy, move)
here is the overview of procument implementation structure:
![procurement structure]({{ site.baseurl }}/images/odoo/stock/procurement_structure.svg)

__Procurement rule is based on Routes__  
Routes and Procurement rules are cross referenced in current odoo,thus it is easy to be confused.To make it simpler,create your routes first,then just associate your routes to your procument rules,don't try to edit procument rules under Routes form.*actually it would't save anything there,it is a bug*

__Routes (stock\_location\_routes)__  

id  |name
----|----
8   |Buy           (product level)
1   |Make To Order (product level)
2   |YourCompany: Receipt in 1 step (default warehouse level)
3   |YourCompany: Ship Only         (default warehouse level)

Routes can be defined at different level,such as:  
1)product level routes (stock\_location\_route where product_selectable='true')
product level routes can be set to product on product form
![product procurement]({{ site.baseurl }}/images/odoo/stock/shiponlyrule/product_procurement.png)
2)warehouse level routes (stock\_route\_warehouse)  
warehouse level routes can be set to warehouse on warehouse form
![wh cfg]({{ site.baseurl }}/images/odoo/stock/shiponlyrule/base_wh_cfg.png)

Any kind of level routes can be associated to procument fule on procurement rule form
![buy_procurement_rule]({{ site.baseurl }}/images/odoo/stock/mto/buy_procurement_rule.png)

__procurement_rules__

id	|name	                    |location_id	|route_id	|warehouse_id
----|---------------------------|---------------|-----------|------------
1	|WH: Stock -> Customers	    |9	            |3			|1
2	|WH: Stock -> Customers MTO	|9	            |1			|1
14	|WH: Stock -> Customers BUY	|11         	|2			|1
9	|YourCompany:  Buy	        |12				|8			|1

To find the suitable procurement rule for a procurement order,it works as (\_find\_suitable\_rule in stock/procurement\_order.py)

1)check procurement order.route\_ids matched (rule maybe by manual specified at procurement order level while running procurement ,this is not much useful because most of the case procurement order should be running automatically. there is another option, you can set routes at sale order line level if having `Choose MTO, drop shipping,... on sales order lines` option enabled at settings/configuration/sales)  
2)product\_route\_ids,ie product or product category routes  
3)warehouse\_route\_ids,ie default warehouse routes  
4)findally 'route\_id', '=', False,ie. global routes


bellow is the odoo program process flow of sale order confirmation: 
![so_process]({{ site.baseurl }}/images/odoo/stock/mto/so_cfm_process.svg)

procurement order's location\_id come from sale order's partner stock property (Customer Location field)

location\_id = order.partner\_shipping\_id.property\_stock\_customer.id

```sql
select * from ir_property where name like '%property_stock_customer%'
```
