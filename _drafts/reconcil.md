
Reconciliation on Bank Statements
Reconciliation is  generally applied to payments of suppliers and customers against corresponding invoices.

Without the reconciliation process, OpenERP would be incapable of marking invoices that have been paid. Suppose that you have got the following situation for the Smith and Offspring customer:

Invoice 145: $500,

Invoice 167: $1200,

Invoice 184: $700.

If you receive a payment of 1200, OpenERP will delay reconciliation because there is a choice of invoices to pay. It could either reconcile the payment against invoices 145 and 184 or against invoice 167.

Invoices are marked as Paid when accounting entries on the invoice have been reconciled with accounting entries about their payment.

Reconciliation in OpenERP can only be carried out in accounts that have been configured as reconcilable 
account_account.py
'reconcile': fields.boolean('Allow Reconciliation', help="Check this box if this account allows reconciliation of journal items.")

account_automatic_reconcile.py
#match amount of credit/debit to do_reconcile ,reconcile automatically all transactions from partners whose balance is 0
def do_reconcile

--
SELECT partner_id FROM account_move_line WHERE account_id=%s AND reconcile_id IS NULL
                AND state <> 'draft' GROUP BY partner_id
                HAVING ABS(SUM(debit-credit)) < write-of-amount AND count(*)>0


# unreconciled transactions 

cr.execute(
    "SELECT count(*) " \
    "FROM account_move_line " \
    "WHERE account_id=%s " \
    "AND reconcile_id IS NULL " \
    "AND state <> 'draft' " 

--http://localhost:8069/web#menu_id=182&action=182

select * from ir_values where value like '%,182'

select * from ir_act_client where id = 182

res_model:account.bank.statement.line
account_bank_statement_line
def get_data_for_reconciliations
select * from account_bank_statement_line order by id 


Journal Items to Reconcile
account.move.line