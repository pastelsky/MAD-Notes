## WIDGETS

###HOMESCREEN WIDGETS:
Widgets or **AppWidgets**, are visual application components that can be added to other applications. 

The most notable example is the default Android home screen, where users can add widgets to their phone-top, though any application you create can become an AppHost and support third-party widgets if you desire.Widgets enable your application to own a piece of interactive screen real estate, and an entry point, directly on the user’s home screen. A good App Widget provides useful, concise, and timely information with a minimal resource cost.

---

###CREATING A WIDGET
App Widgets are implemented as `IntentReceivers`. They use `RemoteViews` to update a view hierarchy hosted within another application process (usually the home screen).

It has 3 components:

1. A **layout resource** that defines the UI for the widget.2. An **XML definition file** that describes the metadata associated with the widget.3. An **Intent Receiver** that defines and controls the widget.

--

####1. XML Layout Resource


Currently, the layouts available are limited to : `FrameLayout`, `LinearLayout`, `RelativeLayout`.
 The Views they contain are restricted to:`AnalogClock`, `Button`, `Chronometer`, `ImageButton`, `ImageView`, `ProgressBar`, `TextView`.

```xml
<?xml version="1.0" encoding="utf-8"?> 

<LinearLayoutxmlns:android="http://schemas.android.com/apk/res/android" 
android:orientation="horizontal" android:layout_width="fill_parent" 
android:layout_height="fill_parent" android:padding="10sp">       	 <ImageView 
	 android:id="@+id/widget_image" 
	 android:layout_width="wrap_content" android:layout_height="wrap_content" 
	 android:src="@drawable/icon"	/>
	 <TextView	android:id="@+id/widget_text" 
	android:layout_width="fill_parent" 
	android:layout_height="fill_parent" 
	android:text="Text Goes Here"	/> 
</LinearLayout>

```
--
####2. XML Widget settings

Widget definition resources are stored as XML in the `res/xml` folder of your project. 
######Attributes:
 1. `initialLayout` : The layout resource to use in constructing the widget’s user interface.2. `minWidth / minHeight` : The minimum width and minimum height of the widget.3. `label` :  The title used by your widget in the widget-picker.4. `updatePeriodMillis` The minimum period between widget updates in milliseconds. Android will wake the device to update your widget at this rate.
5. `configure` :  You can optionally specify a Activity to be launched when your widget is added to the home screen. This can be used to specify widget settings and user preferences.

```xml<?xml version="1.0" encoding="utf-8"?> 
<appwidget-provider
xmlns:android="http://schemas.android.com/apk/res/android" 
android:initialLayout="@layout/my_widget_layout" 
android:minWidth="146dp"android:minHeight="146dp"android:label="My App Widget"android:updatePeriodMillis="3600000" />
```
--
####3. Intent Reciever

Widgets are implemented as Intent Receivers with Intent Filters that catch broadcast Intents. The filters request widget updates using the `AppWidget.ACTION_APPWIDGET_UPDATE`, `DELETED`, `ENABLED`, and `DISABLED` actions. 

 - `RemoteViews` class is used to describe and manipulate a View hierarchy that’s hosted within another application process.
 - 
 - `AppWidgetManager` is used to update App Widgets and provide details related to them.

```javapublic class MyAppWidget extends AppWidgetProvider { 

	@Override	public void onUpdate(Context context, 
	AppWidgetManager appWidgetManager, int[] appWidgetIds) {
	
		final int N = appWidgetIds.length;
				for (int i = 0; i < N; i++) {			int appWidgetId = appWidgetIds[i];
						// Create a Remote View			RemoteViews views = new RemoteViews(context.getPackageName(),			R.layout.my_widget_layout);
						// TODO Update the UI.
					// Notify the App Widget Manager to update the widget
			appWidgetManager.updateAppWidget(appWidgetId, views);
	 	} 
}
```

######Manifest Code

```xml<receiver android:name=".MyAppWidget" android:label="My App Widget"> 	
	<intent-filter>		<action android:name="android.appwidget.action.APPWIDGET_UPDATE" /> 
	</intent-filter>
		<meta-data		android:name="android.appwidget.provider"		android:resource="@xml/my_app_widget_info" /></receiver>
```
--
#####Adding click handlers (making widgets interacte):

