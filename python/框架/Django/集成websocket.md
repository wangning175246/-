#### webSocket

websocket 是基于TCP连接上进行的全双工通信协议
websocket 协议中，没有request和Response的概念，连接一旦建立，就是建立持久连接，双方可以随时向对方发送数据

WebSocket 借助http协议进行握手，握手成功后，就变身成了TCP通道。

#### webSocket的作用

可以主动向客户端推送消息，

tailf的实现、在线协同编辑，如石墨，语雀，腾讯文档

聊天室、多玩家在线游戏，视频弹幕，股票基金实时报价

#### ASGI

ASGI由Django团队提出，为了解决在一个网络框架中同时处理HTTP,HTTP2,WebSocket协议，Django团队开发了Channels插件,为Django带来了ASGI能力。

![ASGI](images\集成websocket\2017-03-13-20-20-59.jpg)

![xx](..\..\image\1564373245921-d2350a29-1ace-42da-a639-e679c28201e4.png)

ASGI中，将一个网络请求划分为三个处理层面，

* 第一层，interface server协议处理服务器，负责对请求协议进行封装，并将不同的协议发到不同的Channle(频道)，
* 第二层，通常是一个队列系统，频道绑定了
* 第三层，的Consumer(消费者)。

比如说HTTP协议的频道绑定了HTTP消费者，当有新的HTTP请求过来时，interface server将请求分发到HTTP频道，HTTP频道绑定的HTTP消费者对该请求进行处理，将处理的结果返回给HTTP频道，最终返回给客户端

ASGI和WSGI

- WSGI (Python Web Server Gateway Interface)：为Python语言定义的Web服务器和Web应用程序或矿建之间的一种简单而通用的接口

- - uWSGI： 符合WSGI 标准的 web 服务软件
  - SWSGI：django默认的wsgi

- ASGI (Asynchroonous Server Gateway Interface)：异步服务网关接口，一个介于网络协议服务和Python应用之间的标准接口，能够处理多种通用的协议类型，包括 HTTP，HTTP2 和 WebSocket

- - Daphne：符合ASGI 标准的 web 服务软件

- WSGI 和 ASGI 的区别：WSGI 是基于HTTP协议模式的，不支持WebSocket，而ASGI就是为了支持Python常用的WSGI所不支持的新的协议标准，即ASGI是WSGI的扩展，并且能够通过asyncio异步运行

#### 基本概念

通道：

​	每建立一个连接就是建立了一个通道，每个通道都有自己的名称，只要知道通道名称就可以向通道中发送数据。

​	每个使用者实例都有一个自动生成的唯一通道名称

通道组：

​	多个通道可以组成通道组，通道组有组名，可以通过组名直接向组中发送消息，组中的所有通道都可以接受到消息

####  配置使用

1. 安装

   `pip install channels`

2. 修改setting文件

   ```python
   INSTALLED_APPS = [
   	....
       'channels',
   	....
   ]
   //django_test 是创建的django的项目名称
   ASGI_APPLICATION = 'django_test.routing.application'
   // 配置队列层
   CHANNEL_LAYERS = {
       'default': {
           'BACKEND': 'channels_redis.core.RedisChannelLayer',
           'CONFIG': {
               "hosts": [('192.168.74.130', 6379)],
           },
       },
   }
   ```

3. 创建路由文件routing.py,和setting.py统计目录下新建routing.py

   ```python
   from channels.routing import ProtocolTypeRouter,URLRouter
   from channels.auth import AuthMiddlewareStack
   import  chat.routing
   application = ProtocolTypeRouter({
       'websocket':AuthMiddlewareStack(
           URLRouter(
               // 指定路由去chat app下的routing下找子路由
               chat.routing.websocket_urlpatterns
           )
       ),
   })
   ```

4. 启动django

   `python manage.py runserver` 

#### 搭建简单聊天室

* django_test/url.py

  ```python
  from django.contrib import admin
  from django.urls import path,include
  
  urlpatterns = [
      path('chat/', include('chart.urls')),
  ]
  ```

* chat/url.py

  ```python
  from django.urls import path
  
  from . import views
  
  urlpatterns = [
      path('', views.index, name='index'),
      path('<str:room_name>/', views.room, name='room'),
  ]
  ```

