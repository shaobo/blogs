---
layout: post
title: Odoo with catch-all email
comments: true
permalink: "odoo-catchall-email"
---

*Mail server provide facility like catchall which will handle all mail with domain means here i try to explain with example that may
be useful to you :
Ex. Company domain name :
     @domain.com
now someone send a mail to xxx@domain.com now there is no any xxx mail address defined in the @domain.com so these kind of mails are
handled under catchall.  [> from Pinakin Nayi: Mail alias in OpenERP 7](http://pinakinnayi.blogspot.com/2013/07/mail-alias-with-openerp-7.html)
*
## config email catchall in gmail
assume you have enterprise version gmail service applied,go to gmail admin console and set info@yourMailDomain.com as your catchall mail account.
the account info@yourMailDomain.com should be created first,and should be logined with a browser to reset its password to avoid the mail client program (the odoo fetchmail module in later) would result connection failed error.
[admin console > routing advance](https://support.google.com/a/answer/33962?hl=en)

## config email catchall in odoo
assume Technical Feature is enabled under Administrator,go to Settings>System Parameters,set the following two parameters:  
* Alias domain(mail.catchall.domain):yourMailDomain.com
* mail.catchall.alias:info

odoo would also using the above setting eg mail.catchall.alias@mail.catchall.domain which is 'info@yourMailDomain.com' as your system outgoing email account,event though you may have other  
email setting under your company data (Settings>General Settings>Companies)

## config Incoming mail servers
setting>configuration>general setting>email,in this form, the Name field should be set like 'companyName <info@yourMailDomain.com>',because it will be used as reply-to field in the email message header like following:

```
from:	  anyoneInOdoo <info@yourMailDomain.com>
reply-to: companyName  <info@yourMailDomain.com>
```

other fields could be:  
  pop.gmail.com, SSL/TLS

## config Outgoing mail servers
config using the same email account ,other info setting as:   
  smtp.gmail.com,TLS (STARTTLS)

## config alias
```
Unless you have very specific needs, you never have to create aliases manually. Alias are defined directly on 'sales teams', 'projects', 'users', 'mail groups', etc.
To define an alias that create leads, just set the alias on the Sales Team.
For a user, its alias is his login by default.
```

you can verify 'Messaging Alias' field in Preferences Tab of users settings which should be userName@yourMailDomain.com
or you can find the Alias listing under Settings>Technical>Email>Aliases
or you can customize group aliases under  
My Groups> Disccssion groups > set alias field

## config Fetchmail Service
verify that fetch mail service is scheduled under Settings>Technical>Scheduled Actions>Fetchmail Service


## test catch-all
send email to unexisting mail account such as sales@yourMailDomain.com,wait the interval of fetchmail service, you should see a new Lead
is created under Sales>Leads
that is because you configed catchall in gmail for unexist mail account message going into info@yourMailDomain.com,also the alias setting for relates sales@yourMailDomain.com to Aliased Model:Lead/Opportunity

### References
[email-messaging-with-odoo](https://www.odoo.com/forum/help-1/question/how-to-set-up-e-mail-messaging-with-odoo-61796)  
[openerp email catchall screenshot](https://www.erponline.vn/vi/support/documentation/16-tutorials/72-t%C3%ADch-h%E1%BB%A3p-email-v%C3%A0o-erponline-odoo)  
[odoo email CN forum](http://shine-it.net/index.php/topic,12643.0.html)

[How can I manage my emails related to leads and opportunities?](https://www.odoo.com/forum/help-1/question/how-can-i-manage-my-emails-related-to-leads-and-opportunities-28)  
[odoo leads](https://doc.odoo.com/book/2/3_CRM_Contacts/leads/)

*seems to be optional:
[smtp relay](http://mhawthorne.net//posts/postfix-configuring-gmail-as-relay.html)  

Authenticate email  
The domains you select will use the DKIM (DomainKeys Identified Mail) protocol for authenticating outgoing emails You must update the DNS records for this domain*




