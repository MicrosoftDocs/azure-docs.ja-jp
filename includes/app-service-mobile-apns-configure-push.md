

1. お使いの Mac で、 **Keychain Access**を起動します。 左側のナビゲーション バーで、**[分類]** の **[自分の証明書]** を開きます。 前のセクションでダウンロードした SSL 証明書を見つけ、その内容を開示します。 (秘密鍵を選択せずに) 証明書のみを選択し、それを[エクスポート](https://support.apple.com/kb/PH20122?locale=en_US)します。
2. [Azure Portal](https://portal.azure.com/) で、**[すべて参照]** > **[App Services]** の順にクリックし、Mobile Apps バックエンドをクリックします。 **[設定]** で、**[App Service Push (App Service プッシュ)]**、通知ハブ名の順にクリックします。 **[Apple Push Notification Service]** > **[証明書のアップロード]** の順に選択します。 (前の手順のクライアント SSL 証明書が実稼働環境用かサンドボックス用かに応じて) 適切な**モード**を選択して、.p12 ファイルをアップロードします。 すべての変更を保存します。

これで、iOS のプッシュ通知と連携するようにサービスが構成されました。

[1]: ./media/app-service-mobile-apns-configure-push/mobile-push-notification-hub.png


<!--HONumber=Dec16_HO2-->


