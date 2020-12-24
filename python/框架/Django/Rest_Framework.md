## 基础配置按照

restful Api 主要是定义我们的接口怎么写，

#### 安装

```bash
pip install djangorestframework
```

#### 配置

setting.py

```python
INSTALLED_APPS = [
    ...
    'rest_framework',
    '自己的app'
]
```

创建apps目录存放所有的模块。

为了能够让django识别我们的apps目录需要在,如果在setting.py中配置如下

```python
import os
import sys
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
# 因为我们新建的apps目录不再django加载模块所搜索的目录，所以我们要加载这个。
sys.path.insert(0,os.path.join(BASE_DIR,"apps"))
```

## 模型序列化

序列化组件

​	序列化一边把查询集queryset和模型实例instance这样复杂的基于Django自定义的数据类型转换成JSON，等通用的交换语言，

​	反序列化，在验证前端传递过来的数据后，将他们转换成Djngao使用的模型实例等复杂数据类型。

Serializer 最底层最基础的类，为我们提供强大的通用方法来控制响应输出。
ModelSerializer 为创建用于处理模型实例和查询集的序列化程序提供了快捷实现方式，但可自定义程度较低

* 定义模型

  ```python
  from django.db import models
  
  # Create your models here.
  
  class Idc(models.Model):
      name    =models.CharField("机房名称",max_length=32)
      address =models.CharField("机房地址",max_length=200)
      phone   =models.CharField("机房联系电话",max_length=15)
      email   =models.EmailField("机房联系email",max_length=15)
      letter  =models.CharField("idc字母简称",max_length=5)
      # 还需要定义
  
      def __str__(self):
          return  self.name
      class Meta:
          db_table='resource_idc'
  ```

* 定义序列化类

  ```python
  class IdcSerializer(serializers.Serializer):
      """
      IDC 序列化类
      """
      id  =serializers.IntegerField(read_only=True)
      name=serializers.CharField(label="机房名称",help_text="机房的名称",error_messages={"required":"这个字段必须","blank":"这个字段不能为空"})
      address=serializers.CharField(label="机房地址",help_text="具体地址信息")
      phone=serializers.CharField(label="电话",help_text="联系电话")
      email=serializers.EmailField(label="联系邮件",help_text="邮箱地址")
      letter=serializers.CharField(label="简称",help_text="字母简称")
  
      def update(self, instance, validated_data):
          instance.name=validated_data.get("name",instance.name)
          instance.address=validated_data.get("address",instance.address)
          instance.phone=validated_data.get("phone",instance.phone)
          instance.email=validated_data.get("email",instance.email)
          instance.letter=validated_data.get("email",instance.email)
          instance.save()
          return instance
  
      def create(self, validated_data):
          return Idc.objects.create(**validated_data)
  ```

  lable：指定的lable会在Django REST framework的root api中有显示
  help_text：帮助信息，会在Django REST framework 提示字段的帮助信息.和接口文档中提示字段中文解释，也可以在model中定义，效果一样
  error_messages 出错时的提示信息，blank 为空的时的提示信息，required 没有提供字段信息时的报错。

* 序列化

  ```python
  from idcs.models import Idc
  from idcs.serializers import IdcSerializer
  from rest_framework.renderers import JSONRenderer
  # 添加数据
  idc=Idc(name="亦庄机房",address="北京亦庄机房",phone="123456",email="wallace@fy.com",letter="yz")
  idc.save()
  idc=Idc(name="酒仙桥机房",address="北京酒仙桥机房",phone="123456",email="wallace@fy.com",letter="jxq")
  idc.save()
  # 查询数据
  idc=Idc.objects.first()
  # 进行序列化
  serialize=IdcSerializer(idc)
  # 序列化称json返回。
  jr=JSONRenderer()
  json_data=jr.render(serialize.data)
  ```

  重写create方法 ,指向save的时候会调用

  重写update方法，update(self,instance,validated_data) 

  instance 表示根据id 查询到的数据，validated_data 是经过验证过的数据

  JSONParser().parse() 它可以直接传递一个request对象进去，会自己把我们reuqest.data 中的数据进行序列化而后返回

#### 模型序列化

```python
class AccountSerializer(serializers.ModelSerializer):
    class Meta:
        model = Account
        fields = ['id', 'account_name', 'users', 'created']
		# fields = '__all__'
		# exclude = ['users']
```

#### 序列化和反序列 自定义数据

可以参考代码 devops中server的序列化。

`to_internal_value` 序列化的时候执行,接收到的是我们提交的所有数据。

`to_representation` 反序列化的时候执行，用与修改返回的序列化数据，默认返回的数据是instence中的所有数据，如果定义的序列化字段在instence中没有找到会响应的字段会报错。我们可以重写这个方法定义我们向返回的数据。

* 反序列化

  web提供数据——>Request post body——>to_internal_value方法——>我们定义的field——>create、update

* 序列化

  querySet——>序列化类——> 序列化的字段——>to_representation——> 转JSON数据

#### 对关联的数据进行序列化显示

`PrimaryKeyRelatedField`、`to_internal_value` 、`to_representation` 的使用

参考 devops git 01

#### 验证

```python
name=serializers.CharField(error_messages={"required":"这个字段必须","blank":"这个字段不能为空"})
```

error_messages 出错时的提示信息，blank 为空的时的提示信息，required 没有提供字段信息时的报错。

web提供数据——>Request post body——>to_internal_value方法——>我们定义的field(字段定义的验证)——>自定义字段验证(validate_字段名)——>自定义对象验证(validate)——>create、update

##### validate_字段名

​	接受一个参数value，表示当前字段的数据

```python
def validate_title(self, value):  # 对单一字段校验
    if "BDYJY" not in value.upper():
        return value
    raise serializers.ValidationError('标题里含有非法字符')  
```

##### validate

接受一个参数attrs 序列传递进行的所有数据，

