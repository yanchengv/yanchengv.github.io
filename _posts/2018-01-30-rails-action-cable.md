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
   #stream_from params[:room]  #可以是客户端创建订阅时的参数临时创建
  end
  def unsubscribed
    # Any cleanup needed when channel is unsubscribed
  end

end
```

**3.创建发布消息方法**
```
def my_broadcast
ActionCable.server.broadcast(
          'room_channel', # 这是流的名字，要跟在 stream_from 定义的保持一致
          title: 'the title',
          body: "server send message #{rand(666)}"
      )
end
```
**4.前端js订阅websocket接口**
```
<stript>
    function addMessage(msg) {
        $("#log").append("<p>" + msg + "</p>");
    }

 // 本地测试的地址是ws://localhost:28080 
// 正式的地址则是下面nginx配置的地址  `socket = new WebSocket("ws://www.balawo.com/cable");`,
// 如果是https则是 `socket = new WebSocket("wss://www.balawo.com/cable");`
	
   socket = new WebSocket("ws://localhost:28080");
   socket.onopen = function() {
        addMessage("Socket Status: " + socket.readyState + " (open)");
       socket.send(JSON.stringify({"command": "subscribe","identifier":"{\"channel\":\"RoomChannel\"}"})) 
			 
       //socket.send发送订阅的频道，RoomChannel是频道的类名;从现有订阅中至少选择一个感兴趣的订阅，否则将无法建立连接,一旦订阅了某个频道，用户也就   成为了订阅者,room是可以让服务器端接受临时创建频道
			 
	  //socket.send(JSON.stringify({"command": "subscribe","identifier":"{\"channel\":\"RoomChannel\",\"room\":\"room1\"}"}))
    
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
5.执行上面的`my_broadcast`方法，前端就会收到广播

6. 如果是正式环境 则需要配置nginx 
   
```

	upstream balawo_web {
				 server unix:/data/www/balawo_web/shared/tmp/sockets/puma.sock fail_timeout=0;
		    }
  
	server {
	 listen 80;
	 server_name www.balawo.com;
	 root /data/www/balawo_web/current/public;
	 location ~* ^(/assets|/favicon.ico|/41orptZsat.txt) {
			gzip_static on;
			access_log        off;
			expires           max;
			add_header Cache-Control public;
    }

    location / {
      client_max_body_size 100m;         #主要是这个参数，限制了上传文件大大小
      proxy_redirect     off;
      proxy_set_header   Host $host;
      proxy_set_header   X-Forwarded-Host $host;
      proxy_set_header   X-Forwarded-Server $host;
      proxy_set_header   X-Real-IP        $remote_addr;
      proxy_set_header   X-Forwarded-For  $proxy_add_x_forwarded_for;
      proxy_buffering    on;
      proxy_pass         http://balawo_web;
      access_log /data/www/balawo_web/shared/log/nginx.access.log;
      error_log /data/www/balawo_web/shared/log/nginx.error.log;

    }
    location /cable {
       proxy_pass           http://127.0.0.1:28080/;
       proxy_http_version 1.1;
       proxy_set_header Upgrade $http_upgrade;
       proxy_set_header Connection "Upgrade";
  }
	}

		```

6. [代码案例](https://github.com/yanchengv/action_cable_as_api_example)

参考：

[connecting-to-actioncable-from-ios-app](https://stackoverflow.com/questions/35145429/connecting-to-actioncable-from-ios-app?rq=1 )

[how-to-use-actioncable-as-api](https://stackoverflow.com/questions/35320791/how-to-use-actioncable-as-api/36120630)

[how-to-use-actioncable-as-api ](https://stackoverflow.com/questions/35320791/how-to-use-actioncable-as-api )

[issues](https://github.com/rails/rails/issues/28447 )

[rails-actioncable-connectionserver-with-nodejsclient](https://stackoverflow.com/questions/44556858/rails-actioncable-connectionserver-with-nodejsclient?noredirect=1&lq=1)