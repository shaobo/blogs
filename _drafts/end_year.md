Accounting -> Periodical processing -> End of period -> Generate opening entries

This opening entries are supposed to create only once in a year. In openerp accouting entries are separate for each fiscal year. When a new fiscal year is starting, we will start the new year with all the balances in the previous year.

Samba, in that case you will need a reporting engine or a view model (same as OpenERP/Odoo used for analytic view) that will generate the information you need. The data is there, it just need some processing, e.g. taking the sum of all confirmed transactions from the beginning of fiscal year up to a certain date. Note that since OpenERP/Odoo does not have monthly or periodical balance aside from the start of Fiscal Year, the balance of each month/period may change if the there is any transaction being confirmed or cancelled in that and the preceeding months/period. If your accounting team requires that month/period balance to be static, then only generate/check the report after the said month/period and all preceeding month/period been closed, which also mean that no transaction can be made on those months/periods anymore.



SELECT a.id
            FROM account_account a
            LEFT JOIN account_account_type t ON (a.user_type = t.id)
            WHERE a.active
              AND a.type not in ('view', 'consolidation') AND t.close_method ='balance'
--select * from account_move_line where account_id =12


select COALESCE(SUM(l.debit),0) - COALESCE(SUM(l.credit), 0) as balance from account_move_line l where account_id in (SELECT a.id
            FROM account_account a
            LEFT JOIN account_account_type t ON (a.user_type = t.id)
            WHERE a.active
              AND a.type not in ('view', 'consolidation') AND t.close_method ='balance') and journal_id <> 9
              
SELECT a.id
FROM account_account a
LEFT JOIN account_account_type t ON (a.user_type = t.id)
WHERE a.active
  AND a.type not in (\\'view\\', \\'consolidation\\')
  AND a.company_id = %s
  AND t.close_method = %s'

SELECT reconcile_id, name, create_uid, create_date, write_uid, write_date,
                     statement_id,currency_id, date_maturity, partner_id,
                     blocked, credit, 'draft' , debit, ref, account_id,
                     amount_currency, quantity, product_id, company_id
                   FROM account_move_line
                   WHERE account_id IN (9,17) and account_move_line.state <> 'draft' AND account_move_line.period_id IN (SELECT id FROM account_period WHERE fiscalyear_id IN (1))
                   AND reconcile_id IS NULL

http://openerpbook.com/accounting/3_9/entries_at_the_start_of_a_year.html
https://doc.odoo.com/7.0/zh_CN/book/3/3_9/entries_at_the_start_of_a_year/
http://acespritechblog.com/2012/11/19/how-to-create-opening-balance-for-new-year-in-openerp/
http://openerp-doc.readthedocs.org/en/latest/