```python
class BookSerializer(serializers.Serializer):
    title = serializers.CharField(max_length=32, validators=[my_validate])
	def validate(self, attrs):  # 对多个字段校验
        # attrs是一个字典，里面是传过来的所有字段
        if 'python' in attrs['title'].lower() and attrs['post_category']==1:
            return attrs
        else:
            raise serializers.ValidationError('传的参数有误，请重新上传')
```

##### 自定义验证

```python
class BookSerializer(serializers.Serializer):
    title = serializers.CharField(max_length=32, validators=[my_validate])
    
def my_validate(value):  # 自定义校验
    if "sb" in value.lower():
        raise serializers.ValidationError('有脏话')
    return value
```

##### ValidationError

在抛出自定义的异常的时候，可以传递给ValidationError 一个字典，

```python
def validate(self, attrs):
    phone_number=attrs["phone_number"]
    r_code=attrs["code"]
    code = rds.get(phone_number)
    if  code is None:
		
        raise serializers.ValidationError({"code":"验证码已过期"})
        elif code == r_code:
            return attrs
        else:
            raise serializers.ValidationError()
```

前段返回的提示信息

```python
{
    "code": [
        "验证码已过期"
    ]
}
```

#### 定义序列化字段

* CharField ... 普通字段

* SerializerMethodField 

  ```python
  class groupSerializer(serializers.ModelSerializer):
      id=serializers.IntegerField(required=False)
      name=serializers.CharField()
      # 获取用户的所有权限
      permissions=serializers.SerializerMethodField()
  
      def get_permissions(self,obj):
          # print()
          return obj.permissions.all().values()
  
      class Meta():
          model=Group
          fields=["id","name","permissions"]
  
      def to_representation(self,instance):
          s=super().to_representation(instance)
          # print(s)
          return s
  ```

* PrimaryKeyRelatedField

  ```python
  class user_group_Serializer(serializers.ModelSerializer):
      id=serializers.CharField()
      name=serializers.CharField()
      # many=True 表示一个返回结果中permissions可以是多个对象，source表示
      users=serializers.PrimaryKeyRelatedField(many=True,source="user_set.all",queryset=Permission.objects.all())
  
      # def get_permissions(self,obj):
      #     print()
      #     return obj.user_permissions.all().values("id","name")
  
      class Meta():
          model=Group
          fields=["id","name","users"]
          # fields=("__all__")
  
      def to_representation(self,instance):
          s=super().to_representation(instance)
          s["users"]=instance.user_set.all().values("id","username")
          return s
  ```

* 自定义序列化字段

  处理多对多字段的显示

  重写CharField的to_representation方法，这个方法就是用来显示最终数据的

  ```python
  class MyCharField(serializers.CharField):
      def to_representation(self, value):
          # value就是QuerySet对象列表
          data_list = []
          for row in value:
              data_list.append({'name': row.name, 'province': row.province.name})
          return data_list
  
  
  class UserInfoSerializer(serializers.Serializer):
      name = serializers.CharField(max_length=32)
      pwd = serializers.CharField(max_length=128)
      # 通过自定义的MyCharField中的to_representation处理多对多字段的QuerySet对象
      been_city = MyCharField(source="been_city.all")
    
  ```

* ListField方法

  ```python
  class MyCharField(serializers.CharField):
      def to_representation(self, value):
          # value就是每个QuerySet对象
  
          return {‘id’:value.pk, "name": value.name, "province":value.province.name}
  
  
  class UserInfoSerializer(serializers.Serializer):
      name = serializers.CharField(max_length=32)
      pwd = serializers.CharField(max_length=128)
      # 通过自定义的MyCharField中的to_representation处理多对多字段的QuerySet对象
      been_city = serializers.ListField(child=MyCharField(),source="been_city.all")
  ```

* 序列化对象

  多级分类序列化序列化

  mode.py

  ```python
  class GoodsClassify(models.Model):
      name=models.CharField(max_length=20)
      c_name=models.CharField(max_length=20)
      parent=models.ForeignKey("self",on_delete=models.SET_NULL,null=True,related_name="sub_set")
      image_url=models.CharField(max_length=20)
  
  ```

  serializers.py

  ```python
  class GoodsclassifySerializersThree(serializers.ModelSerializer):
      class Meta():
          model=GoodsClassify
          fields=("__all__")
  
  class GoodsclassifySerializersTwo(serializers.ModelSerializer):
      sub_set=GoodsclassifySerializersThree(many=True)
      class Meta():
          model=GoodsClassify
          fields=("__all__")
  
  class GoodsclassifySerializers(serializers.ModelSerializer):
      sub_set=GoodsclassifySerializersTwo(many=True)
      class Meta():
          model=GoodsClassify
          fields=("__all__")
      def to_representation(self, instance):
          s=super().to_representation(instance)
          return s
  ```

  views.py

  ```python
  from rest_framework  import viewsets
  from .models import GoodsClassify
  from .serializer import GoodsclassifySerializers
  
  class GoodsClassifyViewSet(viewsets.ModelViewSet):
      # 查询所有的父级分类
      queryset = GoodsClassify.objects.filter(parent__exact=None).order_by("id")
      serializer_class=GoodsclassifySerializers
  ```

#### 序列化字段参数

* source 中可以写查询语句

  序列化时候，实际是要去对应的表中找source 指定的自定字段，如果没有指定source则找和定义的序列化字段同名的字段。

  ```python
  class group_permission_Serializer(serializers.ModelSerializer):
      id=serializers.CharField()
      name=serializers.CharField()
      # many=True 表示一个返回结果中permissions可以是多个对象，source表示 字段的内容 queryset 表示web页面显示的列表形式
      permissions=serializers.PrimaryKeyRelatedField(many=True,source="permissions.all",queryset=Permission.objects.all())
  
      # def get_permissions(self,obj):
      #     print()
      #     return obj.user_permissions.all().values("id","name")
  
      class Meta():
          model=Group
          fields=["id","name","permissions"]
          # fields=("__all__")
  
      def to_representation(self,instance):
          s=super().to_representation(instance)
          s["permissions"]=instance.permissions.all().values("id","name")
          return s
  ```

