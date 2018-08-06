###Django-template模板语言

**一、常用语法**

只需要记两种特殊符号：

{{  }}和 {% %}

变量相关的用{{}}，逻辑相关的用{%%}。

首先把views里代码贴出了，之后就是在HTML中各种模板语言替换了 本质都是字符串的替换

```
def template(request):
    str = '你好'
    list1 = ['aa','bb','cc']
    ss = '簌簌衣襟落枣花，村南村北响缫车，牛衣古柳卖黄瓜。酒困路长惟欲睡，日高人渴漫思茶，敲门试问野人家。'
    dic = { 'name' : '二哥', 'age' : 18,}
    class Template(object):
        def __init__(self,name,age):
             self.name = name
             self.age = age
    p1 = Template('zzy',18)
    p2 = Template('zxc',28)
    list_dic = [p1,p1]
    file_size = 12345
    import datetime
    date = datetime.datetime.now()
    data = {
    'template' : p1,
    'str' : str,
    'list1' : list1,
    'dic' : dic,
    'ss' : ss,
    'list_dict' : list_dict,
    'file_size' : file_size,
    'date' : date,
    }
    
    return render(request,'template.html',data)
```

 **二、变量**

{{ 变量名 }}

点（.）在模板语言中有特殊的含义，用来获取对象的相应属性值。

下面举例更清晰

```
{#单独字符串#}
{{ s }}

{#类的对象。通过 点属性 可以获取属性值#}
{{ template.name }}

{#字典类型的 直接取key对应的value#}
{{ dic.name }}

</p>
{#列表取值#}
<div>
{% for i in list1 %}
    {{ i.0  }}:{{ i.1 }}    输出：a:a b:b c:c
{% endfor %}
</div>
```

总结模板支持的写法 

```
{# 取list1中的第一个参数 #}
{{ list1.0 }}
{# 取字典中key的值 #}
{{ d.name }}
{# 取对象的name属性 #}
{{ person_list.0.name }}
{# .操作只能调用不带参数的方法 #}
{{ person_list.0.dream }}
```

**三、内置Filters**

先看例子

```
{#大段文字截取,剩余的文字用省略号#}
{{ ss|truncatechars:30 }}      输出：簌簌衣襟落枣花，村南村北响缫车，牛衣古柳卖黄瓜。酒困路...
{#列表中是字典取值#}
<div>{{ list_dic.1.name }}:{{ list_dic.1.age }}</div> 输出：zzy:18
{#计算文件大小#}
{{ file_size|filesizeformat }}
{#时间对象#}
<div> {{ date|date:"Y:m:d H:i:s" }}</div>    输出：2018:03:29 21:29:54
{#切片示例#}
<div>{{ ss|slice:'2:8' }}</div>     输出：衣襟落枣花，
{#求列表的长度#}
<div>
    {{ list1|length }}
</div>
```

**总结知识点**

**default**

```
{{ value:default: "nothing"}}
```

**length** 

```
{{ value|length }}
```

'|'左右没有空格没有空格没有空格

返回value的长度，如 value=['a', 'b', 'c', 'd']的话，就显示4.

**filesizeformat**

```
{{ value|filesizeformat }}
```

将值格式化为一个 “人类可读的” 文件尺寸 （例如 `'13 KB'`, `'4.1 MB'`, `'102 bytes'`, 等等）。例如：

**slice 切片**

```
{{value|slice:"2:-1"}}
```

**date 日期格式化** 

```
{{ value|date:"Y-m-d H:i:s"}}
```

**safe**

Django的模板中会对HTML标签和JS等语法标签进行自动转义，原因显而易见，这样是为了安全。但是有的时候我们可能不希望这些HTML元素被转义，比如我们做一个内容管理系统，后台添加的文章中是经过修饰的，这些修饰可能是通过一个类似于FCKeditor编辑加注了HTML修饰符的文本，如果自动转义的话显示的就是保护HTML标签的源文件。为了在Django中关闭HTML的自动转义有两种方式，如果是一个单独的变量我们可以通过过滤器“|safe”的方式告诉Django这段代码是安全的不必转义。

比如：

```
value = '<a href="#">不需要转义的a标签</a>'
{{ value|safe}}
```

**truncatechars**

如果字符串字符多于指定的字符数量，那么会被截断。截断的字符串将以可翻译的省略号序列（“...”）结尾。

参数：截断的字符数

```
{{ value|truncatechars:9}}
```

with定义一个中间变量，如果觉得自己的变量名太长，可以用with赋一个别名

```
{% with obj=list_dic.1.name %}
    {% for i in obj %}
    {% endfor %}
{% endwith %}
```

csrf_token
在你的form表单中写入csrf_token，这个标签用于跨站请求伪造保护

**四、自定义filter**

**自定义filter代码文件摆放位置：**

```
app01/
    __init__.py
    models.py
    templatetags/  # 在app01下面新建一个package 
        __init__.py
        app01_filters.py  # 建一个存放自定义filter的文件
    views.py
```

