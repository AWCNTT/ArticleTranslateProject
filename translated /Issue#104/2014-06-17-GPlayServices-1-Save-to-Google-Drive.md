---
layout: post
title: "GPlayServices 1：保存到Google Drive"
date: 2014-06-17 01:10
comments: true
categories: Google PlayServices Google Drive
---

#GPlayServices 1：保存到Google Drive
使用[Google Play Services][Google Play Services]的[Drive API][Drive API]保存摘要或者内容到Google Drive。

使用Google Drive API需要以下两步：（但是本文不会提及这些）
 - [设置Google Play Services SDK][Set Up Google Play Services SDK]
 - 在[google开发者控制台][Register your app in Google Developers Console]注册您的应用来激活API

开始代码。

首先使用GoogleApiClient.Builder创建[GoogleApiClient][GoogleApiClient]实例。构建和实现回调接口来管理连接并给[GPlay Service #0][GPlay Service #0]授权。

我们的目标是在Google Drive选择一个文件夹保存文件和图像。


使用[Drive.DriveApi.newOpenFileActivityBuilder()][Drive.DriveApi.newOpenFileActivityBuilder]在Google Drive中选择文件夹。该方法打开一个Open File Activity，它允许用户选择一个Drive 文件。选择后将会返回一个包含之前选择的文件的DriveId的Intent。


我们使用[OnConnected][OnConnected]接口来监听连接请求什么时候完成，设置Intent创建builder和允许Button保存。

```java
	@Override
    public void onConnected(Bundle connectionHint) {
        super.onConnected(connectionHint);

        //Enable the save button and intentPicker
        if (intentPicker == null) {
            intentPicker = buildIntent();
        }

        mSave.setEnabled(true);
    }

    private IntentSender buildIntent(){
        return Drive.DriveApi
                .newOpenFileActivityBuilder()
                .setMimeType(new String[]{DriveFolder.MIME_TYPE})
                .build(mGoogleApiClient);
    }

```

DriveFolder.MIME_TYPE允许用户选择Drive文件夹。在我们例子，用Button来打开文件夹选择器。

```java
	@Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_gdrive);

        //设置保存按钮
        mSave = (Button) findViewById(R.id.save_to_gdrive);
        mSave.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                try {
                    if (mGoogleApiClient!=null && mGoogleApiClient.isConnected()){
                        if (intentPicker==null)
                            intentPicker = buildIntent();
                        //打开文件夹选择器
                        startIntentSenderForResult(
                                intentPicker, REQUEST_CODE_PICKER, null, 0, 0, 0);
                        }
                } catch (IntentSender.SendIntentException e) {
                    Log.e(TAG, "Unable to send intent", e);
                }
            }
        });
        mSave.setEnabled(false);
    }
```

![save to drive picture][save to drive picture]

点击按钮，GoogleApiClient会打开一个选择Drive文件夹的Activity。

![selection of a Drive folder][selection of a Drive folder]

使用以下代码取回Drive文件夹id
```java
	@Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data) {
        switch (requestCode) {
            case REQUEST_CODE_PICKER:
                intentPicker = null;

                if (resultCode == RESULT_OK) {
                    //Get the folder drive id
                    mFolderDriveId = (DriveId) data.getParcelableExtra(
                            OpenFileActivityBuilder.EXTRA_RESPONSE_DRIVE_ID);

                    if (mFolderDriveId != null) {
                        //在GDrive中创建文件
                        Drive.DriveApi.newContents(mGoogleApiClient)
                                .setResultCallback(creatingFileResult);
                    }
                }
                break;
            default:
                super.onActivityResult(requestCode, resultCode, data);
                break;
        }
    }
```

接着我们实现[ResultCallback][ResultCallback]异步方法保存文件夹里面的图片。

```java
	 final private ResultCallback creatingFileResult = new
            ResultCallback() {
                @Override
                public void onResult(DriveApi.ContentsResult result) {
                    if (!result.getStatus().isSuccess()) {
                        showMessage("Error while trying to create new file contents");
                        return;
                    }

                    DriveFolder folder = Drive.DriveApi.getFolder(mGoogleApiClient, mFolderDriveId);
                    OutputStream outputStream = result.getContents().getOutputStream();

                    Bitmap image = BitmapFactory.decodeResource(getResources(), R.drawable.sea);
                    ByteArrayOutputStream bitmapStream = new ByteArrayOutputStream();
                    image.compress(Bitmap.CompressFormat.PNG, 100, bitmapStream);
                    try {
                        outputStream.write(bitmapStream.toByteArray());
                    } catch (IOException e1) {
                        Log.i(TAG, "Unable to write file contents.");
                    }

                    MetadataChangeSet changeSet = new MetadataChangeSet.Builder()
                            .setTitle("New file") //Your title
                            .setMimeType("image/jpeg")  //Your mime type
                            .setStarred(false).build();

                    folder.createFile(mGoogleApiClient, changeSet, result.getContents())
                            .setResultCallback(fileCallback);
                }
            };
```
当然这仅仅是一个例子。
另外也可以用异步来知悉我们图片的保存是否成功。
```java
	 final private ResultCallback fileCallback = new
            ResultCallback() {
                @Override
                public void onResult(DriveFolder.DriveFileResult result) {
                    if (!result.getStatus().isSuccess()) {
                        showMessage("Error while trying to create the file");
                        return;
                    }

                    showMessage("File saved on Google Drive");
                }
            };
```
![picture save success][picture save success]

就这样！

代码在[GitHub][GitHub]。

---


原文地址：[http://gmariotti.blogspot.com/2014/05/gplayservices-1-save-to-google-drive.html](http://gmariotti.blogspot.com/2014/05/gplayservices-1-save-to-google-drive.html)

译者：[whosea](https://github.com/whosea) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)

[picture save success]:http://awcntt-article-image.qiniudn.com/issue104_ex6.png
[selection of a Drive folder]:http://awcntt-article-image.qiniudn.com/issue104_ex4.png
[save to drive picture]:http://awcntt-article-image.qiniudn.com/issue104_ex5.png

[OnConnected]:http://developer.android.com/reference/com/google/android/gms/common/api/GoogleApiClient.ConnectionCallbacks.html#onConnected(android.os.Bundle)

[GitHub]:https://github.com/gabrielemariotti/androiddev/tree/master/playservices

[Drive.DriveApi.newOpenFileActivityBuilder]:http://developer.android.com/reference/com/google/android/gms/drive/DriveApi.html#newOpenFileActivityBuilder()

[ResultCallback]:http://developer.android.com/reference/com/google/android/gms/common/api/ResultCallback.html

[GPlay Service #0]:http://gmariotti.blogspot.com/2014/05/gplayservices-0-configure.html

[Google Play Services]:http://developer.android.com/google/play-services/index.html

[Drive API]:http://developer.android.com/google/play-services/drive.html

[Set Up Google Play Services SDK]:http://developer.android.com/google/play-services/setup.html

[Register your app in Google Developers Console]:https://developers.google.com/drive/android/auth

[GoogleApiClient]:http://developer.android.com/reference/com/google/android/gms/common/api/GoogleApiClient.html