* request 是否必须

* queryset 指定的是在django-rest-framework 的web页面显示选择框中的内容，插入数据的时候，从指定的querset中查询提供的主键是否存在，

* many=True 表示这这张表中数据对应关联字段的多条数据

#### Serializer 自定义类 

> http://www.liujiangblog.com/blog/43/
> https://www.cnblogs.com/wdliu/p/9131500.html
> https://www.cnblogs.com/wupeiqi/articles/7805382.html

```python
# 我们自己的类
class Comment(object):
    def __init__(self,email,content,created=None):
        self.email=email
        self.content=content
        self.created=created or datetime.now()
    def save(self):
        print("数据存储")
# 验证1
class MyValidation(object):
    def __init__(self,base):
        self.base=base

    def __call__(self, value):
        print(value)
        if value == "wd":
            print(value)
            message="关键字%s不能是%s"%(self.base,value)
            raise serializers.ValidationError(message)
# 序列化类
class CommentSerializer(serializers.Serializer):
    email=serializers.EmailField(error_messages={'required':"无效的邮箱地址"})
        # content=serializers.CharField(max_length=20,validators=[MyValidation(base="content")])
    content=serializers.CharField(max_length=20)
    created=serializers.DateTimeField()
	
   
    def create(self,validated_data):
        return Comment(**validated_data)

    def update(self, instance, validated_data):
        instance.email= validated_data.get("email",instance.email)
        instance.content = validated_data.get('content', instance.content)
        instance.created = validated_data.get('created', instance.created)
        instance.save()
        return instance
    # 验证2 validate_字段名 
    # def validate_content(self,value):
    #     if value == "wd":
    #         print(value)
    #         message="关键字%s不能是%s"%("content",value)
    #         raise serializers.ValidationError(message)
    # 验证3
    # def validate(self, attrs):
    #     if attrs["content"] == "wd":
    #         print(attrs["content"])
    #         message="关键字%s不能是%s"%("contentxxx",attrs["content"])
    #         raise serializers.ValidationError(message)
    #     return attrs

# 要序列化的实例，好比1条数据
comment=Comment(email="wallace@fy.com",content="wd")

class RoleInfo(APIView):
    def get(self, request, *args, **kwargs):
        # 序列化，返回字典
        se = CommentSerializer(instance=comment)
        # 渲染成json数据
        json=JSONRenderer().render(se.data)
        # 反序列化需要 json字符流开始
        stream=io.BytesIO(json)
        # 得到json数据
        data=JSONParser().parse(stream)
        # 反序列化
        serializer=CommentSerializer(instance=comment,data=data)
        # 进行数据校验
        if serializer.is_valid():
            # 得到实例化的对象 
            # .save的时候会调用create或者update方法
            # 具体看饭序列化的时候有没有传递instance，如果有传递则update,没有则create
            co = serializer.save()
            print(co)
        else:
            # 输出错误信息
            print(serializer.errors)
        print(serializer.validated_data)
        return HttpResponse("xxx")
```

## 数据库脚本

![image-20200303102920860](images\Rest_Framework\image-20200303102920860.png)

## 解析器

request中的数据，会自动解析json数据，而后存储到request.data中。

返回数据的时候 `from rest_framework.response import Response`

他会把serializer.data 或者serializer.error_messages 类的数据直接序列化称json数据返回，不需要我们手动实现json序列化。

## 视图

基于函数的视图需要用 @api_view 进行装饰，他需要接收一个列表作为参数，表示当前视图允许接收的HTTP方法。

```python
@api_view(["post","get"])
def idc_detail_v2(request,*args,**kwargs)
    pass
```

类视图 需要继承APIView

```python
class idc_list(APIView):
    def get(self,request,*args,**kwargs):
        print(request.data)
        queryset=Idc.objects.all()
        jsonData=IdcSerializer(queryset,many=True)
        return Response(jsonData)
    def post(self,request):
        data=JSONParser().parse(request)
        serializer=IdcSerializer(data=data)
        if serializer.is_valid():
            serializer.save()
            return JsonResponse(serializer)
        else:
            return Response(serializer.error_messages)
```

## 其他配置

#### 包装url

我们请求数据时候可以使用如下格式

```python
http://127.0.0.1:8000/idcs/idc_list.json
http://127.0.0.1:8000/idcs/idc_detail/2.json
```

修改url文件

```python
from django.urls import path, include, re_path
from idcs.views import idc_list,idc_detail
from rest_framework.urlpatterns import format_suffix_patterns
urlpatterns=[
    path("idc_list/",idc_list.as_view()),
    re_path("idc_detail/(?P<pk>\d)/",idc_detail.as_view())
]
urlpatterns=format_suffix_patterns(urlpatterns)
```

#### api route

url.py配置

```python
from django.contrib import admin
from django.urls import path, include, re_path
from django.conf.urls import url
from idcs.views import idc_list,idc_detail,api_route
from rest_framework.urlpatterns import format_suffix_patterns
urlpatterns=[
    re_path("^$",api_route),
    path("idc_list/",idc_list.as_view(),name="idc-list"),
    re_path("idc_detail/(?P<pk>\d)/",idc_detail.as_view())
]
urlpatterns=format_suffix_patterns(urlpatterns)
```

views.py api_route

```python
from rest_framework.reverse import reverse
@api_view(["get"])
def api_route(request,formar=None,*args,**kwargs):
    return Response({
        "idcs": reverse("idc-list",request=request)
    })
```

#### api文档的生成

* pip 安装 coreapi

* 需要在views.py中写注释

  ```python
  class UserViewset(viewsets.ReadOnlyModelViewSet):
      """
      retrieve
          返回指定的用户信息
      list
          返回用户列表
      """
      queryset =User.objects.all()
      serializer_class =UserSerializer
  ```

