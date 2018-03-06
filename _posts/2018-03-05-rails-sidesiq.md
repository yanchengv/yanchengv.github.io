---
title: rails ActiveJob Sidekiq 配置
layout: post
date: '2018-03-05 18:12:04'
categories: rails
---

**1. jobs目录生成job文件**


执行命令`rails g  job demo_job`
```
class DemoJobJob < ApplicationJob
  queue_as :default

  def perform(*args)
    # Do something later
  end
end
```
**2.配置`config/initializers/sidekiq.rb`，使用redis**


```
url = "redis://127.0.0.1:6379/0"
redis_config = {
    url: url,
    namespace: 'sidekiq',
}

Sidekiq.configure_server do |config|
  config.redis = redis_config
end

Sidekiq.configure_client do |config|
  config.redis = redis_config
end
```

**3.配置`config/sidekiq.yml`文件**

```
#启动sidekiq, sidekiq -C config/sidekiq.yml默认执行此配置文件
#:daemon: true
#:verbose: true


:pidfile: tmp/pids/sidekiq.pid
:logfile: ./log/sidekiq.log
:queues:
  - default
  - [float_profit, 2]

development:
  concurrency: 5 # 并发数
staging:
  :concurrency: 10
production:
  :concurrency: 20
	
```

**4.配置`routes.rb`路由后，可以通过`localhost/3000/sidekiq`访问sidekiq后台**

```
  require 'sidekiq/web'
  mount Sidekiq::Web => '/sidekiq'
```

**5.启动sidekiq**

`sidekiq -C config/sidekiq.yml`

**6.执行job任务**

`DemoJobJob.perform_now`

##### 备注：

**1. Sidekiq轮询间隔时间默认是15秒**

例如执行 `DemoJobJob.set(wait: 1.seconds).perform_later()`，默认不是1秒后执行，而是15秒后执行，可以修改中配置可以修改轮询时间为1秒

```
#Sidekiq's scheduler 轮询间隔时间，默认15秒；config/enviroments/development.rb
  Sidekiq.configure_server do |config|
    config.average_scheduled_poll_interval = 1
  end
	
```