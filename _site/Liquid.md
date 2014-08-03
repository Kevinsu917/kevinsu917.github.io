####Liquid template engine
* 开源的
* 基于Ruby开发的模板语言
* 用于加载动态的内容

> [Liquid][shopify] 组合使用标记(*tags*)、对象(*Objects*)和过滤器(*filters*)来动态加载内容。
> 通过使用在Liquid中的**template**文件，这些文件用于制作主题。
> 关于[**Templeates**](http://docs.shopify.com/themes/theme-development/templates)请点击链接查阅
[shopify]:http://docs.shopify.com/themes/liquid-documentation/basics "Shopify Page"

####Tags
**Description:** *用于标记程序的逻辑，告诉templates该怎么去做*
```ruby
    {% if user.name == 'elvis' %}
     Hey Elvis
    {% endif %}
```
**Note**: [Read More](http://docs.shopify.com/themes/liquid-documentation/tags)
***
####Objects
**Description:** *Objects包含一个参数，用于在页面上显示动态内容*
```ruby
    {{ product.title }} <!-- Output: Awesome T-Shirt-->
```
**Note**: [Read More](http://docs.shopify.com/themes/liquid-documentation/objects)
***
####Filters
**Description:** *Filters用于修改输出的字符串、数字、变量和对象*
```ruby
    {{ 'sales' | append: '.jpg' }} <!-- Output: sales.jpg -->
```
**Note**: [Read More](http://docs.shopify.com/themes/liquid-documentation/filters)