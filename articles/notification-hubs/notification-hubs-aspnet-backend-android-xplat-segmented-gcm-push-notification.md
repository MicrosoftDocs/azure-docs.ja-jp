---
title: "Notification Hubs ニュース速報チュートリアル - Android"
description: "Azure Service Bus Notification Hubs を使用して Android デバイスにニュース速報通知を送信する方法を説明します。"
services: notification-hubs
documentationcenter: android
author: ysxu
manager: erikre
editor: 
ms.assetid: 3c23cb80-9d35-4dde-b26d-a7bfd4cb8f81
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: java
ms.topic: article
ms.date: 06/29/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 76ec01c874fceedab7d76b2ef58e4b45b5489f58


---
# <a name="use-notification-hubs-to-send-breaking-news"></a>Notification Hubs を使用したニュース速報の送信
[!INCLUDE [notification-hubs-selector-breaking-news](../../includes/notification-hubs-selector-breaking-news.md)]

## <a name="overview"></a>概要
このトピックでは、Azure Notification Hubs を使用してニュース速報通知を Android アプリケーションにブロードキャストする方法について説明します。 完了すると、興味のあるニュース速報カテゴリに登録し、それらのカテゴリのプッシュ通知だけを受信できるようになります。 このシナリオは、既に興味があると宣言しているユーザーのグループに通知を送信する必要がある多くのアプリケーション (RSS リーダー、音楽ファン向けアプリケーションなど) で一般的なパターンです。

