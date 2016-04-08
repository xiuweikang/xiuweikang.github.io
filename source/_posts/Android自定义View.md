title: Android自定义View
date: 2015-10-19 15:36:40
tags: Android
---
因为Android的UI 大部分都是直接通过xml文件的，但是在xml文件里我们只是写明了 一些键值对，例如 layout_height = "100dp" 一个view的显示绘制逻辑则是在类中实现。

自定义View分为几个步骤:

第一步：在valuse/arrts创建我们view的属性

``` xml
<resources>
<declare-styleable name="customview">
     <attr name="text" format="string" />
     <attr name="testArray" format="integer" />
</declare-styleable>
</resources>
```
也可直接，不过在获取时略微麻烦

``` xml
<attr name="text" format="string" />
<attr name="testArray" format="integer" />
```

第二步：在我们的自定义的view的构造方法中获取这些属性对应的值，这些值就是被我们定义在布局文件里的：例如：

```xml
<com.example.kevin.myapplication.MyView
 		android:layout_width="150dp"
        android:layout_height="120dp"
        spa:testArray="520"
        spa:text="hello world" />
        
```

内部是通过解析xml将其封装在`AttributeSet`对象里，并传入view的构造参数里。因此我们在view的构造函数里是可以直接获取到定义的各个属性的值的。但是一般不推荐，因为当一些值是引用时，获取的是id,我们还需要进行转化。所以大部分是直接 

TypedArray ta =context.obtainStyledAttributes(attrs,R.styleable.customview);

此时declare-styleable：
```java
TypedArray ta = context.obtainStyledAttributes(attrs,R.styleable.customview);
String text = ta.getString(R.styleable.customview_text);
int testArray = ta.getInteger(R.styleable.customview_testArray,1);
```
没有的则需要手动得到id，存入数组

``` java
public class MyTextView extends View {

    private static final String TAG = MyTextView.class.getSimpleName();

    private static final int[] mAttr = { android.R.attr.text, R.attr.testAttr };
    private static final int ATTR_ANDROID_TEXT = 0;
    private static final int ATTR_TESTATTR = 1;

    public MyTextView(Context context, AttributeSet attrs) {
        super(context, attrs);

        // ==>use typedarray
        TypedArray ta = context.obtainStyledAttributes(attrs, mAttr);

        String text = ta.getString(ATTR_ANDROID_TEXT);
        int textAttr = ta.getInteger(ATTR_TESTATTR, -1);
        //输出 text = Hello world! , textAttr = 520
        Log.e(TAG, "text = " + text + " , textAttr = " + textAttr);

        ta.recycle();
    }

}
```

而写成declare-styleable的会自动生成如下：

``` java
public static final class attr {
    public static final int testAttr=0x7f0100a9;
    }
public static final class styleable {
     public static final int test_android_text = 0;
     public static final int test_testAttr = 1;
      public static final int[] test = {
            0x0101014f, 0x7f0100a9
        };
    }
```
第三步：如果我们的view需要支持`wrap_content`则需要重写`onMeasure()`来设置一个最大值。

第四步：根据得到的每个属性的值，用andorid原生的api来写绘制逻辑,重写onDraw()。




       
