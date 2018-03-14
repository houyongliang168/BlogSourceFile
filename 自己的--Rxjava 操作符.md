---
title: Rxjava 操作符
---


> 参考:这是一份全面 & 详细 的RxJava操作符 使用攻略[https://mp.weixin.qq.com/s/2vDZ7h6SL-LR7n3FR6OMrw]()

## 1. 类型



分类 | 作用
---|---
创建操作符 | 创建被观察者（Observable）对象& 发送时间
变换操作符 | 变换 被观察者（Observable）的发送事件
组合/合并操作符 | 组合多个  被观察者（Observable）&合并需要发送的事件
功能性操作符 | 辅查被观察者（Observable）在发送事件时实现一些功能性需求（如错误处理 、线程调度等）
过滤操作符 |过滤/筛选 被观察者（Observable）发送事件& 观察者（Observer）接收的事件
条件/布尔操作符 | 通过设置函数 ，判断被观察者（Observable）发送的事件是否符合条件


## 1.1 创建操作符



![创建操作符](http://mmbiz.qpic.cn/mmbiz_png/MOu2ZNAwZwMTqrq7YRibC2op1FqPjrFn7ibxyDrGr3N81VCpKBWjRIM0ic3su0xlQDevMqxiatBR4XKuC4VKrj2QNg/?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)




> 创建操作符

```
作用： 创建 被观察者（ Observable） 对象 & 发送事件。
Rxjava 创建操作符
	1. 基本创建
		create()
	2. 快速创建和发送事件
		just   底层方法与 fromArray 相同
		formArray  数组
		fromIterable  集合
		never  不发送任何事件
		empty  仅发送 onComplete
		error  仅发送 Error 事件
	3. 延迟创建
		defer()  需要定义静态变量,订阅时动态创建 observable 对象 执行 --订阅之前的对象
		timer   指定一个时间后 发送 long  next(0)
		interval  创建后每隔一段时间发送 ，无线发送
		intervalRange()  每隔断时间发送，可以指定发送次数
		range()  快速创建后 ，连续发送指定范围的事件  int 类型
		rangerLong()  与 range() 类似，是 Long 类型
```


![创建操作符](https://upload-images.jianshu.io/upload_images/944365-d36c6ed319565acc.png)


> RxJava的操作符分类

![RxJava的操作符分类](https://mmbiz.qpic.cn/mmbiz_png/MOu2ZNAwZwMTqrq7YRibC2op1FqPjrFn7uqbia2d0ib4EDcz5kbwfjGXibPqWmnn9TltDiaajiasmLaZFKMXM9LkPqrA/?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)

## 1.2 变换操作符

> 变换操作符 

```
作用： 对事件序列中的事件/整个事件序列进行加工处理（即变换），使得其转变成不同的事件 / 整个事件序列

Rxjava变换操作符
	map()
		使用Map变换操作符中的Function函数对被观察者发送的事件进行统一变换   一般1对1
	FlatMap()    无序
		将被观察者发送的整个事件序列变换  1对多
	ConcatMap()    有序
		将被观察者发送的整个事件序列变换   1对多
	buffer()
		从被观察者发送的事件  获取事件放到缓存区 & 发送


```
[变换操作符](https://www.jianshu.com/p/904c14d253ba)

![变换操作符](https://mmbiz.qpic.cn/mmbiz_png/MOu2ZNAwZwMTqrq7YRibC2op1FqPjrFn7wicCJGyib16PsbHg8omE1ibU9F74WQTplZJibTD7eBsxuoNBibldB9L7swg/?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1)


![image](https://upload-images.jianshu.io/upload_images/944365-dc0a7df673324e21.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

## 1.3 组合/合并操作符
[组合/合并操作符](https://www.jianshu.com/p/c2a7c03da16d)

```
作用：    组合 多个被观察者（Observable） & 合并需要发送的事件

组合/合并操作符
	1. 组合多个被观察者
		按顺序发送
			concat()
				组合多个被观察者一起发送数据，组合后 按发送顺序串行执行，组合被观察者<=4个
			concatArray()
				组合多个被观察者一起发送数据，组合后 按发送顺序串行执行，组合被观察者>4个
		按时间发送 
			merge()
				组合多个被观察者一起发送数据，组合后 按时间线并行执行，组合被观察者<=4个
			mergeArray()
				组合多个被观察者一起发送数据，组合后 按时间线并行执行，组合被观察者>4个
		错误处理
			concatDelayError()
				将错误时间延迟到发送后再执行
			mergeDelayError()
				将错误时间延迟到发送后再执行
	2. 合并多个事件
		按数量
			zip()
				合并多个被观察者 发送事件 ，生成一个新的事件序列，并最终发送
					1.事件的合并方式=严格按照原先事件序列进行对位合并
2. 最终合并的事件数量=多个被观察者中数量最少的数量
		按时间
			combineLatest()
				当两个observable 中的任意一个发送了数据后，将先发送数据 的observable 的最后一个数据	与另外一个 observable 发送的每一个事件结合，最终基于该函数的结果发送数据
					与zip区别：
1. zip 按个数合并 ，即1对1 合并
2. combineLatest 按时间合并，即在最后一个时间点上合并
			combineLatestDelayError()
				类似于 concatDelayError ,即错误处理在 事件处理完后执行
		合并成一个事件发送
			reduce() 聚合
				将被观察者合并为 一个事件后&发送 
					聚合的逻辑根据需求撰写，但本质是 前两个数据聚合后，与后一个数据继续进行聚合，以此类推
			collect()
				将被观察者发送的事件收集到一个数据集合里
					收集的逻辑是 将孤立的对象 放在一个集合里处理
	3. 发送事件前追加发送事件
		startWith()
			在一个被观察者发送事件前，追加 事件
		startWithArray()
			在一个被观察者发送事件前，追加 事件
		追加数据顺序，后调用先追加
	4. 统计发送事件数量
		count()
			统计被观察者发送事件的数量
				返回结果 Long
	组合 是不对原有的内容进行变化，合并 会对原有的数据进行改变；组合类似 list,合并类似set .

```

应用场景：

     组合多个被观察者
     合并多个事件
     发送事件前追加发送事件
     统计发送事件数量
     
![image](https://upload-images.jianshu.io/upload_images/944365-ff482b5b1d2a097c.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)
![image](https://upload-images.jianshu.io/upload_images/944365-a7b33256c9f07fac.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)
![image](https://upload-images.jianshu.io/upload_images/944365-214478680237ffb8.png?imageMogr2/auto-orient/)

## 1.4 功能性操作符

```
功能性操作符总结
	1. 连接观察者和被观察者
		即订阅，subscribe()
			在订阅的时候才能 连接观察者和被观察者，才会产生回调，产生事件流
	2. 线程调度--快速、方便指定 & 控制被观察者 & 观察者 的工作线程
		subscribeOn()
			指定被观察者 发送事件的线程（传入RxJava内置的线程类型）
			多个subscribeOn() 只有第一次 指定线程有效   
				被观察者的线程 = 第一次指定的线程 = 新的工作线程，第二次指定的线程（主线程）无效
		observerOn()
			指定观察者 接收 & 响应事件的线程（传入RxJava内置的线程类型）
			多次指定观察者 接收 & 响应事件的线程，则每次指定均有效，即每指定一次，就会进行一次线程的切换
		1.被观察者 （Observable） / 观察者（Observer）的工作线程 = 创建自身的线程
2.若被观察者 （Observable） / 观察者（Observer）在主线程被创建，那么他们的工作（生产事件 / 接收& 响应事件）就会发生在主线程
		1. 解决的问题：对于一般的需求场景，需要在子线程中实现耗时的操作；然后回到主线程实现 UI操作
2. 解决方案：
2.1被观察者 （Observable） 在 子线程 中生产事件（如实现耗时操作等等）
2.2观察者（Observer）在 主线程 接收 & 响应事件（即实现UI操作）
		实现的方式：
采用 RxJava内置的线程调度器（ Scheduler ），即通过 功能性操作符subscribeOn（） & observeOn（）实现
	3. 延迟操作
		delay()
			需求场景:即在被观察者发送事件前进行一些延迟的操作
			作用:使得被观察者延迟一段时间再发送事件
			重载方法：
				1. 指定延迟时间
delay(long delay,TimeUnit unit)
 参数1 = 时间；参数2 = 时间单位
				2. 指定延迟时间 & 调度器
delay(long delay,TimeUnit unit,mScheduler scheduler)
// 参数1 = 时间；参数2 = 时间单位；参数3 = 线程调度器
				3. 指定延迟时间  & 错误延迟
 错误延迟，即：若存在Error事件，则如常执行，执行后再抛出错误异常
delay(long delay,TimeUnit unit,boolean delayError)
// 参数1 = 时间；参数2 = 时间单位；参数3 = 错误延迟参数

				4. 指定延迟时间 & 调度器 & 错误延迟
指定延迟多长时间并添加调度器，错误通知可以设置是否延迟
delay(long delay,TimeUnit unit,mScheduler scheduler,boolean delayError)
参数1 = 时间；参数2 = 时间单位；参数3 = 线程调度器；参数4 = 错误延迟参数
	4. 在事件的生命周期中操作
		do()
在事件发送 & 接收的整个生命周期过程中进行操作
rg：如发送事件前的初始化、发送事件后的回调请求等
		分类
			1. 当	Observable每发送一次数据事件就会调用一次
				doOnEach()  在 onNext()之前(doOnNext()之前)
				含 onNext() , onError() ,onCompleted()
			2.Next事件 
				执行onNext 之前调用 
					doOnNext()
				执行onNext 之后调用 
					doAfterNext()
			3. 发送事件完毕后调用
				发送错误事件时
					doOnError()  
onError() 之前
				正常发送事件完毕后
					doOnCompleted()
				无论正常发送完毕 / 异常终止
					doAfterTerminate（） 
在 doFinally() 之后
				最后执行
					doFinally()
			4. 订阅相关
				观察者订阅时调用
					doOnSubscribe()
				观察者取消订阅时调用
					doOnUnsubscribe()
	5. 错误处理
		发送事件过程中，遇到错误时的处理机制
		解决方案1：发送数据
			发送一个特殊事件&正常终止
				onErrorReturn()
类似转换 ，拦截 Throwable ，并将 Throwable 转为 其他类型，重走 onNext() 方法
			发送一个新的 Observable
				onErrorResumeNext()
					onErrorResumeNext（）拦截的错误 = Exception,返回 Observable 对象
				onExceptionResumeNext()
					直接拦截所有的 Exception,本身就是 Observable 对象
		解决方案2：重试

			重试 retry()
重试，即当出现错误时，让被观察者（Observable）重新发射数据
				<-- 1. retry（） -->
// 作用：出现错误时，让被观察者重新发送数据
// 注：若一直错误，则一直重新发送

<-- 2. retry（long time） -->
// 作用：出现错误时，让被观察者重新发送数据（具备重试次数限制
// 参数 = 重试次数
 
<-- 3. retry（Predicate predicate） -->
// 作用：出现错误后，判断是否需要重新发送数据（若需要重新发送& 持续遇到错误，则持续重试）
// 参数 = 判断逻辑

<--  4. retry（new BiPredicate<Integer, Throwable>） -->
// 作用：出现错误后，判断是否需要重新发送数据（若需要重新发送 & 持续遇到错误，则持续重试
// 参数 =  判断逻辑（传入当前重试次数 & 异常错误信息）

<-- 5. retry（long time,Predicate predicate） -->
// 作用：出现错误后，判断是否需要重新发送数据（具备重试次数限制
// 参数 = 设置重试次数 & 判断逻辑

			让 Observable 重新订阅
				retryUtil()
出现错误后，判断是否需要重新发送数据
					若需要重新发送 & 持续遇到错误，则持续重试
					作用类似于retry（Predicate predicate）
具体使用类似于retry（Predicate predicate），唯一区别：返回 true 则不重新发送数据事件
			retryWhen（）
遇到错误时，将发生的错误传递给一个新的被观察者（Observable），并决定是否需要重新订阅原始被观察者（Observable）& 发送事件
	6. 重复发送操作
		repeat（）
无条件重复发送
			repeat（）无限发送
repeate(int i)  重复发送i次
		repeatWhen（）
有条件循环重复发送
			原理
将原始 Observable 停止发送事件的标识（Complete（） /  Error（））转换成1个 Object 类型数据传递给1个新被观察者（Observable），以此决定是否重新订阅 & 发送原来的 Observable
			1.若新被观察者（Observable）返回1个Complete / Error事件，则不重新订阅 & 发送原来的 Observable
2.若新被观察者（Observable）返回其余事件时，则重新订阅 & 发送原来的 Observable
```



类型 | 含义| 应用场景
---|---|---
Schedulers.immediate() | 当前线程 = 不指定线程|默认
AndroidSchedulers.mainThread() | Android主线程|操作UI
Schedulers.newThread() | 常规新线程|耗时等操作
Schedulers.io() | 	io操作线程|网络请求、读写文件等io密集型操作
Schedulers.computation() | CPU计算操作线程|大量计算操作
> 注：RxJava内部使用 线程池 来维护这些线程，所以线程的调度效率非常高。


![image](https://upload-images.jianshu.io/upload_images/944365-3997d7540acda975.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)


## 1.5过滤操作符


```
过滤操作符
	1. 根据指定条件过滤事件 
		filter(Predicate p):
过滤 特定条件的事件
			传入 Predicate 对象，其实现test() 方法，其返回值为boolean 类型，返回 true 继续发送；返回 false 即不发送
		ofType() 
过滤 特定数据类型的数据
			ofType(xx.class) 传入的是  类.class   对象
		skip（	long i） / skipLast（long i ）
跳过某个事件
常用两种构造方法 (long count)/(long time,@NotNull TimeUnit unit)
			skip（long  i）跳过正序前i个事件
 skipLast（long  i ）跳过正序后i项
			skip(long time,@NotNull TimeUnit unit)  跳过time 秒发送的数据
 skipLast(long time,@NotNull TimeUnit unit) 跳过最后time 秒发送的数据
		distinct（） / distinctUntilChanged（）
过滤事件序列中重复的事件 / 连续重复的事件
	2. 根据指定数量过滤事件
		take（） & takeLast（）
通过设置指定的事件数量，仅发送特定数量的事件
			take（）指定观察者最多能接收到的事件数量
			takeLast（）
指定观察者只能接收到被观察者发送的最后几个事件
	3.根据指定时间过滤事件
		throttleFirst（）/ throttleLast（）
在某段时间内，只发送该段时间内第1次事件 / 最后1次事件
常用的构造方法内参数：（long windowDuration ,@NotNull TimeUnit unit）
eg： （1，TimeUnit.SECOND）表示每s 采用数据
			throttleFirst（）
在某段时间内，只发送该段时间内第1次事件
			throttleLast（）
在某段时间内，只发送该段时间内第1次事件
		Sample（）
在某段时间内，只发送该段时间内最新（最后）1次事件
			仅需要把上文的 throttleLast（） 改成Sample（）操作符即可
		throttleWithTimeout （） / debounce（）
发送数据事件时，若2次发送事件的间隔＜指定时间，就会丢弃前一次的数据，直到指定时间内都没有新数据发射时才会发送后一次的数据
构造参数： （long  timeout,@NotNull TimeUnit unit）
eg ：debounce(1, TimeUnit.SECONDS)//每1秒中采用数据
	4. 根据指定事件位置过滤事件
		firstElement（） / lastElement（）
仅选取第1个元素 / 最后一个元素
		elementAt（）
指定接收某个元素（通过 索引值 确定）
注：允许越界，即获取的位置索引 ＞ 发送事件序列长度
eg: elementAt(6,10)   获取的位置索引 ＞ 发送事件序列长度时，设置默认参数  该显示默认为 10 
		elementAtOrError（）
在elementAt（）的基础上，当出现越界情况（即获取的位置索引 ＞ 发送事件序列长度）时，即抛出异常
```
![过滤型操作符](http://upload-images.jianshu.io/upload_images/944365-83fb8e7038dfd51a.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

## 1.6 布尔操作符


```
布尔操作符
	1. 作用   通过设置函数，判断被观察者（Observable）发送的事件是否符合条件
	2. 类型
		all（）
			判断发送的每项数据是否都满足 设置的函数条件
若满足，返回 true；否则，返回 false
		takeWhile（）
			判断发送的每项数据是否满足 设置函数条件
若发送的数据满足该条件，则发送该项数据；否则不发送
		skipWhile（）
			判断发送的每项数据是否满足 设置函数条件
1. 直到该判断条件 = false时，才开始发送Observable的数据
2.与takeWhile 相反
		takeUntil（）
			执行到某个条件时，停止发送事件
		skipUntil（）
			等到 skipUntil（） 传入的Observable开始发送数据，（原始）第1个Observable的数据才开始发送数据
		SequenceEqual（）
			判定两个Observables需要发送的数据是否相同
    若相同，返回 true；否则，返回 false
		contains（）
			判断发送的数据中是否包含指定数据
1.若包含，返回 true；否则，返回 false
2.内部实现 = exists（）
		isEmpty（）
			判断发送的数据是否为空
若为空，返回 true；否则，返回 false
		amb（）
			当需要发送多个 Observable时，只发送 先发送数据的Observable的数据，而其余 Observable则被丢弃。
		defaultIfEmpty（）
			在不发送任何有效事件（ Next事件）、仅发送了 Complete 事件的前提下，发送一个默认值
```

![布尔操作符](http://upload-images.jianshu.io/upload_images/944365-3234d4bc974afbc8.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
