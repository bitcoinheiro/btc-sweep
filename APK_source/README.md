# **Android Studio: Step-by-Step WebView APK Guide**

This guide details how to wrap your all files on this folder APK_source into a native Android application. This is the recommended method for using the camera features on a mobile device without requiring a web server.


### **Prerequisites**

* **Android Studio** (Latest version: Hedgehog, Iguana, or Ladybug).  
* All files on folder btc-sweep/APK_Source/


### **Step 1: Create a New Project**

1. Open Android Studio and select **New Project**.  
2. Choose **Empty Views Activity**.  
3. **Name:** BTC Sweep
4. **Language:** Java.  
5. **Minimum SDK:** API 24: Android 7.0 or higher.


### **Step 2: Configure Permissions & Meta-Data (GrapheneOS Fix)**

Navigate to app \> manifests \> AndroidManifest.xml.

1. Add these **above** the ``\<application\>`` tag:
```
\<uses-permission android:name="android.permission.INTERNET" /\>  
\<uses-permission android:name="android.permission.CAMERA" /\>  
\<uses-feature android:name="android.hardware.camera" android:required="false" /\>  
\<uses-feature android:name="android.hardware.camera.autofocus" android:required="false" /\>
```

2. Inside the ``\<application\>`` tag, **below** the activity but before the closing ``\</application\>``, add this meta-data. This satisfies the Trichome WebView's Barcode Detection requirement:

```
\<meta-data  
    android:name="com.google.android.gms.version"  
    android:value="@integer/google\_play\_services\_version" /\>

\<\!-- Force hardware acceleration for QR scanning textures \--\>  
\<meta-data  
    android:name="android.webkit.WebView.EnableSafeBrowsing"  
    android:value="false" /\>
```

### **Step 3: Add your HTML file**

1. Right-click app \-\> **New \> Folder \> Assets Folder**.  
2. Copy your all the content from the dowloaded ``APK_source`` folder into the newly created assets folder (except the ``icon`` folder)
>  * html5.qrcode.min.js
>  * index.html
>  * qrcode.min.js
>  * tailwindcss.js

### **Step 4: Add an App Icon (optional)**

1. Right-click the **app** folder \-\> **New \> Image Asset**.  
2. Configure your launcher icons with icon file from ``icon`` folder and click Finish.


### **Step 5: Setup the Layout**

Navigate to app \> res \> layout \> activity\_main.xml:
```
\<?xml version="1.0" encoding="utf-8"?\>  
\<RelativeLayout xmlns:android="\[http://schemas.android.com/apk/res/android\](http://schemas.android.com/apk/res/android)"  
    android:layout\_width="match\_parent"  
    android:layout\_height="match\_parent"\>  
    \<WebView  
        android:id="@+id/webview"  
        android:layout\_width="match\_parent"  
        android:layout\_height="match\_parent" /\>  
\</RelativeLayout\>
```

### **Step 6: Fix "Unresolved appcompat"**

In build.gradle.kts (Module: :app), add to dependencies:  
> implementation("androidx.appcompat:appcompat:1.7.1")  
> implementation("com.google.android.gms:play-services-basement:18.9.0") // Added to provide the version integer

Click Sync Now.


### **Step 7: The Logic (MainActivity.java)**

Add the following bellow your top package name string. Remove everything else.

``package com.yourname.yourappname;``

```
import android.Manifest;  
import android.content.pm.PackageManager;  
import android.os.Bundle;  
import android.webkit.PermissionRequest;  
import android.webkit.WebChromeClient;  
import android.webkit. WebSettings;  
import android.webkit.WebView;  
import androidx.appcompat.app.AppCompatActivity;  
import androidx.core.app.ActivityCompat;  
import androidx.core.content.ContextCompat;

public class MainActivity extends AppCompatActivity {  
    private WebView myWebView;

    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        setTheme(androidx.appcompat.R.style.Theme\_AppCompat\_DayNight\_NoActionBar);  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity\_main);

        myWebView \= findViewById(R.id.webview);  
          
        if (myWebView \!= null) {  
            if (ContextCompat.checkSelfPermission(this, Manifest.permission.CAMERA)   
                \!= PackageManager.PERMISSION\_GRANTED) {  
                ActivityCompat.requestPermissions(this, new String\[\]{Manifest.permission.CAMERA}, 1);  
            }

            WebSettings webSettings \= myWebView.getSettings();  
            webSettings.setJavaScriptEnabled(true);  
            webSettings.setDomStorageEnabled(true);  
            webSettings.setAllowFileAccess(true);  
            webSettings.setMediaPlaybackRequiresUserGesture(false);  
              
            // Hardening for GrapheneOS  
            webSettings.setMixedContentMode(WebSettings.MIXED\_CONTENT\_NEVER\_ALLOW);

            myWebView.setWebChromeClient(new WebChromeClient() {  
                @Override  
                public void onPermissionRequest(final PermissionRequest request) {  
                    runOnUiThread(() \-\> {  
                        // Granting specific camera resource for Trichome  
                        request.grant(request.getResources());  
                    });  
                }  
            });

            myWebView.loadUrl("file:///android\_asset/index.html");  
        }  
    }

    @Override  
    protected void onDestroy() {  
        if (myWebView \!= null) {  
            myWebView.clearCache(true);  
            myWebView.clearHistory();  
        }  
        super.onDestroy();  
    }  
}
```

### **Step 8: Build and Install**

1. Connect phone or start Emulator.  
2. Click **Green Play Button** to preview the app.
3. Verify if works and then **Build** > **Generate APKs**
4. If it does not work, verify all steps and try to debug using the Studio. Enjoy!