* 修改url.py

  ```python
  from rest_framework.routers import DefaultRouter
  from idcs.views import IdcViewsetUrl
route=DefaultRouter()
  route.register("idcs",IdcViewsetUrl,base_name="idcs")
  urlpatterns=[
      path('admin/', admin.site.urls),
      re_path("^",include(route.urls)),
      re_path("^docs/",include_docs_urls("接口文档"))
  ]
  
  ```
  

## mixins

minxns(混合)增强视图的功能，独立的组件或者类是没有任何意义的，我们需要把他们组装起来，混合起来使用才有意义。不同的功能组合起立使用

#### 原始的代码

```python
class idc_list(APIView):
    def get(self,request,*args,**kwargs):
        print(request.data)
        queryset=Idc.objects.all()
        jsonData=IdcSerializer(queryset,many=True)
        print("format",format)
        return Response(jsonData.data)
    def post(self,request):
        # 会解析传递过请求头中含有 Content-Type：application/json的数据，读取body中的内容
        # 而后返回body中的内容，解析过的数据
        data=JSONParser().parse(request)
        serializer=IdcSerializer(data=data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        else:
            return Response(serializer.error_messages)

class idc_detail(APIView):
    def get(self,request,pk,*args,**kwargs):
        try:
            print(pk)
            idc=Idc.objects.get(pk=pk)
        except Idc.DoesNotExist:
            return Response(status=404)
        return Response(IdcSerializer(idc).data)
    def post(self,request,pk,*args,**kwargs):
        try:
            print(pk)
            idc=Idc.objects.get(pk=pk)
            print(idc.name,idc.id)
        except Idc.DoesNotExist:
            return Response(status=404)
        data = JSONParser().parse(request)
        serializer=IdcSerializer(instance=idc,data=data)
        if serializer.is_valid():
            serializer.save()
            return Response(serializer.data)
        else:
            return Response(serializer.error_messages)
    def delete(self,request,pk,*args,**kwargs):
        try:
            print(pk)
            idc=Idc.objects.get(pk=pk)
        except Idc.DoesNotExist:
            return Response(status=404)
        idc.delete()
        return HttpResponse(data="数据删除")
```

#### 使用mixins简化后的 

```python
from rest_framework import mixins,generics

class idc_list_v2(generics.GenericAPIView,
                  # 创建数据的post
                  mixins.CreateModelMixin,
                  # 获取所有数据的
                  mixins.ListModelMixin,
                  ):
    #  查询的queryset语句
    queryset = Idc.objects.all()
    #  使用的序列化的类
    serializer_class = IdcSerializer
    #  只用这样就可以实现上面获取所有idc数据的列表。
    def get(self,request,*args,**kwargs):
        return self.list(request=request,*args,**kwargs)

    def post(self,request,*args,**kwargs):
        return self.create(request=request,*args,**kwargs)

class idc_detail_v2(generics.GenericAPIView,
                    # 表示获取指定一条记录的
                    mixins.RetrieveModelMixin,
                    # 更新数据的
                    mixins.UpdateModelMixin,
                    # 删除数据的
                    mixins.DestroyModelMixin):
    queryset = Idc.objects.all()
    serializer_class = IdcSerializer
    def get(self, request, *args, **kwargs):
        return self.retrieve(request=request, *args, **kwargs)

    def put(self, request, *args, **kwargs):
        return self.update(request=request, *args, **kwargs)

    def delete(self,request,*args,**kwargs):
        return self.destroy(request,*args,**kwargs)

```

#### 再次简化

把上面 post、get、put 简化掉，使用generics中提供的方法

```python
###### 使用混合Mixins v3#####
from rest_framework import generics
class idc_list_v3(# 自动封装了get和post请求
                  generics.ListCreateAPIView
                  ):
    #  查询的queryset语句
    queryset = Idc.objects.all()
    #  使用的序列化的类
    serializer_class = IdcSerializer

class idc_detail_v3(
    # 这个里面封装了get put post方法
    generics.RetrieveUpdateDestroyAPIView
        ):
    queryset = Idc.objects.all()
    serializer_class = IdcSerializer
```

同样返回相同的结果

## 视图集 viewSet

REST框架包括一个用于处理ViewSets的抽象，它允许开发人员集中精力对API的状态和交互进行建模，并根据常规约定自动处理URL构造，

ViewSet类与View类几乎相同，不同之处在于他们提供诸如read或者update之类的操作，而不是get或put等方法处理程序

ViewSet类只绑定到一组方法处理程序，当它被实例化成一组视图的时候，通常通过使用一个Route类来处理自己定义的URL conf的复杂性。

**合并上面的代码到一个视图中**

url.py中的修改

```python
##### viewset v4######
idc_list=IdcViewSet.as_view({
    #  get 方法对应 list方法
    "get":"list",
    # post 方法对应 create方法
    "post":"create"
})
idc_detail=IdcViewSet.as_view({
    "get":"retrieve",
    "put":"update",
    "delete":"destroy"
})

urlpatterns=[
    re_path("^$",api_route),
    path("idc_list/",idc_list,name="idc-list"),
    re_path("idc_detail/(?P<pk>\d)/",idc_detail)
]
```

views.py 

view代码合并到了一个里面

```python
###### 使用ViewSet v4#####
from rest_framework import viewsets
class IdcViewSet(
        viewsets.GenericViewSet,
        mixins.ListModelMixin,
        mixins.RetrieveModelMixin,
        mixins.DestroyModelMixin,
        mixins.UpdateModelMixin,
        mixins.CreateModelMixin
                ):
    queryset = Idc.objects.all()
    serializer_class = IdcSerializer
```

#### 合并处理url

url.py

后面的我们的url就不需要在每个app中都写，只用在总的url中写就可以了

```python
##### URL处理  v4######
from rest_framework.routers import DefaultRouter
route=DefaultRouter()
route.register("idcs",IdcViewsetUrl)
urlpatterns=[
    re_path("^",include(route.urls))
]
```

