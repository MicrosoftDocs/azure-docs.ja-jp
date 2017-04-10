
1. [Azure Portal](https://portal.azure.com/) で、**[すべて参照]** > **[App Services]** の順にクリックし、Mobile Apps バックエンドをクリックします。 **[設定]** で、**[App Service Push (App Service プッシュ)]**、通知ハブ名の順にクリックします。
2. **[Windows (WNS)]** に移動し、Live サービス サイトから取得した**セキュリティ キー** (クライアント シークレット) と**パッケージ SID** を入力し、**[保存]** をクリックします。

    ![ポータルで WNS キーを設定する](./media/app-service-mobile-configure-wns/mobile-push-wns-credentials.png)

バックエンドは WNS を使用してプッシュ通知を送信するよう構成されました。


<!--HONumber=Dec16_HO2-->


