
1. [Azure Portal](https://portal.azure.com/) で、**[すべて参照]** > **[App Services]** の順にクリックし、Mobile Apps バックエンドをクリックします。 **[設定]** で、**[App Service Push (App Service プッシュ)]**、通知ハブ名の順にクリックします。
2. **[Google (GCM)]** をクリックし、前の手順で Firebase から取得した **[サーバー キー]** の値を入力し、**[保存]** をクリックします。

    ![ポータルで GCM API キーを設定する](./media/app-service-mobile-android-configure-push/mobile-push-api-key.png)

これで、Mobile Apps バックエンドは、Firebase Cloud Messaging を使用するように構成されました。 これにより、通知ハブを使用して、Android デバイスで実行中のアプリにプッシュ通知を送信できます。

<!-- URLs. -->


<!-- images -->


<!--HONumber=Dec16_HO2-->


