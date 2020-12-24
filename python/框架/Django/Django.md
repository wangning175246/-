## 请求和响应

#### request

* GET

  `request.GET.get("page","1")` #获取url传递过来的参数，如果为空则默认为1

  `request.GET.list("key") `如果传递参数时候前端传递过来的参数，一个key对应多个value使用获取

* POST

* 其他请求

  我们可以把request.body中的数据转换成成QueryDict类型，需要满足的条件是

  * 请求头要要求

    Content-Type: application/x-www-form-urlencoded

    如果请求头中有 Content-Type: application/x-www-form-urlencoded django才能把request.body中的数据转换到request.post中才能有值

  * 数据格式要求 ，from 表单提交的就是这种格式

    name=alex&age=18&gender=男

  ```python
  def Login(request):
      if request.method == "DELETE":
          print(request.body)
          data=QueryDict(request.body)
          print(data)
          return HttpResponse("删除数据")
      return HttpResponse("其他请求")
  ```


#### Response

* JsonResponse

```python
from django.http import JsonResponse,HttpResponse
def index(request):
    # return JsonResponse("index",safe=False) #如果不是字典需要加上safe=False，否则不用加
    return JsonResponse({"name":"wallace"})
```

#### QueryDict

在django中我们可以直接使用queryDict这种数据类型,他是django中类似字典的一个数据类型。默认是不可以修改的。

request.GET request.POST 返回给我们的类型就是queryDict类型。在一个正常的请求/响应中他是不可变，如果向改变他的值需要.copy，而后去修改

可以把name=alex&age=18&gender=男 这种格式的字符串直接转换成QueryDict格式

```python
from django.http import QueryDict
a=QueryDict("name=alex&age=18&gender=男")
print(a) # a 就是queryDict类型了 
# a["name"]="wallace" 是不能直接修改QueryDict方法的如果要修改需要，如下操作
c=a.copy()
c["name"]="wallace"
print(c["name"])
# <QueryDict: {'name': ['alex'], 'age': ['18'], 'gender': ['男']}>
```

而后我们可以使用QuertDict提供的方法，进行操作，他的多数方法和字典的方法是相同的。

```python
QueryDict.get("ket") #获取指定key的值，
QueryDict.items()
QueryDict.value()
QueryDict.copy() # 复制修改QueryDict对象。
QueryDict.urlencode()  # 把QuertDict类型数据转换成url
QueryDict.dict() #把自身转换成成字典，转换成字典后有可能会丢失数据，如果一个key有多个值，会丢失数据
QueryDict.setlistdefault()
...
```

#### jsonResponse

直接返回json数据，safe=False 如果 safe=False表示序列化的内容是一个数组

## 路由配置

django启动的时候通过manager.py加载setting.py配置文件，路由的配置主要是在setting.py 中 `ROOT_URLCONF = 'django_example.urls'`  定义是我们顶层的路由文件的路径.可以修改成其他的路径。

url文件中urlpatterns是一个列表，每一个元素都是一个url实例，里面定义是url(模式)路径和视图函数对应关系。url路径中可以包含正则表达式，url路径中包含的正则式在第一次访问时候被编译的。

当访问的路径匹配到了定义url模式会调用后面的视图函数或者as_views的返回结果(类视图)。

url模式中如果定义了正则进行组模式匹配，会把匹配到的内容传递到对应的函数视图或类视图中。因此函数视图和类视图定义的时候必须是可接收参数的。

如果没有匹配到正则表达式，或者执行视图函数的过程中出现了错误，django会调用一个适当错误处理视图。

#### url.py文件定义

url.py 也是urlconf模块，里面必须定义urlpatterns，他是一个url或者path实例类型的列表，

```python
from django.contrib import admin
from django.urls import path
from app01.views import index,querydict,Login

urlpatterns = [
    path('admin/', admin.site.urls),
    path('app01/', index),
    path('qd/', querydict),
    path('login/', Login),
]
```

###### path，url函数

定义我们的url和视图函数的对应关系

path(regex,view,kwargs=None,name=None)

* regex 是一个url路径可以包含正则表达 
* kwargs 传递参数用
* name url名称，可以django中内部通过这个name获取到url路径，存放是和url路径的对应关系 template中会用到，

###### include函数

引用其他app中urlconf模块(也就是url文件)，或做url路径分级include(urlconf_module,namespace=None,app_name=None)
include(pattern_list)
include((pattern_list,instance_namespace ),namespace=None)
include((pattern_list,app_namespace ,instance_namespace ))

* urlconf_module 其他模块中的
* namespace url命名空间,大型项目会用，一般不用定义
* app_namespace app的命名空间 ,大型项目会用，一般不用定义
* pattern_list 可迭代的django.conf.urls.url()实例，和urlpattern一样
* instance_namespace 实例的命名空间, 大型项目会用，一般不用定义

