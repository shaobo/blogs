---
layout: post
title: Odoo Pricelists 
comments: true
permalink: "odoo-pricelists"
---
## Pricelists structure
> Some companies are notorious for their complicated pricelists. Many forms of price variation are used, such as end-of-year refunds, discounts, change of terms and conditions with time, various prepayments, cascaded rebates, seasonal promotions, and progressive price reductions. [*OpenERP Pricing Policies*](https://doc.odoo.com/book/6/6_16_Sales/6_16_Sales_pricing/#creating-price-lists)

Sale or purchase pricelist may have many versions,each version of pricelist contains priceitem lines,
priceitem calculation can be based on product fields or other pricelists.


*  Two basic product `pricelists` for Sales and Purchase were defined by default (Sales ‣ Configuration ‣ Pricelists ‣ Pricelists)  
__product_pricelists:__

	Pricelist Name             |  Type (*defined in product\_pricelist\_type*)
	-------------              |-----------------------
	Public Pricelist           | Sale Pricelist
	Default Purchase Pricelist | Purchase Pricelist


*  One pricelist may have many `pricelist versions`
![pricelist_versions]({{ site.baseurl }}/images/odoo/product/pricelist_versions.png)

*  A pricelist version is made up of a set of rules (`pricelist items` or pricelist lines) that apply to the basic product prices.
![pricelist_items]({{ site.baseurl }}/images/odoo/product/pricelist_items.png)

*  Price calculation can be based on fields defined in `product_price_types` , or on other pricelists

	__product_price_types:__  

		name         | field (s *of product*)
		-------------|-----------------------
		Public Price | list_price
		Cost Price   | standard_price

product\_pricelist\_item.py

``` python
'base': fields.selection(_price_field_get, 'Based on', required=True, size=-1, help="Base price for computation."),
'base_pricelist_id': fields.many2one('product.pricelist', 'Other Pricelist')
```

*  [*product price fields*](http://www.mindissoftware.com/2014/12/23/Odoo-Product-price/)  product_product.py 

``` python
'price': fields.function(_product_template_price,string='Price'),

'list_price': fields.float('Sale Price', help="Base price to compute the customer price. Sometimes called the catalog price."),

'lst_price' : fields.related('list_price', string='Public Price'),

'standard_price': fields.property(help="Cost price of the product template used for standard stock valuation in accounting and used as a base price on purchase orders.", 
                                  groups="base.group_user", string="Cost Price"),

'pricelist_id': fields.dummy(string='Pricelist', relation='product.pricelist', type='many2one'),
```