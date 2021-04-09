---
title: Azure Notification Hubs でプッシュ通知を設定する | Microsoft Docs
description: Azure portal でプラットフォーム通知システム (PNS) の設定を使用して Azure Notification Hubs を設定する方法について説明します。
services: notification-hubs
author: sethmanheim
manager: femila
ms.service: notification-hubs
ms.workload: mobile
ms.topic: quickstart
ms.date: 06/22/2020
ms.author: sethm
ms.reviewer: thsomasu
ms.lastreviewed: 02/14/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5dd1044895ba55d1fbc6be7f4f4a2d7f615daa16
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94887265"
---
# <a name="quickstart-set-up-push-notifications-in-a-notification-hub"></a>クイック スタート:通知ハブのプッシュ通知を設定する

Azure Notification Hubs には、使いやすくスケールアウトにも対応したプッシュ エンジンが用意されています。Notification Hubs を使用すると、あらゆるプラットフォーム (iOS、Android、Windows、Baidu) にあらゆるバックエンド (クラウドまたはオンプレミス) から通知を送信することができます。 詳細については、「[Azure Notification Hubs とは](notification-hubs-push-notification-overview.md)」を参照してください。

このクイック スタートでは、Notification Hubs におけるプラットフォーム通知システム (PNS) の設定を使用して、複数のプラットフォームに対するプッシュ通知を設定します。 このクイック スタートでは、Azure portal での手順を紹介します。 Azure CLI を使用した場合の手順については、「[Google Firebase Cloud Messaging](?tabs=azure-cli#google-firebase-cloud-messaging-fcm)」に記載されています。

通知ハブを作成していない場合は、ここで作成します。 詳細については、「[Azure portal 内で Azure 通知ハブを作成する](create-notification-hub-portal.md)」または「[Azure CLI を使用して Azure 通知ハブを作成する](create-notification-hub-azure-cli.md)」を参照してください。

## <a name="apple-push-notification-service"></a>Apple Push Notification Service

Apple Push Notification Service (APNS) を設定するには、次の手順に従います。

1. Azure portal の **[通知ハブ]** ページで、左側のメニューの **[Apple (APNS)]** を選択します。

1. **[認証モード]** で **[証明書]** または **[トークン]** を選択します。

   a. **[証明書]** を選択した場合:
   * ファイル アイコンを選択し、アップロードする *.p12* ファイルを選択します。
   * パスワードを入力します。
   * **[サンドボックス]** モードを選択します。 または、ストアからアプリを購入したユーザーにプッシュ通知を送信する場合は、 **[Production]\(運用\)** モードを選択します。

     ![Azure portal における APNS 証明書構成のスクリーンショット](./media/notification-hubs-ios-get-started/notification-hubs-apple-config-cert.png)

   b. **[トークン]** を選択した場合:

   * **[キー ID]** 、 **[バンドル ID]** 、 **[チーム ID]** 、 **[トークン]** に値を入力します。
   * **[サンドボックス]** モードを選択します。 または、ストアからアプリを購入したユーザーにプッシュ通知を送信する場合は、 **[Production]\(運用\)** モードを選択します。

     ![Azure portal における APNS トークン構成のスクリーンショット](./media/configure-notification-hub-portal-pns-settings/notification-hubs-apple-config-token.png)

詳細については、[Azure Notification Hubs を使用して iOS アプリにプッシュ通知を送信する](ios-sdk-get-started.md)方法に関するページを参照してください。

## <a name="google-firebase-cloud-messaging-fcm"></a>Google Firebase Cloud Messaging (FCM)

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Google FCM のプッシュ通知を設定するには、次の手順に従います。

1. Azure portal の **[通知ハブ]** ページで、左側のメニューの **[Google (GCM/FCM)]** を選択します。
2. 前に保存した Google FCM プロジェクトの **API キー** を貼り付けます。
3. **[保存]** を選択します。

   ![Google FCM 用に Notification Hubs を構成する方法を示したスクリーンショット](./media/notification-hubs-android-push-notification-google-fcm-get-started/fcm-server-key.png)

これらの手順が完了すると、通知ハブが正常に更新されたことをアラートで確認できます。 **[Save]\(保存\)** ボタンが無効になります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Google Firebase Cloud Messaging (FCM) プロジェクトの **API キー** が必要となります。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment-h3.md)]

- この記事では、Azure CLI のバージョン 2.0.67 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

### <a name="set-up-push-notifications-for-google-fcm"></a>Google FCM のプッシュ通知を設定する

