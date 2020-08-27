---
layout: post
title: AndroidStudio4.0 调用Opencv4.4的相机
category: 技术, android
tags: React
keywords: React
--- 

## 环境配置 ##

1. AndroidStudio 4.0版本
2. opencv-android 4.4SDK
3. JDK1.4（自行配置）
4. SDK（自行配置）
其中，在AndroidStudio左上角的SDK Manager中的SDK Tool一栏安装如下几个Tools：NDK, CMAKE， Android SDK platform-Tools以及Inter x86 Exmulator

## SDK导入 ##
1. 第一步，创建java project。选择Native C++ project, 更改项目名，Minimum SDK(我选择的是API 21：Android 5.0), 然后next, Finish。
2. 首先run一下项目，确保项目没有出错。
	![运行项目结果](https://winterwindwang.github.io/assets/img/2020-08-27-first_run.png)
3. 导入Opencv-android 4.4包：文件（file）-> 新建（New）-> 导入模块(import Module)->选择opncv-android-sdk目录下的sdk目录
	![目录详情](https://winterwindwang.github.io/assets/img/2020-08-27-import_opencvsdk.png)
	完成后点击Finish
4. 完成以后，将导入的opencv-sdk设置成nadroid studio项目的依赖项：文件（file）-> 项目结构（Project Structure）->依赖项(Dependencice),选择app，然后如下图所示操作，完成以后，如果没有报错，那么opencv就导入成功了
	![打开依赖项](https://winterwindwang.github.io/assets/img/2020-08-27-project_structure.png)
	![将opencv-sdk作为依赖项](https://winterwindwang.github.io/assets/img/2020-08-27-select_sdk.png)
	这种方式导入可以不用做额外的修改，有一些方法导入需要将build.gradle(opencv)中的```apply plugin: 'com.android.application'```更改成```apply plugin: 'com.android.library'```
5. 新建一个资源目录，右键选择app，选择新建（New）-> 文件夹（Folder）-> JNI文件夹（JNI Folder）,如图。
	![新建JNI Folder](https://winterwindwang.github.io/assets/img/2020-08-27-new_jnifolder.png)
	新建完成后，在opencv-android-sdk目录下的sdk/native/libs目录中的文件全部拷贝到新建的jniLibs文件夹中
	![libs目录地址](https://winterwindwang.github.io/assets/img/2020-08-27-libs_dir.png)
	
## 代码 ##
1. 完成上述操作后，就可以在项目中使用opencv-sdk了，这里仅使用调用opencv 相机（camera）作为一个使用例子；
2. 首先是资源文件夹中的layout，将下述代码(sdk中faceDetect的代码)直接替换掉项目中原始, 直接上代码吧
```
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <org.opencv.android.JavaCameraView
        android:layout_width="fill_parent"
        android:layout_height="fill_parent"
        android:id="@+id/fd_activity_surface_view" />

</LinearLayout>
```
	
上面是layout中的代码，接下来是MainActivity.java中的代码

```
package com.example.opencvdemo;

import androidx.appcompat.app.AppCompatActivity;

import android.os.Bundle;
import android.util.Log;
import android.view.WindowManager;
import android.widget.TextView;

import org.opencv.android.BaseLoaderCallback;
import org.opencv.android.CameraActivity;
import org.opencv.android.CameraBridgeViewBase;
import org.opencv.android.LoaderCallbackInterface;
import org.opencv.android.OpenCVLoader;
import org.opencv.core.Mat;

import java.util.Collection;
import java.util.Collections;
import java.util.List;

public class MainActivity extends CameraActivity implements CameraBridgeViewBase.CvCameraViewListener2 {

    private static final String TAG = "Opencv::Activity";
    private Mat mRgba;
    private Mat mGray;
    private CameraBridgeViewBase mOpenCvCameraView;

    private BaseLoaderCallback mLoaderCallback = new BaseLoaderCallback(this) {
        @Override
        public void onManagerConnected(int status) {
            switch(status){
                case LoaderCallbackInterface.SUCCESS:
                {
                    mOpenCvCameraView.enableView();
                    Log.i(TAG,"OpenCV Loaded successfully");
                }break;
                default:
                {
                    super.onManagerConnected(status);
                }break;
            }
        }
    };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        Log.i(TAG,"called onCreate");
        super.onCreate(savedInstanceState);
        getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);

        setContentView(R.layout.activity_main);
        mOpenCvCameraView = (CameraBridgeViewBase) findViewById(R.id.fd_activity_surface_view);
        mOpenCvCameraView.setVisibility(CameraBridgeViewBase.VISIBLE);
        mOpenCvCameraView.setCvCameraViewListener(this);
    }

    @Override
    public void onPause() {

        super.onPause();
        if(mOpenCvCameraView !=null){
            mOpenCvCameraView.disableView();
        }
    }

    @Override
    public void onResume() {
        super.onResume();
        if(!OpenCVLoader.initDebug()){
            Log.d(TAG,"OpenCV Library not found! Using OpenCV Manager for initialization");
            OpenCVLoader.initAsync(OpenCVLoader.OPENCV_VERSION_3_0_0,this, mLoaderCallback);
        }else{
            Log.d(TAG, "OpenCV library found inside package. Using it!");
            mLoaderCallback.onManagerConnected(LoaderCallbackInterface.SUCCESS);
        }
    }


    protected List<? extends CameraBridgeViewBase> getCameraViewList(){
        return Collections.singletonList(mOpenCvCameraView);
    }

    public void onDestroy() {
        super.onDestroy();
        mOpenCvCameraView.disableView();
    }

    @Override
    public void onCameraViewStarted(int width, int height) {
        mGray = new Mat();
        mRgba = new Mat();
    }

    @Override
    public void onCameraViewStopped() {
        mGray.release();
        mRgba.release();
    }

    @Override
    public Mat onCameraFrame(CameraBridgeViewBase.CvCameraViewFrame inputFrame) {
        mRgba = inputFrame.rgba();
        mGray = inputFrame.gray();
        return mRgba;   // 非常重要！！！ 这个决定你的UI界面中显示的是什么，如果返回是null的话，界面中是没有图像显示的，一片黑。所以对图像的调整，以及相机操作之类的操作
    }
}

```

最后配置 ```AndroidManifest.xml```文件的配置，加上以下代码

```
<uses-permission android:name="android.permission.CAMERA"/>
    <supports-screens android:resizeable="true"
        android:smallScreens="true"
        android:normalScreens="true"
        android:largeScreens="true"
        android:anyDensity="true" />
    <uses-feature android:name="android.hardware.camera" android:required="false"/>
    <uses-feature android:name="android.hardware.camera.autofocus" android:required="false"/>
    <uses-feature android:name="android.hardware.camera.front" android:required="false"/>
    <uses-feature android:name="android.hardware.camera.front.autofocus" android:required="false"/>
```

运行项目，Logcat出现以下输出
![Logcat输出](https://winterwindwang.github.io/assets/img/2020-08-27-opencv_log.png)

虚拟机出现屏幕中的框就说明相机调用成功了
![虚拟机输出](https://winterwindwang.github.io/assets/img/2020-08-27-camera_success.png)





