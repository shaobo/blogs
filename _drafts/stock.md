---
layout: post
title: Odoo warehousing procurement
comments: true
permalink: "odoo-warehousing-procurement"
---

reordering rules (stock_warehouse_orderpoint) defines Minimum stock rules,can trigger procurement order creation.
procurement order needs to find a suitable procurement rule (A rule describe what a procurement should do: produce, buy, move)


where comes the default warehouse routes association:
  select * from stock_route_warehouse
select * from stock_location_route where id in (2,3,4)
*warehouse level has routes (YourCompany: Receipt in 1 step
YourCompany: Ship Only
YourCompany: Cross-Dock)
associated*

Routes and Procurement rules are cross referenced,this is easy to be confused,the easy way to handle is first create your routes,then just associate your routes to your procument rules,don't edit procument rules under Routes form,actually it would't save anything,may also confuse yourself.to make it simpler,procurement rule is based on routes.

__Routes (stock_location_routes)__
routes can be defined at different level,such as:
1)warehouse level routes
select * from stock_route_warehouse

2)product level routes
select * from stock_location_route where product_selectable='true'

id  |name
----|----
8   |Buy           (product level)
1   |Make To Order (product level)
2   |YourCompany: Receipt in 1 step (default warehouse level)
3   |YourCompany: Ship Only         (default warehouse level)

product level routes can be set to product on product form
![product procurement]({{ site.baseurl }}/images/odoo/stock/shiponlyrule/product_procurement.png)

warehouse level routes can be set to warehouse on warehouse config form
*from Configuration/Routes form,you can see that this kind of warehouse level routes is application on Product Categories*
![wh cfg]({{ site.baseurl }}/images/odoo/stock/shiponlyrule/base_wh_cfg.png)

any kind level of routes can be associated to procument fule on procurement rule config form
![buy_procurement_rule]({{ site.baseurl }}/images/odoo/stock/mto/buy_procurement_rule.png)

__procurement_rules__

id	|name	                    |location_id	|route_id	|warehouse_id
----|---------------------------|---------------|-----------|------------
1	|WH: Stock -> Customers	    |9	            |3			|1
2	|WH: Stock -> Customers MTO	|9	            |1			|1
14	|WH: Stock -> Customers BUY	|11         	|2			|1
9	|YourCompany:  Buy	        |12				|8			|1


To find the suitable procurement rule for a procurement order,it works as
(_find_suitable_rule in stock/procurement_order.py)
1)check procurement order.route_ids matched (rule maybe by manual specified at procurement order level while running procurement ,this is not much useful because most of the case procurement order should be running automatically. there is another option, you can set routes at sale order line level if having `Choose MTO, drop shipping,... on sales order lines` option enabled at settings/configuration/sales)
2)product_route_ids,ie product or product category routes
3)warehouse_route_ids,ie default warehouse routes
4)findally 'route_id', '=', False,ie. global routes

program process flow for sale order confirmation: 
![so_process]({{ site.baseurl }}/images/odoo/stock/mto/so_cfm_process.svg)
procurement order's location_id come from sale order's partner stock property (Customer Location)
sale_stock/sale_order.py
_prepare_order_line_procurement(self, cr, uid, order, line, group_id=False, context=None):
        vals = super(sale_order, self)._prepare_order_line_procurement(cr, uid, order, line, group_id=group_id, context=context)
location_id = order.partner_shipping_id.property_stock_customer.id
select * from ir_property where name like '%property_stock_customer%'

how child procurement's location id is determind?
 
procurement order creation 1
[('location_id', 'in', [9, 2]), '|', ('warehouse_id', '=', 1), ('warehouse_id', '=', False)]
+ [('route_id', 'in', product_route_ids)]
find MTO procurement rule

