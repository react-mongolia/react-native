---
id: native-modules-android
title: Натив модулиуд
---

Заримдаа апп нь React Native-ын зохих хариу өгөх модульгүйгээр платформ API-д хандах хэрэгтэй болдог. Магадгүй Javascript-руу дахин өөрчлөхгүйгээр бэлэн байгаа Java код ашиглах эсвэл зураг процесс хийх, өгөгдлийн сан эсвэл ахисан түвшний өргөтгөл гэх мэтэд зориулсан ажиллагааг сайжруулах, олон процесст зориулсан код бичих хэрэг гарч болно.

Бид React Native-ыг хүн өөрөө натив код бичих боломжтой байхаар загварчилсан ба платформыг бүрэн ашиглах боломжийг та бүхэнд олгохыг хүссэн. Энэ нь илүү ахисан түвшний функц бөгөөд бид энгийн хөгжүүлэлтийн явцад төдийлөн ашиглагдахгүй байх гэж бодож байна. Гэхдээ байх нь чухал. Хэрэв React Native-т танд хэрэгтэй натив функц байхгүй бол та өөрөө хийх боломжтой юм. 

## Натив модулийн тохиргоо

Натив модулиуд нь ихэвчлэн npm пакэж хэлбэрээр байдаг. Энгийн javascript файлууд, зүйлсээс бусдаар бол тэд нь Android сангийн төслүүд агуулсан байна. Энэхүү төсөл нь NPM-ын талаас бол бусад медиа төрлийн зүйл байх юм. Энэ нь ямар нэг онцгой зүйлгүй гэсэн үг. Код автоматаар үүсгэх тухай үндсэн ойлголттой болохыг хүсвэл [Натив Модулийн Тохиргоо](native-modules-setup) гэснийг уншина уу.

### Gradle-ыг идэвхжүүлэх