pattern_list 做url路由分级

```python
urlpatterns = [
    path('admin/', admin.site.urls),
    path('app01/', index),
    path('qd/', querydict),
    path('login/', Login),
    path("user/",include(
        [
           path("login",add)
           path("logout",delete)
           path("chanagepass",edit)
        ]
    ))
]
```

###### url 参数

对url路径进行正则分组匹配，正则分组可以是匿名组也可以是名组，对应的参数分别是位置参数和关键字参数，而后把匹配的内容传递到视图函数中

也可以在path或者url函数中通过**kwargs传递参数。

url.py

```python
from django.contrib import admin
from django.urls import path,include,re_path
from app01.views import index,querydict,Login,article,kwacrticle

urlpatterns = [
    re_path('^articles1/2013/$', article,{"foo":"bor"}),
    re_path('^articles2/(2013)/$', article),
    re_path('^articles3/([0-9]{4})/$', article),
    re_path('^articles4/([0-9]{4})/([0-9]{2})/$', article),
    re_path('^articles5/([0-9]{4})/([0-9]{2})/$', kwacrticle),
    re_path('^articles6/(?P<year>[0-9]{4})/(?P<mount>[0-9]{2})/$', article),
    re_path('^articles7/(?P<year>[0-9]{4})/(?P<mount>[0-9]{2})/$', kwacrticle),
]
```

views.py

```python
def article(request,*args,**kwargs):
    # 接收匿名参数。是一个元组
    print(args)
    # 接收命名参数，是一个字典
    print(kwargs)
    return HttpResponse("wallace")

def kwacrticle(request,year,mount):
    #直接接收传递过来的值复制给year和mount，
    #如果是命名参数参数的名称必须和前面怎知分组的名称一致。否则会报错。
    print(year,mount)
    return HttpResponse("wallace")
```

###### 向视图中传递参数的几种方法

* 请求的url中通过?name="wallace"的方式
* 正则匹配请求路径中的内容传递参数
* 定义路由的时候通过path或者url函数的**kwargs传递参数到视图函数中

## 静态文件处理

![image-20200303104939681](images\Django\image-20200303104939681.png)

## 视图

函数视图

类视图

​	listview 查看代码 app01/views1.py

## 中间键，请求过程

http://projectsedu.com/archives/

## 分页

django的分页主要是靠 Paginator实现的

必须要传递要传递的两个参数

* `object_list` query_set对象
* `per_page` 每页显示多少条数据

```python
from  django.core.paginator import Paginator,EmptyPage,PageNotAnInteger
def dg_paging(request):
    current_pag=request.GET.get("p")
    "current_page获取的是当前的页数"
    Data=User.objects.all()[start:end].values("username","email")
    paginator=Paginator(Data,10)
    "paginator.num_pages 这个总的页数"
    "paginator.page_range 这个是页面的索引范围"
    "paginator.page 这个是page对象"
    "传入得是总的数据和一页显示的个数"
	try:
		posts=paginator.page(current_pag)
        "如果我们传入的current_pag不是一个整数，则会出现PageNotAnInteger的异常"
        "如果我们传入的current_pag超过了最大页数，则会出现EmtyPage这个异常"
        "返回的这个posts对象中包含的信息有"
        "posts.number当前页的页码"
        "posts.object_list 当前页的数据"
        "posts.next_page_number()返回的是下一页的页数"
        "posts.previous_page_number()返回的是上一页的页数"
        "posts.has_next()是否是上一页"\
        "posts.has_previous()是否有上一页"
        "posts.paginator这个返回的paginator对象"
        "posts.star_index 返回当前页的第一个对象"
        "posts.end_index 返回当前页的最后一个对象，"
	except PageNotAnInteger:
		posts=paginator.page(1)
	except EmptyPage:
		posts=paginator.page(paginator.num_pages)
	print(posts)
	return render(request,"dg_paging.html",{"page":posts})

```

## Seesion

```python
class Login(ViewSet):
	def create(self,request,*args,**kwargs):
        request.session["useer_id"]=user.id
        request.session.set_expiry(60)
        return Response("wallace")
```

设置session后，django会把session存储到数据库的django_session表中，而后在返回到用户的数据中请求头中会带有 set-Cookie: session:sesssion-Key

session_data 存储的是加密后的数据，

![image-20200808175931066](images\Django\image-20200808175931066.png)

下次这个用户发起请求的时候回自动带上这个这个cookie

```http
Cookie: sessionid=deah1f3eyvk45z1as8heo3469bjckgg9
```

## django的日志

django使用python内建的loggin模块进行打印日志，python的loggin配置由四部分组成