views.py 不用做什么修改

```python
###### 使用URL处理 v5#####
# ModelViewSet 他是自己继承了ListModelMixin、RetrieveModelMixin、RetrieveModelMixin...
# 所以我们直接继承它就可以了

class IdcViewsetUrl(viewsets.ModelViewSet):
    queryset = Idc.objects.all()
    serializer_class = IdcSerializer
```

## 视图类选择

ApiView viewSet  GenericViewSet ,GenericAPIView, ModelViewSet,Minxins，

APIView  是rest_framework 的核心视图方法，里面定义了验证，限速，等一些rest_framework 的方法，需要我们定义get post 等方法。

GenericAPIView(APIView) 是对APIView的再一次封装,方便和Mixin类进行组合

Minxins里面的CreateModelMixin CreateModelMixin ... 主要是主要是简化了一些操作，和别的类配合使用. 提供了 List、Retrieve、Destroy 等http方法的映射方法。

CreateAPIView ,ListAPIView ... 主要是GenericViewSet和Minxins中 的类结合使用使用，共同完成view函数

​	所以CreateAPIView .... 类是可以单独使用的 但是需要手动绑定url。

ViewSetMixin 主要是对提供对http的方法和Minxins的方法映射

ViewSet  继承了 ViewSetMixin APIView 他是视图集，对http的post get update delete 方法映射成了 Create   list Retrieve update Destroy  ... 等方法。实现了url的自动绑定。 

GenericViewSet(继承了ViewSetMixin和GenericAPIView)，GenericAPIView(APIView)

​	他们里面主要是组合了对数据库的一些操作，一般有数据库操作的视图都会继承这个两个类。

ModelViewSet 继承了mixins中的类和GenericViewSet类，所以是实现了http方法到mixins的关系绑定

ReadOnlyModelViewSet ModelViewSet  继承 GenericViewSet  Minxins.* 实例对实现了对model和序列化非常简单的操作。

get：
	list
	get
create
update
delete

我们的类需要实现什么操作。而后决定继承什么视图类

继承ViewSet 是实现的视图，

```python
class Dats(viewsets.ViewSet):
    permission_classes = (AllowAny,)
    def list(self, request, *args, **kwargs):
        return Response("hahahah")
```

## 分页

定义分页类

```python
from rest_framework.pagination import PageNumberPagination

class UserPagination(PageNumberPagination):
    # 0表示不分页
    page_size = 5
    page_size_query_param = 'page_size'
    max_page_size = 100
```

使用分页类

```python
from rest_framework import viewsets
# 尽量不要使用这种写法
# from django.contrib.auth.models import User
# 使用下面这种写法
from django.contrib.auth import get_user_model
User=get_user_model()
from .serializers import UserSerializer
from .pagination import UserPagination
# Create your views here.
class UserViewset(viewsets.ReadOnlyModelViewSet):
    """
    retrieve
        返回指定的用户信息
    list
        返回用户列表
    """
    queryset =User.objects.all()
    serializer_class =UserSerializer
    pagination_class = UserPagination
```

## 搜索

#### 基础搜索 get_queryset

http://127.0.0.1:8000/users/?username=wang

```python
from rest_framework import viewsets
# 尽量不要使用这种写法
# from django.contrib.auth.models import User
# 使用下面这种写法
from django.contrib.auth import get_user_model
User=get_user_model()
from .serializers import UserSerializer
from .pagination import UserPagination
# Create your views here.
class UserViewset(viewsets.ReadOnlyModelViewSet):
    """
    retrieve
        返回指定的用户信息
    list
        返回用户列表
    """
    queryset =User.objects.all()
    serializer_class =UserSerializer
    pagination_class = UserPagination
    def get_queryset(self):
        querySet=super(UserViewset, self).get_queryset()
        username=self.request.query_params.get("username",None)
        if username:
            querySet=querySet.filter(username__icontains=username)
        return querySet

```

#### drf的搜索

![image-20200304101517063](images\Rest_Framework\image-20200304101517063.png)

filter.SeachFilter 是`from rest_framework import filters`

search_fields  表示可以从哪些字段中进行搜索，只要是要搜索的内容在指定的字段中存在，都会显示出来

search_fileds=("^name","goods_brief","goods_desc")

​	设置字段的搜索条件，如^name 表示要搜的内容在name必须是以搜索的内容开头的，其他的没有指定的则表示默认,= 表示精确匹配@表示全文搜索

#### 排序

![image-20200304103244340](images\Rest_Framework\image-20200304103244340.png)

指定排序的条件

![image-20200304103302007](C:\Users\18434\OneDrive\笔记\python\框架\Django\image\Rest Framework\image-20200304103302007.png)

#### django-filter

​	主要是进行复杂的搜索和过滤的，django的默认是不能进行过滤的，

* 安装`pip install django-filter`

* 配置setting.py

  ```python
  INSTALLED_APPS = [
  	...
      'django-filter',
  	...
  ]
  # 定义全局默认的搜索后端，
  # 也可以在views中单独定义生效，如果views中没有定义使用全局的，定义了则使用views中的
  REST_FRAMEWORK={
      'DEFAULT_FILTER_BACKENDS': (DjangoFilterBackend,),
  }
  ```

* 简单搜索

  views.py

  ```python
  from django_filters.rest_framework import DjangoFilterBackend
  from .filters import UserFilter
  class UserViewset(viewsets.ReadOnlyModelViewSet):
      """
      retrieve
          返回指定的用户信息
      list
          返回用户列表
      """
      queryset =User.objects.all()
      serializer_class =UserSerializer
      pagination_class = UserPagination
      filter_backends = (DjangoFilterBackend,)
      filter_fields=("username",)
      filter_class=UserFilter
  ```

  filters.py

  ```python
  import django_filters
  from django.contrib.auth import get_user_model
  User=get_user_model()
  class UserFilter(django_filters.FilterSet):
      username = django_filters.CharFilter(field_name="username",lookup_expr='contains')
          # name=username, 表示从数据库中那个表中进行搜索
          # lookup_expr 搜索的条件
          #   iexact 精确匹配，不区分大小写   exact 区分大小写匹配
          #   icontains 模糊匹配
      class Meta:
          model = User
          fields = ['username',]
  ```