Хэрэв та Java коддоо өөрчлөлт хийх бол цаг хэмнэх зорилгоор [Gradle Daemon](https://docs.gradle.org/2.9/userguide/gradle_daemon.html)-ыг идэвхжүүлэхийг санал болгож байна. 

## Toast модуль

[Toast](http://developer.android.com/reference/android/widget/Toast.html) ашиглах тухай энэхүү зөвлөмжийг уншина уу. Javascript-ээр тоаст мессеж хийдэг болгоё гэж бодъё. Эхлээд натив модулиа үүсгэнэ. Натив модуль нь Java класс байх ба  `ReactContextBaseJavaModule`  гэсэн өргөтгөлтэй ихэвчлэн байдаг. Javascript-д шаарддаг ажиллах орчинг бий болгодог. Бидний зорилго бол дэлгэц дээр богино мессеж харуулахын тулд JavaScript-ээс `ToastExample.show('Awesome', ToastExample.SHORT);` гэж бичиж оруулах юм. 

`android/app/src/main/java/com/your-app-name/` дотор `ToastModule.java` нэртэй доорхыг агуулсан Java класс үүсгэ:

```java
// ToastModule.java

package com.your-app-name;

import android.widget.Toast;

import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.bridge.ReactContext;
import com.facebook.react.bridge.ReactContextBaseJavaModule;
import com.facebook.react.bridge.ReactMethod;

import java.util.Map;
import java.util.HashMap;

public class ToastModule extends ReactContextBaseJavaModule {

  private static final String DURATION_SHORT_KEY = "SHORT";
  private static final String DURATION_LONG_KEY = "LONG";

  public ToastModule(ReactApplicationContext reactContext) {
    super(reactContext);
  }
}
```

`ReactContextBaseJavaModule` нь `getName` -ыг ажиллуулахыг шаарддаг. Үүний зорилго нь Javascript дээр уг классыг харуулдаг `NativeModule`-ын стринг нэрийг буцаах юм. Тэгэхээр бид үүнийг `ToastExample` гэж нэрлэх ба Javascript дээр `React.NativeModules.ToastExample` гэж дуудна.

```java
  @Override
  public String getName() {
    return "ToastExample";
  }
```

`getConstants` гэх өөр нэг хэрэглүүр нь Javascript-т ирсэн тогтмол утгыг буцаадаг. Гүйцэтгэлийг заавал шаардлагагүй ч Javascript-ээс Java руу шууд дамжих утгыг урьдчилан тодорхойлоход чухал үүрэгтэй. 

```java
  @Override
  public Map<String, Object> getConstants() {
    final Map<String, Object> constants = new HashMap<>();
    constants.put(DURATION_SHORT_KEY, Toast.LENGTH_SHORT);
    constants.put(DURATION_LONG_KEY, Toast.LENGTH_LONG);
    return constants;
  }
```
Javascript руу хэрэглүүр өгөхийн тулд Java руу нь `@ReactMethod` ашиглах хэрэгтэй. Энэхүү холбох хэрэглүүрийн буцах төрөл нь үргэлж `void` байна.  React Native bridge үйлдэл нь синхрон бус байдаг. Тиймээc Javascript руу аливаа үр дүнг дамжуулах ганц арга нь буцааж дуудах эсвэл эвент өгөх юм (доорхыг харна уу).

```java
  @ReactMethod
  public void show(String message, int duration) {
    Toast.makeText(getReactApplicationContext(), message, duration).show();
  }
```

### Аргументын төрлүүд

Аргументын доорх төрлүүдийг `@ReactMethod` гэж тэмдэглэсэн хэрэглүүрүүд дэмждэг. Эдгээр нь шууд JavaScript-ын адил төрлийг шууд хуулдаг

```
Boolean -> Bool
Integer -> Number
Double -> Number
Float -> Number
String -> String
Callback -> function
ReadableMap -> Object
ReadableArray -> Array
```

[ReadableMap](https://github.com/facebook/react-native/blob/master/ReactAndroid/src/main/java/com/facebook/react/bridge/ReadableMap.java) болон [ReadableArray](https://github.com/facebook/react-native/blob/master/ReactAndroid/src/main/java/com/facebook/react/bridge/ReadableArray.java)  хоёроос дэлгэрэнгүй уншина уу.

### Модулиа бүртгүүлэх 

Java-д хийх сүүлийн алхам бол модулиа бүртгүүлэх юм. Аппын чинь пакэж дотор `createNativeModules` дотор энэ үйлдлийг хийнэ. Хэрэв модулиа бүртгүүлэхгүй бол Javascript-ээр ажиллахгүй. 

`android/app/src/main/java/com/your-app-name/` фолдер дотор доорхыг агуулсан `CustomToastPackage.java` нэртэй шинэ Java класс үүсгэ:

```java
// CustomToastPackage.java

package com.your-app-name;

import com.facebook.react.ReactPackage;
import com.facebook.react.bridge.NativeModule;
import com.facebook.react.bridge.ReactApplicationContext;
import com.facebook.react.uimanager.ViewManager;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class CustomToastPackage implements ReactPackage {

  @Override
  public List<ViewManager> createViewManagers(ReactApplicationContext reactContext) {
    return Collections.emptyList();
  }

  @Override
  public List<NativeModule> createNativeModules(
                              ReactApplicationContext reactContext) {
    List<NativeModule> modules = new ArrayList<>();

    modules.add(new ToastModule(reactContext));

    return modules;
  }

}
```
Пакэж нь `MainApplication.java` файлын `getPackages` хэрэглүүрт өгөгдсөн байх хэрэгтэй. Уг файл нь таны react-native application directory дотор android фолдерт байна.  Зам нь `android/app/src/main/java/com/your-app-name/MainApplication.java`.

```java
// MainApplication.java

...
import com.your-app-name.CustomToastPackage; // <-- Add this line with your package name.
...

protected List<ReactPackage> getPackages() {
    return Arrays.<ReactPackage>asList(
            new MainReactPackage(),
            new CustomToastPackage()); // <-- Add this line with your package name.
}
```

Шинэ ажиллагаа руугаа Javascript-ээс хялбар ханддаг байхын тулд натив модулиа Javascript модуль болгох нь түгээмэл байдаг. Заавал ингэх шаардлагагүй ч таны санг ашиглах хэрэглэгчид байнга `NativeModules` гэж дуудах ажлыг нь хөнгөвчилж өгөх юм. Javascript-ын энэхүү файл нь дараа нь ямар нэг Javascript-ын ажиллагаа нэмэхэд дөхөм байх юм. 

Доорхыг агуулсан `ToastExample.js` нэртэй Javascript файл үүсгэ:

```javascript
/**
 * This exposes the native ToastExample module as a JS module. This has a
 * function 'show' which takes the following parameters:
 *
 * 1. String message: A string with the text to toast
 * 2. int duration: The duration of the toast. May be ToastExample.SHORT or
 *    ToastExample.LONG
 */
import {NativeModules} from 'react-native';
module.exports = NativeModules.ToastExample;
```

Одоо өөр Javascript файлаасаа ингэж дуудна:

```javascript
import ToastExample from './ToastExample';

ToastExample.show('Awesome', ToastExample.SHORT);
```

Энэ Javascript файл нь `ToastExample.js`-тай ижил түвшинд байгаа гэдгийг шалгаарай. 

## Toast-аас гадна

### Буцааж дуудах

Натив модулиуд нь буцааж дуудах гэсэн онцгой төрлийн аргументыг дэмждэг. Ихэнх тохиолдолд Javascript-д функцийн дуудсан үр дүнг өгдөг. 

```java
import com.facebook.react.bridge.Callback;

public class UIManagerModule extends ReactContextBaseJavaModule {

...

  @ReactMethod
  public void measureLayout(
      int tag,
      int ancestorTag,
      Callback errorCallback,
      Callback successCallback) {
    try {
      measureLayout(tag, ancestorTag, mMeasureBuffer);
      float relativeX = PixelUtil.toDIPFromPixel(mMeasureBuffer[0]);
      float relativeY = PixelUtil.toDIPFromPixel(mMeasureBuffer[1]);
      float width = PixelUtil.toDIPFromPixel(mMeasureBuffer[2]);
      float height = PixelUtil.toDIPFromPixel(mMeasureBuffer[3]);
      successCallback.invoke(relativeX, relativeY, width, height);
    } catch (IllegalViewOperationException e) {
      errorCallback.invoke(e.getMessage());
    }
  }

...
```

Үүнийг ашиглан Javascript-д уг хэрэглүүрт хандах боломжтой:

```javascript
UIManager.measureLayout(
  100,
  100,
  (msg) => {
    console.log(msg);
  },
  (x, y, width, height) => {
    console.log(x + ':' + y + ':' + width + ':' + height);
  },
);
```

Натив модуль нь буцаан дуудах үйлдлийг нэг л удаа хүргэнэ. Гэхдээ буцааж дуудах үйлдлийг хадгалаад дараа нь хүргэж болдог. 

Натив функц ажиллаж дууссаны дараа шууд буцаан дуудах үйлдэл хийгддэггүй гэдгийг санах хэрэгтэй. Холбох үйлдэл нь синхрон бус явагддаг гэдгийг мартаж болохгүй. 


### Promises

Натив модуль promise гүйцэтгэдэг. Тиймдээ ч таны кодыг хялбарчилна.  Ялангуяа ES2016-ын `async/await` синтакс ашиглаж байгаа үед. Холболт бүхий натив хэрэглүүрийн сүүлийн параметр нь `Promise` бол холбогдох JS хэрэглүүр нь нэг JS Promise объект руу буцаадаг. 

Дээрх кодыг буцаах дуудлагын оронд promise ашиглавал иймэрхүү харагдана:

```java
import com.facebook.react.bridge.Promise;

public class UIManagerModule extends ReactContextBaseJavaModule {

...
  private static final String E_LAYOUT_ERROR = "E_LAYOUT_ERROR";
  @ReactMethod
  public void measureLayout(
      int tag,
      int ancestorTag,
      Promise promise) {
    try {
      measureLayout(tag, ancestorTag, mMeasureBuffer);

      WritableMap map = Arguments.createMap();

      map.putDouble("relativeX", PixelUtil.toDIPFromPixel(mMeasureBuffer[0]));
      map.putDouble("relativeY", PixelUtil.toDIPFromPixel(mMeasureBuffer[1]));
      map.putDouble("width", PixelUtil.toDIPFromPixel(mMeasureBuffer[2]));
      map.putDouble("height", PixelUtil.toDIPFromPixel(mMeasureBuffer[3]));

      promise.resolve(map);
    } catch (IllegalViewOperationException e) {
      promise.reject(E_LAYOUT_ERROR, e);
    }
  }

...
```
Энэ хэрэглүүрийн Javascript тал нь Promise-руу хүргэнэ. Энэ нь бид дуудахдаа `await` түлхүүр үг ашиглан үр дүнг нь хүлээнэ гэсэн үг:

```javascript
async function measureLayout() {
  try {
    var {relativeX, relativeY, width, height} = await UIManager.measureLayout(
      100,
      100,
    );

    console.log(relativeX + ':' + relativeY + ':' + width + ':' + height);
  } catch (e) {
    console.error(e);
  }
}

measureLayout();
```

### Threading

Натив модуль нь ямар салбар процесс дээр дуудагдаж байгаа тухай ямар нэг ойлголт авах учиргүй. Одоогийн гүйцэтгэж буй даалгавар нь ирээдүйд өөр байх боломжтой учраас тэр. Хэрэв дуудахад блок хийх шаардлагатай бол салбар процесст болон буцаах дуудлагад их ачаал ирнэ. 

### JavaScript руу эвент илгээх

Натив модуль нь эвентийн тухай мэдээллийг шууд хүргэхгүйгээр дохио мэдээлэл Javascript руу өгч болдог. Доорх код шиг `ReactContext`-аас `RCTDeviceEventEmitter`-ыг авч ашиглах нь хамгийн хялбар арга юм.

```java
...
private void sendEvent(ReactContext reactContext,
                       String eventName,
                       @Nullable WritableMap params) {
  reactContext
      .getJSModule(DeviceEventManagerModule.RCTDeviceEventEmitter.class)
      .emit(eventName, params);
}
...
WritableMap params = Arguments.createMap();
...
sendEvent(reactContext, "keyboardWillShow", params);
```

JavaScript модуль нь `Subscribable` ашиглан `addListenerOn`-аар дамжуулан эвентийг хүлээн аван бүртгэж чадна.


```javascript
import { DeviceEventEmitter } from 'react-native';
...

var ScrollResponderMixin = {
  mixins: [Subscribable.Mixin],


  componentDidMount() {
    ...
    this.addListenerOn(DeviceEventEmitter,
                       'keyboardWillShow',
                       this.scrollResponderKeyboardWillShow);
    ...
  },
  scrollResponderKeyboardWillShow:function(e: Event) {
    this.keyboardWillOpenTo = e;
    this.props.onKeyboardWillShow && this.props.onKeyboardWillShow(e);
  },
```

Мөн та эвентийн талаарх мэдээллийг `DeviceEventEmitter` ашиглан хүлээх боломжтой.

```javascript
...
componentDidMount() {
  this.subscription = DeviceEventEmitter.addListener('keyboardWillShow', function(e: Event) {
    // handle event
  });
}

componentWillUnmount() {
  // When you want to stop listening to new events, simply call .remove() on the subscription
  this.subscription.remove();
}
...
```

### `startActivityForResult`-аас үйл ажиллагааны үр дүнг хүлээн авах

Та `startActivityForResult` ашиглан ямар нэг үйл ажиллагаа эхлүүлсэн бол `onActivityResult` ашиглан түүнийхээ үр дүнг хүлээн авах хэрэгтэй. Ингэхийн тулд `BaseActivityEventListener`-ыг өргөтгөх эсвэл  `ActivityEventListener`-ыг ажиллуулах шаардлагатай. API өөрчлөлт орсон ч сайн ажиллаж байдаг тул эхнийх нь илүү давуу талтай. Тэгээд та тухайн модулийнхаа байгуулагч функц дотор нь бүртгүүлнэ. 


```java
reactContext.addActivityEventListener(mActivityResultListener);
```

Одоо та доорх аргын дагуу `onActivityResult`-ыг харах боломжтой:

```java
@Override
public void onActivityResult(
  final Activity activity,
  final int requestCode,
  final int resultCode,
  final Intent intent) {
  // Your logic here
}
```

Тодорхой харуулахын тулд энгийн зураг сонгох үйлдлийг хийе. Зураг сонгогч нь JavaScript-т `pickImage` гэж хэрэглүүрийг ашиглана. Ингээд тухайн зургийг дуудсан замд нь буцаана. 

```java
public class ImagePickerModule extends ReactContextBaseJavaModule {

  private static final int IMAGE_PICKER_REQUEST = 467081;
  private static final String E_ACTIVITY_DOES_NOT_EXIST = "E_ACTIVITY_DOES_NOT_EXIST";
  private static final String E_PICKER_CANCELLED = "E_PICKER_CANCELLED";
  private static final String E_FAILED_TO_SHOW_PICKER = "E_FAILED_TO_SHOW_PICKER";
  private static final String E_NO_IMAGE_DATA_FOUND = "E_NO_IMAGE_DATA_FOUND";

  private Promise mPickerPromise;

  private final ActivityEventListener mActivityEventListener = new BaseActivityEventListener() {

    @Override
    public void onActivityResult(Activity activity, int requestCode, int resultCode, Intent intent) {
      if (requestCode == IMAGE_PICKER_REQUEST) {
        if (mPickerPromise != null) {
          if (resultCode == Activity.RESULT_CANCELED) {
            mPickerPromise.reject(E_PICKER_CANCELLED, "Image picker was cancelled");
          } else if (resultCode == Activity.RESULT_OK) {
            Uri uri = intent.getData();

            if (uri == null) {
              mPickerPromise.reject(E_NO_IMAGE_DATA_FOUND, "No image data found");
            } else {
              mPickerPromise.resolve(uri.toString());
            }
          }

          mPickerPromise = null;
        }
      }
    }
  };

  public ImagePickerModule(ReactApplicationContext reactContext) {
    super(reactContext);

    // Add the listener for `onActivityResult`
    reactContext.addActivityEventListener(mActivityEventListener);
  }

  @Override
  public String getName() {
    return "ImagePickerModule";
  }

  @ReactMethod
  public void pickImage(final Promise promise) {
    Activity currentActivity = getCurrentActivity();

    if (currentActivity == null) {
      promise.reject(E_ACTIVITY_DOES_NOT_EXIST, "Activity doesn't exist");
      return;
    }

    // Store the promise to resolve/reject when picker returns data
    mPickerPromise = promise;

    try {
      final Intent galleryIntent = new Intent(Intent.ACTION_PICK);

      galleryIntent.setType("image/*");

      final Intent chooserIntent = Intent.createChooser(galleryIntent, "Pick an image");

      currentActivity.startActivityForResult(chooserIntent, IMAGE_PICKER_REQUEST);
    } catch (Exception e) {
      mPickerPromise.reject(E_FAILED_TO_SHOW_PICKER, e);
      mPickerPromise = null;
    }
  }
}
```

### LifeCycle эвент тандах

`onResume`, `onPause` гэх мэт LifeCycle эвентүүдийг тандах процесс нь `ActivityEventListener`-ыг хэрхэн ажиллуулдагтай их төстэй. Модуль нь `LifecycleEventListener`-ыг ажиллуулах ёстой. Тэгээд та модулийн байгуулагч функц дотор эвент болохыг хүлээгчээ бүртгүүлэх хэрэгтэй. 


```java
reactContext.addLifecycleEventListener(this);
```

Одоо та доорх хэрэглүүрийг ажиллуулан Lifecycle эвентийн үйл ажиллагааг тандах боломжтой:


```java
@Override
public void onHostResume() {
    // Activity `onResume`
}

@Override
public void onHostPause() {
    // Activity `onPause`
}

@Override
public void onHostDestroy() {
    // Activity `onDestroy`
}
```
