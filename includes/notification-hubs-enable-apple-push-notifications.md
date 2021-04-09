---
title: インクルード ファイル
description: インクルード ファイル
services: notification-hubs
author: sethmanheim
ms.service: notification-hubs
ms.topic: include
ms.date: 02/10/2020
ms.author: sethm
ms.custom: include file
ms.openlocfilehash: 7b5034f2163e8478d7ddb7b9271402b094a809d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95562600"
---
## <a name="generate-the-certificate-signing-request-file"></a>証明書の署名要求ファイルを生成する

Apple Push Notification Service (APNs) では、証明書を使用してプッシュ通知を認証します。 次の手順に従って、通知を送受信するために必要なプッシュ証明書を作成します。 これらの概念の詳細については、[Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) の公式ドキュメントを参照してください。

証明書署名要求 (CSR) ファイルを生成します。これは、Apple が署名済みのプッシュ証明書を生成するために使用します。

1. Mac で、キーチェーン アクセス ツールを実行します。 これは、Launchpad の **ユーティリティ** フォルダーまたは **その他** フォルダーから開くことができます。

1. **[キーチェーン アクセス]** を選択し、 **[証明書アシスタント]** を展開して、 **[認証局に証明書を要求]** を選択します。

    ![Keychain Access を使用して新しい証明書を要求する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > 既定では、キーチェーン アクセスによってリスト内の最初の項目が選択されます。 これは、 **[証明書]** カテゴリで、 **[Apple Worldwide Developer Relations Certification Authority]** が一覧の最初の項目ではない場合に問題になることがあります。 CSR (証明書署名要求) を生成する前に、キー以外の項目があること、または **[Apple Worldwide Developer Relations Certification Authority]** キーが選択されていることを確認します。

1. **[ユーザーのメール アドレス]** を選択し、 **[通称]** の値を入力します。 **[ディスクに保存]** を指定したことを確認してから、 **[続ける]** を選択します。 **[CA のメール アドレス]** は、必要がないため空白のままにします。

    ![必要な証明書情報](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. **[名前]** に CSR ファイルの名前を入力し、 **[場所]** で保存先を選択してから、 **[保存]** を選択します。

    ![証明書のファイル名を選択する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    このアクションにより、選択した場所に CSR ファイルが保存されます。 既定の場所は **[デスクトップ]** です。 ファイル用に選択した場所を忘れないでください。

次に、アプリを Apple に登録し、プッシュ通知を有効にして、エクスポートした CSR をアップロードしてプッシュ証明書を作成します。

## <a name="register-your-app-for-push-notifications"></a>アプリケーションをプッシュ通知に登録する

プッシュ通知を iOS アプリに送信するには、アプリケーションを Apple に登録すると共に、プッシュ通知にも登録します。  

1. ご自分のアプリをまだ登録していない場合は、Apple Developer Center の [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) に移動します。 自分の Apple ID を使用してポータルにサインインし、 **[Identifiers]\(識別子\)** を選択します。 次に、 **[+]** を選択して、新しいアプリを登録します。

    ![iOS Provisioning Portal の [App IDs]\(アプリ ID\) ページ](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. **[Register a New Identifier]\(新しい識別子の登録\)** 画面で、 **[App IDs]\(アプリ ID\)** を選択します。 その後 **[続行]** を選択します。

    ![iOS Provisioning Portal の ID の新規登録ページ](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

3. 新しいアプリで次の 3 つの値を更新してから、 **[Continue]\(続行\)** を選択します。

   * **説明**:アプリのわかりやすい名前を入力します。

   * **[Bundle ID]\(バンドル ID\)** : [アプリ ディストリビューション ガイド](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)の説明のとおりに、 **<組織 ID>.<製品名>** の形式のバンドル ID を入力します。 <*組織 ID*> と <*製品名*> の値は Xcode プロジェクトを作成する際に使用する組織 ID と製品名に一致させる必要があります。 次のスクリーンショットでは、**NotificationHubs** という値が組織 ID として使用され、**GetStarted** という値が製品名として使用されています。 Xcode で正しい発行プロファイルが使用されるように、 **[Bundle Identifier]\(バンドル識別子\)** の値はご自分の Xcode プロジェクトの値と一致させるようにしてください。

      ![iOS Provisioning Portal のアプリ ID の登録ページ](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **[Push Notifications]\(プッシュ通知\)** : **[Capabilities]\(機能\)** セクションの **[Push Notifications]\(プッシュ通知\)** オプションをオンにします。

      ![新しいアプリ ID を登録するフォーム](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      このアクションにより、アプリ ID が生成され、その情報を確認するよう求められます。 **[Continue]\(続行\)** を選択し、 **[Register]\(登録\)** を選択して新しいアプリ ID を確認します。

      ![新しいアプリ ID を確認する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      **[Register]\(登録\)** を選択すると、新しいアプリ ID が **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** ページに 1 行の項目として表示されます。

4. **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** ページの **[Identifiers]\(識別子\)** で、先ほど作成したアプリ ID の行項目を探し、その行を選択すると **[Edit your App ID Configuration]\(App ID 構成の編集\)** 画面が表示されます。

## <a name="creating-a-certificate-for-notification-hubs"></a>Notification Hubs の証明書を作成する
通知ハブを **APNs** と連携させるには、証明書が必要です。 これには、次の 2 とおりの方法があります。

1. Notification Hubs に直接アップロードできる **.p12** を作成します。  
2. [トークンベースの認証](../articles/notification-hubs/notification-hubs-push-notification-http2-token-authentication.md) ("*より新しいアプローチ*") に使用できる **.p8** を作成します。

「[APNS のトークンベース (HTTP/2) 認証](../articles/notification-hubs/notification-hubs-push-notification-http2-token-authentication.md)」にも記載されているように、新しい方のアプローチには (証明書を使用した場合と比べて) 多くの利点があります。 ただし、ここでは両方のアプローチの手順を取り上げています。 

### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>オプション 1: Notification Hubs に直接アップロードできる .p12 プッシュ証明書を作成する

1. チェック マークが付いた **[Push Notifications]\(プッシュ通知\)** オプションまで下へスクロールします。証明書を作成するために **[Configure]\(構成\)** を選択します。

    ![アプリ ID の編集ページ](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

2. **[Apple Push Notification service SSL Certificates]\(Apple Push Notification Service の SSL 証明書\)** ウィンドウが表示されます。 **[Development SSL Certificate]\(開発 SSL 証明書\)** セクションで **[Create Certificate]\(証明書の作成\)** ボタンを選択します。

    ![アプリ ID の証明書の作成ボタン](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    **[Create a new Certificate]\(新しい証明書の作成\)** 画面が表示されます。

    > [!NOTE]
    > このチュートリアルでは、一意のデバイス トークンを生成するためにアプリで使用される開発証明書を使用します。 運用証明書の場合も同じ処理を行います。 通知の送信と同じ証明書の種類を使用するようにします。

3. **[Choose File]\(ファイルの選択\)** を選択して、最初のタスクで CSR ファイルを保存した場所を参照し、証明書名をダブルクリックして読み込みます。 その後 **[続行]** を選択します。

4. ポータルで証明書が作成されたら、 **[Download]\(ダウンロード\)** ボタンを選択します。 この証明書を保存し、この保存場所を覚えておいてください。

    ![生成された証明書のダウンロード ページ](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    証明書がダウンロードされ、自分のコンピューターの **ダウンロード** フォルダーに保存されます。

    ![Downloads フォルダーの証明書ファイルを見つける](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > 既定では、ダウンロードした開発証明書の名前は **aps_development.cer** になっています。

5. ダウンロードしたプッシュ証明書 **aps_development.cer** をダブルクリックします。 このアクションで、以下の図のように、新しい証明書がキーチェーンにインストールされます:

    ![新しい証明書が表示されたキーチェーン アクセス証明書リスト](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > 証明書の名前は場合によって異なりますが、名前の前には **Apple Development iOS Push Services** が付けられます。

6. Keychain Access の **[Certificates]** カテゴリで、作成した新しいプッシュ証明書を右クリックします。 **[書き出す]** を選択し、ファイルに名前を付けて、 **.p12** 形式を選択します。次に、 **[保存]** を選択します。

    ![p12 形式として証明書をエクスポートする](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    パスワードを使用して証明書を保護することもできますが、これはオプションです。 パスワードの作成を省略する場合は、 **[OK]** をクリックします。 エクスポートした .p12 証明書のファイル名と場所を書き留めます。 これらは、APNs での認証を有効にするために使用されます。

    > [!NOTE]
    > 実際の .p12 ファイルの名前と場所は、このチュートリアルの図に示されているものと異なる場合があります。

### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>オプション 2: トークンベースの認証に使用できる .p8 証明書を作成する

1. 次の情報をメモしておきます。

    - **アプリ ID プレフィックス** (**チーム ID**)
    - **バンドル ID**
    
2. **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** に戻って **[Keys]\(キー\)** をクリックします。

   > [!NOTE]
   > **APNS** 用に構成されたキーが既にある場合は、作成直後にダウンロードした .p8 証明書を再利用できます。 その場合、手順 **3**. から手順 **5**. は無視してかまいません。

3. [ **+** ] ボタン (または **[Create a key]\(キーの作成\)** ボタン) をクリックして新しいキーを作成します。
4. **[Key Name]\(キー名\)** に適切な値を入力し、 **[Apple Push Notifications service (APNs)]** オプションをオンにして、 **[Continue]\(続行\)** をクリックし、次の画面で **[Register]\(登録\)** をクリックします。
5. **[Download]\(ダウンロード\)** をクリックして **.p8** ファイル (*AuthKey_* で始まるファイル) を安全なローカル ディレクトリに移動し、 **[Done]\(完了\)** をクリックします。

   > [!NOTE] 
   > .p8 ファイルは必ず安全な場所に保管してください (さらにバックアップを保存すること)。 キーのダウンロード後は、サーバー コピーが削除されるため、再ダウンロードすることはできません。
  
6. **[Keys]\(キー\)** で、先ほど作成したキー (または既存のキーを使用するように選択した場合はそのキー) をクリックします。
7. **[Key ID]\(キー ID\)** の値をメモしておきます。
8. 任意の適切なアプリケーション ([**Visual Studio Code**](https://code.visualstudio.com) など) で .p8 証明書を開き、キーの値をメモします。 **-----BEGIN PRIVATE KEY-----** と **-----END PRIVATE KEY-----** で挟まれた値が該当します。

    ```
    -----BEGIN PRIVATE KEY-----
    <key_value>
    -----END PRIVATE KEY-----
    ```

    > [!NOTE]
    > これは、後で **Notification Hub** の構成に使用する **トークン値** です。 

以上の手順を終えると、次の情報が確認済みとなります。これらの情報は、後出の「[APNs 情報を使用して通知ハブを構成する](#configure-your-notification-hub-with-apns-information)」で使用します。

- **チーム ID** (手順 1. を参照)
- **バンドル ID** (手順 1. を参照)
- **キー ID** (手順 7. を参照)
- **トークン値** (.p8 キー値) (手順 8. を参照)

## <a name="create-a-provisioning-profile-for-the-app"></a>アプリケーションのプロビジョニング プロファイルを作成する

1. [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) に戻り、 **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** を選択します。左側のメニューから **[Profiles]\(プロファイル\)** を選択し、 **[+]** を選択して新しいプロファイルを作成します。 **[Register a New Provisioning Profile]\(新しいプロビジョニング プロファイルの登録\)** 画面が表示されます。

1. **[Development]\(開発\)** で、プロビジョニング プロファイルの種類として **[iOS App Development]\(iOS アプリ開発\)** を選択し、 **[Continue]\(続行\)** を選択します。

    ![プロビジョニング プロファイル リスト](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. 次に、 **[App ID]\(アプリ ID\)** ドロップダウン リストで、自分が作成したアプリ ID を選択し、 **[Continue]\(続行\)** を選択します。

    ![アプリ ID を選択する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. **[Select certificates]\(証明書の選択\)** ウィンドウで、コード署名に使用する開発証明書を選択し、 **[Continue]\(続行\)** を選択します。 この証明書は、作成したプッシュ証明書ではありません。 存在しない場合は、作成する必要があります。 証明書が存在する場合は、次の手順に進みます。 開発証明書が存在しない場合に証明書を作成するには:

    1. "**No Certificates are available (利用可能な証明書がありません)** " と表示されたら、 **[Create Certificate]\(証明書の作成\)** を選択します。
    2. **[Software]\(ソフトウェア\)** セクションで、 **[Apple Development]\(Apple 開発\)** を選択します。 その後 **[続行]** を選択します。
    3. **[Create a New Certificate]\(新しい証明書の作成\)** 画面で、 **[Choose File]\(ファイルの選択\)** を選択します。
    4. 先ほど作成した **証明書署名要求** 証明書を参照して選択し、 **[Open]\(開く\)** を選択します。
    5. **[続行]** をクリックします。
    6. 開発証明書をダウンロードします。この保存場所を覚えておいてください。

1. **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** ページに戻り、左側のメニューから **[Profiles]\(プロファイル\)** を選択し、 **[+]** を選択して新しいプロファイルを作成します。 **[Register a New Provisioning Profile]\(新しいプロビジョニング プロファイルの登録\)** 画面が表示されます。

1. **[Select certificates]\(証明書の選択\)** ウィンドウで、先ほど作成した開発証明書を選択します。 その後 **[続行]** を選択します。

1. 次に、テストに使用するデバイスを選択し、 **[Continue]\(続行\)** を選択します。

1. 最後に、 **[Provisioning Profile Name]\(プロビジョニング プロファイル名\)** でプロファイルの名前を選択し、 **[Generate]\(生成\)** を選択します。

    ![プロビジョニング プロファイル名を選択する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. 新しいプロビジョニング プロファイルが作成されたら、 **[Download]\(ダウンロード\)** を選択します。 この保存場所を覚えておいてください。

1. プロビジョニング プロファイルの場所を参照し、それをダブルクリックして Xcode 開発マシンにインストールします。

## <a name="create-a-notification-hub"></a>通知ハブを作成する

このセクションでは、通知ハブを作成し、.p12 プッシュ証明書またはトークンベースの認証を使用して APNs での認証を構成します。 既に作成した通知ハブを使用する場合は、手順 5. に進んでください。

[!INCLUDE [notification-hubs-portal-create-new-hub](notification-hubs-portal-create-new-hub.md)]

## <a name="configure-your-notification-hub-with-apns-information"></a>APNs 情報を使用して通知ハブを構成する

**[Notification Services]** で **[Apple (APNS)]** を選択し、先ほど「[Notification Hubs の証明書を作成する](#creating-a-certificate-for-notification-hubs)」セクションで選択したアプローチに応じて適切な手順に従います。  

> [!NOTE]
> App Store またはアドホック配布プロファイルを使用してアプリをビルドする場合は、 **[Application Mode]\(アプリケーション モード\)** に **[Production]\(運用\)** を使用します。 これにより、デバイスは、ストアからアプリを購入したユーザーにプッシュ通知を送信できるようになります。

### <a name="option-1-using-a-p12-push-certificate"></a>オプション 1: .p12 プッシュ証明書を使用する

1. **[Certificate]** を選択します。

1. ファイル アイコンを選択します。

1. 先ほどエクスポートした .p12 ファイルを選択し、 **[Open]\(開く\)** を選択します。

1. 必要に応じて、適切なパスワードを指定します。

1. **[サンドボックス]** モードを選択します。

    ![Azure portal で APNs 証明書を構成する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-apple-config-cert.png)

1. **[保存]** を選択します。

### <a name="option-2-using-token-based-authentication"></a>オプション 2: トークンベースの認証を使用する

1. **[トークン]** を選択します。
1. 先ほど取得した次の値を入力します。

    - **キー ID**
    - **バンドル ID**
    - **チーム ID**
    - **トークン** 

1. **[サンドボックス]** を選択します。
1. **[保存]** を選択します。 

これで、APNs での通知ハブの構成が完了しました。 接続文字列を使用してアプリを登録し、プッシュ通知を送信することもできます。