* 过滤价格在某个值之间的，商品名字是指定字符的

  ![image-20200304101840454](images\Rest_Framework\image-20200304101840454.png)

* 自定搜索方法

  views.py

  ```python
  from django_filters.rest_framework import DjangoFilterBackend
  from .filters import ServerFilter
  class ServerViewset(viewsets.ReadOnlyModelViewSet):
      """
      list
          列出所有服务器信息
      retrieve
          读取一个服务器信息。
      """
      queryset = Server.objects.all()
      serializer_class = ServerSerializers
      filter_backends = (ServerFilter,)
      filter_fields=("hostname",)
      filter_class=ServerFilter
  ```

  filters.py

  ```python
  import django_filters
  from .models import Server
  from django.db.models import Q
  class ServerFilter(django_filters.FilterSet):
      # 自定义搜索
      hostname = django_filters.CharFilter(field_name="hostname",method="search_hostname")
          # name=username, 表示从数据库中那个表中进行搜索
          # lookup_expr 搜索的条件
          #   iexact 精确匹配，不区分大小写   exact 区分大小写匹配
          #   icontains 模糊匹配  contains 区分大小写
      # 输入主机明 可以搜索 ip和主机名
      def search_hostname(self,queryset,name,value):
          return queryset.filter(Q(hostname__icontains=value)|Q(ip__icontains=value))
  
      class Meta:
          model = Server
          fields = ['hostname',]
  ```

## 认证方式

##### 认证配置  authentication_classes

一般情况下我们需要自己定义认证类

认证的时候时候会调用 认证类的authenticate方法，认证成功之后会返回user对象，而后设置到request.user中。

只要配置中的一个认证类通过了认证就算通过了认证，

认证类认证通过后会返回User对象。可以通过request.user获取到认证通过的用户

- BasicAuthentication 是基础的http认证

``` 
http -a admin:password123 POST http://127.0.0.1:8000/snippets/ code="print(789)"
```

- SessionAuthentication

  这个认证类回会去检查request对象中user属性是否有值，如果没有则返回AnonymousUser。

#### session + cookie

服务端生成session id，保存到数据库中，而后返回给客户端，客户端进行下次请求的时候会带有请求头，而后服务端会进行数据库认证，返回是否认证通过的消息。

用户的自己的数据全局存储到后端的数据库或者redis中,可以设置过期时间，删除，

客户端只存储session id，

cookie 是服务器发送到用户浏览器并保存在本地的一小块数据，它会在浏览器下次向同一服务器再发起请求时被携带并发送到服务器上

#### token

token 验证是无状态的，服务器不记录哪些用户登录了或者哪些 JWT 被发布了，token 的认证方式类似于临时的证书签名, 并且是一种服务端无状态的认证方式, 非常适合于 REST API 的场景. 所谓无状态就是服务端并不会保存身份认证相关的数据。

用户登录成功后服务器端生成token，而后把token进行加密后返回给客户端。后续用户请求数据的时候会在请求头带有token，可以设置token的有效期，生成的token的是加密的，生成token的方式有很多中，`jwt`就是其中的一种生成方式，

认证token的时候不用查询数据库，不用在数据库中保存token的信息。

### 简单的用户认证

##### 创建用户

* django shell 进行创建

```python
from django.contrib.auth.models import User
# 创建用户
user=User.objects.create_user("wallace","wallace@wallace.com","wallace")
#因为密码 进行了加密保存，所以我们不能直接修改数据库，进行修改密码
#查询修改用户密码
user=User.objects.get(username="wallace")
user.set_password("654321")
user.save()
```

##### 用户认证

用户认证通过后，登陆后，会生成一个Cookie包含在响应头中给用户返回，

​	Cookie:  sessionid=5c79cnipfhfvvyqj042baegx8ybcvkco

view.py

```go
def login(request):
    if request.method == "GET":
        return render(request,"login.html")
    if request.method == "POST":
        username=request.POST.get("username")
        password=request.POST.get("password")
        #  调用django的系统的认证进行认证
        user_obj=authenticate(username=username,password=password)
        print(user_obj)
        if user_obj:
			login(request,user_obj) // 只有登陆后才会生成Cookie
            return HttpResponse("认证成功")
        return HttpResponse("认证失败")
```

login.html

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
    <form method="post" action="#">
        用户名<input name="username" type="text">
        密码<input name="password" type="password">
        <input type="submit">
    </form>
</body>
</html>
```

url.py

```pytho
urlpatterns = [
    path('admin/', admin.site.urls),
    path('app01/', index),
    path('qd/', querydict),
    path('login/', login),
]
```

### JWT 

#### 认证配置

https://www.cnblogs.com/guapitomjoy/p/11939050.html

https://lion1ou.win/2017/01/18/

setting.py

```python
REST_FRAMEWORK = {
    # 用户登陆认证方式
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'rest_framework_jwt.authentication.JSONWebTokenAuthentication',
    ),
    'DEFAULT_PERMISSION_CLASSES':(
        "rest_framework.permissions.IsAuthenticated",
    )
}
JWT_AUTH = {
    'JWT_EXPIRATION_DELTA': datetime.timedelta(days=1), # 有效期设置
}
```

url配置

```python
urlpatterns = [
    re_path("^", include(route.urls)),
    re_path("^auth/",obtain_jwt_token)
]
```

测试访问

使用postman进行测试post提交用户密码到http://127.0.0.1:8000/auth/login ，会返回内容是生成的token，我们可以设定配置指定 jwt_response_payload_handler 方法，定义返回的数据。

```json
{
    "token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxLCJ1c2VybmFtZSI6IndhbGxhY2UiLCJleHAiOjE1ODAwMjY1NzQsImVtYWlsIjoid2FsbGFjZUBmeS5jb20ifQ.gvYlR9hv8kcEdrbhAdPSUexxIzXePwCd5D2MJiDdLbE"
}
```

后续的请求请求需要带有token去请求其他数据 

``` shell  Authorization
 Authorization:'JWT eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJ1c2VyX2lkIjoxLCJ1c2VybmFtZSI6IndhbGxhY2UiLCJleHAiOjE1ODAwMTY4NDUsImVtYWlsIjoid2FsbGFjZUBmeS5jb20ifQ.rXZA9-3i1wtA9PDvsLOkZpDI_0mKPsazIKwmEP-Fyuc'
