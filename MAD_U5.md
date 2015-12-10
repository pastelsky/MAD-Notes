## WIDGETS

###HOMESCREEN WIDGETS:
Widgets or **AppWidgets**, are visual application components that can be added to other applications. 

The most notable example is the default Android home screen, where users can add widgets to their phone-top, though any application you create can become an AppHost and support third-party widgets if you desire.

---

###CREATING A WIDGET
App Widgets are implemented as `IntentReceivers`. They use `RemoteViews` to update a view hierarchy hosted within another application process (usually the home screen).

It has 3 components:

1. A **layout resource** that defines the UI for the widget.

--

####1. XML Layout Resource


Currently, the layouts available are limited to : `FrameLayout`, `LinearLayout`, `RelativeLayout`.
 

```xml
<?xml version="1.0" encoding="utf-8"?> 

<LinearLayout
android:orientation="horizontal" android:layout_width="fill_parent" 
android:layout_height="fill_parent" android:padding="10sp">       
	 android:id="@+id/widget_image" 
	 android:layout_width="wrap_content" android:layout_height="wrap_content" 
	 android:src="@drawable/icon"
	 <TextView
	android:layout_width="fill_parent" 
	android:layout_height="fill_parent" 
	android:text="Text Goes Here"
</LinearLayout>

```
--
####2. XML Widget settings

Widget definition resources are stored as XML in the `res/xml` folder of your project. 
######Attributes:

5. `configure` :  You can optionally specify a Activity to be launched when your widget is added to the home screen. This can be used to specify widget settings and user preferences.

```xml
<appwidget-provider

android:initialLayout="@layout/my_widget_layout" 
android:minWidth="146dp"
```
--
####3. Intent Reciever

Widgets are implemented as Intent Receivers with Intent Filters that catch broadcast Intents. The filters request widget updates using the `AppWidget.ACTION_APPWIDGET_UPDATE`, `DELETED`, `ENABLED`, and `DISABLED` actions. 

 - `RemoteViews` class is used to describe and manipulate a View hierarchy that’s hosted within another application process.
 - 
 - `AppWidgetManager` is used to update App Widgets and provide details related to them.

```java

	@Override
	AppWidgetManager appWidgetManager, int[] appWidgetIds) {
	
		final int N = appWidgetIds.length;
		
			
			
		
			appWidgetManager.updateAppWidget(appWidgetId, views);
	 
}
```

######Manifest Code

```xml
	<intent-filter>
	</intent-filter>
	
```
--
#####Adding click handlers (making widgets interacte):

```java
views.setOnClickPendingIntent(R.id.my_text_view, pendingIntent);
----
##LIVE FOLDERS



It has 2 components:



Each Live Folder item can display up to three pieces of information: an icon, a title, and a description.

--
#### 1. Live Folder Content Providers
Live Folders use a standard set of column names:


--
#### 2. Live Folder Activity
	
		
			
			// 1. Set data provider
			intent.setData(EarthquakeProvider.LIVE_FOLDER_URI); 
			
			// 2. Set action on clicked
			intent.putExtra(LiveFolders.EXTRA_LIVE_FOLDER_BASE_INTENT,
			
			//3. Set display mode as list
			
			//4. Set Icon
			
			//5. Set Name of Live folder
			intent.putExtra(LiveFolders.EXTRA_LIVE_FOLDER_NAME, "Earthquakes");
			
	}
~~~

######Manifest Code

~~~xml
	
	
</activity>
~~~

##ADDING SEARCH TO AN APPLICATION

Most Android devices feature a hardware search key. Using this framework you can expose your application-specific search functionality whenever a user presses the search button. The search box will dynamically display search results as the user types a query.

It has 3 components:

### 1. Application search Metadata
The **Application search Metadata** is a searchable metadata XML resource in the `res/xml` folder. This file specifies which  Content Provider you will be performing the search on, and the action to fire if a suggested search result is clicked.

~~~xml
<searchable xmlns:android="http://schemas.android.com/apk/res/android" 
	android:label="@string/app_name"
	android:searchSuggestAuthority="myauthority" 
	android:searchSuggestIntentAction="android.intent.action.VIEW">
~~~

###2. A search results activity

The search query that caused this search result Activity to be displayed will be returned within the calling Intent using the `SearchMananger.USER_QUERY` extra as shown in the following:

public class SearchActivity extends ListActivity { 

	@Override
		
		//1.Get the search term
		
		//2.Build query URL
		String searchQuery = Uri.withAppendedPath(EarthquakeProvider.SEARCH_URI,
	
		//3. Perform search
 		
 		//4. Set list adapter
	 	SimpleCursorAdapter searchResults = new SimpleCursorAdapter(this,
	
	}
```