```javaIntent intent = new Intent("com.paad.ACTION_WIDGET_CLICK");PendingIntent pendingIntent = PendingIntent.getBroadcast(this, 0, intent, 0); 
views.setOnClickPendingIntent(R.id.my_text_view, pendingIntent);```
----
##LIVE FOLDERS

**Live Folders** are a unique and powerful means by which your applications can expose data from their Content Providers directly on the home screen. They provide dynamic shortcuts to information stored in your application. *eg. A Live Folder to open a starred contacts list.*

It has 2 components:
1. A **Content Provider** that provides the items to be displayed using the correct column names.
2. An **Activity** responsible for creating and configuring the Live Folder by generating and return- ing a specially formatted Intent.

Each Live Folder item can display up to three pieces of information: an icon, a title, and a description.

--
#### 1. Live Folder Content Providers
Live Folders use a standard set of column names:
1. `LiveFolders._ID` : A unique identifier used to indicate which item was selected if a user clicks the Live Folder list.2. `LiveFolders.NAME` : The primary text, displayed in a large font. This is the only required column.3. `LiveFolders.DESCRIPTION` : A longer descriptive field in a smaller font, displayed beneath the name column.4. `LiveFolders.IMAGE` : An image displayed at the left of each item.When displayed, a Live Folder will use these column names to extract data from your Content Provider for display.You can also apply a projection that maps the required column names to columns used within your existing Content Provider.

--
#### 2. Live Folder ActivityThe Live Folder itself is created with an Intent returned as a result from an Activity. The Intent’s data property indicates the URI of the Content Provider supplying the data (with the appropriate projection applied), while a series of extras are used to configure settings such as the display mode, icon, and folder name.~~~java@Overridepublic void onCreate(Bundle savedInstanceState) {	super.onCreate(savedInstanceState);	String action = getIntent().getAction();
			if (LiveFolders.ACTION_CREATE_LIVE_FOLDER.equals(action)) {
					Intent intent = new Intent();
			
			// 1. Set data provider
			intent.setData(EarthquakeProvider.LIVE_FOLDER_URI); 
			
			// 2. Set action on clicked
			intent.putExtra(LiveFolders.EXTRA_LIVE_FOLDER_BASE_INTENT,			new Intent(Intent.ACTION_VIEW, EarthquakeProvider.CONTENT_URI));
			
			//3. Set display mode as list			intent.putExtra(LiveFolders.EXTRA_LIVE_FOLDER_DISPLAY_MODE, 			LiveFolders.DISPLAY_MODE_LIST);
			
			//4. Set Icon			intent.putExtra(LiveFolders.EXTRA_LIVE_FOLDER_ICON, 			Intent.ShortcutIconResource.fromContext(context,			R.drawable.icon)); 
			
			//5. Set Name of Live folder
			intent.putExtra(LiveFolders.EXTRA_LIVE_FOLDER_NAME, "Earthquakes");			setResult(RESULT_OK, createLiveFolderIntent(this)); }
					else setResult(RESULT_CANCELED);			finish(); 
	}
~~~

######Manifest Code

~~~xml<activity android:name=".MyLiveFolder " android:label="My Live Folder">
		<intent-filter>		<action android:name="android.intent.action.CREATE_LIVE_FOLDER"/>	</intent-filter> 
	
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
	android:searchSuggestIntentAction="android.intent.action.VIEW"></searchable>
~~~

###2. A search results activity

The search query that caused this search result Activity to be displayed will be returned within the calling Intent using the `SearchMananger.USER_QUERY` extra as shown in the following:
```java
public class SearchActivity extends ListActivity { 

	@Override	public void onCreate(Bundle savedInstanceState) {		super.onCreate(savedInstanceState);
		
		//1.Get the search term		String searchTerm = getIntent().getStringExtra(SearchManager.USER_QUERY); 
		
		//2.Build query URL
		String searchQuery = Uri.withAppendedPath(EarthquakeProvider.SEARCH_URI,		searchTerm);
	
		//3. Perform search 		Cursor c = getContentResolver().query(searchQuery, null, null, null, null); 
 		
 		//4. Set list adapter
	 	SimpleCursorAdapter searchResults = new SimpleCursorAdapter(this,		android.R.layout.simple_list_item, c, from, to);
			setListAdapter(searchResults); 
	}}
```