编写自定义filter

在filter的文件写你的代码

举例：实现在页面中输入三个数自动输出他们的和

编写自定义filter

```
from django import template
register = template.Library()
 
 
@register.filter(name="cut")
def cut(value, arg):
    return value.replace(arg, "")
 
 
@register.filter(name="addSB")
def add_sb(value):
    return "{} SB".format(value)
```

使用自定义filter

```
{# 先导入我们自定义filter那个文件 #}
{% load app01_filters %}
 
{# 使用我们自定义的filter #}
{{ somevariable|cut:"0" }}
{{ d.name|addSB }}
```

### for循环

```
<ul>
{% for user in user_list %}
    <li>{{ user.name }}</li>
{% endfor %}
</ul>
```

**for ... empty  如果替换内容为空就显示empty下面的内容**

```
<ul>
{% for user in user_list %}
    <li>{{ user.name }}</li>
{% empty %}
    <li>空空如也</li>
{% endfor %}
</ul>
```

**if,elif和else**

```
{% if user_list %}
  用户人数：{{ user_list|length }}
{% elif black_list %}
  黑名单数：{{ black_list|length }}
{% else %}
  没有用户
{% endif %}
```

**if和else**

```
{% if user_list|length > 5 %}
  七座豪华SUV
{% else %}
    黄包车
{% endif %}
```

if语句支持 and 、or、==、>、<、!=、<=、>=、in、not in、is、is not判断。

## 母板

模板代码

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="x-ua-compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>Title</title>
  {% block page-css %}
   
  {% endblock %}
</head>
<body>
 
<h1>这是母板的标题</h1>
 
{% block page-main %}
 
{% endblock %}
<h1>母板底部内容</h1>
{% block page-js %}
 
{% endblock %}
</body>
</html>
```

我们通常会在母板中定义页面专用的CSS块和JS块，方便子页面替换。

```
{% block page_css %}
{% endblock %}
{% block page_js %}
{% endblock %}
```

**继承母板**

在子页面中在页面最上方使用下面的语法来继承母板。

```
{% extends 'layouts.html' %}
```

**块（block）**

通过在母板中使用`{% block  xxx %}`来定义"块"。

在子页面中通过定义母板中的block名来对应替换母板中相应的内容。

```
{% block page-main %}
  <p>世情薄</p>
  <p>人情恶</p>
  <p>雨送黄昏花易落</p>
{% endblock %}
```

**组件**

可以将常用的页面内容如导航条，页尾信息等组件保存在单独的文件中，然后在需要使用的地方按如下语法导入即可。

```
{% include 'navbar.html' %}
```

**静态文件相关**

```
    {#    动态获取static路径，当你setting里面的STATIC_URL = '/static/'修改了名字，也不用担心#}
    {#    <link rel="stylesheet" href="/static/bootstrap/css/bootstrap.min.css">#}
{#    先导入static 会自动去#}
      {% load static %}
    <link rel="stylesheet" href={% static "bootstrap/css/bootstrap.min.css" %} >
```

某个文件多处被用到可以存为一个变量

```
{% load static %}
{% static "images/hi.jpg" as myphoto %}
<img src="{{ myphoto }}"></img>
```

五、自定义simpletag

和自定义filter类似，只不过接收更灵活的参数。

定义注册simple tag  

1：首先在app中写一个求和的函数，并且做一些处理

```
# 导入模板方法
from django import template
register=template.Library()
# 注册这个filter方法
@register.simple_tag(name='add_sum')
def add_sum(a,b,c):
    return a+b+c
```

2.在你要作用的HTML页面中写执行代码

```
{#2:写好函数后导入函数py文件#}
{#2:注意写法，用一个花括号，同时传入的参数不加逗号#}
<div>
{% load add_sum %}
{% add_sum 1 2 3%}

</div>
```

### inclusion_tag

多用于返回html代码片段 需要三个文件 第一个写自定义的filter  ；第二个是写执行filter的，在作用的html页面内写 ；第三个filter代码要替换的字符串

templatetags/add_sum.py

```
from django import template
 
register = template.Library()
# 当我们调用func方法，就会返回inclusion_2.html里面的内容
@register.inclusion_tag('inclusion_2.html')
def func(num):
    num=1 if num<1 else int(num)   #三元运算
    data=['第{}项'.format(i) for i in  range(1,num+1)]  #[第1项，第2项....]
    return {'data':data}    #模板语言：返回数据给inclusion_2.html中，实现字符串的替换
```

templates/snippets/*inclusion_2.html*

```
<ul>
  {% for choice in data %}
    <li>{{ choice }}</li>
  {% endfor %}
</ul>
```

templates/index.html

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta http-equiv="x-ua-compatible" content="IE=edge">
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <title>inclusion_tag test</title>
</head>
<body>
 #导入写自定义filter方法的文件
{% load add_sum %}
#调用方法中func函数，传进一个参数
{% func 10 %}
</body> </html>
```