1. [az notification-hub credential gcm update](/cli/azure/ext/notification-hub/notification-hub/credential/gcm#ext-notification-hub-az-notification-hub-credential-gcm-update) コマンドを使用して、Google API キーを通知ハブに追加します。

   ```azurecli
   az notification-hub credential gcm update --resource-group spnhubrg --namespace-name spnhubns    --notification-hub-name spfcmtutorial1nhub --google-api-key myKey
   ```

2. Android アプリが通知ハブと接続するためには接続文字列が必要です。  使用できるアクセス ポリシーを一覧表示するには、[az notification-hub authorization-rule list](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list) コマンドを使用します。  アクセス ポリシーの接続文字列を取得するには、[az notification-hub authorization-rule list-keys](/cli/azure/ext/notification-hub/notification-hub/authorization-rule#ext-notification-hub-az-notification-hub-authorization-rule-list-keys) コマンドを使用します。  プライマリ接続文字列を直接取得するには、`--query` パラメーターに **primaryConnectionString** または **secondaryConnectionString** を指定します。

   ```azurecli
   #list access policies for a notification hub
   az notification-hub authorization-rule list --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --output table

   #list keys and connection strings for a notification hub access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --output json

   #get the primaryConnectionString for an access policy
   az notification-hub authorization-rule list-keys --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --name myAccessPolicyName --query primaryConnectionString
   ```

3. [az notification-hub test-send](/cli/azure/ext/notification-hub/notification-hub#ext-notification-hub-az-notification-hub-test-send) コマンドを使用して、Android アプリに対するメッセージ送信をテストします。

   ```azurecli
   #test with message body
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --message "my message body"

   #test with JSON string
   az notification-hub test-send --resource-group spnhubrg --namespace-name spnhubns --notification-hub-name spfcmtutorial1nhub --notification-format gcm --payload "{\"data\":{\"message\":\"my JSON string\"}}"
   ```

その他のプラットフォーム向けの Azure CLI リファレンスは、[az notification-hub credential](/cli/azure/ext/notification-hub/notification-hub/credential) コマンドで入手してください。

Android アプリケーションへの通知の送信に関する詳細については、「[Firebase を使用して Android デバイスにプッシュ通知を送信する](notification-hubs-android-push-notification-google-fcm-get-started.md)」を参照してください。

---

## <a name="windows-push-notification-service"></a>Windows プッシュ通知サービス

Windows プッシュ通知サービス (WNS) を設定するには、次の手順に従います。

1. Azure portal の **[通知ハブ]** ページで、左側のメニューの **[Windows (WNS)]** を選択します。
2. **[パッケージ SID]** と **[セキュリティ キー]** に値を入力します。
3. **[保存]** を選択します。

   ![[パッケージ SID] ボックスと [セキュリティ キー] ボックスを示すスクリーンショット](./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png)

詳細については、[Azure Notification Hubs を使用して UWP アプリに通知を送信する](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)方法に関するページを参照してください。

## <a name="microsoft-push-notification-service-for-windows-phone"></a>Windows Phone 用 Microsoft プッシュ通知サービス

Windows Phone 用 Microsoft プッシュ通知サービス (MPNS) を設定するには、次の手順に従います。

1. Azure portal の **[通知ハブ]** ページで、左側のメニューの **[Windows Phone (MPNS)]** を選択します。
1. 非認証プッシュ通知または認証済みプッシュ通知を有効にします。

   a. 非認証プッシュ通知を有効にするには、 **[非認証プッシュを有効にする]**  >  **[保存]** の順に選択します。

      ![非認証プッシュ通知を有効にする方法を示したスクリーンショット](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

   b. 認証済みプッシュ通知を有効にするには、次の手順に従います。
      * ツール バーの **[証明書のアップロード]** を選択します。
      * ファイル アイコンを選択し、証明書ファイルを選択します。
      * 証明書のパスワードを入力します。
      * **[OK]** を選択します。
      * **[Windows Phone(MPNS)]** ページで、 **[保存]** を選択します。

詳細については、[Notification Hubs を使用して Windows Phone アプリにプッシュ通知を送信する](notification-hubs-windows-mobile-push-notifications-mpns.md)方法に関するページを参照してください。

## <a name="baidu-android-china"></a>Baidu (Android China)

Baidu のプッシュ通知を設定するには、次の手順に従います。

1. Azure portal の **[通知ハブ]** ページで、左側のメニューの **[Baidu (Android China)]** を選択します。
2. Baidu コンソールから取得した、Baidu クラウド プッシュ プロジェクトの **API キー** を入力します。
3. Baidu コンソールから取得した、Baidu クラウド プッシュ プロジェクトの **秘密鍵** を入力します。
4. **[保存]** を選択します。

    ![Baidu (Android China) のプッシュ通知構成を示した Notification Hubs のスクリーンショット](./media/notification-hubs-baidu-get-started/AzureNotificationServicesBaidu.png)

これらの手順が完了すると、通知ハブが正常に更新されたことをアラートで確認できます。 **[Save]\(保存\)** ボタンが無効になります。

詳細については、[Baidu での Notification Hubs の使用](notification-hubs-baidu-china-android-notifications-get-started.md)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、Azure portal 内で通知ハブに対してプラットフォーム通知システムの設定を構成する方法について説明しました。

さまざまなプラットフォームに対してプッシュ通知を送信する方法について詳しくは、以下のチュートリアルを参照してください。

* [Azure Notification Hubs を使用して iOS アプリにプッシュ通知を送信する](ios-sdk-get-started.md)
* [Notification Hubs と Google FCM を使用して Android デバイスに通知を送信する](notification-hubs-android-push-notification-google-fcm-get-started.md)
* [Windows デバイス上で動作する UWP アプリに通知を送信する](notification-hubs-windows-store-dotnet-get-started-wns-push-notification.md)
* [MPNS を使用して Windows Phone 8 アプリに通知を送信する](notification-hubs-windows-mobile-push-notifications-mpns.md)
* [Notification Hubs と Baidu クラウド プッシュを使用して通知を送信する](notification-hubs-baidu-china-android-notifications-get-started.md)
