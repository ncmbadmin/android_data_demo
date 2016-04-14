# なんでこれを作ったか？

スマホアプリってデータを保存したいということって何って思われますね。
実は色々場面で使われています。例えば、ゲームアプリだとスコアをユーザ間共有したり、写真アプリだと画像をどこかに保存したいしたり、そのようなことですね！

うちのサービス [ニフティクラウド mobile backend - mBaaS](http://mb.cloud.nifty.com/) というものを提供していて、初心者でも、数行のコードですぐデータ保存できちゃいますので、今回簡単に紹介します。今までMonacaで見てくれている方が多いかと思いますが、今回はAndroidという切り口でやってみました！

# 作るもの

Android Studioでボタンが一個アプリを作成し、
ボタンを押したら、データが保存されます。
イメージ的は以下のようになります。
![5554_Nexus_5_API_23.png](https://qiita-image-store.s3.amazonaws.com/0/18698/da334246-9887-de61-c43a-fb086d37cfcb.png)


# 準備

* Android Studio
* mBaaSの[アカウント作成](http://mb.cloud.nifty.com/signup.htm)

# 手順

* テンプレートプロジェクトをダウンロード
* SDKを追加（済み・最新SDKを利用したい場合、更新作業として行ってください）
* アプリ作成し、キーを設定
* 動作確認

# STEP 1. テンプレートプロジェクト

* プロジェクトの[Githubページ](https://github.com/ncmbadmin/android_data_demo)から「Download ZIP」をクリックします。
* プロジェクトを解凍します
* AndroidStudioを開きます、既存プロジェクトを開くことを選択します。

![5554_Nexus_5_API_23_2.png](https://qiita-image-store.s3.amazonaws.com/0/18698/e6d33cfd-978d-8688-a7ad-de0e9bc90daf.png)
先ほどダウンロードしたプロジェクトを選択し開きます。
![androidstudio1.png](https://qiita-image-store.s3.amazonaws.com/0/18698/f1766700-09c9-8b24-b569-997ec9ba5207.png)


# STEP 2. SDKを追加と設定 (済み)

SDKとはニフティクラウドmobile backendが提供している「データストア」「プッシュ通知」などの機能をAndroidからも簡単にコード書ける（数行ぐらい）ライブラリーのものです。

![002.png](https://qiita-image-store.s3.amazonaws.com/0/18698/75b7512c-7dec-9931-b8f6-66f6dd5a73af.png)

mBaaSでは、Android, iOS, Unity, JavaScript SDKを提供しています。
今回Android SDKの追加し方と設定を紹介します。
※ダウンロードしたプロジェクトには既に設定済みですが、最新ではない場合、ご自身で入れ替えてください！またご自身のプロジェクトにもSDKを追加したい場合も同じく実装必要です。

* SDKダウンロード
SDKはここ（SDK[リリースページ](https://github.com/NIFTYCloud-mbaas/ncmb_android/releases)）から取得してください.
  - NCMB.jarファイルがダウンロードします。
* SDKをインポート
  - app/libsフォルダにNCMB.jarをコピーします
* 設定追加
  - app/build.gradleファイルに以下を追加します

```
dependencies {
    compile 'com.google.code.gson:gson:2.3.1'
    compile files('libs/NCMB.jar')
}
```
  - androidManifestの設定

<application>タグの直前に以下のpermissionを追加します。

```
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
```


# STEP 3. アプリキー設定

* 会員登録（無料）をし、登録ができたらログインをすると下図のように「アプリの新規作成」画面出るのでアプリを作成します
![003.png](https://qiita-image-store.s3.amazonaws.com/0/18698/366743e0-834f-7f3a-e4f8-0e224f260684.png)
* アプリ作成されると下図のような画面になります
* この２種類のAPIキー（アプリケーションキーとクライアントキー）は先ほどインポートしたAndroidStudioで作成するAndroidアプリにニフティクラウドmobile backendの紐付けるため、あとで使います.

![004.png](https://qiita-image-store.s3.amazonaws.com/0/18698/19a7c03c-ee30-4273-707c-122d668ee89a.png)

この後動作確認でデータが保存される場所も確認しておきましょう

![005.png](https://qiita-image-store.s3.amazonaws.com/0/18698/736ec57c-cd46-18b6-34e9-70d0875ecf53.png)

* AndroidStudioでの設定

![MainActivity_java_-_android_data_demo-master_-____Downloads_android_data_demo-master_.png](https://qiita-image-store.s3.amazonaws.com/0/18698/84e4d3dc-2af8-f44d-5eea-3f9296c35fc1.png)

![ニフティクラウドmobile_backend_2.png](https://qiita-image-store.s3.amazonaws.com/0/18698/9d9e4ccf-d126-3023-f8f2-f767d699e7a1.png)



# STEP 4. 確認

アプリにてボタンをタブし、保存成功！とメッセージが表示しました。
![5554_Nexus_5_API_23_3.png](https://qiita-image-store.s3.amazonaws.com/0/18698/da17975f-6bc7-1665-2fbb-f12895088637.png)
mBaaS側もデータが保存されたことを確認しています！

![ニフティクラウドmobile_backend_7.png](https://qiita-image-store.s3.amazonaws.com/0/18698/b88029ab-f823-7964-2176-aafea0ab7b93.png)



# コード説明

* SDKおよび必要なライブラリーをインポートします

```
import com.nifty.cloud.mb.core.DoneCallback;
import com.nifty.cloud.mb.core.NCMB;
import com.nifty.cloud.mb.core.NCMBException;
import com.nifty.cloud.mb.core.NCMBObject;
```

* SDKを初期化

MainActivityのOnCreateメソッドに実装、ここでAPIキーを渡します。

```java


 @Override
    protected void onCreate(Bundle savedInstanceState) {
       <省略>
        //**************** APIキーの設定とSDKの初期化 **********************
        NCMB.initialize(this, "YOUR_APPLICATION_KEY", "YOUR_CLIENT_KEY");
    }
```

* データを保存する実装

* mBaaSのAndroid SDKが提供するNCMBObjectクラスがデータストアを操作するためのクラス。データを保存するには、このクラスが提供するsaveInBackgroundメソッドを利用し、保存します。
* NCMBObjectのインスタンスobjを作成し、"TestClass"を指定することで、データストアにTestClassクラスを操作することができます。
* インスタンスobjに（キー、バリュー）というふうに、設定します。この場合、"キー"が"message", その"バリュー"が"Hello, NCMB!"として指定しています。
* saveInBackground()を実施することで、非同期に保存が行われます。非同期実施するため、DoneCallBack()を使って、成功・失敗処理を指定します。
 - 成功する場合、アラートで保存したIDを合わせて表示します。
 - 失敗する場合、アラートでエラーを合わせて表示します。

```java

        final NCMBObject obj = new NCMBObject("TestClass");
        obj.put("message", "Hello, NCMB!");
        obj.saveInBackground(new DoneCallback() {
            @Override
            public void done(NCMBException e) {
                if (e != null) {
                    //保存失敗
                    new AlertDialog.Builder(MainActivity.this)
                            .setTitle("Notification from Nifty")
                            .setMessage("Error:" + e.getMessage())
                            .setPositiveButton("OK", null)
                            .show();

                } else {
                    //保存成功
                    new AlertDialog.Builder(MainActivity.this)
                            .setTitle("Notification from Nifty")
                            .setMessage("Save successfull! with ID:" + obj.getObjectId())
                            .setPositiveButton("OK", null)
                            .show();

                }
            }
        });
```

# 参考

サンプルコードをカスタマイズすることで、様々な機能を実装できます！
データ保存・データ検索・会員管理・プッシュ通知などの機能を実装したい場合には、
以下のドキュメントもご参考ください。

* [ドキュメント](http://mb.cloud.nifty.com/doc/current/)
* [ドキュメント・データストア](http://mb.cloud.nifty.com/doc/current/datastore/basic_usage_android.html)
* [ドキュメント・会員管理](http://mb.cloud.nifty.com/doc/current/user/basic_usage_android.html)
* [ドキュメント・プッシュ通知](http://mb.cloud.nifty.com/doc/current/push/basic_usage_android.html)

# 最後に

データを保存するってサーバを立てたり、自分でサーバ運用とか、設計とか、アプリからサーバーとのやりとりも色々考慮しなければなりません。
最短方法というのは、このようにmBaaSサービスを使って、運用、設計など自分でやらなくて済む、開発も数行コード書けばいいという便利なものはいかがでしょうか？

# Contributing

*    Fork it!
*    Create your feature branch: git checkout -b my-new-feature
*    Commit your changes: git commit -am 'Add some feature'
*    Push to the branch: git push origin my-new-feature
*    Submit a pull request :D

# License

    MITライセンス
    NIFTY Cloud mobile backendのAndroid SDKのライセンス
