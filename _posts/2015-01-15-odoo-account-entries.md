---
layout: post
title: Odoo accounting elements
comments: true
permalink: "odoo-accounting-elements"
---
## Account Journal
Accounting entries need to be orgnized in an accounting journal, which is a record of transactions ordered by date. So you should create a Sales Journal for customer invoices, a Sales Refund journal for customer credit notes, a Purchase Journal for supplier invoices, a Purchase Refund journal for supplier credit notes and a Bank Journal for bank transactions. Other journals can be added as well.
![account journal form]({{ site.baseurl }}/images/odoo/account/account_journal_form.png)

Account journal is made up of journal entries,and journal entry is made up of journal items, and journal item is made up of account entries. bellow is a journal entry `SAJ/2015/008` which belongs to journal of `Sales Journal - (test) (EUR)`,and
has 3 journal items (1 of `Debtors - (test)` account and 2 of `Product Sales - (test)` accounts)
![account journal entries]({{ site.baseurl }}/images/odoo/account/journal_entry.png)  

 
The default credit and debit account in journal allows the software to automatically generate counterpart entries when you are entering data through Journal Items.
![account journals]({{ site.baseurl }}/images/odoo/account/journal.png)  

``` python
account_journal.py
'centralisation': fields.boolean('Centralized Counterpart', help="Check this box to determine that each entry of this journal won't create a new counterpart but will share the same counterpart. This is used in fiscal year closing.")
#You cannot create more than one move per period on a centralized journal.
account_move.py 
def _check_centralisation() #len(move_ids) should not > 1 
	move_ids = self.search(cursor, user, [
	                    ('period_id', '=', move.period_id.id),
	                    ('journal_id', '=', move.journal_id.id),
	                    ])
```
## Account Invoice
Invoice associates 1) the partner account used for this invoice, 
2)account journal used for automatically generating journal Items to account_move model
![account invoice]({{ site.baseurl }}/images/odoo/account/account_invoice_form.png) 
 
account_move model records invoicing results which are grouped by account journals 
![account invoice]({{ site.baseurl }}/images/odoo/account/account_invoice.png)

```python
addons/account/account_invoice.py
# mapping invoice type to journal type
TYPE2JOURNAL = {
    'out_invoice': 'sale',
    'in_invoice': 'purchase',
    'out_refund': 'sale_refund',
    'in_refund': 'purchase_refund',
}

# mapping invoice type to refund type
TYPE2REFUND = {
    'out_invoice': 'out_refund',        # Customer Invoice
    'in_invoice': 'in_refund',          # Supplier Invoice
    'out_refund': 'out_invoice',        # Customer Refund
    'in_refund': 'in_invoice',          # Supplier Refund
}
def _default_journal(self):
	return self.env['account.journal'].search([('type', 'in', filter(None, map(TYPE2JOURNAL.get, inv_types))], limit=1)

reconciled = fields.Boolean(string='Paid/Reconciled',
        store=True, readonly=True, compute='_compute_reconciled',#check dependencies as bellow 
        help="It indicates that the invoice has been paid and the journal entry of the invoice has been reconciled with one or several journal entries of payment.")
@api.one
@api.depends('account_id', 'move_id.line_id.account_id', 'move_id.line_id.reconcile_id')
def _compute_reconciled(self):
    self.reconciled = self.test_paid()

```

## References
[What is an accounting journal and how can I configure it?](https://www.odoo.com/forum/how-to/concepts-16/what-is-an-accounting-journal-and-how-can-i-configure-it-54079)