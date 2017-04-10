1. Android Studio のツールバーのアイコンをクリックするか、またはメニューから **[ツール]**  ->  **[Android]**  ->  **[SDK マネージャー]** を順にクリックして、Android SDK マネージャーを開きます。 まだインストールしていない場合は、プロジェクトで使用する目的のバージョンの Android SDK を探し、**[Show Package Details (パッケージの詳細を表示)]** をクリックして開き、**[Google APIs (Google API)]** をオンにします。
2. **[SDK Tools]** タブをクリックします。 Google Play Services をまだインストールしていない場合は、次に示すように **[Google Play Services]** をクリックします。 次に、 **[適用]** をクリックしてインストールします。 
   
    SDK のパスは後の手順で使用するので、メモしておいてください。 
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-sdk-manager.png)
3. アプリ ディレクトリの **build.gradle** ファイルを開きます。
   
    ![](./media/notification-hubs-android-studio-add-google-play-services/notification-hubs-android-studio-add-google-play-dependency.png)
4. 次の行を *dependencies*の下に追加します。 
   
           compile 'com.google.android.gms:play-services-gcm:9.2.0'
5. ツール バーで **[Sync Project with Gradle Files]** をクリックします。
6. **AndroidManifest.xml** を開き、このタグを *application* タグに追加します。
   
        <meta-data android:name="com.google.android.gms.version"
            android:value="@integer/google_play_services_version" />



<!--HONumber=Jan17_HO1-->


