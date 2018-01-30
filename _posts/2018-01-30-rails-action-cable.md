---
title: ActionCable作为API,被app调用例子
layout: post
date: '2018-01-30 11:00:00'
categories: rails
---

**1.cable.yml配置**
```
local: &local
  url: redis://localhost:6379

development: *local
test: *local

production:
  adapter: redis
  url: redis://localhost:6379/1

```
**2.app/channels/目录下创建 room_channel.rb**
```
class RoomChannel < ApplicationCable::Channel
  def subscribed
    stream_from "room_channel"
  end
  def unsubscribed
    # Any cleanup needed when channel is unsubscribed
  end

end
```
**3.routes.rb添加**
```
mount ActionCable.server => '/cable'
```
**4.创建发布消息方法**
```
def my_broadcast
ActionCable.server.broadcast(
          'room_channel', # 这是流的名字，要跟在 stream_from 定义的保持一致
          title: 'the title',
          body: "server send message #{rand(666)}"
      )
end
```
**5.前端js订阅websocket接口**
```
<stript>
    function addMessage(msg) {
        $("#log").append("<p>" + msg + "</p>");
    }

   socket = new WebSocket("ws://localhost:3001/cable");
   socket.onopen = function() {
        addMessage("Socket Status: " + socket.readyState + " (open)");

        //socket.send发送订阅的频道，RoomChannel是频道的类名,这一步很关键，只有发送后才能开始接受消息
        socket.send(JSON.stringify({"command": "subscribe","identifier":"{\"channel\":\"RoomChannel\"}"})) 
    }

    socket.onclose = function() {
        addMessage("Socket Status: " + socket.readyState + " (closed)");
    }

    socket.onmessage = function(msg) {
        console.log(JSON.parse(msg.data).message)
        addMessage("Received: " + JSON.parse(msg.data).message.body);
    }

</script>
```
6.执行上面的`my_broadcast`方法，前端就会收到广播

参考：

[connecting-to-actioncable-from-ios-app](https://stackoverflow.com/questions/35145429/connecting-to-actioncable-from-ios-app?rq=1 )

[how-to-use-actioncable-as-api](https://stackoverflow.com/questions/35320791/how-to-use-actioncable-as-api/36120630)

[how-to-use-actioncable-as-api ](https://stackoverflow.com/questions/35320791/how-to-use-actioncable-as-api )

[issues](https://github.com/rails/rails/issues/28447 )

[rails-actioncable-connectionserver-with-nodejsclient](https://stackoverflow.com/questions/44556858/rails-actioncable-connectionserver-with-nodejsclient?noredirect=1&lq=1)