######Manifest code

~~~xml
<activity android:name=".SearchActivity" android:label="Earthquake Search"> 	<intent-filter>
	</intent-filter>

~~~
###3. A content provider to supply results.

Modify existing content provider accordingly / use projections.

----

##LIVE WALLPAPER

Live Wallpaper is a new way to add an application component to the home screen introduced in Android `2.1`, which lets you create dynamic, interactive home-screen backgrounds. It is also a means for displaying information to your users directly on the home screen.

It has three components:



--
####1. Live Wallpaper Definition Resource
	android:author="@string/author" 
	android:description="@string/description" 
	android:thumbnail="@drawable/wallpapericon"
~~~

####2. Wallpaper service

Extend the `WallpaperService` class to create a wrapper Service that instantiates a `Wallpaper Service Engine` class.
 

~~~java
public class MyWallpaperService extends WallpaperService { 

	@Override
	}
~~~

######Manifest Code

~~~xml
<service android:name=".MyWallpaperService" 
	android.permission="android.permission.BIND_WALLPAPER"> 
	
	<intent-filter>
	</intent-filter>
	
</service>
~~~

####3. Wallpaper Service Engine

The `WallpaperService.Engine` class is where you create the Live Wallpaper itself.

A `Surface` is a specialized drawing canvas that supports updates from background threads, making it ideal for creating smooth, dynamic, and interactive graphics. 

~~~java
public class MyWallpaperServiceEngine extends WallpaperService.Engine { 
	
	@Override
	}

		int xPixelOffset, int yPixelOffset) { 
		
		super.onOffsetsChanged(xOffset, yOffset, xOffsetStep, yOffsetStep,
		// TODO Handle homescreen offset events when wallpaper is panned.
	
	}
}
~~~

----

##PLAYING AUDIO

Transitions in states:

1. **Initialize** the Media Player with media to play.

###Audio

####1. Packaging Audio as an Application Resource


####2,3. Initializing Audio Content for Playback



~~~java
Context appContext = getApplicationContext();

//From resource identifier

//From file
	Uri.parse("file:///sdcard/localfile.mp3"));

//From URL
	 Uri.parse("http://site.com/audio/audio.mp3"));

~~~
#### 3,4. Control playback

~~~java
//Start playback
mediaPlayer.start(); // .stop(), .pause() also supported

//Get duration
int duration = mediaPlayer.getDuration();

//Don't let screen sleep
mediaPlayer.setScreenOnWhilePlaying(true);

//Volume control
mediaPlayer.setVolume(1f, 0.5f);  //values for left and right channels
~~~

----

## RECORDING AUDIO AND VIDEO

2. **Custom UI**:If you need more fine-grained control over the video capture UI or recording settings, you can use the `MediaRecorder` class.

--

#### 1. Using Intent

Video capture action supports two optional extras :

1. `EXTRA_OUTPUT` By default, the video recorded by the video capture action will be stored in the default Media Store. If you want to record it elsewhere, you can specify an alternative URI using this extra.


	`0`:  low (MMS) quality videos
	
	`1`: high (full resolution) videos.

```java
private static int RECORD_VIDEO = 1;


	//Start video recorder
	}
	
	//After recording done, fetch the video
		
		if (requestCode == RECORD_VIDEO) {
		}
```

#### 2. Using the Media Recorder (CUSTOM UI)