######Manifest code

~~~xml
<activity android:name=".SearchActivity" android:label="Earthquake Search"> 	<intent-filter>		<action android:name="android.intent.action.SEARCH" />		<category android:name="android.intent.category.DEFAULT" /> 
	</intent-filter>	<meta-data		android:name="android.app.searchable"		android:resource="@xml/searchable" /></activity>

~~~
###3. A content provider to supply results.

Modify existing content provider accordingly / use projections.

----

##LIVE WALLPAPER

Live Wallpaper is a new way to add an application component to the home screen introduced in Android `2.1`, which lets you create dynamic, interactive home-screen backgrounds. It is also a means for displaying information to your users directly on the home screen.

It has three components:

1. A Live Wallpaper **XML resource**2. A **Wallpaper Service** implementation3. A **Wallpaper Engine** implementation (returned through the Wallpaper Service)

--
####1. Live Wallpaper Definition ResourceThe Live Wallpaper resource definition is an XML file stored in the `res/xml ` folder. Use attributes within the` <wallpaper>` tag to define the author name, wallpaper description, and thumbnail to display in the Live Wallpaper gallery at run time. You can also use the `settingsActivity` tag to specify an Activity to launch to configure the wallpaper’s settings.~~~xml<wallpaper xmlns:android="http://schemas.android.com/apk/res/android" 
	android:author="@string/author" 
	android:description="@string/description" 
	android:thumbnail="@drawable/wallpapericon"/>
~~~

####2. Wallpaper service

Extend the `WallpaperService` class to create a wrapper Service that instantiates a `Wallpaper Service Engine` class.
 All the drawing and interaction for Live Wallpaper is handled in the `Wallpaper Service Engine` class.

~~~java
public class MyWallpaperService extends WallpaperService { 

	@Override	public Engine onCreateEngine() {		return new MyWallpaperServiceEngine(); 
	}}
~~~

######Manifest Code

~~~xml
<service android:name=".MyWallpaperService" 
	android.permission="android.permission.BIND_WALLPAPER"> 
	
	<intent-filter>		<action android:name="android.service.wallpaper.WallpaperService" /> 
	</intent-filter>
		<meta-data		android:name="android.service.wallpaper"		android:resource="@xml/wallpaper"	/> 
</service>
~~~

####3. Wallpaper Service Engine

The `WallpaperService.Engine` class is where you create the Live Wallpaper itself.The Wallpaper Service Engine encapsulates a `Surface` which is used to display the wallpaper and handle touch events. 

A `Surface` is a specialized drawing canvas that supports updates from background threads, making it ideal for creating smooth, dynamic, and interactive graphics. 

~~~java
public class MyWallpaperServiceEngine extends WallpaperService.Engine { 
	
