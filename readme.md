https://github.com/havesource/cordova-plugin-push#master

# As of Sept 23 2022, fixes for Android 12/Cordova-android 11 not release official repo yet,

# The fixes only available in master branch so we clone master branch as local for record purpose

# Changes

# This changes is to enable push notification on show when app is foreground in ios

- (void)userNotificationCenter:(UNUserNotificationCenter _)center
  willPresentNotification:(UNNotification _)notification
  withCompletionHandler:(void (^)(UNNotificationPresentationOptions options))completionHandler
  {
  NSLog( @"NotificationCenter Handle push from foreground" );
  // custom code to handle push while app is in the foreground
  PushPlugin \*pushHandler = [self getCommandInstance:@"PushNotification"];
  pushHandler.notificationMessage = notification.request.content.userInfo;
  pushHandler.isInline = YES;
  [pushHandler notificationReceived];

      // completionHandler(UNNotificationPresentationOptionNone);
          completionHandler(UNNotificationPresentationOptionAlert);

  }

- from
  completionHandler(UNNotificationPresentationOptionNone);
- to
  completionHandler(UNNotificationPresentationOptionAlert);

# Added this code to apps/showroom-mb/local_plugins/cordova-plugin-push/master/cordova-plugin-push/src/ios/PushPlugin.m

- Note: The code is to make sure ios return FCM token to Typescript after initialized APNS
- Redmine :https://dev.silverlakemobility.com/redmine/issues/17455
- the code is inside
- (void)didRegisterForRemoteNotificationsWithDeviceToken:(NSData \*)deviceToken {

  // Get FCM Token after APNS token initialized
  [self initRegistration];

#

# Redmine #19239 - Megat

# PushPlugin.m

- Line 218 - init method - Commented isLine

# AppDelegate+notification.m

- Line 198 - willPresentNotification method - commented notificationReceived method calling
  // [pushHandler notificationReceived];

# www/push.js

- Line 50 - Under success code block, changed
  from \_this.emit(result.additionalData.actionCallback, result);
  to \_this.emit('notification', result);

# src/push.js

- Line 38 - Under success code block, changed
  from \_this.emit(result.additionalData.actionCallback, result);
  to \_this.emit('notification', result);

# Redmine #19928 - Jenn Hong

affected path: showroom-nx/apps/showroom-mb/local_plugins/cordova-plugin-push/master/cordova-plugin-push/src/android/com/adobe/phonegap/push/FCMService.kt

===========

Line 777 - Add logging in catch block for exception event

From -
catch (e: JSONException) {
// nope
}

To -
catch (e: JSONException) {
Log.e(TAG, "JSONException caught", e)
}

=======
local-plugin-push/plugin.xml
<service android:name="com.adobe.phonegap.push.FCMService" android:exported="true">
to
<service android:name="com.adobe.phonegap.push.FCMService" android:exported="false">

local-plugin-push/cordova-plugin-push/plugin.xml
<service android:name="com.adobe.phonegap.push.FCMService" android:exported="true">
to
<service android:name="com.adobe.phonegap.push.FCMService" android:exported="false">
