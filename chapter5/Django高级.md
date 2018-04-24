#Django高级                                            

## 静态文件处理

- 项目中的CSS、图片、js都是静态文件

### 配置静态文件

- 在settings 文件中定义静态内容

```
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static'),
]1234
```

- 在项目根目录下创建static目录，再创建当前应用名称的目录

```
mysite/static/myapp/1
```

- 在模板中可以使用硬编码

```
/static/my_app/myexample.jpg1
```

- 在模板中可以使用static编码

```
{ % load static from staticfiles %}
<img src="{ % static "my_app/myexample.jpg" %}" alt="My image"/>12
```

## 中间件

- 是一个轻量级、底层的插件系统，可以介入Django的请求和响应处理过程，修改Django的输入或输出

- 激活：添加到Django配置文件中的MIDDLEWARE_CLASSES元组中

- 每个中间件组件是一个独立的Python类，可以定义下面方法中的一个或多个 

  ​

  - *init *：无需任何参数，服务器响应第一个请求的时候调用一次，用于确定是否启用当前中间件
  - process_request(request)：执行视图之前被调用，在每个请求上调用，返回None或HttpResponse对象
  - process_view(request, view_func, view_args, view_kwargs)：调用视图之前被调用，在每个请求上调用，返回None或HttpResponse对象
  - process_template_response(request, response)：在视图刚好执行完毕之后被调用，在每个请求上调用，返回实现了render方法的响应对象
  - process_response(request, response)：所有响应返回浏览器之前被调用，在每个请求上调用，返回HttpResponse对象
  - process_exception(request,response,exception)：当视图抛出异常时调用，在每个请求上调用，返回一个HttpResponse对象

- 使用中间件，可以干扰整个处理过程，每次请求中都会执行中间件的这个方法

- 示例：自定义异常处理

- 与settings.py同级目录下创建myexception.py文件，定义类MyException，实现process_exception方法

```
from django.http import HttpResponse
class MyException():
    def process_exception(request,response, exception):
        return HttpResponse(exception.message)1234
```

- 将类MyException注册到settings.py中间件中

```
MIDDLEWARE_CLASSES = (
    'test1.myexception.MyException',
    ...
)1234
```

- 定义视图，并发生一个异常信息，则会运行自定义的异常处理

## 上传图片

- 当Django在处理文件上传的时候，文件数据被保存在request.FILES
- FILES中的每个键为中的name
- 注意：FILES只有在请求的方法为POST 且提交的带有enctype=”multipart/form-data” 的情况下才会包含数据。否则，FILES 将为一个空的类似于字典的对象
- 使用模型处理上传文件：将属性定义成models.ImageField类型

```
pic=models.ImageField(upload_to='cars/')1
```

- 注意：如果属性类型为ImageField需要安装包Pilow

```
pip install Pillow==3.4.11
```

- 图片存储路径 

  ​

  - 在项目根目录下创建media文件夹
  - 图片上传后，会被保存到“/static/media/cars/图片文件”
  - 打开settings.py文件，增加media_root项

```
MEDIA_ROOT=os.path.join(BASE_DIR,"static/media")1
```

- 使用django后台管理，遇到ImageField类型的属性会出现一个file框，完成文件上传
- 手动上传的模板代码

```
<html>
<head>
    <title>文件上传</title>
</head>
<body>
    <form method="post" action="upload/" enctype="multipart/form-data">
        <input type="text" name="title"><br>
        <input type="file" name="pic"/><br>
        <input type="submit" value="上传">
    </form>
</body>
</html>123456789101112
```

- 手动上传的视图代码

```
from django.conf import settings

def upload(request):
    if request.method == "POST":
        f1 = request.FILES['pic']
        fname = '%s/cars/%s' % (settings.MEDIA_ROOT,f1.name)
        with open(fname, 'w') as pic:
            for c in f1.chunks():
                pic.write(c)
        return HttpResponse("ok")
    else:
        return HttpResponse("error")123456789101112
```

## Admin站点

- 通过使用startproject创建的项目模版中，默认Admin被启用
- 1.创建管理员的用户名和密码