* chat/routing.py

  ```python
  from django.urls import re_path
  
  from . import consumers
  websocket_urlpatterns=[
      re_path(r'ws/chat/(?P<room_name>\w+)/$',consumers.ChatConsumer)
  ]
  ```

* chat/view.py

  ```python
  import json
  
  from django.shortcuts import render
  
  # Create your views here.
  from django.utils.safestring import mark_safe
  
  
  def index(request):
      return render(request, 'chat/index.html', {})
  
  def room(request, room_name):
      return render(request, 'chat/room.html', {
          'room_name_json': mark_safe(json.dumps(room_name))
      })
  ```

* chat/consumers.py

  ```python
  import json
  
  from asgiref.sync import async_to_sync
  from channels.generic.websocket import WebsocketConsumer
  
  class ChatConsumer(WebsocketConsumer):
      # 建立连接是执行的函数
      def connect(self):
          self.room_name=self.scope["url_route"]["kwargs"]["room_name"]
          self.room_group_name='chat_%s'% self.room_name
          async_to_sync(self.channel_layer.group_add)(
              self.room_group_name,
              self.channel_name
          )
          self.accept()
      # 断开连接时执行的函数
      def disconnect(self, code):
          async_to_sync(self.channel_layer.group_discard)(
              self.room_group_name,
              self.channel_name
          )
  	# 接受数据的时候执行的函数
      def receive(self, text_data=None, bytes_data=None):
          text_data_json=json.loads(text_data)
          message=text_data_json["message"]
          print("1")
          async_to_sync(self.channel_layer.group_send)(
              self.room_group_name,
              {
                  #  这个方法会调用 下面定义的chat_message 正在的去发送消息
                  'type':"chat_message",
                  'text':message
              }
          )
  	# 调用他发送数据
      def chat_message(self,event):
          message=event['text']
          print(message)
          self.send(text_data=json.dumps({
              'message': message
          }))
  ```

* template/chat/

  index.html

  ```html
  <!-- chat/templates/chat/index.html -->
  <!DOCTYPE html>
  <html>
  <head>
      <meta charset="utf-8"/>
      <title>Chat Rooms</title>
  </head>
  <body>
      What chat room would you like to enter?<br/>
      <input id="room-name-input" type="text" size="100"/><br/>
      <input id="room-name-submit" type="button" value="Enter"/>
  
      <script>
          document.querySelector('#room-name-input').focus();
          document.querySelector('#room-name-input').onkeyup = function(e) {
              if (e.keyCode === 13) {  // enter, return
                  document.querySelector('#room-name-submit').click();
              }
          };
  
          document.querySelector('#room-name-submit').onclick = function(e) {
              var roomName = document.querySelector('#room-name-input').value;
              window.location.pathname = '/chat/' + roomName + '/';
          };
      </script>
  </body>
  </html>
  ```

  room.html

  ```html
  <!DOCTYPE html>
  <html>
  <head>
      <meta charset="utf-8"/>
      <title>Chat Room</title>
  </head>
  <body>
      <textarea id="chat-log" cols="100" rows="20"></textarea><br/>
      <input id="chat-message-input" type="text" size="100"/><br/>
      <input id="chat-message-submit" type="button" value="Send"/>
  </body>
  <script>
      var roomName = {{ room_name_json }};
  
      var chatSocket = new WebSocket(
          'ws://' + window.location.host +
          '/ws/chat/' + roomName + '/');
  
      chatSocket.onmessage = function(e) {
          var data = JSON.parse(e.data);
          var message = data['message'];
          document.querySelector('#chat-log').value += (message + '\n');
      };
  
      chatSocket.onclose = function(e) {
          console.error('Chat socket closed unexpectedly');
      };
  
      document.querySelector('#chat-message-input').focus();
      document.querySelector('#chat-message-input').onkeyup = function(e) {
          if (e.keyCode === 13) {  // enter, return
              document.querySelector('#chat-message-submit').click();
          }
      };
  
      document.querySelector('#chat-message-submit').onclick = function(e) {
          var messageInputDom = document.querySelector('#chat-message-input');
          var message = messageInputDom.value;
          chatSocket.send(JSON.stringify({
              'message': message
          }));
  
          messageInputDom.value = '';
      };
  </script>
  </html>
  ```

  