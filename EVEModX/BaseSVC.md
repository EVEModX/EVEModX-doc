此文档描述EVE的Service机制

需要解决的问题:

* 写一个Service
* 研究`ServiceManager`的event，搞清楚区别

~~最终实现的效果：此Mod注册并运行起来一个Service，Service向sm注册一个event，再实现一个exec_in_main_thread的方法，内容就是把传进来来的方法作为一个参数触发一个event，svc收到event之后执行传进来的方法~~

# `CoreService` 类 (别名`Service`)

##自身属性

* `__required__` = `['Sessions']`
* `state`: SERVICE_STOPPED/START_PENDING/STOP_PENDING/RUNNING/CONTINUE_PENDING/PAUSE_PENDING/PAUSED/FAILED

## `__init__()` 解读

* `self.__servicename__` 从 `self.__replaceservice__` 里得到，默认值是 `self.__guid__` 里面第一个点之后的名称
* `self.__logname__` 应该是在log里面的名称
* `self.logChannel`  从guid来，这个channel的意思是？
* 调用 `self.InitService()` 应该是用来实现自身Service功能上的初始化
* `self.__counters__` 计数？
* `isLogInfo` `isLogNotice` `isLogWarning` log相关    

##  `_MachoResolveAdditional()`

部分服务需要依赖角色所处的地点

## `LockService(LockID)`

`self.serviceLocks` 存放所有的锁. 如果ID没有对应的锁就从`locks.Rlock`获取一个。

锁机制可能由CCP自己实现，类名为`locks`

## `LogMethodCall(*args,**keywords)`

记录函数调用？

## `GetHtmlState(writer,session=None,request=None)`

用HTML输出自身的各类属性

# `ServiceManager`类

文件位置:`/client/carbon/common/script/sys/serviceManager.py`

##自身属性

* `state`
* `services`
* `dependants`
* `notify`
* `notifyObs`
* `startInline`
* `blockedServices`
* `startupTimes`
* 只允许一个`ServiceManager`实例存在