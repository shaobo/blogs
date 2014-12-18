---
layout: post
title: Odoo accounting
comments: true
permalink: "odoo-accounting"
---

*You need to start off with a minimal set of accounts, and to do that you will need a couple of account types. You can structure your accounts into a chart at any time (and, in fact, you can structure them into several additional charts at the same time as you will see in the chapter Configuring Accounts from A to Z) [< odoo book](https://doc.odoo.com/book/)*

## Basic Accounts
odoo built-in accounts are orgnized in tree structure,which can be queried out by  

```sql
select a.id,a.code,a.name,a.type,a.reconcile,b.name acount_type,b.close_method,b.code,b.report_type,a.parent_id  
from account_account a,account_account_type b  
where a.user_type = b.id order by id
```

![accounts-tree]({{ site.baseurl }}/images/account_struc.svg)


the accounts are used in two major reports:
1,资产负债表 Balance Sheet (Assets Accounts)
Assets = Liabilities + Shareholders’ Equity

2,损益表Profit & Loss (Expense Accounts)
Profit = Income - Expense

Account type's Deferral Method:

Set the method that will be used to generate the end of year journal entries for all the accounts of this type,

'None' means that nothing will be done.
'Balance' will generally be used for cash accounts.
'Detail' will copy each existing journal item of the previous year, even the reconciled ones.
'Unreconciled' will copy only the journal items that were unreconciled on the first day of the new fiscal year.

递延方法:本类型会计科目从本期结转至下期时的结转方法:
有 None(不结转)、Balance(余额结转)、Detail(原样抄至下期)、Unreconciled(未核销)。
系统会根据设置的结转方法,自动将科目结转至下期。
如果选择 None,则要自己手工填写结转分录,结转科目。
Unreconciled 主要是指应税账款、应付账款,未核销的应收、应付账款科目应该结转至下期。

## Other Modules relate to Accounting
with defining some default properties, so that you do not have to think about which account is used for which transaction every time you do something. The main new properties are the four that associate accounts payable and receivable to partners, and expenses and income to product categories.  

Account Receivable：和客户发生的销售业务记账凭证中，应收账款对应的会计科目  
Account Payable：和业务伙伴发生的采购业务记账凭证中，应付账款对应的会计科目  
Income Account: 产品收入科目，产品销售业务的会计分录的贷方，借方是应收账款  
Expense Account: 产品成本科目，产品采购业务的会计分录的借方,贷方是应付账款  
Stock Output Account: 产品出库科目，产品出库时会计分录的借方，贷方是库存管理中库位设置时指定的科目  
Stock Input Account: 产品入库科目，产品入库时会计分录的贷方，借方是库存管理中库位设置时指定的科目  

## Other TERM
- liquidity 流动比率（Current Ratio）=流动资产÷流动负债
- Journal Vocher：收付款凭证归类 
- Customer Vocher：客户收款凭据  sales receipt
- Supplier Vocher：供应商付款凭据 supplier receipt

*A voucher is an accounting document representing an internal intent to make a payment to an external entity, such as a vendor or service provider. A voucher is produced usually after receiving a vendor invoice, after the invoice is successfully matched to a purchase order.[>wikipedia](http://en.wikipedia.org/wiki/Voucher)*

#references#
- [Double-entry bookkeeping system](http://en.wikipedia.org/wiki/Double-entry_bookkeeping_system)
- [accounting dashboard](http://openerp-61-users-guide.readthedocs.org/en/latest/book/3/3_8/company_financial_analysis/)
- [Account creation](https://www.odoo.com/forum/how-to/accounting-finance-8/how-can-i-create-a-new-account-54197)
- [CN odoo forum](http://shine-it.net/index.php/topic,2431.0.html)
- [openerp book](http://openerp-61-users-guide.readthedocs.org/en/latest/book/)