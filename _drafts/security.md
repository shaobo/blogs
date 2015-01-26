
## Allows to configure inventory valuations on products and product categories

user need to be in a group of `Technical Settings/Manage Inventory Valuation and Costing Methods`
*groups under Technical Settings is a special not shared groups,be able to edit either in the menu Settings>users or  Settings>groups > Technical Settings/
select * from res_groups where share is not true  and category_id = 3
*

```xml
this group is defined in stock_account/security/stock_account_security.xml as bellow:

<record id="group_inventory_valuation" model="res.groups">
    <field name="name">Manage Inventory Valuation and Costing Methods</field>
    <field name="category_id" ref="base.module_category_hidden"/>
</record>

and refered in stock_account/product_view.xml
<field name="standard_price" position="replace">
<field name="cost_method" groups="stock_account.group_inventory_valuation" attrs="{'invisible': [('type','=','service')]}"/>
                    <label string="Cost Price" for="standard_price" align="1.0" groups="base.group_user"/>
```

implied_group in addons/purchase/res_config.py

```python
'group_costing_method':fields.boolean("Use 'Real Price' or 'Average' costing methods.",
            implied_group='stock_account.group_inventory_valuation',
            help="""Allows you to compute product cost price based on average cost."""),

```

or have `Generate accounting entries per stock movement` checked at Setting>Configuration>Accounting
will result the same effect as have `Manage Inventory Valuation and Costing Methods` checked
because in stock_account/res_config.py the implied_group was linked to group_stock_inventory_valuation
*res_config.py for implied\_group*
```python
'group_stock_inventory_valuation': fields.boolean("Generate accounting entries per stock movement",
            implied_group='stock_account.group_inventory_valuation',
            help="""Allows to configure inventory valuations on products and product categories."""),
```

create Attributes under Sales > configuration > Product categories & Attributes > Attributes
add price_extra for variants under Products/Variants tab:Variants Prices icon

website aboutus page image is saved on 
http://tsda:8069/website/image/ir.attachment/52_49a7be4/datas 

需不需采购操作？如果不考虑采购，直接设置库存数量吗？

--initial spec
http://www.odoomrp.com/en_US/blog/blog-noticias-1/post/odoomrp-1
--initial inventory
https://www.odoo.com/fr_FR/forum/how-to/warehouse-management-6/how-can-i-record-my-initial-inventory-55257

https://www.odoo.com/fr_FR/forum/how-to/warehouse-management-6/how-can-i-see-for-one-of-my-products-where-the-stock-is-located-55238