* 记录器 Logger

  * Logger为日志系统的入口，每个logger命名都是bucket写入需要处理的消息

  * 每个logger都有一个日志级别。日志级别表示该logger将要处理的消息的严重性，python定义以下几种日志级别

    NOTSET 无级别

    DEBUG 用于调试目的的底层系统信息

    INFO 普通的系统信息

    WARNING 表示出现一个较小的问题

    ERROR 表示出现的一个较大的问题

    CRITICAL 表示出现的一个致命的问题

  * 写入logger的每条信息都是一条日志，每条日志也具一个日志级别，它对应的消息的严重性，每个日志记录还可以包含描述正在打印的事件的元信息

  * 当一条消息传递给logger时，消息的日志级别将与logger的日志级别进行比较，如果消息的日志级别大于logger的日志级别，该消息将会往下继续处理，如果小于该消息将会被忽略。

  * logger 一旦决定消息需要处理，它将传递消息给一个Handler

* 处理程序 Handler

  * Handler决定如何去处理logger中的每条消息，它表示一个特定的日志行为，例如将消息写到标准输出，写道文件中或者网络socket,其他各种处理器
  * 与logger一样，handler也有一个日志级别，如果消息的日志级别小于handler的级别，handler将忽略该消息
  * Logger可以有多个handler,而每个handler可以有不同的日志级别，利用这种方式，可以根据消息的重要性提供不同形式的处理

* 过滤器 Filter

  * filter用于对logger传递给Handler的日志记录进行额外的控制*
  * 默认情况下，满足日志级别的任何消息都将被处理，通过安装一个filter，你可以对日志处理添加额外的条件，例如，你可以安装一个filter，只是允许处理特定源的ERROR消息，
  * Filters还可以用于修改将要处理的日志记录的优先级，例如如果日志记录满足特定的条件，你可以编写一个filter将日志从ERROR降为WARNING
  * Filters可以安装在logger上或者Handler上，多个filter可以串联起来实现多filter行为。

* 格式化 Fomatter

  日志记录需要转换成文本，Fotmat表示文本的格式，Fomatter通常由包含日志记录属性的Python格式字符串组成，你可以编写自定义的fomatter来实现自己的格式

  日志格式format

  | 格式           | 描述                                           |
  | :------------- | ---------------------------------------------- |
  | %(name)s       | 记录器的名称。                                 |
  | %(levelno)s    | 数字形式的日志记录的级别                       |
  | %(levelname)s  | 日志记录级别的文本名称                         |
  | %(filename)s   | 执行日志记录调用的源文件的文件名称             |
  | %(pathname)s   | 执行日志记录调用的源文件的路径名称             |
  | %(funcName)s   | 执行日志记录调用的函数名称                     |
  | %(module)s     | 执行日志记录调用的模块名称                     |
  | %(lineno)s     | 执行日志记录调用的行号                         |
  | %(created)s    | 执行日志记录的时间                             |
  | %(asctime)     | 日期时间 这可以通过datefmt进行自定义日期格式化 |
  | %(msecs)s      | 毫秒部分                                       |
  | %(thread)d     | 线程ID                                         |
  | %(threadName)s | 线程名称                                       |
  | %(process)d    | 进程ID                                         |
  | %(message)s    | 记录的消息                                     |

#### 自定义的Logger配置

Logger对应的值时字典，每一个键对应的都是logger的名字，每一个值又是一个字段，描述了如何配置对应的Logger实例

​	\-level logger的级别
​	-propagate logger的传播设置，如果日志由多级，我自己的日志我自己处理，不需要上级处理
​	-filters logger的filter的标识符的列表
​	-handlers logger的handler的标识符的列表 ，只要高于level定义的日志级别，才会交给handlers处理。

###### setting.py 添加配置

```python
LOGGING={
    'version':1,
    'disable_existing_loggers':False,
    # 格式化定义
    'formatters':{
        # 自定义格式化
        'default':{
            'format':'%(asctime)s %(levelname)s %(pathname)s[%(lineno)s] %(message)s',
            'datefmt':'%Y-%m-%d %H:%M:%S'
        }
    },
    # 定义处理器
    'handlers':{
        # 输出到标准输出
        'console':{
            'level':'DEBUG',
            'class':'logging.StreamHandler',
            'formatter':'default'
        },
        # 输入到文件
        'file':{
            'level':'DEBUG',
            'class':'logging.FileHandler',
            'filename':'debug.log',
            'formatter':'default'
        }
    },
    #  定义loggers和bucket。
    'loggers':{
        'reboot':{
            'level':'DEBUG',
            'handlers':['console','file'],
            'propagate':False
        }
    }
}
```

###### views,py使用