```java

MediaRecorder mediaRecorder = new MediaRecorder();

mediaRecorder.setAudioSource(MediaRecorder.AudioSource.MIC); 
mediaRecorder.setVideoSource(MediaRecorder.VideoSource.CAMERA);

 mediaRecorder.setOutputFormat(MediaRecorder.OutputFormat.DEFAULT);
 
mediaRecorder.setAudioEncoder(MediaRecorder.AudioEncoder.DEFAULT); 
mediaRecorder.setVideoEncoder(MediaRecorder.VideoEncoder.DEFAULT);

mediaRecorder.setOutputFile("/sdcard/myoutputfile.mp4");

mediaRecorder.prepare();

// Step 6. Start recording!
mediaRecorder.start(); 

// Step 7. Finish recording
mediaRecorder.stop(); 
mediaRecorder.release();

```

---

## TAKING PICTURES WITH CAMERA

2. **Full image**:  If you specify an output URI using a MediaStore.EXTRA_OUTPUT extra in the launch Intent, the full-size image taken by the camera will be saved to the specified location. 

###1. Using Intent
The easiest way to take a picture using the device camera is using the `ACTION_IMAGE_CAPTURE` Media.

```java
private static int TAKE_PICTURE = 1;

//For getting a full image

	
	//File to save image to in SD Card
	
	//Start camera 

//Get clicked image

	 if (requestCode == TAKE_PICTURE) {
		//TODO Do something with the full image stored
		
	}
```

###2. Controlling camera yourself

######Step 1. Add permission to manifest

```xml
<uses-permission android:name="android.permission.CAMERA"/>
```
######Step 2 Create activity for taking pic

```java

//Step 1: Instantiate camera object 
Camera camera = Camera.open();

//Step 2: Set camera parameters
Camera.Parameters parameters = camera.getParameters();

//parameters object can be used to set SceneMode, 
//FlashMode, WhiteBalance, FocusMode etc.

camera.setParameters(parameters); 

//Step 4: *DIY* Create a surface view
// so that you can preview live feed from camera

//Step 5: Take picture
 
 //Step 6: Define callbacks 
 
	public void onShutter() { /*  Do something when the shutter closes.*/ }

	public void onPictureTaken(byte[] data, Camera camera) { 
	/* Do something with the image RAW data. */
	}


	public void onPictureTaken(byte[] data, Camera camera) {
	
		
 			outStream.write(data);
			
			Log.d("CAMERA", e.getMessage());
	}
```

---
##RECORDING AUDIO

You can use the AudioRecord class to record audio directly from the hardware buffers.

######Step 1: Specify recording permissions in manifest

```xml
<uses-permission android:name="android.permission.RECORD_AUDIO"/>
```

######Step 2: Create an activity for recording audio

```java
int frequency = 11025;

// Step 1. Create new file to store audio

try {



BufferedOutputStream bos = new BufferedOutputStream(os);
DataOutputStream dos = new DataOutputStream(bos);

//Step 4. Get and set buffer size
int bufferSize = AudioRecord.getMinBufferSize(frequency, channelConfiguration,

short[] buffer = new short[bufferSize];

//Step 5.  Create a new AudioRecord object to record the audio.
channelConfiguration, audioEncoding, bufferSize);

//Step 6. Start Recording
audioRecord.startRecording();

//Step 7. Stop recording

//Step 8. Close file stream
	
```

---
##SENDING SMS & MMS 
Android provides full SMS functionality from within your applications through the `SMSManager` class. Using the SMS Manager, you can replace the native SMS application to send text messages, react to incoming texts, or use SMS as a data transport layer.

####1. Using intent

```java


startActivity(smsIntent);
```

To make it a **MMS**:

```java

mmsIntent.putExtra("sms_body", "Please see the attached image"); 
mmsIntent.putExtra("address", "07912355432"); 
mmsIntent.putExtra(Intent.EXTRA_STREAM, attached_Uri); 
mmsIntent.setType("image/png");
```

####2. Sending SMS Messages Manually

######Step 1. Add permission to manifest

```xml
<uses-permission android:name="android.permission.SEND_SMS"/>
```

######Step 2. Create activity to send sms

```java
//Step 1. Instantiate SmsManager 
SmsManager smsManager = SmsManager.getDefault();

//Step 2. Set number and message
String sendTo = "5551234";

//Step 3. Send message!

smsManager.sendTextMessage(sendTo, null, myMessage, null, null);
```
