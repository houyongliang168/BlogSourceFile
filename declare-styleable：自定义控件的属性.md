---
title: declare-styleable：自定义控件的属性 20180312
---
参考：
[declare-styleable：自定义控件的属性](http://blog.csdn.net/congqingbin/article/details/7869730)


## 一.基本使用
1.1 在res/values文件下定义一个attrs.xml文件，代码如下：
```bash
<?xml version="1.0" encoding="utf-8"?> 
<resources> 
    <declare-styleable name="ToolBar"> 
        <attr name="buttonNum" format="integer"/> 
        <attr name="itemBackground" format="reference|color"/> 
    </declare-styleable> 
</resources>
注：在res-vlaues文件夹下创建资源文件attrs.xml或则自定义一个资源文件xx.xml，都可以。
```
1.2 在布局xml中如下使用该属性:

```bash
<?xml version="1.0" encoding="utf-8"?> 
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android" 
    xmlns:toolbar="http://schemas.android.com/apk/res/cn.zzm.toolbar" 
    android:orientation="vertical" 
    android:layout_width="fill_parent" 
    android:layout_height="fill_parent" 
    > 
    <cn.zzm.toolbar.ToolBar android:id="@+id/gridview_toolbar" 
        android:layout_width="fill_parent" 
        android:layout_height="wrap_content" 
        android:layout_alignParentBottom="true" 
        android:background="@drawable/control_bar" 
        android:gravity="center" 
        toolbar:buttonNum="5" 
        toolbar:itemBackground="@drawable/control_bar_item_bg"/> 
</RelativeLayout>
```

1.3 在自定义组件中，可以如下获得xml中定义的值：
```bash
TypedArray a = context.obtainStyledAttributes(attrs,R.styleable.ToolBar); 
buttonNum = a.getInt(R.styleable.ToolBar_buttonNum, 5); 
itemBg = a.getResourceId(R.styleable.ToolBar_itemBackground, -1);

a.recycle();
就这么简单的三步，即可完成对自定义属性的使用。
```

```bash
首先来看看attrs.xml文件。

该文件是定义属性名和格式的地方，需要用<declare-styleable name="ToolBar"></declare-styleable>包围所有属性。其中name为该属性集的名字，主要用途是标识该属性集。那在什么地方会用到呢？主要是在第三步。看到没？在获取某属性标识时，用到"R.styleable.ToolBar_buttonNum"，很显然，他在每个属性前面都加了"ToolBar_"。

在来看看各种属性都有些什么类型吧：string , integer , dimension , reference , color , enum......

前面几种的声明方式都是一致的，例如：<attr name="buttonNum" format="integer"/>。 
只有enum是不同的，用法举例：

<attr name="testEnum"> 
    <enum name="fill_parent" value="-1"/> 
    <enum name="wrap_content" value="-2"/> 
</attr>

如果该属性可同时传两种不同的属性，则可以用“|”分割开即可。

 

让我们再来看看布局xml中需要注意的事项。

首先得声明一下：xmlns:toolbar=http://schemas.android.com/apk/res/cn.zzm.toolbar 
注意，“toolbar”可以换成其他的任何名字，后面的url地址必须最后一部分必须用上自定义组件的包名。自定义属性了，在属性名前加上“toolbar”即可。

 

最后来看看java代码中的注意事项。

在自定义组件的构造函数中，用

TypedArray a = context.obtainStyledAttributes(attrs,R.styleable.ToolBar);

来获得对属性集的引用，然后就可以用“a”的各种方法来获取相应的属性值了。这里需要注意的是，如果使用的方法和获取值的类型不对的话，则会返回默认值。因此，如果一个属性是带两个及以上不用类型的属性，需要做多次判断，知道读取完毕后才能判断应该赋予何值。当然，在取完值的时候别忘了回收资源哦！
```
## 二. 自定义属性数据类型简介：
1. reference：参考指定Theme中资源ID。
```bash
1.定义：
    <declare-styleable name="My">
        <attr name="label" format="reference" >
    </declare-styleable>
2.使用：

    <Buttonzkx:label="@string/label" >
注意：由于reference是从资源文件中获取：所以在XML文件中写这个属性的时候必须 personattr:name="@string/app_name"这种格式，否则会出错
```

2. Color：颜色
```bash
1.定义：
   <declare-styleable name="My">
        <attr name="textColor" format="color" />
    </declare-styleable>
2.使用：

     <Button zkx:textColor="#ff0000"/>
```

3. boolean：布尔值
```bash
1.定义：
     <declare-styleable name="My">
        <attr name="isVisible" format="boolean" />
    </declare-styleable>
2.使用：

      <Button zkx:isVisible="false"/>
```

4. dimension：尺寸值
```bash
1.定义：
     <declare-styleable name="My">
        <attr name="myWidth" format="dimension" />
    </declare-styleable>
2.使用：

     <Button zkx:myWidth="100dip"/>
```

5. float：浮点型
```bash
1.定义：
    <declare-styleable name="My">
        <attr name="fromAlpha" format="float" />
    </declare-styleable>
2.使用：

    <alpha zkx:fromAlpha="0.3"/>
```

6. integer：整型
```bash
1.定义：
    <declare-styleable name="My">
        <attr name="frameDuration" format="integer" />
    </declare-styleable>
2.使用：

     <animated-rotate zkx:framesCount="22"/>
```

7. string：字符串
```bash
1.定义：
     <declare-styleable name="My">
        <attr name="Name" format="string" />
    </declare-styleable>
2.使用：

     <rotate zkx:pivotX="200%"/>
```

8. fraction：百分数
```bash
1.定义：
    <declare-styleable name="My">
        <attr name="pivotX" format="fraction" />
    </declare-styleable>
2.使用：

     <rotate zkx:Name="My name is zhang kun xiang"/>
```

9. enum：枚举
```bash
1.定义：
     <declare-styleable name="My">
        <attr name="language">
            <enum name="English" value="1"/>
        </attr>
    </declare-styleable>
2.使用：

   <Button zkx:language="English"/>
```

10. flag：位或运算
```bash
1.定义：
    <declare-styleable name="My">
        <attr name="windowSoftInputMode">
    	<flag name="stateUnspecified" value="1" />
    	<flag name = "adjustNothing" value = "0x30" />
        </attr>
    </declare-styleable>
数据多点：
    <declare-styleable name="名称">
        <attr name="windowSoftInputMode">
            <flag name = "stateUnspecified" value = "0" />
            <flag name = "stateUnchanged" value = "1" />
            <flag name = "stateHidden" value = "2" />
            <flag name = "stateAlwaysHidden" value = "3" />
            <flag name = "stateVisible" value = "4" />
            <flag name = "stateAlwaysVisible" value = "5" />
            <flag name = "adjustUnspecified" value = "0x00" />
            <flag name = "adjustResize" value = "0x10" />
            <flag name = "adjustPan" value = "0x20" />
            <flag name = "adjustNothing" value = "0x30" />
        </attr>
    </declare-styleable>
2.使用：

     <activity android:windowSoftInputMode="stateUnspecified | adjustNothing">
数据多点：
    <activity
        android:name = ".StyleAndThemeActivity"
        android:label = "@string/app_name"
        android:windowSoftInputMode = "stateUnspecified | stateUnchanged　|　stateHidden">
        <intent-filter>
            <action android:name = "android.intent.action.MAIN" />
            <category android:name = "android.intent.category.LAUNCHER" />
        </intent-filter>
    </activity>
```

11. 属性定义时可以指定多种类型值：
```bash
1.定义：
     <declare-styleable name = "名称">
	<attr name="background" format="reference|color" />
    </declare-styleable>
2.使用：

       <ImageView android:background = "@drawable/图片ID|#00FF00"/>
```

