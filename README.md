# Android Image Open Using Gallery
Android demo app to open any image using Gallery (it shows a chooser)

This demo app shows a bare bone android app to open any image stored in Documents or Downloads etc, in an image viewer (Gallery or any default image viewer)

On most places on internet you will find these 3 steps. Fourth one is extremely important:

1) Add permission in AndroidManifest

         <uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
         <uses-permission android:name="android.permission.WRITE_EXTERNAL_STORAGE" />

         <provider
              android:name="androidx.core.content.FileProvider"
              android:authorities="com.example.fileopen.fileprovider"
              android:exported="false"
              android:grantUriPermissions="true">
              <meta-data
                  android:name="android.support.FILE_PROVIDER_PATHS"
                  android:resource="@xml/provider_paths" />
          </provider>
2) Add provider_paths.xml under /res/
   
        <?xml version="1.0" encoding="utf-8"?>
        <paths>
            <files-path name="files" path="."/>
            <external-files-path name="my_images" path="." />
            <external-path name="files_root" path="." />
        </paths>

3) Use an ACTION_VIEW intent in your activity like:

       Uri uri = FileProvider.getUriForFile(this, this.getApplicationContext().getPackageName() + ".fileprovider", fileStr);
       intent = new Intent();
       intent.setAction(Intent.ACTION_VIEW);
       intent.addFlags(Intent.FLAG_GRANT_READ_URI_PERMISSION);
  
       intent.setDataAndType(uri, "image/*");
       startActivity(intent);

4) Yoy MUST add explicit permission in your activity. If permissions are not granted, no matter if you have already provided permissions in your AndroidManifest.xml, the app will not work and keep giving 'unable to diaplay image' kind of errors ..

        protected boolean shouldAskPermissions() {
            return (Build.VERSION.SDK_INT > Build.VERSION_CODES.LOLLIPOP_MR1);
        }
    
        @TargetApi(23)
        protected void askPermissions() {
            String[] permissions = {
                    "android.permission.READ_EXTERNAL_STORAGE",
                    "android.permission.WRITE_EXTERNAL_STORAGE"
            };
            int requestCode = 200;
            requestPermissions(permissions, requestCode);
        }

        @Override
        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
    
            if (shouldAskPermissions()) {
                askPermissions();
            }
        }
