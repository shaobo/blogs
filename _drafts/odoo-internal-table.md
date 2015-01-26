__locate the behind res_model by parameteres of an URL__

starting from the `ir_values` view /action relation table 

``` sql
--http://localhost:8069/web#menu_id=182&action=182

select * from ir_values where value like '%,235'

select * from ir_ui_menu where id  = 182

select * from ir_act_client where id = 182

select * from ir_act_window where id = 235

```

`field.property`

""" Assign the property field `name` for the records of model `model`
            with `values` (dictionary mapping record ids to their value).
        """
--property_stock_inventory field in stock/product_template.py
select * from ir_model_data where name ='property_stock_inventory'

--_fnct_write to ir_property table in class property of Field.py
select * from ir_property where name ='property_stock_inventory'


Active the developer mode,active from Administrator/About OpenERP. After activate it, click the Corporate Intelligence button (displayed as a question mark) at the top left of the title of the list or form. The process for this order is shown in the window

__group reference "stock.group_adv_location"__

--res_group id
select * from ir_model_data where name ='group_adv_location'
--the group info
select * from res_groups where id = 34