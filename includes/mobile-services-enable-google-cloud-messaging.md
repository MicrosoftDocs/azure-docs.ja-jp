
1. [Google Cloud Console](https://console.developers.google.com/project)に移動し、Google アカウントの資格情報でサインインします。 
2. **[プロジェクトの作成]** をクリックし、プロジェクト名を入力して、**[作成]** をクリックします。 要求された SMS の確認を実行し、 **[Create]** をもう一度クリックします。
   
    ![新しいプロジェクトの作成](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   
   
     新しい**プロジェクト名**を入力し、**[プロジェクトの作成]** をクリックします。
3. **[Utilities and More (ユーティリティおよびその他)]** ボタンをクリックし、**[プロジェクト情報]** をクリックします。 **プロジェクト番号**をメモしておきます。 クライアント アプリの `SenderId` 変数として、この値を設定する必要があります。
   
    ![ユーティリティなど](./media/mobile-services-enable-google-cloud-messaging/notification-hubs-utilities-and-more.png)
4. プロジェクトのダッシュボードで、**[Mobile APIs (Mobile API)]** の **[Google Cloud Messaging]** をクリックし、次のページで **[Enable API (API を有効にする)]** をクリックし、サービス使用条件に同意します。 
   
    ![Enabling GCM](./media/mobile-services-enable-google-cloud-messaging/enable-GCM.png)
   
    ![Enabling GCM](./media/mobile-services-enable-google-cloud-messaging/enable-gcm-2.png) 
5. プロジェクト ダッシュボードで、**[資格情報]**  >  **[資格情報の作成]**  >  **[API キー]** の順にクリックします。 
   
    ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)
6. **[新しいキーの作成]** で **[サーバー キー]** をクリックし、キーの名前を入力して、**[作成]** をクリックします。
7. **[API KEY]** の値をメモしておきます。
   
    この API キー値を使用して、Azure が GCM で認証し、アプリの代わりにプッシュ通知を送信できるようにします。

