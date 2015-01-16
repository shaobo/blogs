
Reconciliation on Bank Statements
Reconciliation is s generally applied to payments of suppliers and customers against corresponding invoices.

Without the reconciliation process, OpenERP would be incapable of marking invoices that have been paid. Suppose that you have got the following situation for the Smith and Offspring customer:

Invoice 145: 50,

Invoice 167: 120,

Invoice 184: 70.

If you receive a payment of 120, OpenERP will delay reconciliation because there is a choice of invoices to pay. It could either reconcile the payment against invoices 145 and 184 or against invoice 167.

Invoices are marked as Paid when accounting entries on the invoice have been reconciled with accounting entries about their payment.

Reconciliation in OpenERP can only be carried out in accounts that have been configured as reconcilable 
account_account.py
'reconcile': fields.boolean('Allow Reconciliation', help="Check this box if this account allows reconciliation of journal items.")

--http://localhost:8069/web#menu_id=182&action=182

select * from ir_values where value like '%,182'

select * from ir_act_client where id = 182

res_model:account.bank.statement.line
account_bank_statement_line
def get_data_for_reconciliations
select * from account_bank_statement_line order by id 


Journal Items to Reconcile
account.move.line