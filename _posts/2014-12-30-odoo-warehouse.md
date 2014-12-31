---
layout: post
title: Odoo warehouse
comments: true
permalink: "odoo-warehouse"
---

warehouse may have many stock locations , like accounts, stock locations are hierarchical, so you can carry out analyses at various levels of detail.

``` sql
--parent is in field location_id
select a.id,a.complete_name ||'->' || b.complete_name from stock_location a,stock_location b where a.location_id = b.id order by 2;

--one stock warehouse has many stock locations,by default odoo only enables one stock location ,'Physical Locations splx WH splx Stock'
select lot_stock_id 'Location Stock',view_location_id 'View Location',wh_pack_stock_loc_id 'Packing Location',wh_input_stock_loc_id 'Input Location' ,wh_qc_stock_loc_id 'Quality Control Location' ,wh_output_stock_loc_id 'Output Location' from stock_warehouse;

select id,location_id,complete_name from stock_location;
```
| id |   parent    |  name                                                 | active 
| --- | ----------- | ----------------------------------------------------- | ------ 
| 11 |           1 | Physical Locations / WH                               | true 
| 12 |          11 | `Physical Locations / WH / Stock`                       | true 
| 13 |          11 | Physical Locations / WH / Input                       | false
| 14 |          11 | Physical Locations / WH / Quality Control             | false
| 15 |          11 | Physical Locations / WH / Output                      | false
| 16 |          11 | Physical Locations / WH / Packing Zone                | false
| 17 |           7 | Physical Locations / Your Company: Transit Location    | false
