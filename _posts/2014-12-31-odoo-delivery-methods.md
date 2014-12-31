---
layout: post
title: Odoo delivery methods
comments: true
permalink: "odoo-delivery-methods"
---

> Each carrier (e.g. UPS) can have several delivery methods (e.g. UPS Express, UPS Standard) with a set of pricing rules attached to each method.
These methods allow to automatically compute the delivery price according to your settings on the sales order (based on the quotation) or the invoice (based on the delivery orders).

## Create a new deliver method 
A delivery\_carrier model

- should be associated with a delivery product whose product category needs to be set to `All / Saleable / Services`
- should be associated with a partner (ie. a Transport Company ) 
- should set its Available in the website (website_publish) field to true
- could have advanced Pricing per Destination (zip\_from~zip\_to,states,countries)

``` python
~delivery.py  
    def get_price(self, cr, uid, id, order, dt, context=None):
	total = 0
	weight = 0
	volume = 0
	quantity = 0
	product_uom_obj = self.pool.get('product.uom')
	for line in order.order_line:
	    if not line.product_id or line.is_delivery:
		continue
	    q = product_uom_obj._compute_qty(cr, uid, line.product_uom.id, line.product_uom_qty, line.product_id.uom_id.id)
	    #1,Gross Weight in product,ie. weight field in model product_template
	    weight += (line.product_id.weight or 0.0) * q
	    volume += (line.product_id.volume or 0.0) * q
	    quantity += q
	total = order.amount_total or 0.0

	#2,get price from delivery methods configuration,quantitiy,the price can be played on gross weight,and order total amount 
	return self.get_price_from_picking(cr, uid, id, total,weight, volume, quantity, context=context)

```

## Advanced Pricing per Destination
By having Advanced Pricing per Destination checked,different pricelist can be created under delivery Grid,such as a common pricing rule ![a common pricing rule]({{ site.baseurl }}/images/odoo/Delivery_methods_pricing.png)

and another rule for far state district ![pricing rule for far state district]({{ site.baseurl }}/images/odoo/Delivery_per_Destination.png)
while confirming order,the delivery price that match to your order will be displayed ![delivery choosing]({{ site.baseurl }}/images/odoo/Delivery_effect.png)