becuase of 'move' action setting,just run this procurement order
    def _run(self, cr, uid, procurement, context=None):
        if procurement.rule_id and procurement.rule_id.action == 'buy':
            #make a purchase order for the procurement
            return self.make_po(cr, uid, [procurement.id], context=context)[procurement.id]
        return super(procurement_order, self)._run(cr, uid, procurement, context=context)

becuase of 'make_to_order' setting,another procurement order (child) would be created under stock_move module
stock_move.py
action_confirm()
 for move in self.browse(cr, uid, states['confirmed'], context=context):
            if move.procure_method == 'make_to_order':
                self._create_procurement(cr, uid, move, context=context)
                states['waiting'].append(move.id)
                states['confirmed'].remove(move.id)


only buy action would generate purchase order:
purchase/procurement_order.py
 def _run(self, cr, uid, procurement, context=None):
        if procurement.rule_id and procurement.rule_id.action == 'buy':
            #make a purchase order for the procurement
            return self.make_po(cr, uid, [procurement.id], context=context)[procurement.id]
        return super(procurement_order, self)._run(cr, uid, procurement, context=context)

prepare new procurement order by the move
 def _prepare_procurement_from_move(self, cr, uid, move, context=None):
        origin = (move.group_id and (move.group_id.name + ":") or "") + (move.rule_id and move.rule_id.name or move.origin or "/")
        group_id = move.group_id and move.group_id.id or False
        if move.rule_id:
            if move.rule_id.group_propagation_option == 'fixed' and move.rule_id.group_id:
                group_id = move.rule_id.group_id.id
            elif move.rule_id.group_propagation_option == 'none':
                group_id = False
        return {
            'name': move.rule_id and move.rule_id.name or "/",
            'origin': origin,
            'company_id': move.company_id and move.company_id.id or False,
            'date_planned': move.date,
            'product_id': move.product_id.id,
            'product_qty': move.product_qty,
            'product_uom': move.product_uom.id,
            'product_uos_qty': (move.product_uos and move.product_uos_qty) or move.product_qty,
            'product_uos': (move.product_uos and move.product_uos.id) or move.product_uom.id,
            'location_id': move.location_id.id,
            'move_dest_id': move.id,
            'group_id': group_id,
            'route_ids': [(4, x.id) for x in move.route_ids],
            'warehouse_id': move.warehouse_id.id or (move.picking_type_id and move.picking_type_id.warehouse_id.id or False),
            'priority': move.priority,




Route option (underlying model stock.location.route)

Action: buy would create a purchase order for current procurement order
move from another location can be chained to create another new procurement order 

procurement.rule
------------------            

class procurement_order(osv.osv):
    _inherit = "procurement.order"

    def create(self, cr, uid, vals, context=None):
        procurement_id = super(procurement_order, self).create(cr, uid, vals, context=context)
        self.run(cr, uid, [procurement_id], context=context)
        self.check(cr, uid, [procurement_id], context=context)
        return procurement_id

stock/product_template.py
    'route_ids': fields.many2many('stock.location.route', 'stock_route_product', 'product_id', 'route_id', 'Routes', domain="[('product_selectable', '=', True)]",
                                    help="Depending on the modules installed, this will allow you to define the route of the product: whether it will be bought, manufactured, MTO/MTS,..."),
    }

procurement rules is based on routes (buy) which then have pull rules (company Buy)

sale_stock/product_product.py
    def need_procurement(self, cr, uid, ids, context=None):
        #when sale/product is installed alone, there is no need to create procurements, but with sale_stock
        #we must create a procurement for each product that is not a service.
        for product in self.browse(cr, uid, ids, context=context):
            if product.type != 'service':
                return True
        re


