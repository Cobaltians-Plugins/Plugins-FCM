# FCM 
FCM Plugin allows you to manage your notifications with the Google's service Firebase Cloud Messaging. To run this plugin with the Build Kristal server, you'll need to use the AppInfos plugin to get the device ID.
You'll also need to get the application's API key.

# How to use
* Import the plugin to your project as explain [here](https://github.com/cobaltians/cobalt/wiki/Plugins-usage)
* Add the `cobalt.appInfos.js` to your web JS folder
* Add an HTML link to the `cobalt.appInfos.js` plugin script after the cobalt link in the `HEAD` tag

# Initialization of FCM
First, you need to initialize the FCM service. 
## iOS
Add this line into the `didFinishLaunchingWithOptions` function in your AppDelegate: 

	FcmManager.initFCM(application: application)

## Android
First, you have to import Firebase into your project as it's explain [here](https://firebase.google.com/docs/android/setup). If you're using the Firebase Assistant, take care about using the same Google application as your iOS project.

Then you need to create two services, in order to catch token's refreshing and notifications. If you dont have one, create a new folder "services" into your `app/src/main/java` folder. In this directory, create two services: 

* The first extends the FirebaseMessagingService which we'll call FcmMessagingService here. Override the `onMessageReceived(RemoteMessage remoteMessage)` method by calling the FcmPlugin's `onMessageReceived(RemoteMessage remoteMessage)` method. You should have a service which looks like this: 


	    public class FcmMessagingService extends FirebaseMessagingService {
    		protected final static String TAG = FcmMessagingService.class.getSimpleName();


    		public FcmMessagingService() {
    		}

    		@Override
    		public void onMessageReceived(RemoteMessage remoteMessage) {
        		super.onMessageReceived(remoteMessage);
        		
        		// Your service delegates notifications management to FcmPlugin.
        		FcmPlugin.onMessageReceived(remoteMessage);
    		}
		}

* The second extends the FirebaseInstanceIdService which we'll call FcmInstanceIdService here. Override the `onTokenRefresh()` method by calling the FcmPlugin's `onTokenRefresh()` method. You should have a service like this: 

	    public class FcmInstanceIdService extends FirebaseInstanceIdService {
			public FcmInstanceIdService() {}

    		@Override
    		public void onTokenRefresh() {
        		// Get updated InstanceID token.
        		FcmPlugin.onTokenRefresh();
    		}
		}

# Lifecycle of FCM
You can manage the FCM's connection and disconnection as you want, you just have to call appropriate functions.
## iOS
For example, you can connect your application into the `applicationDidBecomeActive` function in your AppDelegate and disconnect into the `applicationDidEnterBackground` function in the same file.

	// To connect your application to FCM
	FcmManager.connect()
	
	// To disconnect your application to FCM
	FcmManager.disconnect()
	
## Android
You don't need to manage the connection/disconnection with Android. However, you have to instantiate the FcmPlugin. Just add this line to your `onCreate` method of your Application class:

	FcmPlugin.getInstance(Cobalt.getAppContext());

# Get the FCM Token

	cobalt.fcm.getToken(function(data){
    	cobalt.log("Token received = ", data.token);
    });
    
To avoid errors, you have to manage all your FCM actions inside of the getToken's callback. In doing this, you're sure that you'll not call an FCM action before your token was generated.

# Register a token on the server

	cobalt.fcm.tokenRegister(donnees, function(data){
		// data contains the server response
        cobalt.log("Saving the token : ", data.message);
    });
    
# Get the list of topics for an uid

	cobalt.fcm.getTopics(donnees, function(data){
		// data contains an array of topics
        cobalt.log("Recovery of topics = ", data);
    });

# Topic's subscription/unsubscription

	// To subscribe to a topic
	cobalt.fcm.subscribeToTopic('nameTopic', function(){
		cobalt.log("Subscription to topic nameTopic");
	});

	// To unsubscribe from a topic
	cobalt.fcm.unsubscribeFromTopic('nameTopic', function(){
		cobalt.log("Unsubscription from topic nameTopic");
	});
	
# Save a topic on the server
If you have to save your topics name on the server, you can call the saveTopic function like this: 

	cobalt.fcm.saveTopic(donnees, function(data){
		cobalt.log("Saving topic...", data);
	);


# Sending a notification

	cobalt.fcm.sendNotification(donnees, function(data){
		// data contains the server response
        cobalt.log("Envoi de notif... ", data);
    });

## Details of data to send
You can have the details of data to send to each action [here](https://sites.google.com/site/kristalwiki/services/builds-kristal-io/pushapi)

Repositories: 

* [Plugins-FCM-iOS](https://github.com/Cobaltians-Plugins/Plugins-FCM-iOS)
* [Plugins-FCM-Android](https://github.com/Cobaltians-Plugins/Plugins-FCM-Android)
* [Plugins-FCM-Web](https://github.com/Cobaltians-Plugins/Plugins-FCM-Web)
