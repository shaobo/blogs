---
layout: post
title: Odoo warehousing procurement test (Make To Order)
comments: true
permalink: "odoo-warehousing-procurement-mto"
---

Now let's test `Make To Order` procurement opition setting, odoo could create purchase order for buying products from your supplier automatically if there doesn't have enough stock inventory. here is the test case to verify it:

__Basic configuration__   
setting/configuration/warehouse:
![module cfg]({{ site.baseurl }}/images/odoo/stock/shiponlyrule/base_module_cfg.png)
warehouse/configuration:
![wh cfg]({{ site.baseurl }}/images/odoo/stock/shiponlyrule/base_wh_cfg.png)

__Central part:chained Procurement Rules__  
Under Warehouse/configuration/Procurement Rules,create two procurement rules `WH: Stock -> Customers MTO` and `WH: Stock -> Customers BUY`,they are logically chained by procurement order's ruling process.
![procurement_ruling]({{ site.baseurl }}/images/odoo/stock/mto/procurement_rules.svg)

Procurement order will be created with `WH: Stock -> Customers MTO` procurement rule assigned, is the parent procurement order
which will then create its child procuremnt order with `WH: Stock -> Customers BUY` procurement rule assigned.

How does procurement order get its suitable procurement rule assigned? you may need understand [procurement implementation structure]({{ site.baseurl }}/odoo-warehousing-procurement/).

Procurement order created with the route of product setting(ie. `Make To Order` of our test product) could match the procurement\_rule record `WH: Stock -> Customers MTO` by warehouse location of `Partner Locations/Customers` and route_id of `Make To Order`
![mto_procurement_rule]({{ site.baseurl }}/images/odoo/stock/mto/mto_procurement_rule.png)

How could procurement order create another procurement order?
`WH: Stock -> Customers MTO` procurement rule takes  a `Move From Another Location` Action, then Moving Options ,it has move supply method of `create procurement`,so the child procurement order will be created,or you can say it's procurement chaining.

The new child procurement order would also need to have a procurement rule assigned,so create the `WH: Stock -> Customers BUY` as
![buy_procurement_rule]({{ site.baseurl }}/images/odoo/stock/mto/buy_procurement_rule.png)

the child procurement order would match this procurement rule record `WH: Stock -> Customers BUY` 
by the warehouse location of `WH/Stock`,ie. it gets the warehouse's default route.
And since this procurement rule takes Buy action,it will create a new purchase order,that is what we expected by specifing `Make To Order` at product's procurement.

__1,set procurements of product__  
Supply Chain Information~Routes: have `Make To Order` option checked    
qyt 3 on hand
![product procurement]({{ site.baseurl }}/images/odoo/stock/mto/product_procurement_mto.png)

__2,create Reordering rules__  
Min qty:1  
Max qty:50  
![reordering_rule]({{ site.baseurl }}/images/odoo/stock/shiponlyrule/order_point.png)

__3,create sale order__  
qty:6  
there would have a warn that stock is not enough(3 < 6),just save the order
![not_enough_alert]({{ site.baseurl }}/images/odoo/stock/mto/not_enough_alert.png)

__4,confirm the sale order__  
because of above step `2, Reordering rule config`,ie.miniumum stock setting,order point is triggered,Note this time, _TWO_ percurement orders are automatically created by mrp_jit module and in running status: 
![procurement order]({{ site.baseurl }}/images/odoo/stock/mto/procurement_order.png)
the first procurement order is linked to the sale order that was just created manually in above step 3.  
becuase of lack of product stock,the second procurement order is created to generate a purchase order to buy product to fullfill the sale order.
this is the point what `Make To Order` procurement option means.
*(it is not so smart enough,even though there is 3 units availabe on stock which is ignored by the procurement order,it would be better to generate a purchase order of 3 units = 6  - 3 )*.

__5,confirm the automatically generated purchase order__
![stock_move_waiting]({{ site.baseurl }}/images/odoo/stock/mto/procurement_rfq.png)
this will generate a new stock move.

__6,confirm the automatically generated stock moves__
respect to the above procurement orders(maybe sale order+po),two stock moves are automatically generated too,the first stock move (ie.WH->customer) is for the sale order shipping which is depended on the avaliability of the second one that is the purchasing stock move (ie.supplier->WH)
![stock_move_waiting]({{ site.baseurl }}/images/odoo/stock/mto/stock_moves.png)

by confirming the two stock moves to fullfil the sale order,you can see the sale order is in delivered status
![so delivered]({{ site.baseurl }}/images/odoo/stock/mto/so_delivered.png)