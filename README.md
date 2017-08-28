OAuthKit 
=============


<img src="http://i.imgur.com/Fmmv1Zh.png" height="200"/> 

A convenience library for Android that uses signpost and an okhttp client to complete OAuth 1.0 easily with an RxJava adapted Retrofit service.

It was started from codepath's excellent helper code and uses the signpost-okhttp module.


To use:

1) add to gradle as a dependency:

    compile project(':oauthkit')



2) There is really no means to hide private key info in the end, so we do the simple thing.

 Add this configuration to local.properties in the root of your app.
 This file is automatically generated and can be ignored by vcs.
  *remember to escape ‘colon :’ with a  backslash ’\’ and enclose values in quotes!
 
in local.properties:
 
    baseUrl="base_url_here"
    callbackUrl="oauth\://cprest"  
    consumerKey="<consumer_key_from_api_provider>"
    consumerSecret="<consumer_secret_from_api_provider>"
    requestTokenEndpoint="req_url_here"
    accessTokenEndpoint="access_url_here"
    authorizationEndpoint="auth_url_here"


2) In your login activity:
 extend OAuthLoginActivity
 
 e.g.
 
        LoginActivity extends OAuthLoginActivity
 
 3) In your AndroidManifest.xml
 
      <activity android:name="com.anubis.oauthkit.OAuthLoginActivity" />
 
 4)In your AndroidManifest.xml
 
 **Place this intent filter in your launcher activity, which should be the login activity from 2)
    
        <intent-filter>
                <action android:name="android.intent.action.VIEW" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="android.intent.category.BROWSABLE" />

                <data
                    android:host="cprest"
                    android:scheme="oauth" />
       </intent-filter>
**Note: if you change the name of the callbackUrl in the local.properties, it must match the host and scheme in the
data element of this filter.

5)in your LoginActivity, 
      
      OAuthBaseClient client;
  
       client = OAuthBaseClient.getInstance(<pass_in_the_context>, this);
       client.connect();
   
   Then override the success and failure callbacks:
   
        @Override
        public void onLoginSuccess(OkHttpOAuthConsumer consumer, String baseUrl) {
        
        }
        @Override
        public void onLoginFailure(Exception e) {}
        
        //note here that you can pass the consumer from success callback into an okhttpclient, which
        // can be set into an RxJava adapted Retrofit Builder
        e.g. (elsewhere in code)
         OkHttpClient okHttpClient = new OkHttpClient.Builder()
                .addInterceptor(new SigningInterceptor(consumer))
                .addNetworkInterceptor(new StethoInterceptor())
                .build();


        RxJavaCallAdapterFactory rxAdapter = RxJavaCallAdapterFactory.createWithScheduler(Schedulers.io());
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl(baseUrl)
                .client(okHttpClient)
                .addConverterFactory(converter)
                .addCallAdapterFactory(rxAdapter)
                .build();
        }
    

        
 Now you have a signing retrofit service!
 
 6) All response params and tokens are set in SharedPreferences.
 
 Access them via:
          
          SharedPreferences authPrefs = getApplicationContext().getSharedPreferences("OAuthKit_Prefs", 0);
 
 ENJOY!
     




 
 