	@Override	public void onCreate(SurfaceHolder surfaceHolder) {		super.onCreate(surfaceHolder);		// TODO Handle initialization. 
	}
 	@Override	public void onOffsetsChanged(float xOffset, float yOffset,		float xOffsetStep, float yOffsetStep, 
		int xPixelOffset, int yPixelOffset) { 
		
		super.onOffsetsChanged(xOffset, yOffset, xOffsetStep, yOffsetStep,			xPixelOffset, yPixelOffset); 
		// TODO Handle homescreen offset events when wallpaper is panned.	}
		@Override	public void onTouchEvent(MotionEvent event) {		super.onTouchEvent(event);		// TODO Handle touch and motion events. 
	}	@Override	public void onSurfaceCreated(SurfaceHolder holder) {		super.onSurfaceCreated(holder);		// TODO Surface has been created, run the Thread that will // update the 		display.	} 
}
~~~

----

##PLAYING AUDIOAndroid includes a comprehensive `Media Player` to simplify the playback of audio and video. You can play media stored in application resources, local files, Content Providers, or streamed from a network URL.

Transitions in states:

1. **Initialize** the Media Player with media to play.2. **Prepare** the Media Player for playback.3. **Start** the playback.4. **Pause or stop** the playback prior to its completing.5. Playback **complete**. 

###Audio

####1. Packaging Audio as an Application ResourceYou can include audio files in your application package by adding them to the `res/raw` folder of your resources hierarchy.
Raw resources are not compressed or manipulated in any way when packaged into your application, making them an ideal way to store pre-compressed files such as audio content.

####2,3. Initializing Audio Content for Playback
Create a new `MediaPlayer` object using the  static `create` method and set the data source of the audio. `create` autmoatically calls `prepare`.Source can be :
 - A **resource identifier** - A URI to a **local file** using the `file:// schema` - A URI to an **online audio** resource as a URL - A URI to a local **Content Provider** row

~~~java
Context appContext = getApplicationContext();

//From resource identifierMediaPlayer resourcePlayer = MediaPlayer.create(appContext, R.raw.my_audio);

//From fileMediaPlayer filePlayer = MediaPlayer.create(appContext, 
	Uri.parse("file:///sdcard/localfile.mp3"));

//From URLMediaPlayer urlPlayer = MediaPlayer.create(appContext,
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

## RECORDING AUDIO AND VIDEOAndroid offers two alternatives for recording audio and video within your application.
1. **Video camera app** : The simplest technique is to use Intents to launch the video camera app. This option lets you specify the output location and video recording quality, while letting the native video recording application handle the user experience and error handling.
2. **Custom UI**:If you need more fine-grained control over the video capture UI or recording settings, you can use the `MediaRecorder` class.

--

#### 1. Using Intent

Video capture action supports two optional extras :

1. `EXTRA_OUTPUT` By default, the video recorded by the video capture action will be stored in the default Media Store. If you want to record it elsewhere, you can specify an alternative URI using this extra.
2. `EXTRA_VIDEO_QUALITY` The video record action allows you to specify an image quality using an integer value.  

	`0`:  low (MMS) quality videos
	
