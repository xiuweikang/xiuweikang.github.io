title: Android Training重点
date: 2015-12-06 23:35:10
tags: Android
---


## 适配

Android屏幕有两种属性,size 和 density

> size: small,normal,large,xlarge
> density: low (ldpi), medium (mdpi), high (hdpi), extra high (xhdpi)

屏幕的布局(landscape or portrait)也被认为是不同的

Android系统会根据当前设备的信息来获取相应的资源。 例如将某个布局资源放在res/layout-large下时，当屏幕为large时，会取这里的。

[提供资源列表](http://developer.android.com/intl/zh-cn/guide/topics/resources/providing-resources.html#BestMatch)



> 文字：txtName.setText(getResources().getText(R.string.name));

> 图片：imgIcon.setBackgroundDrawableResource(R.drawable.icon);

> 颜色：txtName.setTextColor(getResouces().getColor(R.color.red));

> 布局：setContentView(R.layout.main);

> 控件：txtName = (TextView)findViewById(R.id.txt_name);

## View相关

### View基础


### ActionBar
ActionBar的overflow 并不是在每个设备上都显示的，如果有menu物理键时则不会显示，可以通过如下设置：

``` java
private void setOverflowShowingAlways() {
	try {
		ViewConfiguration config = ViewConfiguration.get(this);
		Field menuKeyField = ViewConfiguration.class.getDeclaredField("sHasPermanentMenuKey");
		menuKeyField.setAccessible(true);
		menuKeyField.setBoolean(config, false);
	} catch (Exception e) {
		e.printStackTrace();
	}
}
```

### LayoutInflater

> inflate(int resource, ViewGroup root, boolean attachToRoot) 

> 1. 如果root为null，attachToRoot将失去作用，设置任何值都没有意义。
2. 如果root不为null，attachToRoot设为true，则会给加载的布局文件的指定一个父布局，即root。
3. 如果root不为null，attachToRoot设为false，则会将布局文件最外层的所有layout属性进行设置，当该view被添加到父view当中时，这些layout属性会自动生效。



### LinearLayout
1. **android:layout_weight**的真实含义是:一旦View设置了该属性(假设有效的情况下)，那么该 View的宽度等于原有宽度(android:layout_width)加上剩余空间的占比！对于所有的View默认的权重是0，如果只设置了一个View的权重大于0，则该View将占据除去别的View本身占据的空间的所有剩余空间。因此这里设置EditText的权重为1，使其能够占据除了按钮之外的所有空间。



2. 当 android:orientation=”vertical” 时， 只有水平方向的设置才起作用，垂直方向的设置不起作用。 
即：layout_gravity = left，right，center_horizontal 是生效的。 
当 android:orientation=”horizontal” 时， 只有垂直方向的设置才起作用，水平方向的设置不起作用。 
即：top，bottom，center_vertical 是生效的。
3.**margin与padding的区别**:首先margin代表的是偏移,比如marginleft = “5dp”表示组件离容器左边缘偏移5dp; 而padding代表的则是填充,而填充的对象针对的是组件中的元素,比如TextView中的文字 
比如为TextView设置paddingleft = “5dp”,则是在组件里的元素的左边填充5dp的空间！ 
margin针对的是容器中的组件，而padding针对的是组件中的元素，要区分开来！

### TextView


``` java
public class MainActivity extends Activity {  
    private TextView txtZQD;  

    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
        txtZQD = (TextView) findViewById(R.id.txtZQD);  
        Drawable[] drawable = txtZQD.getCompoundDrawables();  
        // 数组下表0~3,依次是:左上右下  
        drawable[1].setBounds(100, 0, 200, 200);  
        txtZQD.setCompoundDrawables(drawable[0], drawable[1], drawable[2],  
                drawable[3]);  
    }  
}  

```
> ①Drawable[] drawable = txtZQD.getCompoundDrawables( ); 
获得四个不同方向上的图片资源,数组元素依次是:左上右下的图片
②drawable1.setBounds(100, 0, 200, 200); 
接着获得资源后,可以调用setBounds设置左上右下坐标点,比如这里设置了代表的是: 
长是:从离文字最左边开始100dp处到200dp处 
宽是:从文字上方0dp处往上延伸200dp!
③txtZQD.setCompoundDrawables(drawable[0], drawable1, drawable2, 
drawable3);为TextView重新设置drawable数组!没有图片可以用null代替哦! 
PS：另外，从上面看出我们也可以直接在Java代码中调用setCompoundDrawables为 
TextView设置图片！


* TextView在XML中可以设置`autoLink`属性，值如果是all，则根据协议头匹配。 在java代码中：

``` java
TextView txt = (TextView) findViewById(R.id.txt_link);
txt.setAutoLinkMask(Linkify.ALL);
txt.setMovementMethod(LinkMovementMethod.getInstance());//必须存在，此方法在需要响应用户事件时使用，如点击一个电话号码就跳转到拨号页面。如果不执行这个方法是不会响应事件的，即便文本看着已经是下划线蓝色字了
        
```

* TextView同时还支持Html标签，通过`setText(Html.fromHtml(....))` 设置。常用有：

		<font>：设置颜色和字体。
		<big>：设置字体大号
		<small>：设置字体小号
		<i><b>：斜体粗体
		<a>：连接网址
		<img>：图片


``` java
TextView t1 = (TextView)findViewById(R.id.txtOne);
String s1 = "<font color='blue'><b>百度一下，你就知道~：</b></font><br>";
s1 += "<a href = 'http://www.baidu.com'>百度</a>";
t1.setText(Html.fromHtml(s1));
t1.setMovementMethod(LinkMovementMethod.getInstance());//如果想链接有效时设置        

```

* 除了上面的HTML可以定制我们TextView的样式外，还可以使用SpannableString和SpannableStringBuilder来完成，两者区别：前者针对的是不可变文本，而后者则是针对可变文本

- SpannableString可供我们使用的API有下面这些：

  - BackgroundColorSpan 背景色

  - ClickableSpan 文本可点击，有点击事件

  - ForegroundColorSpan 文本颜色（前景色）

  - MaskFilterSpan 修饰效果，如模糊(BlurMaskFilter)、浮雕(EmbossMaskFilter)

  - MetricAffectingSpan 父类，一般不用

  - RasterizerSpan 光栅效果

  - StrikethroughSpan 删除线（中划线）

  - SuggestionSpan 相当于占位符

  - UnderlineSpan 下划线

  - AbsoluteSizeSpan 绝对大小（文本字体）

  - DynamicDrawableSpan 设置图片，基于文本基线或底部对齐。

  - ImageSpan 图片

  - RelativeSizeSpan 相对大小（文本字体）

  - ReplacementSpan 父类，一般不用

  - ScaleXSpan 基于x轴缩放

  - StyleSpan 字体样式：粗体、斜体等

  - SubscriptSpan 下标（数学公式会用到）

  - SuperscriptSpan 上标（数学公式会用到）

  - TextAppearanceSpan 文本外貌（包括字体、大小、样式和颜色）

  - TypefaceSpan 文本字体

  - URLSpan 文本超链接

- 实现滚动效果 

``` xml
android:ellipsize="marquee"
android:focusable="true"
android:focusableInTouchMode="true"
```      
- 字间距：

  - android:textScaleX：调节字间距的，默认值1.0f，值是float
  - Java中setScaleX(2.0f); 
  
- 行间距： 
Android系统中TextView默认显示中文时会比较紧凑，为了让每行保持的行间距

  - android:lineSpacingExtra：设置行间距，如”3dp” 
  - android:lineSpacingMultiplier：设置行间距的倍数，如”1.2”
  - Java代码中可以通过: setLineSpacing方法来设置


### EditText

- android:selectAllOnFocus="true" 当EditText获得焦点后会全选文本
- inputType 限制输入类型 如 phone
- EditText默认是多行显示的，并且能够自动换行，即当一行显示不完的时候，他会自动换到第二行
- 另外EditText还为我们提供了设置英文字母大写类型的属性：android:capitalize 
默认none，提供了三个可选值：
  - sentences：仅第一个字母大写
  - words：每一个单词首字母大小，用空格区分单词
  - characters:每一个英文字母都大写
- android:windowSoftInputMode 
Activity主窗口与软键盘的交互模式，可以用来避免输入法面板遮挡问题，Android1.5后的一个新特性。 


### Button

- clickable在xml设置成false时，扔可点击触发事件，在java中则不会触发，enable则完全不可点击，并且有置灰效果
- shape 可以理解成 drawable资源，可以在selector中使用

### ImageView
- ①background通常指的都是背景,而src指的是内容!! 
- ②当使用src填入图片时,是按照图片大小直接填充,并不会进行拉伸 
而使用background填入图片,则是会根据ImageView给定的宽度来进行拉伸 
- Java代码中设置blackground和src属性:
  - 前景(对应src属性):setImageDrawable( ); 
  - 背景(对应background属性):setBackgroundDrawable( );


### ViewPager
- mViewPager.setOffscreenPageLimit(1); 可以保存一页的状态，在第一次时，会调用两个页面的响应的oncreate()，onResume(). 默认是1.
- 和ViewFlipper的区别：ViewFlipper继承ViewAnimator，切换view的时候是有动画效果的，适合做ppt，多界面的程序欢迎引导界面，算是个轻量级的组件，适合展示静态数据，少量数据。
ViewPager继承ViewGroup。看官网描述，这货和Fragment是好搭档，Fragment有自己的生命周期。也就是说ViewPager更适用复杂的视图切换，而且Viewpager有自己的adapter，这也让其适应复杂对象，实现数据的动态加载。





 
