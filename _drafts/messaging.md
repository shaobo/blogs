odoo下的设计mail_thread class 作为基础的messaging
http://www.slideshare.net/openobject/performance2014-35689113?related=5

实现在新创建product时进行messaging，那么在
代码中初步看来只需增加两点即可。
（可查询新发消息select * from mail_message where model='product.product'）。

class product_product(osv.osv):
    1）_inherit = ['mail.thread']

def create(self, cr, uid, vals, context=None):
        if context is None:
            context = {}
        ctx = dict(context or {}, create_product_product=True)
        return super(product_product, self).create(cr, uid, vals, context=ctx) 
      //2）这儿的super（..）.create 会自动调用mail_thread.create(self, cr, uid, values, context=None) 


同理理论上在product_product下增加编辑修改处理方法，
def write(..)
       super(..).write(..) 应该会自动调用
mail_thread.write(self, cr, uid, values, context=None) 
进行消息处理

未经确切验证，不知我的理解是否正确