```python
from django.http import JsonResponse
from django.views import View
from django.contrib.auth.models import User
import  logging
# 使用的logger的bucket，如指定的backer，不存在会交给 顶级的root去处理，如果logger中reboot没有定义，则会交给LOGGING中定义的root去处理
log=logging.getLogger("reboot")
class get_info(View):
    def get(self,request,*args,**kwargs):
        log.debug("第一条日志")
        id=kwargs.get("id")
        data=User.objects.filter(id=id).values("id","username","email")
        return JsonResponse(list(data),safe=False)
```



#### django 内置logger

* django 获取所有的日志
* django.server  获取请求相关的日志
* django.request 处理与请求相关的日志，5xx响应报出error日志，4xx报出WARNING日志
* django.db.backends 处理与数据库之间交互的日志
* django.security.* 处理与安全相关的日志
* django.db.backends.schemea 处理数据库迁移时的日志

日志级别 django 时父级，django.db.backends.schemea ，db就是django的子级，backends是db的子级，schemea是backends的子级，是以点进行区分级别

###### 配置使用django日志

配置了django的logger，django会去自己去调用。

```python
LOGGING={
    'version':1,
    'disable_existing_loggers':False,
    # 格式化定义
    'formatters':{
        'default':{
            'format':'%(asctime)s %(levelname)s %(pathname)s[%(lineno)s] %(message)s',
            'datefmt':'%Y-%m-%d %H:%M:%S'
        },
        'server': {
            'format': '%(asctime)s %(levelname)s %(message)s',
            'datefmt': '%Y-%m-%d %H:%M:%S'
        }
    },
    # 定义处理器
    'handlers':{
        'console':{
            'level':'DEBUG',
            'class':'logging.StreamHandler',
            'formatter':'default'
        },
        'file':{
            'level':'DEBUG',
            'class':'logging.FileHandler',
            'filename':'debug.log',
            'formatter':'default'
        },
        'server': {
            'level': 'DEBUG',
            'class': 'logging.FileHandler',
            'filename': 'server.log',
            'formatter': 'server'
        }
    },
    #  定义loggers和bucket。
    'loggers':{
        # 自定义的日志，必须我们自己调用的
        'reboot':{
            'level':'DEBUG',
            'handlers':['console','file'],
            'propagate':False
        },
        # django的内部所有日志，都会调用这个进行处理，django自己调用的
        'django':{
            'level': 'DEBUG',
            'handlers': ['console', 'file'],
            'propagate':True
        },
        # django.server类的日志单独处理
        'django.server':{
            'level': 'DEBUG',
            'handlers': ['console', 'server'],
            # 表示django.server的日志就我自己处理不会交给它的父级去处理，
            # 如果propagate 设置成true，表示自己处理之后还会在交给上级去处理，也就是django去处理
            # 这时候就会debug.log中也会有请求相关的日志。
            # 设置成False之后表示，自己处理之后不会交给上级处理
            'propagate': False
        }
        # ... 可以把django内部的日志进行分离
    }
}
```

如果配置了以上，则如果时请求相关的日志会交给loggers中的django.server取处理，django内部所有日志输出会调用loggers中django去处理

在django的项目根目录下会产生2个文件，server.log和debug.log

#### LOGGING中root配置

root顶层的logger，处理所有

* 处理未定义的logger，

  log=logging.getLogger("reboot"),如果reboot没有定义则会交给root去处理

  log.dubug("xxxxx") 这个会交给root去处理

* 所有loggers的父级都是root，顶层的日志处理，loggers中所有 propagate True的最终都会交给root去处理。

  propagate True 子级别的日志交给父级，父级别交给root，如果父级是false就不会交给root了

```python
LOGGING={
    'version':1,
    'disable_existing_loggers':False,
    # 格式化定义
    'formatters':{
        'default':{
            'format':'%(asctime)s %(levelname)s %(pathname)s[%(lineno)s] %(message)s',
            'datefmt':'%Y-%m-%d %H:%M:%S'
        },
        'server': {
            'format': '%(asctime)s %(levelname)s %(message)s',
            'datefmt': '%Y-%m-%d %H:%M:%S'
        }
    },
    # 定义处理器
    'handlers':{
        'console':{
            'level':'DEBUG',
            'class':'logging.StreamHandler',
            'formatter':'default'
        },
        'file':{
            'level':'DEBUG',
            'class':'logging.FileHandler',
            'filename':'debug.log',
            'formatter':'default'
        },
        'server': {
            'level': 'DEBUG',
            'class': 'logging.FileHandler',
            'filename': 'server.log',
            'formatter': 'server'
        },
        'root': {
            'level': 'DEBUG',
            'class': 'logging.FileHandler',
            'filename': 'root.log',
            'formatter': 'server'
        }
    },
    #  定义loggers和bucket。
    'loggers':{
        'reboot':{
            'level':'DEBUG',
            'handlers':['console','file'],
            'propagate':False
        },
        # django的内部所有日志
        'django':{
            'level': 'DEBUG',
            'handlers': ['console', 'file'],
            'propagate':True
        },
        # django.server类的日志单独处理
        'django.server':{
            'level': 'DEBUG',
            'handlers': ['console', 'server'],
            'propagate': False
        }
    },
    'root':{
        'level': 'DEBUG',
        'handlers': ['root']
    }
}
```

