####Liquid template engine
* ��Դ��
* ����Ruby������ģ������
* ���ڼ��ض�̬������

> [Liquid][shopify] ���ʹ�ñ��(*tags*)������(*Objects*)�͹�����(*filters*)����̬�������ݡ�
> ͨ��ʹ����Liquid�е�**template**�ļ�����Щ�ļ������������⡣
> ����[**Templeates**](http://docs.shopify.com/themes/theme-development/templates)�������Ӳ���
[shopify]:http://docs.shopify.com/themes/liquid-documentation/basics "Shopify Page"

####Tags
**Description:** *���ڱ�ǳ�����߼�������templates����ôȥ��*
```ruby
    {% if user.name == 'elvis' %}
     Hey Elvis
    {% endif %}
```
**Note**: [Read More](http://docs.shopify.com/themes/liquid-documentation/tags)
***
####Objects
**Description:** *Objects����һ��������������ҳ������ʾ��̬����*
```ruby
    {{ product.title }} <!-- Output: Awesome T-Shirt-->
```
**Note**: [Read More](http://docs.shopify.com/themes/liquid-documentation/objects)
***
####Filters
**Description:** *Filters�����޸�������ַ��������֡������Ͷ���*
```ruby
    {{ 'sales' | append: '.jpg' }} <!-- Output: sales.jpg -->
```
**Note**: [Read More](http://docs.shopify.com/themes/liquid-documentation/filters)