ブロードキャスト シナリオは、通知ハブでの登録の作成時に 1 つ以上の "*タグ*" を追加することで有効にします。 通知がタグに送信されると、タグに登録されたすべてのデバイスが通知を受信します。 タグは文字列にすぎないため、事前にプロビジョニングする必要はありません。 タグの詳細については、「 [Notification Hubs のルーティングとタグ式](notification-hubs-tags-segment-push-message.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
このトピックは、「[Notification Hubs の使用][get-started]」で作成したアプリケーションが基になります。 このチュートリアルを開始する前に、「[Notification Hubs の使用][get-started]」を完了している必要があります。

## <a name="add-category-selection-to-the-app"></a>アプリケーションにカテゴリ選択を追加する
最初の手順として、既存のメイン アクティビティに UI 要素を追加して、ユーザーが登録するカテゴリを選択できるようにします。 ユーザーにより選択されるカテゴリは、デバイスに格納されます。 アプリが起動すると、通知ハブにデバイス登録が作成され、選択されたカテゴリがタグとして追加されます。

1. res/layout/activity_main.xml ファイルを開き、コンテンツを次の内容と置き換えます。
   
        <LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
            xmlns:tools="http://schemas.android.com/tools"
            android:layout_width="match_parent"
            android:layout_height="match_parent"
            android:paddingBottom="@dimen/activity_vertical_margin"
            android:paddingLeft="@dimen/activity_horizontal_margin"
            android:paddingRight="@dimen/activity_horizontal_margin"
            android:paddingTop="@dimen/activity_vertical_margin"
            tools:context="com.example.breakingnews.MainActivity"
            android:orientation="vertical">
   
                <CheckBox
                    android:id="@+id/worldBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_world" />
                <CheckBox
                    android:id="@+id/politicsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_politics" />
                <CheckBox
                    android:id="@+id/businessBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_business" />
                <CheckBox
                    android:id="@+id/technologyBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_technology" />
                <CheckBox
                    android:id="@+id/scienceBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_science" />
                <CheckBox
                    android:id="@+id/sportsBox"
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:text="@string/label_sports" />
                <Button
                    android:layout_width="wrap_content"
                    android:layout_height="wrap_content"
                    android:onClick="subscribe"
                    android:text="@string/button_subscribe" />
        </LinearLayout>
2. res/values/strings.xml ファイルを開き、次の行を追加します。
   
        <string name="button_subscribe">Subscribe</string>
        <string name="label_world">World</string>
        <string name="label_politics">Politics</string>
        <string name="label_business">Business</string>
        <string name="label_technology">Technology</string>
        <string name="label_science">Science</string>
        <string name="label_sports">Sports</string>
   
    main_activity.xml の画像レイアウトは次のようになります。
   
    ![][A1]
3. ここで、**MainActivity** クラスと同じパッケージに **Notifications** クラスを作成します。
   
        import java.util.HashSet;
        import java.util.Set;
   
        import android.content.Context;
        import android.content.SharedPreferences;
        import android.os.AsyncTask;
        import android.util.Log;
        import android.widget.Toast;
   
        import com.google.android.gms.gcm.GoogleCloudMessaging;
        import com.microsoft.windowsazure.messaging.NotificationHub;
   
        public class Notifications {
            private static final String PREFS_NAME = "BreakingNewsCategories";
            private GoogleCloudMessaging gcm;
            private NotificationHub hub;
            private Context context;
            private String senderId;
   
            public Notifications(Context context, String senderId, String hubName, 
                                    String listenConnectionString) {
                this.context = context;
                this.senderId = senderId;
   
                gcm = GoogleCloudMessaging.getInstance(context);
                hub = new NotificationHub(hubName, listenConnectionString, context);
            }
   
            public void storeCategoriesAndSubscribe(Set<String> categories)
            {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                settings.edit().putStringSet("categories", categories).commit();
                subscribeToCategories(categories);
            }
   
            public Set<String> retrieveCategories() {
                SharedPreferences settings = context.getSharedPreferences(PREFS_NAME, 0);
                return settings.getStringSet("categories", new HashSet<String>());
            }
   
            public void subscribeToCategories(final Set<String> categories) {
                new AsyncTask<Object, Object, Object>() {
                    @Override
                    protected Object doInBackground(Object... params) {
                        try {
                            String regid = gcm.register(senderId);
   
                            String templateBodyGCM = "{\"data\":{\"message\":\"$(messageParam)\"}}";
   
                            hub.registerTemplate(regid,"simpleGCMTemplate", templateBodyGCM, 
                                categories.toArray(new String[categories.size()]));
                        } catch (Exception e) {
                            Log.e("MainActivity", "Failed to register - " + e.getMessage());
                            return e;
                        }
                        return null;
                    }
   
                    protected void onPostExecute(Object result) {
                        String message = "Subscribed for categories: "
                                + categories.toString();
                        Toast.makeText(context, message,
                                Toast.LENGTH_LONG).show();
                    }
                }.execute(null, null, null);
            }
   
        }
   
    このクラスは、このデバイスが受信するニュースのカテゴリを格納するためにローカル ストレージを使用します。 ローカル ストレージには、これらのカテゴリを登録するメソッドも格納されます。
4. **MainActivity** クラスで、**NotificationHub** および **GoogleCloudMessaging** のプライベート フィールドを削除し、**Notifications** に対するフィールドを追加します。
   
        // private GoogleCloudMessaging gcm;
        // private NotificationHub hub;
        private Notifications notifications;
5. 次に、**onCreate** メソッドで、**hub** フィールドの初期化と **registerWithNotificationHubs** を削除します。 次に、 **Notifications** クラスのインスタンスを初期化する次の行を追加します。 

        protected void onCreate(Bundle savedInstanceState) {
            super.onCreate(savedInstanceState);
            setContentView(R.layout.activity_main);
            MyHandler.mainActivity = this;

            NotificationsManager.handleNotifications(this, SENDER_ID,
                    MyHandler.class);

            notifications = new Notifications(this, SENDER_ID, HubName, HubListenConnectionString);

            notifications.subscribeToCategories(notifications.retrieveCategories());
        }

    `HubName` と `HubListenConnectionString` は、通知ハブの名前と取得済みの *DefaultListenSharedAccessSignature* の接続文字列のプレースホルダーである `<hub name>` と `<connection string with listen access>` によって既に設定されています。

    > [AZURE.NOTE] クライアント アプリケーションを使用して配布される資格情報は一般にセキュリティで保護されないため、クライアント アプリケーションではリッスン アクセス用のキーだけを配布してください。 リッスン アクセスにより、アプリケーションが通知を登録できるようになりますが、既存の登録を変更することはできないため、通知を送信できません。 通知を送信して既存の登録を変更するセキュリティで保護されたバックエンド サービスでは、フル アクセス キーが使用されます。


1. 次に、[購読] ボタンのクリック イベントを処理する次の imports と `subscribe` メソッドを追加します。
   
        import android.widget.CheckBox;
        import java.util.HashSet;
        import java.util.Set;
   
        public void subscribe(View sender) {
            final Set<String> categories = new HashSet<String>();
   
            CheckBox world = (CheckBox) findViewById(R.id.worldBox);
            if (world.isChecked())
                categories.add("world");
            CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
            if (politics.isChecked())
                categories.add("politics");
            CheckBox business = (CheckBox) findViewById(R.id.businessBox);
            if (business.isChecked())
                categories.add("business");
            CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
            if (technology.isChecked())
                categories.add("technology");
            CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
            if (science.isChecked())
                categories.add("science");
            CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
            if (sports.isChecked())
                categories.add("sports");
   
            notifications.storeCategoriesAndSubscribe(categories);
        }
   
    このメソッドは、カテゴリのリストを作成し、 **Notifications** クラスを使用してそのリストをローカル ストレージに格納し、対応するタグを通知ハブに登録します。 カテゴリが変更されると、新しいカテゴリで登録が再作成されます。

これで、アプリケーションがデバイス上のローカル ストレージに一連のカテゴリを格納したり、ユーザーがカテゴリの選択を変更したときに通知ハブに登録できるようになりました。

## <a name="register-for-notifications"></a>通知を登録する
この手順では、ローカル ストレージに格納されたカテゴリを使用して、起動時に通知ハブに通知します。

> [!NOTE]
> Google Cloud Messaging (GCM) によって割り当てられた registrationId はいつでも変更できるので、通知の失敗を回避するため、通知を頻繁に登録するようにしてください。 この例では、アプリケーションが起動するたびに通知を登録します。 頻繁に実行されるアプリケーションの場合 (1 日に複数回など)、帯域幅を節約するため、前回の登録から 1 日経過していない場合は登録をスキップできます。
> 
> 

1. **MainActivity** クラスの **onCreate** メソッドの最後に次のコードを追加します。
   
        notifications.subscribeToCategories(notifications.retrieveCategories());
   
    これにより、アプリケーションが起動するたびに、ローカル ストレージからカテゴリを取得し、これらのカテゴリの登録を要求するようになります。 
2. 次に、`MainActivity` クラスの `onStart()` メソッドを次のように更新します。
   
    @Override  protected void onStart() {
   
        super.onStart();
        isVisible = true;
   
        Set<String> categories = notifications.retrieveCategories();
   
        CheckBox world = (CheckBox) findViewById(R.id.worldBox);
        world.setChecked(categories.contains("world"));
        CheckBox politics = (CheckBox) findViewById(R.id.politicsBox);
        politics.setChecked(categories.contains("politics"));
        CheckBox business = (CheckBox) findViewById(R.id.businessBox);
        business.setChecked(categories.contains("business"));
        CheckBox technology = (CheckBox) findViewById(R.id.technologyBox);
        technology.setChecked(categories.contains("technology"));
        CheckBox science = (CheckBox) findViewById(R.id.scienceBox);
        science.setChecked(categories.contains("science"));
        CheckBox sports = (CheckBox) findViewById(R.id.sportsBox);
        sports.setChecked(categories.contains("sports"));
    }
   
    これにより、以前に保存されたカテゴリの状態に基づいてメイン アクティビティが更新されます。

これで、アプリケーションが完成し、デバイスのローカル ストレージに一連のカテゴリを格納できるようになりました。ローカル ストレージは、ユーザーがカテゴリの選択を変更したときに通知ハブに登録するために使用されます。 次に、このアプリケーションにカテゴリ通知を送信できるバックエンドを定義します。

## <a name="sending-tagged-notifications"></a>タグ付けされた通知の送信
[!INCLUDE [notification-hubs-send-categories-template](../../includes/notification-hubs-send-categories-template.md)]

## <a name="run-the-app-and-generate-notifications"></a>アプリケーションを実行して通知を生成する
1. Android Studio で、アプリケーションをビルドし、デバイスまたはエミュレーターで起動します。
   
    アプリケーションの UI には、購読するカテゴリを選択できる一連の切り替えボタンが表示されている点に注目してください。
2. 1 つ以上のカテゴリ切り替えボタンを有効にし、 **[Subscribe]**をクリックします。
   
    アプリケーションにより、選択されたカテゴリがタグに変換され、選択されたタグの新しいデバイス登録が通知ハブから要求されます。 登録されたカテゴリが返され、トースト通知に表示されます。
3. .NET コンソール アプリケーションを実行して、新しい通知を送信します。  または、 [Azure クラシック ポータル]で通知ハブの [デバッグ] タブを使用して、タグ付けされたテンプレート通知を送信できます。
   
    選択されたカテゴリの通知がトースト通知として表示されます。

## <a name="next-steps"></a>次のステップ
このチュートリアルでは、ニュース速報をカテゴリごとにブロードキャストする方法について説明しました。 他の高度な Notification Hubs シナリオを取り上げている、次のいずれかのチュートリアルを行うことをお勧めします。

* [Notification Hubs を使用したローカライズ ニュース速報のブロードキャスト]
  
    ニュース速報アプリケーションを拡張して、ローカライズした通知を送信できるようにする方法について説明します。

<!-- Images. -->
[A1]: ./media/notification-hubs-aspnet-backend-android-breaking-news/android-breaking-news1.PNG

<!-- URLs.-->
[get-started]: notification-hubs-android-push-notification-google-gcm-get-started.md
[Notification Hubs を使用したローカライズ ニュース速報のブロードキャスト]: /manage/services/notification-hubs/breaking-news-localized-dotnet/
[Notification Hubs によるユーザーへの通知]: /manage/services/notification-hubs/notify-users
[モバイル サービス]: /develop/mobile/tutorials/get-started/
[Notification Hubs の概要]: http://msdn.microsoft.com/library/jj927170.aspx
[方法: Notification Hubs (Windows ストア)]: http://msdn.microsoft.com/library/jj927172.aspx
[アプリの提出のページ]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[マイ アプリケーション]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Windows 向け Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Azure クラシック ポータル]: https://manage.windowsazure.com
[wns オブジェクト]: http://go.microsoft.com/fwlink/p/?LinkId=260591



<!--HONumber=Nov16_HO3-->


