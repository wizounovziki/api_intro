## IMH Face Recognizer for Medicine Dispensation

### Recognizer Mobile Application

Application is build with React-native and Expo framework to ensure cross-platform compatibility

#### To start

1. Clone repo
2. cd into **`Recognizer`** Folder
3. Use **`npm install`** to install expo and necessary modules
4. Use **`yarn start`** or **`expo start`** to start application
5. Both android and ios system need to download and install **`expo`** in order to continue
6. You will see a qrcode appearing in terminal window, scan the qrcode with **expo application if android** and **camera if ios system**
7. Application will be built automatically after connect

#### To deploy to android system (generate unsigned APK)

1. From **`Recognizer`** folder, eject project with **`expo eject`** **(WARNING! Ejected project cannot be injected back! Commit changes first)**
2. **`android`** and **`ios`** folders will be generated, import android project into android studio
3. Config **`expo-camera`** module in order to use. Add the following to **`build.gradle(project:android)`**:
```java
maven {
    // expo-camera bundles a custom com.google.android:cameraview
    url "$rootDir/../node_modules/expo-camera/android/maven"
} 
```
4. After build finished, in **`java/com/recognizer/MainApplicaiton`** add **`import com.facebook.react.BuildConfig;`**
5. In **`AndoirdManifest.xml`** add **`com.recognizer`** prefix to **`.MainApplicaiton`** and **`.MainActivity`**
6. In **`AndoirdManifest.xml`** remove **`ACCESS_COARSE_LOCATION`**, **`MANAGE_DOCUMENTS`**, **`WRITE_SETTINGS`**
7. Remove **`expo-updates`** with the following:
```diff
-import android.net.Uri;
-import expo.modules.updates.UpdatesController;
-import javax.annotation.Nullable;
-
 public class MainApplication extends Application implements ReactApplication {
   private final ReactModuleRegistryProvider mModuleRegistryProvider = new ReactModuleRegistryProvider(
     new BasePackageList().getPackageList(),

...

     protected String getJSMainModuleName() {
       return "index";
     }
-
-    @Override
-    protected @Nullable String getJSBundleFile() {
-      if (BuildConfig.DEBUG) {
-        return super.getJSBundleFile();
-      } else {
-        return UpdatesController.getInstance().getLaunchAssetFile();
-      }
-    }
-
-    @Override
-    protected @Nullable String getBundleAssetName() {
-      if (BuildConfig.DEBUG) {
-        return super.getBundleAssetName();
-      } else {
-        return UpdatesController.getInstance().getBundleAssetName();
-      }
-    }
   };

...

   public void onCreate() {
     super.onCreate();
     SoLoader.init(this, /* native exopackage */ false);
-
-    if (!BuildConfig.DEBUG) {
-      UpdatesController.initialize(this);
-    }
-
     initializeFlipper(this, getReactNativeHost().getReactInstanceManager());
   }
 }
```
8. To adjust for https without ssl verification, create new **`CustomClientFactory`** in project and **`setOkHttpClientFactory`** in **`MainApplication.java`** accordingly:
```java
import com.facebook.react.modules.network.OkHttpClientFactory;
import com.facebook.react.modules.network.OkHttpClientFactory;
import com.facebook.react.modules.network.OkHttpClientProvider;
import com.facebook.react.modules.network.ReactCookieJarContainer;

import java.security.cert.CertificateException;
import java.util.ArrayList;
import java.util.List;
import java.util.concurrent.TimeUnit;

import javax.net.ssl.HostnameVerifier;
import javax.net.ssl.SSLContext;
import javax.net.ssl.SSLSession;
import javax.net.ssl.SSLSocketFactory;
import javax.net.ssl.TrustManager;
import javax.net.ssl.X509TrustManager;

import okhttp3.CipherSuite;
import okhttp3.ConnectionSpec;
import okhttp3.OkHttpClient;
import okhttp3.TlsVersion;

import static android.content.ContentValues.TAG;

public class CustomClientFactory implements OkHttpClientFactory {
    private static final String TAG = "OkHttpClientFactory";
    @Override
    public OkHttpClient createNewNetworkModuleClient() {
        try {
            // Create a trust manager that does not validate certificate chains
            final TrustManager[] trustAllCerts = new TrustManager[]{
                    new X509TrustManager() {
                        @Override
                        public void checkClientTrusted(java.security.cert.X509Certificate[] chain, String authType) throws CertificateException {
                        }

                        @Override
                        public void checkServerTrusted(java.security.cert.X509Certificate[] chain, String authType) throws CertificateException {
                        }

                        @Override
                        public java.security.cert.X509Certificate[] getAcceptedIssuers() {
                            return new java.security.cert.X509Certificate[]{};
                        }
                    }
            };

            // Install the all-trusting trust manager
            final SSLContext sslContext = SSLContext.getInstance("SSL");
            sslContext.init(null, trustAllCerts, new java.security.SecureRandom());
            // Create an ssl socket factory with our all-trusting manager
            final SSLSocketFactory sslSocketFactory = sslContext.getSocketFactory();



            OkHttpClient.Builder builder = new OkHttpClient.Builder()
                    .connectTimeout(0, TimeUnit.MILLISECONDS).readTimeout(0, TimeUnit.MILLISECONDS)
                    .writeTimeout(0, TimeUnit.MILLISECONDS).cookieJar(new ReactCookieJarContainer());
            builder.sslSocketFactory(sslSocketFactory, (X509TrustManager) trustAllCerts[0]);
            builder.hostnameVerifier(new HostnameVerifier() {
                @Override
                public boolean verify(String hostname, SSLSession session) {
                    return true;
                }
            });

            OkHttpClient okHttpClient = builder.build();
            return okHttpClient;
        } catch (Exception e) {
            Log.e(TAG, e.getMessage());
            throw new RuntimeException(e);
        }
    }

}
```
```diff
 @Override
   public void onCreate() {
     super.onCreate();
     SoLoader.init(this, /* native exopackage */ false);  
+    OkHttpClientProvider.setOkHttpClientFactory(new CustomClientFactory()); 
 }
```
9. Sync project
10. Add **`assets`** folder to **`android/app/src/main`**
11. Run the following:
**```react-native bundle --platform android --dev false --entry-file index.js --bundle-output android/app/src/main/assets/index.android.bundle --assets-dest android/app/src/main/res/```**
12. Connect to your tablet / phone, use **`react-native run-android`** to build to device
13. Generated apk can be found in **`android/app/build/outputs/apk`**
14. To debug while connecting to devices, use **`adb logcat *:E`**