	`1`: high (full resolution) videos.

```java
private static int RECORD_VIDEO = 1;
private void recordVideo(Uri outputpath) {

	//Start video recorder	Intent intent = new Intent(MediaStore.ACTION_VIDEO_CAPTURE);	startActivityForResult(intent, RECORD_VIDEO); 
	}
	
	//After recording done, fetch the video	@Override	protected void onActivityResult(int requestCode,		int resultCode, Intent data) { 
		
		if (requestCode == RECORD_VIDEO) {			Uri recordedVideo = data.getData();			// Do something with the recorded video
		}}
```

#### 2. Using the Media Recorder (CUSTOM UI)

```java

MediaRecorder mediaRecorder = new MediaRecorder();
// Step 1. Configure the input sources (mic/camera)
mediaRecorder.setAudioSource(MediaRecorder.AudioSource.MIC); 
mediaRecorder.setVideoSource(MediaRecorder.VideoSource.CAMERA);
// Step 2. Set the output format
 mediaRecorder.setOutputFormat(MediaRecorder.OutputFormat.DEFAULT);
 // Step 3. Specify the audio and video encoding 
mediaRecorder.setAudioEncoder(MediaRecorder.AudioEncoder.DEFAULT); 
mediaRecorder.setVideoEncoder(MediaRecorder.VideoEncoder.DEFAULT);
// Step 4. Specify the output file 
mediaRecorder.setOutputFile("/sdcard/myoutputfile.mp4");
// Step 5. Prepare to record 
mediaRecorder.prepare();

// Step 6. Start recording!
mediaRecorder.start(); 

// Step 7. Finish recording
mediaRecorder.stop(); 
mediaRecorder.release();

```

---

## TAKING PICTURES WITH CAMERA
The image capture action supports two modes:1. **Thumbnail**:  By default, the picture taken by the image capture action will return a thumbnail Bitmap in the data extra within the Intent parameter returned in onActivityResult. 
2. **Full image**:  If you specify an output URI using a MediaStore.EXTRA_OUTPUT extra in the launch Intent, the full-size image taken by the camera will be saved to the specified location. 

###1. Using Intent
The easiest way to take a picture using the device camera is using the `ACTION_IMAGE_CAPTURE` Media.

```java
private static int TAKE_PICTURE = 1;

//For getting a full imageprivate void saveFullImage() {
	Intent intent = new Intent(MediaStore.ACTION_IMAGE_CAPTURE);
	
	//File to save image to in SD Card	File file = new File(Environment.getExternalStorageDirectory(), "test.jpg");	intent.putExtra(MediaStore.EXTRA_OUTPUT, Uri.fromFile(file));
	
	//Start camera 	startActivityForResult(intent, TAKE_PICTURE);}

//Get clicked image@Overrideprotected void onActivityResult(int requestCode,int resultCode, Intent data) {

	 if (requestCode == TAKE_PICTURE) {		
		//TODO Do something with the full image stored		// in outputFileUri
		
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

//Step 5: Take picture camera.takePicture(shutterCallback, rawCallback, jpegCallback);
 
 //Step 6: Define callbacks 
 ShutterCallback shutterCallback = new ShutterCallback() { 
	public void onShutter() { /*  Do something when the shutter closes.*/ }};
PictureCallback rawCallback = new PictureCallback() { 
	public void onPictureTaken(byte[] data, Camera camera) { 
	/* Do something with the image RAW data. */
	}};
PictureCallback jpegCallback = new PictureCallback() { 

	public void onPictureTaken(byte[] data, Camera camera) {
			// Save the image JPEG data to the SD card 		try {
					FileOutputStream outStream = new FileOutputStream("/sdcard/test.jpg");
 			outStream.write(data);			outStream.close();
					} catch (Exception e) { 
			Log.d("CAMERA", e.getMessage());		}
	}};
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
int frequency = 11025;int channelConfiguration = AudioFormat.CHANNEL_CONFIGURATION_MONO;int audioEncoding = AudioFormat.ENCODING_PCM_16BIT;

// Step 1. Create new file to store audioFile file = new File(Environment.getExternalStorageDirectory(), "raw.pcm");

try {	file.createNewFile();} catch (IOException e) {}

try {
OutputStream os = new FileOutputStream(file); 
BufferedOutputStream bos = new BufferedOutputStream(os);
DataOutputStream dos = new DataOutputStream(bos);

//Step 4. Get and set buffer size
int bufferSize = AudioRecord.getMinBufferSize(frequency, channelConfiguration,audioEncoding);

short[] buffer = new short[bufferSize];

//Step 5.  Create a new AudioRecord object to record the audio.AudioRecord audioRecord = new AudioRecord(MediaRecorder.AudioSource.MIC, frequency,
channelConfiguration, audioEncoding, bufferSize);

//Step 6. Start Recording
audioRecord.startRecording();while (isRecording) {	int bufferReadResult = audioRecord.read(buffer, 0, bufferSize);	for (int i = 0; i < bufferReadResult; i++) dos.writeShort(buffer[i]);}

//Step 7. Stop recordingaudioRecord.stop();

//Step 8. Close file stream	dos.close();
	} catch (Throwable t) {}
```

---
##SENDING SMS & MMS 
Android provides full SMS functionality from within your applications through the `SMSManager` class. Using the SMS Manager, you can replace the native SMS application to send text messages, react to incoming texts, or use SMS as a data transport layer.

####1. Using intent

```javaIntent smsIntent = new Intent(Intent.ACTION_SENDTO, Uri.parse("sms:55512345")); //number to send to
smsIntent.putExtra("sms_body", "Press send to send me"); 

startActivity(smsIntent);
```

To make it a **MMS**:

```java
Uri attached_Uri = Uri.parse("content://media/external/images/media/1");// Create a new MMS intentIntent mmsIntent = new Intent(Intent.ACTION_SEND, attached_Uri);
mmsIntent.putExtra("sms_body", "Please see the attached image"); 
mmsIntent.putExtra("address", "07912355432"); 
mmsIntent.putExtra(Intent.EXTRA_STREAM, attached_Uri); 
mmsIntent.setType("image/png");startActivity(mmsIntent);
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
String sendTo = "5551234";String myMessage = "Android supports programmatic SMS messaging!";

//Step 3. Send message!

smsManager.sendTextMessage(sendTo, null, myMessage, null, null);
```

