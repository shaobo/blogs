---
layout: post
title: Odoo warehousing procurement test (take from stock)
comments: true
permalink: "odoo-warehousing-procurement-test"
---

product's default procurement action is taking products from existing stock,so unenough stock wouldn't trigger purchase order creation
.This procurement opition assums that you have initialized enough inventory stock,also you don't want odoo to buy goods from your supplier automatically. here is the test case to verify it:

__basic configuration__   
1)setting/configuration/warehouse:
![module cfg]({{ site.baseurl }}/images/odoo/stock/shiponlyrule/base_module_cfg.png)
2)warehouse/configuration:
![wh cfg]({{ site.baseurl }}/images/odoo/stock/shiponlyrule/base_wh_cfg.png)

__1,set procurements of product__  
Supply Chain Information~Routes: leave both `Buy | Make To Order` options unchecked    
qyt 0 on hand
![product procurement]({{ site.baseurl }}/images/odoo/stock/shiponlyrule/product_procurement.png)

__2,create Reordering rules__  
Min qty:1  
Max qty:50  
![reordering_rule]({{ site.baseurl }}/images/odoo/stock/shiponlyrule/order_point.png)

__3,create sale order__  
qty:6  
there would have a warn that stock is not enough(0 < 6),just save the order
![not_enough_alert]({{ site.baseurl }}/images/odoo/stock/shiponlyrule/not_enough_alert.png)

__4,confirm the sale order__  
because of above step `2, Reordering rule config`,ie.miniumum stock setting,order point is triggered,then percurement order is automatically created
![procurement order]({{ site.baseurl }}/images/odoo/stock/shiponlyrule/procurement_order.png)

__5,odoo just only generate ship delivery order__
![stock_move_waiting]({{ site.baseurl }}/images/odoo/stock/shiponlyrule/stock_move_waiting.png)


__conclusion__  
The default procurement setting `Take from stock` is clearly verifed as it would not generate purchase order even lack of stock.
![shiponly_procurement_rule]({{ site.baseurl }}/images/odoo/stock/shiponlyrule/shiponly_procurement_rule.png)

in IDEA case ,since the stock is 0 for the test product,we may need odoo have other procurement options like buy products from supplier, let's move for the next [`Make To Order` procurement test]({{ site.baseurl }}/odoo-warehousing-procurement-mto/).