```
python manage.py createsuperuser
然后按提示填写用户名、邮箱、密码12
```

- 2.在应用内admin.py文件完成注册，就可以在后台管理中维护模型的数据

```
from django.contrib import admin
from models import *

admin.site.register(HeroInfo)1234
```

- 查找admin文件：在INSTALLED_APPS项中加入django.contrib.admin，Django就会自动搜索每个应用的admin模块并将其导入

### ModelAdmin对象

- ModelAdmin类是模型在Admin界面中的表示形式
- 定义：定义一个类，继承于admin.ModelAdmin，注册模型时使用这个类

```
class HeroAdmin(admin.ModelAdmin):
    ...12
```

- 通常定义在应用的admin.py文件里
- 使用方式一：注册参数

```
admin.site.register(HeroInfo,HeroAdmin)1
```

- 使用方式二：注册装饰器

```
@admin.register(HeroInfo)
class HeroAdmin(admin.ModelAdmin):12
```

- 通过重写admin.ModelAdmin的属性规定显示效果，属性主要分为列表页、增加修改页两部分

### 列表页选项

#### “操作选项”的位置

- actions_on_top、actions_on_bottom：默认显示在页面的顶部

```
class HeroAdmin(admin.ModelAdmin):
    actions_on_top = True
    actions_on_bottom = True123
```

#### list_display

- 出现列表中显示的字段
- 列表类型
- 在列表中，可以是字段名称，也可以是方法名称，但是方法名称默认不能排序
- 在方法中可以使用format_html()输出html内容

```
在models.py文件中
from django.db import models
from tinymce.models import HTMLField
from django.utils.html import format_html

class HeroInfo(models.Model):
    hname = models.CharField(max_length=10)
    hcontent = HTMLField()
    isDelete = models.BooleanField()
    def hContent(self):
        return format_html(self.hcontent)

在admin.py文件中
class HeroAdmin(admin.ModelAdmin):
    list_display = ['hname', 'hContent']123456789101112131415
```

- 让方法排序，为方法指定admin_order_field属性

```
在models.py中HeroInfo类的代码改为如下：
    def hContent(self):
        return format_html(self.hcontent)
    hContent.admin_order_field = 'hname'
标题栏名称：将字段封装成方法，为方法设置short_description属性
在models.py中为HeroInfo类增加方法hName：
    def hName(self):
        return self.hname
    hName.short_description = '姓名'
    hContent.short_description = '内容'

在admin.py页中注册
class HeroAdmin(admin.ModelAdmin):
    list_display = ['hName', 'hContent']1234567891011121314
```

#### list_filter

- 右侧栏过滤器，对哪些属性的值进行过滤
- 列表类型
- 只能接收字段

```
class HeroAdmin(admin.ModelAdmin):
    ...
    list_filter = ['hname', 'hcontent']123
```

#### list_per_page

- 每页中显示多少项，默认设置为100

```
class HeroAdmin(admin.ModelAdmin):
    ...
    list_per_page = 10123
```

#### search_fields

- 搜索框
- 列表类型，表示在这些字段上进行搜索
- 只能接收字段

```
class HeroAdmin(admin.ModelAdmin):
    ...
    search_fields = ['hname']123
```

### 增加与修改页选项

- fields：显示字段的顺序，如果使用元组表示显示到一行上

```
class HeroAdmin(admin.ModelAdmin):
    ...
    fields = [('hname', 'hcontent')]123
```

- fieldsets：分组显示

```
class HeroAdmin(admin.ModelAdmin):
    ...
    fieldsets = (
        ('base', {'fields': ('hname')}),
        ('other', {'fields': ('hcontent')})
    )123456
```

- fields与fieldsets两者选一

### InlineModelAdmin对象

- 类型InlineModelAdmin：表示在模型的添加或修改页面嵌入关联模型的添加或修改
- 子类TabularInline：以表格的形式嵌入
- 子类StackedInline：以块的形式嵌入

```
class HeroInline(admin.TabularInline):
    model = HeroInfo

class BookAdmin(admin.ModelAdmin):
    inlines = [
        HeroInline,
    ]1234567
```

