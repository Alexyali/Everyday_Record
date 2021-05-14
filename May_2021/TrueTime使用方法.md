# TrueTime使用方法

[TrueTime](https://github.com/instacart/truetime-android)是应用于Android端的NTP客户端，它可以计算当前设备以NTP服务时间为准的“正确”时间。它包括Vanilla和Rx两个版本，其中Rx版本执行完全的NTP协议。下面都使用Rx版本，因此需要导入RxJava的包。

### Installation

1. 首先在application的`build.gradle(:app)`文件中加入

```java
repositories {
    maven {
        url "https://jitpack.io"
    }
}

dependencies {
    // ...
    implementation 'com.github.instacart.truetime-android:library-extension-rx:master-SNAPSHOT'

    // or if you want the vanilla version of Truetime:
    implementation 'com.github.instacart.truetime-android:library:master-SNAPSHOT'
    // 为了支持TrueTimeRx.build().subscribeOn(Schedulers.io())    
    implementation "io.reactivex.rxjava2:rxandroid:2.0.2"
}
```

2. 需要在`class App extends Application`的`onCreate()`中初始化

首先需要`java/com.example.<project>/`文件夹内添加`App.java`文件，初始化TrueTime：

```java
package com.example.<project>; // <project> is the name of your project
import android.app.Application;

import java.util.Date;
import android.util.Log;

import com.instacart.library.truetime.TrueTimeRx;
import io.reactivex.android.schedulers.AndroidSchedulers;
import io.reactivex.observers.DisposableSingleObserver;
import io.reactivex.schedulers.Schedulers;

public class App extends Application {
	@Override
	public void onCreate() {
		super.onCreate();
        //初始化
		initRxTrueTime();
	}
    /**
     * Initialize the TrueTime using RxJava.
     */
    private void initRxTrueTime() {
        DisposableSingleObserver<Date> disposable = TrueTimeRx.build()
                .withConnectionTimeout(31_428)
                .withRetryCount(100)
                .withSharedPreferencesCache(this)
                .withLoggingEnabled(true)
                .initializeRx("pool.ntp.org") // ntp server address
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribeWith(new DisposableSingleObserver<Date>() {
                    @Override
                    public void onSuccess(Date date) {
                        Log.d(TAG, "Success initialized TrueTime :" + date.toString());
                    }

                    @Override
                    public void onError(Throwable e) {
                        Log.e(TAG, "something went wrong", e);
                    }
                });    
}
```

其次需要在`AndroidManifest.xml`中添加`App`文件

```java
<application
	//...
	android:name=".App"
	//...
	</application>
```

3. 调用`TrueTimeRx.now()`

在`MainActivity.java`中调用函数，并输出客户端和服务器时间偏差

```java
import com.instacart.library.truetime.TrueTimeRx;
import android.util.Log;

public class MainActivity extends AppCompatActivity {
	@Override
	protected void onCreate(Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);
		Date trueTime = TrueTimeRx.now();
		Date deviceTime = new Date();
        Log.d("kg",
        String.format(" [trueTime: %d] [devicetime: %d] [drift_sec: %f]",
        	trueTime.getTime(),
        	deviceTime.getTime(),
        	(trueTime.getTime() - deviceTime.getTime()) / 1000F));		
	}
}
```

### 循环调用初始化函数完成时间校准

因为手机的CPU频率不一定和服务器相同，因此在一次校准后，时间偏差可能会继续改变。因此需要持续校准。TrueTime每调用initialize函数就会发送一次ntp请求。需要每隔一段时间调用初始化函数以完成时间校准。

这里采用`handler.postDelayed`实现循环调用

```java
// 创建Handler对象
Handler handler = new Handler(Looper.getMainLooper());
// 创建Runnable对象
Runnable runnable = new Runnable() {
	@Override
	public void run() {
        // 初始化
		initRxTrueTime();
		// 实现1000ms的定时
        handler.postDelayed(this, 1000);
	}
};
// 调用runnable对象
handler.postDelayed(runnable, 1000);
// 关闭定时器
handler.removeCallbacks(runnable); 
```

