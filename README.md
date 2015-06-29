# SecuringContentProviders-Android
 Securing Content Providers

 Content Providers are the means to share data to other apps. As, there is a need to have a communication channel between different consumers and suppliers, there is a need to make this channel more secure. Below are the points that need to be taken into consideration.
	
* If your content provider is just for your app’s use then set it to be android:exported=false in the manifest. If you are intentionally exporting the content provider then you should also specify one or more permissions for reading and writing.

* Define custom readPermissions and writePermissions for the contentProvider. Also, it should be noted that having the write permission does not automatically imply the read permission. 
e.g. :

		<provider
		   android:name=".SecureContentProvider"
		  android:authorities="cp.securecode.dt.provider"
		  android:readPermission="com.dt.secureContentProvider.READ_DATA"
		  android:writePermission="com.dt.secureContentProvider.WRITE_DATA">
		</provider>
	
* Using its query() method requires the read permission, while using its    insert(),     update(), or delete() methods requires the write permission, as specified in the manifest declaration (READ_DATA and WRITE_DATA in this case).

* However, when permissions are enforced using these methods, they apply to all of the data within a given provider. At times, it is needed to grant permissions to certain portions of a Content Provider’s database for a short period of time. In such scenarios, uriPermission system of android is used. In order to use it, a Content Provider must be configured to allow this in the Android Manifest. Because this system is used to extend permissions beyond what is normally configured, the mechanism is turned off by default. There are two ways to enable URI permissions: one that covers any URI contained within the Content Provider and another that covers only a subset of URIs. In order to enable temporary URI permissions globally within a Content Provider, set the grantUriPermissions attribute in the <provider> declaration to true, like this:
e.g. :

		<provider
		  android:name=".SecureContentProvider"
		  android:authorities="cp.securecode.dt.provider"
		  android:readPermission="com.dt.secureContentProvider.READ_DATA"
		  android:writePermission="com.dt.secureContentProvider.WRITE_DATA"
		  android:grantUriPermissions=”true”>
		</provider>

* The global configuration allows the Content Provider to grant permissions to any accessible URI within it. It is possible that the user is interested in granting temporary permission to a subset of URIs. In such cases, user can omit the grantUriPermissions attribute and instead include separate <grant-uri-permission> elements within the <provider> element.
 e.g:

		<provider
		              android:name=".SecureContentProvider"
		              android:authorities="cp.securecode.dt.provider"
		              android:readPermission= "com.dt.secureContentProvider.READ_DATA"
		              android:writePermission="com.dt.secureContentProvider.WRITE_DATA">
		 <grant-uri-permission android:path="/attachemnts/"></grant-uri-permission>
		 <grant-uri-permission android:pathPattern=".*public*."></grant-uri-permission>
		 <grant-uri-permission android:pathPrefix="/messages"></grant-uri-permission>
		 </provider>

* The next step is to grant these permissions using the app. Below is the code snippet for the same.

		Intent intent = new Intent(Intent.ACTION_VIEW);
		intent.setFlags(Intent.FLAG_ACTIVITY_NEW_TASK);
		intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
		intent.setDataAndType(uri, "image/gif");
		startActivity(intent);

* Alternatively, the other app can directly be granted the READ/WRITE permission on the URI using the below code snippet.

		uri = "content://com.dt.securecode.testap.mailprovider/attachments/42";
		Context.grantUriPermission("com.dt.securecode.testapp", uri,
		Intent.FLAG_GRANT_READ_URI_PERMISSION)

* Also, do not forget to revoke the permission once the activity has been closed. Below is the way to do that.

		uri = "content://com.dt.securecode.testap.mailprovider/attachments/42";
		Context.revokeUriPermission(uri, Intent.FLAG_GRANT_READ_URI_PERMISSION);
 			      

* If you are using a content provider for sharing data between only your own apps, it is preferable to use the android:protectionLevel attribute set to “signature” protection for the custom permissions. 

* When accessing a content provider, use parameterized query methods such as query(), update(), and delete() to avoid potential SQL injection from untrusted sources.



https://www.safaribooksonline.com/library/view/application-security-for/9781449322250/ch04.html 
http://code.google.com/android/reference/android/R.styleable.html#AndroidManifestGrantUriPermission