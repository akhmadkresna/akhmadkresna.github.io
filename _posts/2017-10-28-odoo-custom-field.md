---
layout: post
title: "Odoo : Add custom field"
description: "Create odoo custom module for add a field on certain odoo backend interface."
tags: [web, odoo, custom, module, open source, python, framework]
---

Before you develop anything make sure you enable `odoo developer mode` from setting menu top right corner choose about. About wizard will pop-up and then you click `activate developer mode` in the top right corner. Odoo Developer mode help you to get what name of view/object you want to inherit to use that name on your code.

{% include image.html path="post/odoofield/activatedevmode.png" path-detail="post/odoofield/activatedevmode.png" alt="odoo developer mode" %}

This tutorial will contain steps to create odoo custom field on product form view interface.

### Lets create the module :

Every odoo custom module should have these 2 files : 
1. `__init__.py` file for call/import your python files from your module otherwise odoo wouldn’t load/read/detect your python files. 
2. `__openerp__.py` this file will contain your module desc and for call/import interface code such as xml, qweb, js etc. 

For add new custom field we will need a python files and a xml file. Python file will contain code for inherit odoo object to add new attribute(field) structurally. Xml file will contain code for inherit odoo view to show the field we just created on python file before.

According to OCA (Odoo Community Association), here is odoo module hierarchy should look like :


{% highlight xml %}

# ODOO MODULE
- __init__.py
- __openerp__.py  
- models
  - __init__.py
  - your_python_file.py
- views
  - your_xml_files.xml

{% endhighlight %}


* `/__init__.py`: import your subfolder that will contain python codes. in this case is models folder.
{% highlight python %}
import models
{% endhighlight %}
* `/models/__init__.py`: `__init__.py` which is located inside models folder will use for load you python files like in the code below.
{% highlight python %}
# import your python files without extension
import your_python_file
{% endhighlight %}

* `__openerp__.py`: This file will contain your module desc and for call/import interface code such as xml, qweb, js etc. In this case your `__openerp__.py` should look like this :

{% highlight python %}

{
    'name': 'Custom Product field Module',
    'version': '1.0',
    'summary': 'Custom Product Module. . . ',
    'description':'''
        add custom field on product form view
    ''',
    'category': 'Custom Products',
    'author': 'Akhmad Kresna',
    'website': 'http://www.yourwebsite.com',
    'depends': ['product'],
    'data': [
      'views/product_custom_view.xml',
    ],

}
{% endhighlight %}
* `Your_python_file.py`: This file will contain code for inherit odoo object to add new attribute(field) structurally. So you will need object name of view you wanna add custom field. in this case we gonna add custom field on product form view.

`1.` go to product form view can be from Inventory/Inventory Control/Product.

`2.` use odoo developer mode to retrieve the name of current view object from edit form view menu.
    
{% include image.html path="post/odoofield/editformview.png" path-detail="post/odoofield/editformview.png" alt="odoo formview" %}

`3.` odoo edit form view wizard will popup. Wizard will contain information related to current form view such as its object and xml id.

{% include image.html path="post/odoofield/editformviewwizard.png" path-detail="post/odoofield/editformviewwizard.png" alt="odoo formview" %}

NOW `your_python_file.PY` should look like this

{% highlight python %}
# import basic odoo library for creating object and field
from openerp import models, fields, api

# declare new object/class. "productTemplate" is a new name.
# you could name it as you want
class productTemplate(models.Model):
	# inherit odoo object where you want add a new field
    _inherit = 'product.template'

    # declare your new field 'custom_field'
    # 'Custom field' as its label on the interface
    custom_field = fields.Char('Custom field')
    
{% endhighlight %}

Until this point you technically add a custom field but it will not showed yet until we show it using xml code.

* `Your_xml_file.py` : After you add field on odoo object structure through python code, now we gonna show it on the product form view interface. first decide placement of your new field. I placed it below `barcode field`. So we will need technical name of `barcode field`. Hover to `barcode field` on odoo product form view and field information will show just like in the pic below. and for that to happen make sure you enable odoo developer mode.

{% include image.html path="post/odoofield/productformview.png" path-detail="post/odoofield/productformview.png" alt="odoo form" %}

NOW `your_xml_file.xml` should look like this

{% highlight xml %}
<?xml version="1.0" encoding="utf-8"?>
<openerp>
    <data>
    	<!-- new id name for your new xml record with ir.ui.view model -->
      <record id="view_product_form" model="ir.ui.view">
      	<!-- new name for your new view record -->
		<field name="name">product.form.inherit</field>
		<!-- object of the view -->
		<field name="model">product.template</field>
		<!-- inherit id is view name you want to inherit. we can get it from edit form view wizard like i showed above -->
		<field name="inherit_id" ref="product.product_template_only_form_view" />
		<field name="arch" type="xml">
		<!-- place your new custom after 'barcode' field -->
		  <field name="barcode" position="after">
            <field name="custom_field"/>
          </field>
        </field>
      </record>
    </data>
</openerp>
    
{% endhighlight %}

And Thats it. Restart your odoo server, go to Apps menu, update app list, search install your new module.

<a href="https://github.com/akhmadkresna/odoo-custom-modules/tree/master/add_field">Download Source code here!</a>

If you have any questions about this article, just leave comment below
