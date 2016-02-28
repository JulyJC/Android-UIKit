##Translucent Bar
一、引入；Android 4.4 开始引入

二、实现方法
 1、在values、values-v19、values-v21的style.xml都设置一个 Translucent System Bar 风格的Theme

values/style.xml
```xml
<style name="ImageTranslucentTheme" parent="AppTheme">
    <!--在Android 4.4之前的版本上运行，直接跟随系统主题-->
</style>
```

values-v19/style.xml
```xml
<style name="ImageTranslucentTheme" parent="Theme.AppCompat.Light.DarkActionBar">
    <item name="android:windowTranslucentStatus">true</item>
    <item name="android:windowTranslucentNavigation">true</item>
     <item name="android:statusBarColor">@color/color_31c27c</item>
    <!-- 可以不适用设置背景颜色这一条，设置的情况是头部颜色非渐变固定，这样仅仅使用颜色就可以完成，
          更多的情况是使用与头部是渐变性背景，需要状态栏融入的 --!>
</style>
```

values-v21/style.xml
```xml
<style name="ImageTranslucentTheme" parent="Theme.AppCompat.Light.DarkActionBar">
    <item name="android:windowTranslucentStatus">false</item>
    <item name="android:windowTranslucentNavigation">true</item>
    <!--Android 5.x开始需要把颜色设置透明，否则导航栏会呈现系统默认的浅灰色-->
    <item name="android:statusBarColor">@android:color/transparent</item>
</style>
```

2、在AndroidManifest.xml中对指定Activity的theme进行设置
```xml
<activity
    android:name=".ui.ImageTranslucentBarActivity"
    android:label="@string/image_translucent_bar"
    android:theme="@style/ImageTranslucentTheme" />
```

3、在Activity的布局文件中设置背景图片，同时，需要把android:fitsSystemWindows设置为true
 activity_image_translucent_bar.xml
 ```xml
 <?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="@mipmap/env_bg"
    android:fitsSystemWindows="true">
</RelativeLayout>
```

* 上面的方式需要在每个activity的布局中进行添加属性，比较麻烦，但是不能在theme中进行添加，否则会改变其他UI的表现
  可以使用baseActivity进行统一添加，主要是在oncreate中获取view进行添加
  ```java
  public abstract class BaseActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        supportRequestWindowFeature(Window.FEATURE_NO_TITLE);

        setContentView(getLayoutResId());//把设置布局文件的操作交给继承的子类

        ViewGroup contentFrameLayout = (ViewGroup) findViewById(Window.ID_ANDROID_CONTENT);
        View parentView = contentFrameLayout.getChildAt(0);
        if (parentView != null && Build.VERSION.SDK_INT >= 14) {
            parentView.setFitsSystemWindows(true);
        }
    }
   }
 ```


上面需要注意的地方是，无论你在哪个SDK版本的values目录下，设置了主题，都应该在最基本的values下设置一个同名的主题。这样才能确保你的app能够正常运行在 Android 4.4 以下的设备。否则，肯定会报找不到Theme的错误。