#### handlers

可以其找一些有哪些handlers

###### FileHandler

```python
LOGGING={
   	...
    'handlers':{
        'server': {
            # 表示这个日志处理handlers处理的日志级别，如果日志级别比这个小则不处理
            'level': 'DEBUG',
            # 这个是具体的处理日志类，
            'class': 'logging.FileHandler',
            # filename 是传递给上面日志处理类的参数
            'filename': 'server.log',
            # 这个是使用那个formatter进行日志格式化。
            'formatter': 'server'
        }
    }
    ....
}
```

TimeRotatingFileHandler

按照时间对日志进行切割,接收的参数

​	when 
​		h 小时 
​		S 秒 
​		D 天 
​		...
​	interval 表示多次时间一个周期，1表示1天切割一次

```python
LOGGING={
   	...
    'handlers':{
        'server': {
            # 表示这个日志处理handlers处理的日志级别，如果日志级别比这个小则不处理
            'level': 'DEBUG',
            # 这个是具体的处理日志类，
            'class': 'logging.handlers.TimeRotatingFileHandler',
            # filename 是传递给上面日志处理类的参数
            'filename': 'server.log',
            'when':'D',
            'interval':1,
            # 这个是使用那个formatter进行日志格式化。
            'formatter': 'server'
        }
    }
    ....
}
```



## model

#### model的定义

每个model实际是一个类，但是它继承了我们的`django.db.models.Model`

```python
class User(models.Model):
    字段名称=models.字段类型(参数)
    class Meta():
        ...
    def __str__(self):
        ....
```

##### 字段类型

​	CharField
​	BooleanField
​	IntergerField
​	DataField/DataTimeField
​	EmailField
​	TextField
​	TimeField
.	...

其他查看官方文档

##### 自增主键字段

* 默认情况下Django会给每个模型添加下面的字段

  id=models.AutoField(primary_key=True)

* 如果Django看到的你显示的设置了Field.primary_key.就不会自动增加id列

* 每个模型只能有一个字段指定primary_key=True，每个表只能有一个主键

##### 字段的自述名，

* 每个字段类型都接收一个可选的位置参数，字段的自述名，用来描述字段是做什么用的，不会同步到数据库中的，Djngao将根据字段的属性名称自动创建自述名--将属性名称的下划线替换称空格
* Foreinkey,ManyToManyField和OneToOneField这三个可以使用verbose_name指定自述名，

`first_name=models.CharField("第一个名称",max_length=20)`

##### 字段选项

​	每个字段都有一些特有的参数，例如CharField(和它的派生类)需要max_length参数来指定VARCHAR数据库字段的大小。

* max_length 设置这个字段的最大长度

* null=True 这个字段是否可以为空,也就是如果没有提供，则默认为空。

* blank 

  主要是做字段表单验证用的 

  如果True，则该字段可以不填

  如果未False，则该字段必须有值，可以是空字符串，但是必须有，不能不提供。

  字段没有加blank的时候,默认为False

  <img src="images\Django\blank_False.png" alt="blank_True" style="zoom: 67%;" />

  加上blank=True的时候

  <img src="images\Django\blank_true.png" alt="Usemame:etwork Timeline " style="zoom:67%;" />

* default 默认值,

  可以是一个值或者调用对象，是django级别的。

  不会同步到数据库，和sql语句中的中default不同

* primary_key 主键

* db_column 指定列名，在数据库中显示的列的名称

* db_index=True是否是索引

* unique 创建唯一索引

* primary 是否是主键

* unique_for_data 如果字段是时间，只对时间的天做索引

* unique_for_month 如果字段是时间 只对月份做索引

* unique_for_year 如果字段是时间，只对年份做索引

* auto_now 创建时自动生成时间

* choices 预先顶一个元祖，这个地段的值只能是元祖的某一个       

  ```python
  class UserGroup(models.Model):
      user_type_choices=((1,'普通用户'),(2,'超级用户'),(3,"特权用户"))
      username=models.CharField(max_length=40)
  	user_type=models.IntegerField(choices=user_type_choices,default=1) 
  ```

  表中的数据

  <img src="images\Django\choices.png" alt="表格" style="zoom:67%;" />

  view中进行查询的时候我们可以获取

  ```python
  class Serialize(APIView):
      def get(self,request,*args,**kwargs):
          roles=models.name.objects.all()
          ret=roles.first()  # 取出第一条记录
          print(ret.username) # 输出的结果结果是wang
          print(ret.age)   # 输出的结果是 10 
          # 这个输出的结果是 普通用户 直接获取的是元祖中对于的内容
          print(ret.get_user_type_display()) 
          return HttpResponse(ret)
  ```