### 重写admin模板

- 在项目所在目录中创建templates目录，再创建一个admin目录
- 设置模板查找目录：修改settings.py的TEMPLATES项，加载模板时会在DIRS列表指定的目录中搜索

```
'DIRS': [os.path.join(BASE_DIR, 'templates')],1
```

- 从Django安装的目录下（django/contrib/admin/templates）将模板页面的源文件admin/base_site.html拷贝到第一步建好的目录里
- 编辑base_site.html文件
- 刷新页面，发现以刚才编辑的页面效果显示
- 其它管理后台的模板可以按照相同的方式进行修改

## 分页

- Django提供了一些类实现管理数据分页，这些类位于django/core/paginator.py中

### Paginator对象

- Paginator(列表,int)：返回分页对象，参数为列表数据，每面数据的条数

#### 属性

- count：对象总数
- num_pages：页面总数
- page_range：页码列表，从1开始，例如[1, 2, 3, 4]

#### 方法

- page(num)：下标以1开始，如果提供的页码不存在，抛出InvalidPage异常

#### 异常exception

- InvalidPage：当向page()传入一个无效的页码时抛出
- PageNotAnInteger：当向page()传入一个不是整数的值时抛出
- EmptyPage：当向page()提供一个有效值，但是那个页面上没有任何对象时抛出

### Page对象

#### 创建对象

- Paginator对象的page()方法返回Page对象，不需要手动构造

#### 属性

- object_list：当前页上所有对象的列表
- number：当前页的序号，从1开始
- paginator：当前page对象相关的Paginator对象

#### 方法

- has_next()：如果有下一页返回True
- has_previous()：如果有上一页返回True
- has_other_pages()：如果有上一页或下一页返回True
- next_page_number()：返回下一页的页码，如果下一页不存在，抛出InvalidPage异常
- previous_page_number()：返回上一页的页码，如果上一页不存在，抛出InvalidPage异常
- len()：返回当前页面对象的个数
- 迭代页面对象：访问当前页面中的每个对象

### 示例

#### 创建视图pagTest

```
from django.core.paginator import Paginator

def pagTest(request, pIndex):
    list1 = AreaInfo.objects.filter(aParent__isnull=True)
    p = Paginator(list1, 10)
    if pIndex == '':
        pIndex = '1'
    pIndex = int(pIndex)
    list2 = p.page(pIndex)
    plist = p.page_range
    return render(request, 'booktest/pagTest.html', {'list': list2, 'plist': plist, 'pIndex': pIndex})1234567891011
```

#### 配置url

```
url(r'^pag(?P<pIndex>[0-9]*)/$', views.pagTest, name='pagTest'),1
```

#### 定义模板pagTest.html

```
<!DOCTYPE html>
<html>
<head>
    <title></title>
</head>
<body>
<ul>
{%for area in list%}
<li>{{area.id}}--{{area.atitle}}</li>
{%endfor%}
</ul>

{%for pindex in plist%}
{%if pIndex == pindex%}
{{pindex}}&nbsp;&nbsp;
{%else%}
<a href="/pag{{pindex}}/">{{pindex}}</a>&nbsp;&nbsp;
{%endif%}
{%endfor%}
</body>
</html>123456789101112131415161718192021
```

## ajax

### 使用Ajax

- 使用视图通过上下文向模板中传递数据，需要先加载完成模板的静态页面，再执行模型代码，生成最张的html，返回给浏览器，这个过程将页面与数据集成到了一起，扩展性差
- 改进方案：通过ajax的方式获取数据，通过dom操作将数据呈现到界面上
- 推荐使用框架的ajax相关方法，不要使用XMLHttpRequest对象，因为操作麻烦且不容易查错
- jquery框架中提供了.ajax、


