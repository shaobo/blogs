
## Allows to configure inventory valuations on products and product categories

user need to be in a group of `Technical Settings/Manage Inventory Valuation and Costing Methods`
*groups under Technical Settings is a special not shared groups,be able to edit either in the menu Settings>users or  Settings>groups > Technical Settings/
select * from res_groups where  share is not true  and category_id = 3
*

or have Generate accounting entries per stock movement checked at Setting>Configuration>Accounting
,this option will overwrite will have the same effect as the above


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