##### 模型meta选项

使用内部的class Meta定义模型的元数据，这个里面定义的是一些表的属性,元数据，

模型元数据是任何不是字段的数据，比如排序选项，数据库表明，模型的表的联合索引等属性，所有属性不是必须的例如：

```python
class User(models.Model):
    ...
    class Meta():
        index_together=[('username','pwd'),] 创建索引
        unique_together=['字段名称1','字段名称2'...]   这个是对表中的字段建立联合唯一索引的。
        order=["username"]
```

* db_table 定义数据库的表明的,一般是小写，如果不指定默认是 `app_name+"_"+module_name`

* order 这个字段是告诉Django模型对象返回的结果是按照那个字段进行排序的

  它是一个字符串的列表或者元组，每个字符串是以字段名，前面带有可选的“-”前缀表示倒序，前面没有“-”表示正序，使用？来表示随机排序。多个是先按照第一个排序，而后第二个进行排序，

* app_label 这个选择只在一种情况下使用，就是你的模型类不在默认的应用程序包下的models.py文件中，这时候你需要指定你的这个模型类是那个应用程序的

* managed 如果为True，模型同步数据库是由django帮我们的管理的，我们也可以自己管理需要设置为False，我们一般不需要自己管理，

##### 模型的str选项

这个方法的作用有两个，

* 在django的admin中在页面上显示的默认就是我们返回的这个字段，如果没有定义返回的是一个数据对象models.py 定义

  ```python
  class User(models.Model):
      BookeName=models.CharField(max_length=20)
      author=models.CharField(max_length=40)
      def __str__(self):
          return self.BookeName
  ```

  如果没有定义`__str__` 

![str_exit](images\Django\str_esxit.png)

​		如果定义了`__str__`

![image-20191231223547629](images\Django\str.png)

* 2、在我使用在views中使用mode操作数据的库的时候，使用get方法，或者从filter返回的Queryset对象取出的数据对象，而如果定义的str的，则返回的是str中指定的返回的字段

  如果没有定义了`__str__`

  ```python
  obj1=Book.objects.filter(id__gt=1)[0]
  print(obj1)  # 这个的返回结果是一个Book object (2) 
  ```

  如果定义了`__str__`

  ```python
  obj1=Book.objects.filter(id__gt=1)[0]
  print(obj1) # 返回的查询到的第一条的数据的Booname字段的值。
  ```

#### 同步数据库相关的命令

python manage.py showmigrations 查看要迁移的数据库

python manage.py  sqlmigrate session  01 查看迁移具体执行的sql语句

​	session 是应该名称
​	01 是序列号 

python manage.py makemigrations 生成迁移文件，

python manage.py migrate   [appname] [--fake] 进行数据库迁移

* 如果指定了appname 则只会同步appname下的数据变更。

* --fake 如果我们把`django_migrations`表中的同步变更删除，再次执行migrate   会报错提示表已经存在，我们可以加上这参数，表示只是同步migrations信息，不对表做任何变更。

  如果我们删除了django库中`auth_permisstion`，`auth_permisstion`，则执行以下，会把数据恢复到最新

  python manage.py migrate --fake

python manage.py dbshell 可以直接连接进入数据库

#### django默认数据库表介绍

`django_migrations` 记录的我们的同步记录

`django_content_type` 定义所有的modle，每个modle有一个id

`auth_permisstion` 权限表，每一个modle由三个权限，多modle有哪些权限。通过id和`django_content_type`表进行关联

#### 同步成功的前提条件

每次数据库同步，只要下面的数据不一致，则同步会失败，

执行`makemigrations` 就是让`models`和`migations`保持同步，

执行`migrate`   是让`mysql table `、`django_migrations` 和migrations保持一致。

执行 `migrate --fake` 是可以同步django原始的表，不包含我们自己的表。并在`django_migrations`生成同步记录，

models 
migrations 
mysql table
django_migrations

#### 创建数据对象

* `User.object.creat(name="xxx")`或者`User.object.creat(**dict)`这种方式创建

  使用create这种方式不用.save，不会数据做验证，如创建django默认的用户信息使用这种方式密码时明文保存。

  ```python
  from django.contrib.auth.models import User
  User.objects.create(username="wang",email="wang@163.com",password="1234")
  dict={"username":"wallacce","email":"wang@163.com","password":"123456"}
  User.objects.create(**dict)
  ```