- .get、$.post方法，用于进行异步交互
- 由于csrf的约束，推荐使用$.get
- 示例：实现省市区的选择
- 最终实现效果如图： 
  ![这里写图片描述](https://img-blog.csdn.net/20171127234413619?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDUwNTA1OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### 引入js文件

- js文件属于静态文件，创建目录结构如图： 
  ![这里写图片描述](https://img-blog.csdn.net/20171127234432894?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDUwNTA1OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

#### 修改settings.py关于静态文件的设置

```
STATIC_URL = '/static/'
STATICFILES_DIRS = [
    os.path.join(BASE_DIR, 'static'),
]1234
```

#### 在models.py中定义模型

```
class AreaInfo(models.Model):
    aid = models.IntegerField(primary_key=True)
    atitle = models.CharField(max_length=20)
    aPArea = models.ForeignKey('AreaInfo', null=True)1234
```

#### 生成迁移

```
python manage.py makemigrations
python manage.py migrate12
```

#### 通过workbench向表中填充示例数据

- 参见“省市区.sql”
- 注意将表的名称完成替换

#### 在views.py中编写视图

- index用于展示页面
- getArea1用于返回省级数据
- getArea2用于根据省、市编号返回市、区信息，格式都为字典对象

```
from django.shortcuts import render
from django.http import JsonResponse
from models import AreaInfo

def index(request):
    return render(request, 'ct1/index.html')

def getArea1(request):
    list = AreaInfo.objects.filter(aPArea__isnull=True)
    list2 = []
    for a in list:
        list2.append([a.aid, a.atitle])
    return JsonResponse({'data': list2})

def getArea2(request, pid):
    list = AreaInfo.objects.filter(aPArea_id=pid)
    list2 = []
    for a in list:
        list2.append({'id': a.aid, 'title': a.atitle})
    return JsonResponse({'data': list2})1234567891011121314151617181920
```

#### 在urls.py中配置urlconf

```
from django.conf.urls import url
from . import views

urlpatterns = [
    url(r'^$', views.index),
    url(r'^area1/$', views.getArea1),
    url(r'^([0-9]+)/$', views.getArea2),
]12345678
```

#### 主urls.py中包含此应用的url

```
from django.conf.urls import include, url
from django.contrib import admin

urlpatterns = [
    url(r'^', include('ct1.urls', namespace='ct1')),
    url(r'^admin/', include(admin.site.urls)),
]1234567
```

#### 定义模板index.html

- 在项目中的目录结构如图： 
  ![这里写图片描述](https://img-blog.csdn.net/20171127234452455?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvdTAxMDUwNTA1OQ==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)
- 修改settings.py的TEMPLATES项，设置DIRS值

```
'DIRS': [os.path.join(BASE_DIR, 'templates')],1
```

- 定义模板文件：包含三个select标签，分别存放省市区的信息

```
<!DOCTYPE html>
<html>
<head>
    <title>省市区列表</title>
</head>
<body>
<select id="pro">
    <option value="">请选择省</option>
</select>
<select id="city">
    <option value="">请选择市</option>
</select>
<select id="dis">
    <option value="">请选择区县</option>
</select>
</body>
</html>1234567891011121314151617
```

#### 在模板中引入jquery文件

```
<script type="text/javascript" src="static/ct1/js/jquery-1.12.4.min.js"></script>1
```

#### 编写js代码

- 绑定change事件
- 发出异步请求
- 使用dom添加元素

```
 <script type="text/javascript">
        $(function(){

            $.get('area1/',function(dic) {
                pro=$('#pro')
                $.each(dic.data,function(index,item){
                    pro.append('<option value='+item[0]+'>'+item[1]+'</option>');
                })
            });

            $('#pro').change(function(){
                $.post($(this).val()+'/',function(dic){
                    city=$('#city');
                    city.empty().append('<option value="">请选择市</option>');
                    $.each(dic.data,function(index,item){
                        city.append('<option value='+item.id+'>'+item.title+'</option>');
                    })
                });
            });

            $('#city').change(function(){
                $.post($(this).val()+'/',function(dic){
                    dis=$('#dis');
                    dis.empty().append('<option value="">请选择区县</option>');
                    $.each(dic.data,function(index,item){
                        dis.append('<option value='+item.id+'>'+item.title+'</option>');
                    })
                })
            });

        });
    </script>
```