sale order confirm action trigger the sale.wkf_sal workflow e at action_ship_create():
    def action_ship_create(self, cr, uid, ids, context=None):
        """Create the required procurements to supply sales order lines, also connecting
        the procurements to appropriate stock moves in order to bring the goods to the
        sales order's requested location.

        :return: True
        """
        procurement_obj = self.pool.get('procurement.order')
        sale_line_obj = self.pool.get('sale.order.line')
        for order in self.browse(cr, uid, ids, context=context):
            proc_ids = []
            vals = self._prepare_procurement_group(cr, uid, order, context=context)
            if not order.procurement_group_id:
                group_id = self.pool.get("procurement.group").create(cr, uid, vals, context=context)
                order.write({'procurement_group_id': group_id}, context=context)

            for line in order.order_line:
                #Try to fix exception procurement (possible when after a shipping exception the user choose to recreate)
                if line.procurement_ids:
                    #first check them to see if they are in exception or not (one of the related moves is cancelled)
                    procurement_obj.check(cr, uid, [x.id for x in line.procurement_ids if x.state not in ['cancel', 'done']])
                    line.refresh()
                    #run again procurement that are in exception in order to trigger another move
                    proc_ids += [x.id for x in line.procurement_ids if x.state in ('exception', 'cancel')]
                elif sale_line_obj.need_procurement(cr, uid, [line.id], context=context):
                    if (line.state == 'done') or not line.product_id:
                        continue
                    vals = self._prepare_order_line_procurement(cr, uid, order, line, group_id=group_id, context=context)
                    proc_id = procurement_obj.create(cr, uid, vals, context=context)
                    proc_ids.append(proc_id)
            #Confirm procurement order such that rules will be applied on it
            #note that the workflow normally ensure proc_ids isn't an empty list
            procurement_obj.run(cr, uid, proc_ids, context=context)

            #if shipping was in exception and the user choose to recreate the delivery order, write the new status of SO
            if order.state == 'shipping_except':
                val = {'state': 'progress', 'shipped': False}

                if (order.order_policy == 'manual'):
                    for line in order.order_line:
                        if (not line.invoiced) and (line.state not in ('cancel', 'draft')):
                            val['state'] = 'manual'
                            break
                order.write(val)
        return True



 procurement_rule.py
 'route_id': fields.many2one('stock.location.route', 'Route',
            help="If route_id is False, the rule is global")

'location_id': fields.many2one('stock.location', 'Procurement Location'),
        'location_src_id': fields.many2one('stock.location', 'Source Location',
            help="Source location is action=move"),
        'route_id': fields.many2one('stock.location.route', 'Route',
            help="If route_id is False, the rule is global"),
        'procure_method': fields.selection([('make_to_stock', 'Take From Stock'), ('make_to_order', 'Create Procurement')], 'Move Supply Method', required=True, 
                                           help="""Determines the procurement method of the stock move that will be generated: whether it will need to 'take from the available stock' in its source location or needs to ignore its stock and create a procurement over there."""),
        'route_sequence': fields.related('route_id', 'sequence', string='Route Sequence',
            store={
                'stock.location.route': (_get_rules, ['sequence'], 10),
                'procurement.rule': (lambda self, cr, uid, ids, c={}: ids, ['route_id'], 10),
        }),
        'picking_type_id': fields.many2one('stock.picking.type', 'Picking Type',
            help="Picking Type determines the way the picking should be shown in the view, reports, ..."),
        'delay': fields.integer('Number of Days'),
        'partner_address_id': fields.many2one('res.partner', 'Partner Address'),
        'propagate': fields.boolean('Propagate cancel and split', help='If checked, when the previous move of the move (which was generated by a next procurement) is cancelled or split, the move generated by this move will too'),
        'warehouse_id': fields.many2one('stock.warehouse', 'Served Warehouse', help='The warehouse this rule is for'),

stock_move.py
'procure_method': fields.selection([('make_to_stock', 'Default: Take From Stock'), ('make_to_order', 'Advanced: Apply Procurement Rules')], 'Supply Method', required=True, 
                                           help="""By default, the system will take from the stock in the source location and passively wait for availability. The other possibility allows you to directly create a procurement on the source location (and thus ignore its current stock) to gather products. If we want to chain moves and have this one to wait for the previous, this second option should be chosen."""),
