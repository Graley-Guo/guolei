 

# 融合步骤解析
----------


## 一、安装react-native开发环境
详细请参照 [React-Native中文网](http://leanote.leanote.com)
## 二、运行示例apk
在安卓手机中运行附件中的release.apk,此apk模拟安卓界面跳转至网罗天下系统界面
## 三、运行RnDemoTest示例工程
### 解压RnDemoTest.zip
RnDemoTest压缩文件解压后，使用终端进入RnDemoTest根目录。执行 npm install命令。如下图所示：    
![图片标题](http://ogrzkg62w.bkt.clouddn.com/npminsts.png) 
> <font size=2>若出现安装异常失败等，请更新npm至淘宝镜像地址。每次重新执行npm install 命令前都须删除node_modules文件夹，保证不必要的异常情况</font>

### 使用AS打开RnDemoTest
使用Android Studio打开后如图所示：    
![图片标题](http://ogrzkg62w.bkt.clouddn.com/%E7%BB%93%E6%9E%84.png)    
这里解释下同原生android目录不同的部分：

 - node_modules: react-native的模块依赖
 - node_modules: react-native的模块依赖
 - index.android.js : 入口文件，勿修改，勿删除
 - package.json : 模块定义文件，勿删除
 - package-lock.json :用于锁定模块版本号，不建议删除
 
### 运行工程
确认app/src/main/assets下面有index.android.bundle和index.android.bundle.meta 文件,如图所示：
![图片标题](http://ogrzkg62w.bkt.clouddn.com/index.png)

运行后如下图所示：    
![图片标题](http://ogrzkg62w.bkt.clouddn.com/%E7%A4%BA%E4%BE%8B1.png)    
![图片标题](http://ogrzkg62w.bkt.clouddn.com/%E7%A4%BA%E4%BE%8B2.png)    



# 系统修改步骤

----------

此处统一称省端esop、一体机app以及其他待融合应用为宿主应用。
## 宿主应用添加模块依赖
- 把示例工程的package.json和package-lock.json文件拷贝至宿主应用的项目根目录下
- 执行npm install 命令安装相关依赖或者复制示例demo中的node_modules文件夹至宿主应用根目录下

## build.gradle添加相关module
 
 - 参考示例工程修改根目录下的settings.gradle文件,添加module依赖

     
    <pre>rootProject.name = 'rndemo'
    include ':app'
    ​
    include ':react-native-image-picker'
    project(':react-native-image-picker').projectDir = new File(rootProject.projectDir,
    '/node_modules/react-native-image-picker/android')
    include ':react-native-device-info'
    project(':react-native-device-info').projectDir = new File(rootProject.projectDir,
    '/node_modules/react-native-device-info/android')
    include ':react-native-code-push'
    project(':react-native-code-push').projectDir = new File(rootProject.projectDir, 
    '/node_modules/react-native-code-push/android/app')
    include ':react-native-picker'
    project(':react-native-picker').projectDir = new File(rootProject.projectDir, 
    '/node_modules/react-native-picker/android')
    include ':react-native-linear-gradient'
    project(':react-native-linear-gradient').projectDir = new File(rootProject.projectDir,
    '/node_modules/react-native-linear-gradient/android')</pre>

> <font size=2>rootProject.name = 'rndemo'此处为宿主应用自身名称代码</font>


 - 参考示例工程修改根目录下的build.gradle文件,gradle版本使用宿主应用的gradle版本即可
    <pre>
    buildscript {
        repositories {
            google()
            jcenter()
        }
        dependencies {
            classpath 'com.android.tools.build:gradle:3.0.1'
        }
    }
    allprojects {
        repositories {
            google()
            jcenter()
            maven {
                // All of React Native (JS, Obj-C sources, Android binaries) is installed from npm
                url "$rootDir/node_modules/react-native/android"
            }
        }
    }</pre>

> <font size=2>本步骤可能会提示更新依赖module的sdk版本，请根据宿主应用实际情况进行更新操作</font>

 - 参考示例工程修改app下的build.gradle文件
    - 添加ndk依赖
    <pre>ndk {
            abiFilters "armeabi-v7a", "x86"
        }</pre>
    - 添加dependencies
    <pre>
    compile "com.facebook.react:react-native:+"
    compile project(':react-native-image-picker')
    compile project(':react-native-device-info')
    compile project(':react-native-code-push')
    compile project(':react-native-picker')
    compile project(':react-native-linear-gradient')
    </pre>

    - 具体最小sdk版本请参考示例工程
 - SyncNow同步，稍等正常同步
 
完整示例代码参考示例工程
   ```
       apply plugin: 'com.android.application'
       ​
       android {
           compileSdkVersion 26
           defaultConfig {
               applicationId "com.cmos.rndemo"
               minSdkVersion 16
               targetSdkVersion 26
               versionCode 1
               versionName "1.2.0"
               testInstrumentationRunner "android.support.test.runner.AndroidJUnitRunner"
       ​
               ndk {
                   abiFilters "armeabi-v7a", "x86"
               }
           }
           signingConfigs {
               release {
                   storeFile file(MYAPP_RELEASE_STORE_FILE)
                   storePassword MYAPP_RELEASE_STORE_PASSWORD
                   keyAlias MYAPP_RELEASE_KEY_ALIAS
                   keyPassword MYAPP_RELEASE_KEY_PASSWORD
               }
           }
           buildTypes {
               release {
                   minifyEnabled true
                   signingConfig signingConfigs.release
                   proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
               }
           }
       ​
       }
       ​
       dependencies {
           implementation fileTree(dir: 'libs', include: ['*.jar'])
           implementation 'com.android.support:appcompat-v7:26.1.0'
           implementation 'com.android.support.constraint:constraint-layout:1.0.2'
           testImplementation 'junit:junit:4.12'
           androidTestImplementation 'com.android.support.test:runner:1.0.1'
           androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.1'
       ​
           compile "com.facebook.react:react-native:+"
           compile project(':react-native-image-picker')
           compile project(':react-native-device-info')
           compile project(':react-native-code-push')
           compile project(':react-native-picker')
           compile project(':react-native-linear-gradient')
       ​
       }
   ```
## 代码/文件添加

 - 复制示例工程index.android.js至宿主应用根目录
 - 打开AndroidManifest.xml文件,添加以下权限
    ```
        <uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
        <uses-permission android:name="android.permission.INTERNET" />
        <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
        <uses-permission android:name="android.permission.CHANGE_WIFI_STATE" />
        <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
        <uses-permission android:name="android.permission.WRITE_SETTINGS" />
        <uses-permission android:name="android.permission.SYSTEM_ALERT_WINDOW" />
    ```


 - 打开AndroidManifest.xml文件,添加activity
 ```
<activity android:name="com.facebook.react.devsupport.DevSettingsActivity" />
```
 - 修改宿主应用的自定义Application类实现ReactApplication,若无则创建自定义Applicaiton类然后实现ReactApplication,最后添加以下代码,参考示例工程
   ```
   private final ReactNativeHost mReactNativeHost = new ReactNativeHost(this) {
          @Override
          public boolean getUseDeveloperSupport() {
              return BuildConfig.DEBUG;
          }
  ​
          @Override
          protected List<ReactPackage> getPackages() {
              return Arrays.<ReactPackage>asList(
                      new MainReactPackage(),
                      new ImagePickerPackage(), new RNDeviceInfo(), new PickerViewPackage(), new LinearGradientPackage(), new MyReactPackage(),
                      new CodePush(
                              getResources().getString(
                                      BuildConfig.DEBUG ? R.string.reactNativeCodePush_androidDeploymentKey_staging
                                              : R.string.reactNativeCodePush_androidDeploymentKey_production),
                              getApplicationContext(), BuildConfig.DEBUG));
          }
  ​
      };


   ```   
       @Override
           public ReactNativeHost getReactNativeHost() {
               return mReactNativeHost;
           }
               @Override
           public void onCreate() {
               super.onCreate();
               SoLoader.init(this, /* native exopackage */ false);
           }
     
        
    - Applicaiton类的OnCreate方法中执行SoLoader.init(this, false)是为了收集react-native所需要的so包
    - protected List getPackages() {}方法则是注册自定义package等
 - 建议复制示例工程src源代码下的module至宿主应用src下,也可手动创建文件，然后复制代码。如下图所示
     - AESEncrypt:使用AES加解密方式传递参数
    
     - MyNativeModule:react-native调用原生安卓
    
     - MyReactPackage：react-native调用原生安卓,然后再application中进行注册
    
     - ParamsUtil：根据网罗天下app融合解决方案附录中的加解密与参数类型的工具类
    
     - RnActivity：承载react-native的activity，须在AndroidManifest.xml中进行activity注册
     ```
    具体路径请根据宿主应用的实际情况
    <activity android:name=".module.RnActivity" /> 
    ```
    ![.module.RnActivity](http://ogrzkg62w.bkt.clouddn.com/module.png)

 - 宿主应用在业务应用场景下跳转至网罗天下系统部分（示例应用的MainActivity类中）
 <pre> public void goToRN(View view) {
​
        try {
            Intent intent = new Intent(this, RnActivity.class);
            intent.putExtra("param", AESEncrypt.encrypt(makeData(), getResources().getString(R.string.rn_aes_key)));
            Log.d("tag", "加密后：" + AESEncrypt.encrypt(makeData(), getResources().getString(R.string.rn_aes_key)));
            startActivity(intent);
​
        } catch (Exception e) {
            e.printStackTrace();
            Toast.makeText(this, "加密出错！" + e.getMessage(), Toast.LENGTH_LONG).show();
        }
    }
        private String makeData() {
        String pid = et_pid.getText().toString().trim();
        String phone = et_phone.getText().toString().trim();
//        String area = et_area.getText().toString().trim();
​
​
//        String result = "{ billId:\"" + etPhone.getText().toString().trim() + "\" ,accessSource: \" 371 \" }";
        String result = "{billId:\"" + phone + "\" ,accessSource: \"" + "liaoning" + "\"}";
        Log.d("tag", "加密前：" + result);
        return result;
    }</pre>
> <font size=2>accessSource参数值请写liaoning</font>
## 运行项目
此处分为两种情况，一为开启本地服务，运行debug模式；一为直接复制bundle文件至aeests目录下(此处需要复制图片资源)
### 运行项目

 - run 'app'(shift+F10)运行debug应用
 - 提示服务未启动
 ![图片标题](http://ogrzkg62w.bkt.clouddn.com/%E6%9C%8D%E5%8A%A1%E6%9C%AA%E5%90%AF%E5%8A%A8.png)

    <pre>若真机通过数据线连接电脑，执行以下命令  
adb reverse tcp:8081 tcp:8081
然后reload</pre>

### 复制bundle文件与图片资源
bundle文件可以通过命令进行生成，此命令执行时会把相关图片资源放至drawable文件夹下。也可直接复制示例demo工程的相关文件

#### 通过命令生成bundle文件

<pre>React-native bundle --entry-file
index.android.js --bundle-output 
./app/src/main/assets/index.android.bundle --platform android --assets-dest
./app/src/main/res/ --dev false</pre>
> <font size=2>该命令会同步生成图片放至drawable文件夹下，同时assets目录会生成index.android.js和index.android.js.meta文件(assets文件夹若没有需手动创建)</font>

#### 复制示例工程bundle文件与图片资源

 - 复制示例工程的index.android.js和index.android.js.meta文件至assets目录下，若没有assets目录则创建该目录.
 - 复制示例工程各个drawable文件夹下带node_modules开头与rn_res开头的图片资源
![图片标题](http://ogrzkg62w.bkt.clouddn.com/drawable.png)

## 打包生成apk 
此处需要注意，package.json、index.android.js、RnActivity中的组件名称要同settings.gradle中的rootProject.name保持一致.

 - package.json
![图片标题](http://ogrzkg62w.bkt.clouddn.com/rndemo1.png)
 - index.android.js
![图片标题](http://ogrzkg62w.bkt.clouddn.com/rndemo2.png)
 - RnActivity
![图片标题](http://ogrzkg62w.bkt.clouddn.com/rndemo3.png)
 - settings.gradle
![图片标题](http://ogrzkg62w.bkt.clouddn.com/rndemo4.png)

# 附录

## 加密解密
本次参数统一经过AES加解密方式，请复制示例工程的AESEncrypt与res/values下的strings.xml文件。strings.xml中定义加密key，请勿随意替换，否则网罗天下部分单点登录接口将获取参数异常。
![图片标题](http://ogrzkg62w.bkt.clouddn.com/rndemo5.png)
## 热更新
网罗天下app更新采用热更新，具体使用code-push组件。code-push的相关key保存在values/strings.xml中,最终生产环境key值请同网罗天下团队及时沟通确认。
![图片标题](http://ogrzkg62w.bkt.clouddn.com/rndemo5.png)
具体使用在Application中:
![图片标题](http://ogrzkg62w.bkt.clouddn.com/codepush.png)
## 依赖模块
![图片标题](http://ogrzkg62w.bkt.clouddn.com/module22.png)

 - react-native:+:react-native项目必须依赖
 - react-native-image-picker:react-native版相册与相机
 - react-native-device-info:可获取ios与android设备信息
 - react-native-code-push:热更新模块
 - react-native-picker:react-native版时间与城市选择
 - react-native-linear-gradient:react-native渐变组件
## 混淆
请参照示例工程中的混淆文件"proguard-rules.pro"

## 异常
打包releaseapk时，可能出现rn-res...logo01.png is not singed等，请把rn-res..logo01、rn-res..logo02、rn-res..logo03删除即可