* `us=User();us.name="xxx"`或者`obj=model.UserInfo(name="xxx")`

  使用这种方式创建需要执行.save放进行保存，保存的时候会做数据验证。

  ```python
  us=User()
  us.name="wang"
  us.save()
  obj=model.UserInfo(username='wallace',passwd='123')
  obj.save()
  dict={"username":"wallacce","email":"wang@163.com","password":"123456"}
  obj=model.UserInfo(**dict)
  obj.save()
  ```

#### 查询数据对象

###### 查询语句

* exclude 排除指定的内容

  ```python
  c=User.objects.filter(id__gt=10)
  e.exclude(id=15)
  ```

* filter() 查询指定的内容 返回的是QuerSet对象

* 获取单一对象get() 返回的是具体的一个数据对象

  * filter()始终返回一个查询集，即使只有一个对象满足查询条件，这种情况下查询集将只包含一个元素，
  * 如果你知道一个对象满足你的查询，你可以使用管理器的get(),它直接返回该对象，`c=User.objects.get(id=10)`
  * 可以对get使用任何查询表达式和filter一样，
  * 使用get()和filter()的切片的区别，如果没有结果满足，get()将引发一个DoesExist异常，filter回返回空
  * 如果有多条记录满足get的查询条件，Django也报错，这中情况将引发MultipleObjectReturned

###### 字段查询

​	`id=1`
​	`id__gt=1` id大于1的
​	`id__lt=1` id小于1的
​	`id__gte=1` 大于等于1
​	`id__lte=1` 小于等于1
​	`id__in=[1,3,4,7,10]`  查找id在指定的列表的
​	`id__range=[1,5]`  查找id在1-5这个范围的 
​	`title_exact`="xxx" 精确匹配 
​	`title_iexact`="xxx" 大小写不敏感精确匹配
​	`title__containg="P"` 过滤出字段中包含P的行 区分大小写
​	`title__icontaing="P"` 过滤出字段中包含P的行 不区分大小写
​	`title__startswith="P"`  过滤出title字段的值以P开头的行。
​	`title__istartswith="P"` 以 指定的字符串开头
​	`title__endswith="P"` 过滤出title字段的值以P结尾的行
​	`title__iendswith="P"`
​	`time__year/month/day/week_day` 对日期和时间字段匹配 年/月/日/星期
​	`pk=14`  提供了一个快速查询主键的方式，pk表示主键，会自动找表的主键。

###### 对QuerySet对象中的数据进行操作的

a=UserInfor.objects.filter(pk=14)

​	`a.order_by("-字段")` 对指定的字段进行逆序排序
​	`a.order_by("+字段")` 对指定的字段进行正序排序，默认
​	`a.order_by("?")` 随机排序
​	`a.values ('指定要返回的字段')` 返回的是一个可迭代的字典序列

###### values 

User.objects.value("字段名称"，…) 这个返回的也是Queryset对象，他是取User中的所有指定字段的数据数据
返回一个ValueQuerySet---QuerySet的子集
迭代QuerySet返回时模型实例。
迭代ValueQuerySet返回字典而不是模型实例，每个字典表示一个对象，键时模型的属性名称
values()接收可选参数*fields，它指定SELECT应该限制返回哪些字段，如果没有执行则返回所有字段。

不指定参数

```python
c=User.objects.values()
<QuerySet [{'id': 1, 'password': 'pbkdf2_sha256$100000$4uJ8LjZC6OH0$kZmbSwGM6m2Hr0mKx2ognQCKtyBcdZhH8rjh28u0bZ8=', 'last
_login': datetime.datetime(2019, 12, 28, 2, 57, 10, 958940, tzinfo=<UTC>), 'is_superuser': False, 'username': 'wallace',
 'first_name': '', 'last_name': '', 'email': 'wallace@wallace.com', 'is_staff': False, 'is_active': True, 'date_joined':
 datetime.datetime(2019, 12, 28, 2, 27, 21, 502541, tzinfo=<UTC>)} ....>
```

指定参数

```python
c=User.objects.values("username")
<QuerySet [{'username': 'wallace'}, {'username': 'wallace_0'}, {'username': 'wallace_1'},
...>
```

###### 获取将要执行的sql

```python
c=User.objects.filter(id__gt=10)
print(c.query)
```

###### 限制查询集

可以对filter()返回的结果进行切片操作。它体现在sql语句时就是limit子句

```python
c=User.objects.filter(id__gt=10)[10:.20]
print(c.query)
SELECT "auth_user"."id", "auth_user"."password", "auth_user"."last_login", "auth_user"."is_superuser", "auth_user"."user
name", "auth_user"."first_name", "auth_user"."last_name", "auth_user"."email", "auth_user"."is_staff", "auth_user"."is_a
ctive", "auth_user"."date_joined" FROM "auth_user" WHERE "auth_user"."id" > 100 LIMIT 10 OFFSET 10
```