```

#### JWT 的退出

退出的实现可以有多种

1. 服务端什么也不保存，只是客户端删除本地token实现退出，可以实现多用户同时在线
2. 服务端退出，需要服务为每个用户保存各自的token加密串，登录时修改加密串(强制提出其他使用该用户登录的客户端)，退出时候也修改加密字符串
3. 服务端退出，多用户同时在线，为同一个用户生成多个token加密字符串，保存到redis中，如根据用户ip+secret，或者 (user agent)+secret ,退出时候删除自己的就可以了，

## 权限管理

权限管理，一般指根据系统设置的安装规则或者安全策略，用户可以访问而且只能访问自己被授权的资源

权限管理好比钥匙，有了钥匙就能把门打开，但是权限设置是有级别之分的，假如这个系统由多个权限级别，就如同一间屋子由多个门，想要把所有的门打开您必须取到所有的钥匙。

#### django的权限机制

django权限机制能够约束用户行为，控制页面的显示内容，也能是API更加灵活和安全，用好权限机制，能让系统更加强大和健壮。

Djnago用user,group,permission完成了权限机制，这个权限机制将属于model的某个permission赋予user或者gourp,可以理解为全局的权限，即如果用户A对数据模型(model)B有可写权限，那么A能修改model B的所有实例(objects),group的权限也是如此，如果group C 赋予model B的可写权限，则隶属于group C的所有用户都可以修改model B的所有实例。

#### djang权限项

django 用permission对象存储权限，每个model默认都有三个permission,即add model、change model、delete model

permission总是与model对应，如果一个object 不是model的实例，我们无法为它创建/分配权限

#### 默认权限

在INSTALLED_APPS设置中列出django.contrib.auth 后，安装的各个应用中的每个Django模型默认都有三个权限：添加、修改、删除。每次运行manager.py migrate命令创建模型时都会赋予这三个权限。

#### 分组

django.contrib.auth.models.Group模型时用户分类的通用方式，这样便可以为一批用户赋予权限或者添加其他标注，用户所属于分组数量不限制，一个分组中的用户自动获得赋予那个分组的权限。

除了权限之外，分组还是为用户分类的便捷方法，分组后可以给用户添加标签，或者扩展功能。

#### 权限应用

* Permission

  django 定义每个model后，默认都会添加该model的add、change和delete三个permissio，自定义的permission可以在我们model的时候添加。也可以直接在数据库中的权限表添加权限(不建议使用)。

  ```python
  class Role(models.Model):
      name=models.CharField(max_length=32,unique=True)
      class Mate:
          permission=(
              ("view_role","can view role")
          )
  ```

  permission表中的每个权限都是django.contrib.auth.Permission类型的实例，该类型包含三个字段name,codename,content_type

  content_type 反应了permission属于那个model

  codename 如上面的view_role，代码逻辑中检查权限时使用

  name 是permission的描述，将permission打印到屏幕或者页面时默认显示的name

* User Permission 用户权限的操作

  ```python
  user=User.objects.get(username="wallace")
  user.user_permissions=[permission_obj,permission_obj]
  user.user_permissions.add(permission_obj) 增加权限
  user.user_permissions.remove(permission_obj) 删除权限
  user.user_permissions.clear() 清空权限
  user.get_all_permissions() 列出用户的所有权限
  user.get_group_permissions() 列出用户组的所有权限
  ```

* Group Permission 组权限的添加

  ```python
  group.permissions.set([permission_obj,permission_obj])
  group.permissions.add(permission_obj,permission_obj...)
  group.permissions.remove(permission_obj,permission_obj...)
  group.permissions.clear()
  ```

* 权限检查

  函数视图

  ```python
  from django.contrib.auth.decorators import login_required,permission_required
  @login_required
  @permission_required('appname.view_role')
  def my_view(request):
      ...
  ```

  类视图

  ```python
  class RoleViewSet(TemplateView):
      @method_decorator(login_required)
      @method_decorator(permission_required('appname.view_role'))
      def get(self,request,*args,**kwargs):
          pass
  ```

  视图代码中写

  ```python
  if not request.user.hash_perm("appname.view_role")
  	return HttpResponse("Forbidden")
  ```

  模板中使用

  PermissionRequestMixin

  自定义PermissionRequestMixin

#### 权限配置 permission_classes

权限认证一般会调用 权限认证中的has_permission 方法， 如果有此权限返回true，否则返回false。

默认所有用户是由读权限的。

* BasePermission 最基础的权限认证类

* AllowAny 允许任何人访问的权限

* IsAuthenticated 必须认证过后才可以访问

* IsAdminUser admin的访问权限

* IsAuthenticatedOrReadOnly 认证后只读的访问

* DjangoModelPermissions 基于django的模型权限

  get_required_permissions 不同的请求映射到不同的权限，根据请求方法返回不同的权限列表，
  view中必须定义了querySet，
  最后进行权限认证，

  user.has_perms(perms)如果perms 为空列表，则user.has_perms(perms) 返回为True

* DjangoModelPermissionsOrAnonReadOnly 基于django的模型只读访问
* DjangoObjectPermissions 基于django对象权限

**DjangoModelPermissions 的这个权限认证，每个view中都必须定义queryset** 

**如果局部定义了permission_classes 则就不使用全局的了,一般是定义一个全局的，而后局部定义的自己** 

**如下配置：**

* 修改默认的权限配置，settings.py默认的权限配置是AllowAny.

```python
REST_FRAMEWORK={
	...
    'DEFAULT_PERMISSION_CLASSES': (
        'rest_framework.permissions.DjangoModelPermissions',
    ),
    ...
}
```

* 局部权限认证定义

```python
from rest_framework import viewsets
from django.contrib.auth import get_user_model
from rest_framework.permissions import IsAuthenticated, AllowAny,DjangoModelPermissions

