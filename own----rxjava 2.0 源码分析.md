---
title: Rxjava* 2.0 源码分析
---
***Rxjava* 2.0 源码分析** 

参考：Android RxJava 2.0：手把手带你 源码分析RxJava  [https://www.jianshu.com/p/e1c48a00951a](http://note.youdao.com/)


版本：
> compile 'io.reactivex.rxjava2:rxjava:2.0.1'
compile 'io.reactivex.rxjava2:rxandroid:2.0.1'




##  步骤1：创建被观察者(Observable)& 定义需发送的事件



源码分析：

```
/** 
  * 使用步骤1：创建被观察者(Observable)& 定义需发送的事件
  **/
 Observable.create(new ObservableOnSubscribe<Integer>() {
            // 2. 在复写的subscribe（）里定义需要发送的事件
            @Override
            public void subscribe(ObservableEmitter<Integer> emitter) throws Exception {
                emitter.onNext(1);
                emitter.onNext(2);
                emitter.onNext(3);
                emitter.onComplete();
            }
        }
        // 内部类 实现 subscribe 的方法 ObservableOnSubscribe 对象
        );


/** 
  * 源码分析：Observable.create(new ObservableOnSubscribe<Integer>(){...}）
  **/
 
  @SchedulerSupport(SchedulerSupport.NONE)
    public static <T> Observable<T> create(ObservableOnSubscribe<T> source) {
       //非关键代码 ObjectHelper.requireNonNull(source, "source is null");
        //关键代码：
        return RxJavaPlugins.onAssembly(new ObservableCreate<T>(source));
        //   创建 Observable 类 子对象 ->>分析1
        // 注：传入source对象（即我们手动创建的ObservableOnSubscribe对象）
        
    }
    
 /** 
    * 分析1：RxJavaPlugins.onAssembly(new ObservableCreate<T>(source))
    **/

    public static <T> Observable<T> onAssembly(Observable<T> source) {
        Function<Observable, Observable> f = onObservableAssembly;
        // 实例化 Function 对象 ，对 Function 对象赋值
        //第一次 未赋值 f=null
        //f 赋值后  apply(f, source); ->> 分析2
        if (f != null) {
            return apply(f, source);
        }
        
        return source;
        //第一次 未赋值 返回的是 ObservableCreate 对象 ->> 分析4 
      
    }
    
    
/**
 *分析2 apply(f, source)
 */
    
   static <T, R> R apply(Function<T, R> f, T t) {
        try {
            return f.apply(t);
            //返回 R 对象  继续查看代码 ->> 分析3
        } catch (Throwable ex) {
            throw ExceptionHelper.wrapOrThrow(ex);
        }
    } 
  
     
/**
 *分析3 f.apply(t)
 */ 
  public interface Function<T, R> {
    /**
     * Apply some calculation to the input value and return some other value.
     * @param t the input value
     * @return the output value
     * @throws Exception on error
     */
    R apply(T t) throws Exception;
    // 如果 function 有实例 ，会调用其 apply 方法 ，返回 R 对象
}
  
 
     
/**
 *分析4  new ObservableOnSubscribe<T> 对象
 
 ObservableOnSubscribe 对象有 两个 静态内部类 和一个方法
 */    
 
 public final class ObservableCreate<T> extends Observable<T> {
 //ObservableCreate 为Observable 的子类
    final ObservableOnSubscribe<T> source;
   // 仅贴出关键源码
   
     // 构造函数
     // 传入了传入source对象 = 手动创建的ObservableOnSubscribe对象
    public ObservableCreate(ObservableOnSubscribe<T> source) {
        this.source = source;
    }
   
   //其内部的 静态内部类 1 其为 序列化的类
  /** emitter.onNext("1");
    * 此处仅讲解subscribe（）实现中的onNext（）
    * onError（）、onComplete()类似，此处不作过多描述*/
static final class CreateEmitter<T>  extends  AtomicReference<Disposable>
    implements ObservableEmitter<T>, Disposable {
    
    
    private static final long serialVersionUID = -3434801548987643227L;
    
    final Observer<? super T> observer;
    //构造方法  传入 的 是观察者对象
    CreateEmitter(Observer<? super T> observer) {
        this.observer = observer;
    }
    
          @Override
        public void onNext(T t) {
             // 注：发送的事件不可为空
            if (t == null) {
                onError(new NullPointerException("onNext called with null. Null values are generally not allowed in 2.x operators and sources."));
                return;
            }
     // 若无断开连接（调用Disposable.dispose()），则调用观察者（Observer）的同名方法 = onNext（）
     // 观察者的onNext（）的内容 = 使用步骤2中复写内容
            if (!isDisposed()) {
                observer.onNext(t);
            }
        }

        @Override
        public void onError(Throwable t) {
            if (t == null) {
                t = new NullPointerException("onError called with null. Null values are generally not allowed in 2.x operators and sources.");
            }
            if (!isDisposed()) {
                try {
                    observer.onError(t);
                } finally {
                    dispose();
                }
            } else {
                RxJavaPlugins.onError(t);
            }
        }

        @Override
        public void onComplete() {
            if (!isDisposed()) {
                try {
                    observer.onComplete();
                } finally {
                    dispose();
                }
            }
        }
    
    
  //其他   
  @Override
    public void dispose() {
        DisposableHelper.dispose(this);
    }

    @Override
    public boolean isDisposed() {
        return DisposableHelper.isDisposed(get());
    }
    
   }
   
     //其内部的 静态内部类 2 其为 序列化的类
 static final class SerializedEmitter<T>    extends AtomicInteger
    implements ObservableEmitter<T> {
    
        
    }
   
   //内部的 主要方法  翻译为 实际订阅
     /** 
      * 重点关注：复写了subscribeActual（）
      * 作用：订阅时，通过接口回调 调用被观察者（Observerable） 与 观察者（Observer）的方法
      **/
   
      @Override
    protected void subscribeActual(Observer<? super T> observer) {
      // 1. 创建1个CreateEmitter对象（封装成1个Disposable对象 ？）
          // 作用：发射事件
        CreateEmitter<T> parent = new CreateEmitter<T>(observer);
        //  2. 调用观察者（Observer）的onSubscribe（）
        // onSubscribe（）的实现 = 使用步骤2（创建观察者（Observer））时复写的onSubscribe(）
        observer.onSubscribe(parent);

        try {
        // 3. 调用source对象的subscribe（）
        // source对象使用步骤1（创建被观察者（Observable））中创建的ObservableOnSubscribe对象 
        // subscribe（）的实现 = 使用步骤1（创建被观察者（Observable））中复写的subscribe（）->>分析4

            source.subscribe(parent);
        } catch (Throwable ex) {
            Exceptions.throwIfFatal(ex);
            parent.onError(ex);
        }
    }
   
   
}
```
![image](https://upload-images.jianshu.io/upload_images/944365-471ce0ccd7c72425.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

##  步骤2：创建观察者 & 定义响应事件的行为


```
/** 
  * 使用步骤2：创建观察者 & 定义响应事件的行为（方法内的创建对象代码）
  **/
  subscribe(new Observer<Integer>() {
      
              @Override
              public void onSubscribe(Disposable d) {
                  Log.d(TAG, "开始采用subscribe连接");
              }
              // 默认最先调用复写的 onSubscribe（）

              @Override
              public void onNext(Integer value) {
                  Log.d(TAG, "对Next事件"+ value +"作出响应"  );
              }

              @Override
              public void onError(Throwable e) {
                  Log.d(TAG, "对Error事件作出响应");
              }

              @Override
              public void onComplete() {
                  Log.d(TAG, "对Complete事件作出响应");
              }

          });


/** 
  * 源码分析：Observer类
  **/
  
  public interface Observer<T> {
  // 注：Observer本质 = 1个接口

  // 接口内含4个方法，分别用于 响应 对应于被观察者发送的不同事件
    void onSubscribe(@NonNull Disposable d); // 内部参数：Disposable 对象，可结束事件
    void onNext(@NonNull T t);
    void onError(@NonNull Throwable e);
    void onComplete();
}


/** 
  * 特别说明：Subscriber类
  * 定义：RxJava 内置的一个实现了 Observer 的抽象类
  * 作用：扩展Observer 接口 = 新增了2个方法 = 
  *      1. onStart()：在还未响应事件前调用，用于初始化工作
  *      2. unsubscribe()：用于取消订阅。在该方法被调用后，观察者将不再接收 & 响应事件
  *      注：调用该方法前，先使用 isUnsubscribed() 判断状态，确定被观察者Observable是否还持有观察者Subscriber的引用；若引用不能及时释放，就会出现内存泄露
  * 使用方式：与Observer使用几乎相同（实质上，Observer总是会先被转换成Subscriber再使用）
  **/
  Subscriber<String> subscriber = new Subscriber<Integer>() {

            @Override
            public void onSubscribe(Subscription s) {
                Log.d(TAG, "开始采用subscribe连接");
            }

            @Override
            public void onNext(Integer value) {
                Log.d(TAG, "对Next事件作出响应" + value);
            }

            @Override
            public void onError(Throwable e) {
                Log.d(TAG, "对Error事件作出响应");
            }

            @Override
            public void onComplete() {
                Log.d(TAG, "对Complete事件作出响应");
            }
        };




```
## 步骤3：通过订阅（subscribe）连接观察者和被观察者

**源码分析**


```
/** 
  * 使用步骤3：通过订阅（subscribe）连接观察者和被观察者 = subscribe（）
  **/
  subscribe(new Observer<Integer>() {
              // 2. 通过通过订阅（subscribe）连接观察者和被观察者
              // 3. 创建观察者 & 定义响应事件的行为
              @Override
              public void onSubscribe(Disposable d) {
                  Log.d(TAG, "开始采用subscribe连接");
              }
              // 默认最先调用复写的 onSubscribe（）

              @Override
              public void onNext(Integer value) {
                  Log.d(TAG, "对Next事件"+ value +"作出响应"  );
              }

              @Override
              public void onError(Throwable e) {
                  Log.d(TAG, "对Error事件作出响应");
              }

              @Override
              public void onComplete() {
                  Log.d(TAG, "对Complete事件作出响应");
              }

          });

/** 
  * 源码分析：Observable.subscribe（observer）
  * 说明：该方法属于 Observable 类的方法（注：传入1个 Observer 对象）
  **/  

  @Override
  public final void subscribe(Observer<? super T> observer) {

 
    // 仅贴出关键源码
   
 //   ObjectHelper.requireNonNull(observer, "observer is null");
        try {
            observer = RxJavaPlugins.onSubscribe(this, observer);

          //  ObjectHelper.requireNonNull(observer, "Plugin returned null Observer");

            subscribeActual(observer);
        } catch (NullPointerException e) { // NOPMD
            throw e;
        } catch (Throwable e) {
            Exceptions.throwIfFatal(e);
            // can't call onError because no way to know if a Disposable has been set or not
            // can't call onSubscribe because the call might have set a Subscription already
            RxJavaPlugins.onError(e);

            NullPointerException npe = new NullPointerException("Actually not, but can't throw other exceptions due to RS");
            npe.initCause(e);
            throw npe;
        }
  }

/** 
  * Observable.subscribeActual(observer)
  * 说明：属于抽象方法，由子类实现；此处的子类 = 步骤1创建被观察者（Observable）时创建的ObservableCreate类
  * 即 在订阅时，实际上是调用了步骤1创建被观察者（Observable）时创建的ObservableCreate类里的subscribeActual（）
  * 此时，你应该回头看上面的步骤1里的subscribeActual()，应该能理解RxJava的整个订阅流程了。
  **/
  protected abstract void subscribeActual(Observer<? super T> observer);


//对观察者 进行了多次处理  ，f 为空 则用原来的 ，如果 不为空 则走 apply（..,..,..）方法
  public static <T> Observer<? super T> onSubscribe(Observable<T> source, Observer<? super T> observer) {
        BiFunction<Observable, Observer, Observer> f = onObservableSubscribe;
        if (f != null) {
            return apply(f, source, observer);
        }
        return observer;
    }

```
![image](https://upload-images.jianshu.io/upload_images/944365-6976f423cfd292cd.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

## 4. 源码总结
- 在步骤1（创建被观察者（Observable））、步骤2（创建观察者（Observer））时，仅仅只是定义了发送的事件 & 响应事件的行为；
- 只有在步骤3（订阅时），才开始发送事件 & 响应事件，真正连接了被观察者 & 观察者
- 具体源码总结如下
 ![image](https://upload-images.jianshu.io/upload_images/944365-25bb5453a7115bb3.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/700)

## 5. 特别注意：涉及多个被观察者（Observable）的发送事件顺序
![image](https://upload-images.jianshu.io/upload_images/944365-a07f6c33a70b7f30.jpg?imageMogr2/auto-orient/)

实例讲解：

```
/** 
  * 存在涉及多个被观察者（Observable）的情况
  **/
  
    // 创建第1个被观察者（Observable1）
    Observable.create(new ObservableOnSubscribe<Integer>() {
                @Override
                public void subscribe(ObservableEmitter<Integer> emitter) throws Exception {
                    emitter.onNext(1);
                    emitter.onNext(2);
                    emitter.onNext(3);
                }
            })
            // 使用flatMap操作符（内部会创建第2个被观察者（Observable2））
            .flatMap(new Function<Integer, ObservableSource<String>>() {
                @Override
                public ObservableSource<String> apply(Integer integer) throws Exception {
                    final List<String> list = new ArrayList<>();
                    for (int i = 0; i < 3; i++) {
                        list.add("我是事件" + integer + "拆分后的子事件" + i);
                        // 通过flatMap中将被观察者生产的事件序列先进行拆分，再将每个事件转换为一个新的发送三个String事件
                        // 最终合并，再发送给被观察者
                    }
                    return Observable.fromIterable(list);
                }

            })
            .subscribe(new Observer<String>() {
                @Override
                public void onSubscribe(Disposable d) {
                    Log.d(TAG, "开始采用subscribe连接");
                }
                // 默认最先调用复写的 onSubscribe（）

                @Override
                public void onNext(String value) {
                    Log.d(TAG, "响应事件："+ value   );
                }

                @Override
                public void onError(Throwable e) {
                    Log.d(TAG, "对Error事件作出响应");
                }

                @Override
                public void onComplete() {
                    Log.d(TAG, "对Complete事件作出响应");
                }
            });
            // 过程讲解
            // 调用顺序：先回调Observable2的subscribe(Observer) 、subscribeActual(Observer)、再回调Observable1的subscribe(Observer) 、subscribeActual(Observer)
            // Observable的发送顺序 = 先发送Observable1、再发送Observable2


```
测试结果：
![image](https://upload-images.jianshu.io/upload_images/944365-f2e8da22433181a5.png?imageMogr2/auto-orient/)