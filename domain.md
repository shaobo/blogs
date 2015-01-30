procurement_order(osv.osv)
pull_obj.search(cr, uid, domain + [('route_id', 'in', procurement_route_ids)], order='route_sequence, sequence', context=context)

search domain [('location_id', 'in', [9, 2]), '|', ('warehouse_id', '=', 1), ('warehouse_id', '=', False)]
procurement_route_ids is []

openerp/models.py _search()

insert active field Test
warehouse_id False value is replaced with not null test, procurement_route_ids  is empty false tuple,would be added a 'AND FALSE' to  where clauses ,which no result would be queried out.

[('active', '=', 1), ('location_id', 'in', [9, 2]), '|', ('warehouse_id', '=', 1), ('warehouse_id', '=', False), ('route_id', 'in', [])]

['((
(
("active" = %s)  AND  ("location_id" in (%s,%s))
)  

AND  (("warehouse_id" = %s)  OR  "warehouse_id" IS NULL )

)  AND  FALSE)']

[True, 9, 2, 1]