#### 序列化数据对象

```python
user=models.Username.objects.all()
userlist=serializers.serialize("json",user) #这个只能对QuerySet的对象进行序列化
```

#### 一对一

一个表中一个字段和另一个表中的主键关联，关联的键是unqi(不可重复)的，

一个表的建议的字段数为60个，如果大于60个，则建议拆分，这时候就会用到

```python
from django.db import models
from django.contrib.auth.models import User
class Userprofile(models.Model):
    name=models.CharField("中文名称",max_length=20)
    user = models.OneToOneField(User,null=True,on_delete=models.SET_NULL
```

查询和1对多的正向查询一样

```python
from app01.models import Userprofile
from django.contrib.auth.models import User
# 添加数据
up.user=User.objects.get(username="wallace")
up.name="阿宁"
up.user=User.objects.get(name="wallace")
up.save()
# 查询数据
```

#### 一对多，多对一

django使用ddjango.db.models.Foreignkey定义多对一关系

ForeigenKey需要一个位置参数来指定本Model关联的Model.ForeignKey关联的Model是"一"，ForeignKey所在的Model是"多"

比如汽车和制造商的例子，一辆汽车只属于一个制造商，但是一个制造商可以制造多辆汽车。如下Model来表示

```python
class Manufacturer(models.Model):
    name = models.CharField(max_length=30)

class Car(models.Model):
    name=models.CharField(max_length=20)
  manufacturer=models.ForeignKey(null=True,to="Manufacturer",on_delete=models.SET_NULL)

```

###### 正向查询 (ForeignKey所在的模型查询关联的模型)

```python
car=Car.obejct.get(pk=2)
car=manufacture.all() # 返回一条Manufacturer对象。
```

###### 反向查询（ForeignKey指向的模型查询Foreignkey所在的模型）

如果模型有一个Foreignkey,那么该ForeignKey所指向的模型实例可以通过一个管理器返回Foreignkey所在模型的所有实例，默认情况下管理器名称为foo_set ,foo为Foreignkey所在模型的小写名称。

```python
manufacturer=Manufacturer.object.get(pk=1) 
manufacturer.car_set.all() #返回该制造商下的多个car对象
```

#### 添加数据

```python
from app01.models import Manufacturer,Car
Manufacturer.objects.create(name="吉利")
Manufacturer.objects.create(name="宝马")
m = Manufacturer.objects.get(name="吉利")
car=Car()
car.name="远景"
car.manufacturer=m
car.save()
car2=Car(name="帝豪",manufacturer=m)
car2.save()
bm=Manufacturer.objects.filter(name="宝马")
for i in range(20):
	car2=Car(name="车{}".format(i),manufacturer=Manufacturer.objects.filter(name="宝马")[0])
	car2.save()、
bm.car_set.set(Car.objects.filter(id__gt=10)) #会把id>10的车的制造上修改称宝马
bm.car_set.clear() # 会把所有车 制造商是宝马的 都清空，也是这是为空
```

#### 多对多

多对多的时候在那个模型中设置ManyToManyField并不重要，在两个模型中选择一个即可，不要在两个模型中都设置

set 需要传递QuerySet对象
add 需要传递数据对象
remove 需要传递数据对象

操作

```python
from django.contrib.auth.models import User,Group
Group.objects.create(name="sa")
Group.objects.create(name="qa")
user=User.objects.filter(username="wang")
# 给op组里面添加用户
op=Group.objects.get(name="op")
# set的时候需要传递QuerySet对象
op.user_set=user
# add需要传递数据对象
op.user_set.add(User.objects.get(username="wang"))
# 查询用户wang的组
user=User.objects.get(username="wang")
user.groups.all(User.objects.get(username="wallace"))
# 删除组中的用户
qa=Group.objects.get(name="qa")
qa.user_set.remove(user[0])
# 更新wang的组sa
user[0].groups.set(Group.objects.filter(name="sa"))
# 删除用户的组
user[0].groups.remove(Group.objects.get(name="sa"))
# 清空组中的用户
qa.user_set.clear()
```

#### 多关联表 联合查询

可以跨多张表(`表一fk__表二fk__表三name`)

通过个用户表查询具有某个组下的所有用户

`User.objects.filter(groups__name="sa")`

## Django的扩展

#### User对象的扩展

需要修改setting文件

`AUTH_USER_MODEL = "users.User"`

##### 使用一个Proxy模型

##### 使用与User模型的一对一链接

##### 创建一个扩展AbstractBaseUser的自定义的User模型

##### 创建一个扩展AbstractUser的自定义的User模型