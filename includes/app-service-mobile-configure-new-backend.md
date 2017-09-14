
1. **[App Services]** ボタンをクリックして Mobile Apps バックエンドを選択し、**[クイック スタート]** を選択して目的のクライアント プラットフォーム (iOS、Android、Xamarin、Cordova) を選択します。

    ![Mobile Apps のクイックスタートが強調表示された Azure Portal][quickstart]

2. データベース接続が構成されていない場合は、次の手順で作成します。

    ![Azure Portal (Mobile Apps をデータベースに接続)][connect]

    a. 新しい SQL データベースとサーバーを作成します。

    ![Azure Portal (Mobile Apps の新しいデータベースとサーバーを作成)][server]

    b. データ接続が正常に作成されるまで待ちます。

    ![データ接続が正常に作成されたことを示す Azure Portal の通知][notification]

    c. データ接続の作成に成功しました。

    ![Azure Portal の通知 ("データ接続が既に存在します")][already-connection]

3. **[2. テーブル API の作成]** で、**[バックエンド言語]** として Node.js を選択します。 
 
4. 確認要求をそのまま受け入れ、**[TodoItem テーブルを作成する]** を選択します。  
    この操作で新しい TodoItem テーブルがデータベースに作成されます。 

    >[!IMPORTANT]
    > 既存のバックエンドを Node.js に切り替えると、すべてのコンテンツが上書きされます。 .NET バックエンドを作成するには、[Mobile Apps 用 .NET バックエンド サーバー SDK の操作][instructions]に関するページを参照してください。

<!-- Images. -->
[quickstart]: ./media/app-service-mobile-configure-new-backend/quickstart.png
[connect]: ./media/app-service-mobile-configure-new-backend/connect-to-bd.png
[notification]: ./media/app-service-mobile-configure-new-backend/notification-data-connection-create.png
[server]: ./media/app-service-mobile-configure-new-backend/create-new-server.png
[already-connection]: ./media/app-service-mobile-configure-new-backend/already-connection.png

<!-- URLs -->
[instructions]: ../articles/app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#create-app
