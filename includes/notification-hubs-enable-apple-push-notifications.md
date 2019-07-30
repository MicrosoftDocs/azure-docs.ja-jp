---
title: インクルード ファイル
description: インクルード ファイル
services: notification-hubs
author: spelluru
ms.service: notification-hubs
ms.topic: include
ms.date: 08/28/2018
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 3e4549a21ec32f1a2c1c869c3b2e0bd8c2e4204e
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446558"
---
## <a name="generate-the-certificate-signing-request-file"></a>証明書の署名要求ファイルを生成する

Apple Push Notification Service (APNs) では、証明書を使用してプッシュ通知を認証します。 次の手順に従って、通知を送受信するために必要なプッシュ証明書を作成します。 これらの概念の詳細については、[Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) の公式ドキュメントを参照してください。

証明書署名要求 (CSR) ファイルを生成します。これは、Apple が署名済みのプッシュ証明書を生成するために使用します。

1. Mac で、キーチェーン アクセス ツールを実行します。 これは、Launchpad の**ユーティリティ** フォルダーまたは**その他**フォルダーから開くことができます。

1. **[キーチェーン アクセス]** を選択し、 **[証明書アシスタント]** を展開して、 **[認証局に証明書を要求]** を選択します。

    ![Keychain Access を使用して新しい証明書を要求する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

1. **[ユーザーのメール アドレス]** を選択し、 **[通称]** の値を入力します。 **[ディスクに保存]** を指定したことを確認してから、 **[続ける]** を選択します。 **[CA のメール アドレス]** は、必要がないため空白のままにします。

    ![必要な証明書情報](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. **[名前]** に CSR ファイルの名前を入力し、 **[場所]** で保存先を選択してから、 **[保存]** を選択します。

    ![証明書のファイル名を選択する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    このアクションにより、選択した場所に CSR ファイルが保存されます。 既定の場所は **[デスクトップ]** です。 ファイル用に選択した場所を忘れないでください。

次に、アプリを Apple に登録し、プッシュ通知を有効にして、エクスポートした CSR をアップロードしてプッシュ証明書を作成します。

## <a name="register-your-app-for-push-notifications"></a>アプリケーションをプッシュ通知に登録する

通知を iOS アプリにプッシュするには、アプリケーションを Apple に登録すると共に、プッシュ通知にも登録します。  

1. ご自分のアプリをまだ登録していない場合は、Apple Developer Center の [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) に移動します。 その後、Apple ID を使ってサインインし、 **[Identifiers]\(識別子\)** 、 **[App IDs]\(アプリ ID\)** の順に選択します。最後に **+** を選択して、新しいアプリを登録します。

    ![iOS Provisioning Portal の [App IDs]\(アプリ ID\) ページ](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. 新しいアプリで次の 3 つの値を更新してから、 **[Continue]\(続行\)** を選択します。

   * **[名前]** : **[App ID Description]\(アプリ ID の説明\)** セクションの **[Name]\(名前\)** ボックスに、アプリのわかりやすい名前を入力します。

   * **[Bundle Identifier]\(バンドル識別子\)** : **[Explicit App ID]\(明示的なアプリ ID\)** セクションで、[アプリ ディストリビューション ガイド](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)の説明のとおりに `<Organization Identifier>.<Product Name>` の形式で**バンドル識別子**を入力します。 <*組織 ID*> と <*製品名*> の値は Xcode プロジェクトを作成する際に使用する組織 ID と製品名に一致させる必要があります。 次のスクリーンショットでは、*NotificationHubs* という値が組織 ID として使用され、*GetStarted* という値が製品名として使用されています。 Xcode で正しい発行プロファイルが使用されるように、 **[Bundle Identifier]\(バンドル識別子\)** の値はご自分の Xcode プロジェクトの値と一致させるようにしてください。

   * **[Push Notifications]\(プッシュ通知\)** : **[App Services]\(アプリ サービス\)** セクションの **[Push Notifications]\(プッシュ通知\)** オプションを選択します。

     ![新しいアプリ ID を登録するフォーム](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

     このアクションにより、アプリ ID が生成され、その情報を確認するよう求められます。 **[Register]\(登録\)** を選択して、新しいアプリ ID を確定します。

     **[Register]\(登録\)** を選択すると、次の図のような **[Registration complete]\(登録完了\)** 画面が表示されます。 **[完了]** を選択します。

     ![エンタイトルメントが表示されるアプリ ID の登録完了](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)

1. Developer Center にある **[App ID]\(アプリ ID\)** で、自分が作成したアプリ ID を見つけ、その行を選択します。

    ![アプリ ID リスト](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    アプリ ID を選択してアプリの詳細を表示し、下部にある **[Edit]\(編集\)** ボタンを選択します。

    ![アプリ ID の編集ページ](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. 画面の下部までスクロールし、 **[Development Push SSL Certificate]\(開発プッシュ SSL 証明書\)** セクションの **[Create Certificate]\(証明書の作成\)** ボタンを選択します。

    ![アプリ ID の証明書の作成ボタン](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    ここで **[Add iOS Certificate]\(iOS 証明書の追加\)** アシスタントが表示されます。

    > [!NOTE]
    > このチュートリアルでは開発証明書を使用します。 運用証明書の場合も同じ処理を行います。 通知の送信と同じ証明書の種類を使用するようにします。

1. **[Choose File]\(ファイルの選択\)** を選択して、最初の作業で CSR ファイルを保存した場所に移動し、 **[Generate]\(生成\)** を選択します。

    ![生成された証明書の CSR アップロード ページ](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

1. ポータルで証明書が作成されたら、 **[Download]\(ダウンロード\)** ボタンを選択してから **[Done]\(完了\)** を選択します。

    ![生成された証明書のダウンロード ページ](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    証明書がダウンロードされ、自分のコンピューターの**ダウンロード** フォルダーに保存されます。

    ![Downloads フォルダーの証明書ファイルを見つける](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > 既定では、ダウンロードした開発証明書の名前は **aps_development.cer** になっています。

1. ダウンロードしたプッシュ証明書 **aps_development.cer** を選択します。

    このアクションで、以下の図のように、新しい証明書がキーチェーンにインストールされます:

    ![新しい証明書が表示されたキーチェーン アクセス証明書リスト](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > 証明書の名前は場合によって異なりますが、名前の前には **Apple Development iOS Push Services** が付けられます。

1. Keychain Access の **[Certificates]** カテゴリで、作成した新しいプッシュ証明書を右クリックします。 **[書き出す]** を選択し、ファイルに名前を付けて、 **.p12** 形式を選択します。次に、 **[保存]** を選択します。

    ![p12 形式として証明書をエクスポートする](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    エクスポートした .p12 証明書のファイル名と場所を書き留めます。 これらは、APNs での認証を有効にするために使用されます。

    > [!NOTE]
    > このチュートリアルでは、**QuickStart.p12** という名前のファイルを作成します。 ファイル名と場所は同じである必要はありません。

## <a name="create-a-provisioning-profile-for-the-app"></a>アプリケーションのプロビジョニング プロファイルを作成する

1. [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) で、 **[Provisioning Profiles]\(プロビジョニング プロファイル\)** を選択し、 **[All]\(すべて\)** を選択してから、 **+** を選択して新しいプロファイルを作成します。 **[Add iOS Provisiong Profile]\(iOS プロビジョニング プロファイルの追加\)** ウィザードが表示されます。

    ![プロビジョニング プロファイル リスト](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. **[Development]\(開発\)** でプロビジョニング プロファイルの種類として **[iOS App Development]\(iOS アプリ開発\)** を選択し、 **[Continue]\(続行\)** を選択します。

1. 次に、 **[App ID]\(アプリ ID\)** ドロップダウン リストで、自分が作成したアプリ ID を選択し、 **[Continue]\(続行\)** を選択します。

    ![アプリ ID を選択する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. **[Select certificates]\(証明書の選択\)** ウィンドウで、コード署名に使用した通常の開発証明書を選択して、 **[Continue]\(続行\)** を選択します。 この証明書は、作成したプッシュ証明書ではありません。

    ![証明書を選択する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)

1. 次に、テストに使用するデバイスを選択し、 **[Continue]\(続行\)** を選択します。

    ![デバイスを選択する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)

1. 最後に、 **[Profile Name]\(プロファイル名\)** でプロファイルの名前を選択し、 **[Generate]\(生成\)** を選択します。

    ![プロビジョニング プロファイル名を選択する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. 新しいプロビジョニング プロファイルが作成されたら、それをダウンロードして Xcode の開発用マシンにインストールすることを選択します。 **[完了]** を選択します。

    ![プロビジョニング プロファイルをダウンロードする](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)

## <a name="create-a-notification-hub"></a>通知ハブを作成する

このセクションでは、前に作成した .p12 プッシュ証明書を使用して、通知ハブを作成し、APNs での認証を構成します。 既に作成した通知ハブを使用する場合は、手順 5. に進んでください。

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>APNs 情報を使用して通知ハブを構成する

1. **[Notification Services]** で、 **[Apple (APNS)]** を選択します。

1. **[Certificate]** を選択します。

1. ファイル アイコンを選択します。

1. 先ほどエクスポートした .p12 ファイルを選択します。

1. 正しいパスワードを指定します。

1. **[サンドボックス]** モードを選択します。 **[Production] (運用)** モードは、ストアからアプリを購入したユーザーにプッシュ通知を送信する場合にのみ使用します。

    ![Azure portal で APNs 証明書を構成する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

これで、APNs での通知ハブの構成が完了しました。 接続文字列を使用してアプリを登録し、プッシュ通知を送信することもできます。