User=get_user_model()
from .serializers import UserSerializer
from .pagination import UserPagination
from django_filters.rest_framework import DjangoFilterBackend
from .filters import UserFilter

class UserViewset(viewsets.ReadOnlyModelViewSet):
    """
    retrieve
        返回指定的用户信息
    list
        返回用户列表
    """
    queryset =User.objects.all()
    serializer_class =UserSerializer
    pagination_class = UserPagination

    filter_backends = (DjangoFilterBackend,)
    filter_fields=("username",)
    filter_class=UserFilter
    permission_classes = (DjangoModelPermissions,)
    def list(self, request, *args, **kwargs):
        if request.user.u.has_perm("add_idc"):
            return HttpResponse("没有权限")
        return super(UserViewset, self).list(request, *args, **kwargs)
```

#### 自定义查看权限1

models.py

```python
from django.db import models

# Create your models here.

class Idc(models.Model):
    name    =models.CharField("机房名称",max_length=32)
    address =models.CharField("机房地址",max_length=200)
    phone   =models.CharField("机房联系电话",max_length=15)
    email   =models.EmailField("机房联系email",max_length=15)
    letter  =models.CharField("idc字母简称",max_length=5)
    # 还需要定义

    def __str__(self):
        return  self.name
    class Meta:
        db_table='resources_idc'
        permissions=[("view_idc","can view idc")]
```

views.py

```python
from idcs.models import Idc
from idcs.serializers import IdcSerializer
from rest_framework import viewsets
from DevOps.permissions import Permissions
# Create your views here.
class IdcViewsetUrl(viewsets.ModelViewSet):
    """
    retrieve
        返回指定idc列表
    list
        返回idc列表
    update
        更新idc信息
    destroy
        删除idc记录
    create
        创建idc记录
    """
    queryset = Idc.objects.all()
    serializer_class = IdcSerializer
    permission_classes = (Permissions,)
```

permissions.py

```python
from rest_framework.permissions import DjangoModelPermissions

class Permissions(DjangoModelPermissions):
    perms_map = {
        'GET': ['%(app_label)s.view_%(model_name)s'],
        'OPTIONS': [],
        'HEAD': [],
        'POST': ['%(app_label)s.add_%(model_name)s'],
        'PUT': ['%(app_label)s.change_%(model_name)s'],
        'PATCH': ['%(app_label)s.change_%(model_name)s'],
        'DELETE': ['%(app_label)s.delete_%(model_name)s'],
    }
```

#### 自定义查看权限2

修改全局setting.py 配置

```python
REST_FRAMEWORK={
    'DEFAULT_FILTER_BACKENDS': ('django_filters.rest_framework.DjangoFilterBackend',),
    'DEFAULT_PERMISSION_CLASSES': (
        "DevOps.permissions.Permissions",
    ),
}

```

model.py

```python
from django.db import models

# Create your models here.

class Idc(models.Model):
    name    =models.CharField("机房名称",max_length=32)
    address =models.CharField("机房地址",max_length=200)
    phone   =models.CharField("机房联系电话",max_length=15)
    email   =models.EmailField("机房联系email",max_length=15)
    letter  =models.CharField("idc字母简称",max_length=5)

    def __str__(self):
        return  self.name
    class Meta:
        db_table='resources_idc'
        permissions=[("view_idc","can view idc")]
```

views.py

```python

from idcs.models import Idc
from idcs.serializers import IdcSerializer
from rest_framework import viewsets
from DevOps.permissions import Permissions
# Create your views here.


class IdcViewsetUrl(viewsets.ModelViewSet):
    """
    retrieve
        返回指定idc列表
    list
        返回idc列表
    update
        更新idc信息
    destroy
        删除idc记录
    create
        创建idc记录
    """
    queryset = Idc.objects.all()
    serializer_class = IdcSerializer
    # 这个也可以在局部使用
    # permission_classes = (Permissions,)
    permission_map={
        "GET":["idcs.view_idc"]
    }
```

permissions.py

```python
from rest_framework.permissions import DjangoModelPermissions

class Permissions(DjangoModelPermissions):
    def get_custom_perms(self,view,method):
        if hasattr(view,"permission_map"):
           if isinstance(view.permission_map,dict):
               return view.permission_map.get(method,[])
        return []
    def has_permission(self, request, view):
        if getattr(view, '_ignore_model_permissions', False):
            return True

        if not request.user or (
           not request.user.is_authenticated and self.authenticated_users_only):
            return False

        queryset = self._queryset(view)
        perms = self.get_required_permissions(request.method, queryset.model)
        perms.extend(self.get_custom_perms(view,request.method))
        return request.user.has_perms(perms)
```

## router的拆分

group app中

router.py

```python
from rest_framework.routers import DefaultRouter
from .views import Domain,Dnspod,resourceType

domain_route=DefaultRouter()
domain_route.register('resourcetype',resourceType,basename="resourcetype")
```

url.py

```python
from django.contrib import admin
from django.urls import path,include,re_path
from rest_framework.routers import DefaultRouter
from domian.router import domain_route

route=DefaultRouter()

route.registry.extend(domain_route.registry)

urlpatterns = [
    path('admin/', admin.site.urls),
    re_path("^", include(route.urls)),
]

```

