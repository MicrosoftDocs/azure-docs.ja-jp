---
author: mikeparker104
ms.author: miparker
ms.date: 06/02/2020
ms.service: notification-hubs
ms.topic: include
ms.openlocfilehash: 72065ce602c6d29255a3500e26d8c6f36a19ebed
ms.sourcegitcommit: 971a3a63cf7da95f19808964ea9a2ccb60990f64
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/19/2020
ms.locfileid: "85095507"
---
### <a name="register-your-ios-app-for-push-notifications"></a>iOS アプリをプッシュ通知に登録する

プッシュ通知を iOS アプリに送信するには、アプリケーションを Apple に登録すると共に、プッシュ通知にも登録します。  

1. ご自分のアプリをまだ登録していない場合は、Apple Developer Center の [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) に移動します。 Apple ID を使用してポータルにサインインし、 **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** に移動して、 **[識別子]** を選択します。 **+** をクリックして新しいアプリを登録します。

    ![iOS Provisioning Portal の [App IDs]\(アプリ ID\) ページ](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids.png)

1. **[Register a New Identifier]\(新しい識別子の登録\)** 画面で、 **[App IDs]\(アプリ ID\)** を選択します。 その後 **[続行]** を選択します。

    ![iOS Provisioning Portal の ID の新規登録ページ](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-ios-appids-new.png)

1. 新しいアプリで次の 3 つの値を更新してから、 **[Continue]\(続行\)** を選択します。

   * **説明**:アプリのわかりやすい名前を入力します。

   * **[Bundle ID]\(バンドル ID\)** : [アプリ ディストリビューション ガイド](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)の説明のとおりに、**com.<組織 ID>.<製品名>** の形式のバンドル ID を入力します。 次のスクリーンショットでは、`mobcat` 値が組織 ID として使用され、**PushDemo** 値が製品名として使用されています。

      ![iOS Provisioning Portal のアプリ ID の登録ページ](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-bundle.png)

   * **[Push Notifications]\(プッシュ通知\)** : **[Capabilities]\(機能\)** セクションの **[Push Notifications]\(プッシュ通知\)** オプションをオンにします。

      ![新しいアプリ ID を登録するフォーム](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-push.png)

      このアクションにより、アプリ ID が生成され、その情報を確認するよう求められます。 **[Continue]\(続行\)** を選択し、 **[Register]\(登録\)** を選択して新しいアプリ ID を確認します。

      ![新しいアプリ ID を確認する](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-appid-register.png)

      **[Register]\(登録\)** を選択すると、新しいアプリ ID が **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** ページに 1 行の項目として表示されます。

1. **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** ページの **[識別子]** で、作成したアプリ ID の行項目を見つけます。 次に、その行を選択して **[Edit your App ID Configuration]\(App ID 構成の編集\)** 画面を表示します。

#### <a name="creating-a-certificate-for-notification-hubs"></a>Notification Hubs の証明書を作成する

通知ハブが **Apple プッシュ通知サービス (APNS)** で動作できるようにするには、証明書が必要です。証明書は次の 2 つの方法のいずれかで提供できます。

1. [通知ハブに直接アップロードできる p12 プッシュ証明書を作成する](#option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub) (*当初のアプローチ*)

1. [トークンベースの認証に使用できる p8 証明書を作成する](#option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication) (*新しい推奨されるアプローチ*)

「[APNS のトークンベース (HTTP/2) 認証](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-push-notification-http2-token-authentification)」にも記載されているように、新しい方のアプローチには多くの利点があります。 必要な手順は少数ですが、特定のシナリオで必須になることもあります。 ただし、どちらの方法もこのチュートリアルの目的で動作するため、両方の手順が提供されています。

##### <a name="option-1-creating-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hub"></a>オプション 1: 通知ハブに直接アップロードできる p12 プッシュ証明書を作成する

1. Mac で、キーチェーン アクセス ツールを実行します。 これは、Launchpad の**ユーティリティ** フォルダーまたは**その他**フォルダーから開くことができます。

1. **[キーチェーン アクセス]** を選択し、 **[証明書アシスタント]** を展開して、 **[認証局に証明書を要求]** を選択します。

    ![Keychain Access を使用して新しい証明書を要求する](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

   > [!NOTE]
   > 既定では、キーチェーン アクセスによってリスト内の最初の項目が選択されます。 これは、 **[証明書]** カテゴリで、 **[Apple Worldwide Developer Relations Certification Authority]** が一覧の最初の項目ではない場合に問題になることがあります。 CSR (証明書署名要求) を生成する前に、キー以外の項目があること、または **[Apple Worldwide Developer Relations Certification Authority]** キーが選択されていることを確認します。

1. **[ユーザーのメール アドレス]** を選択し、 **[通称]** の値を入力します。 **[ディスクに保存]** を指定したことを確認してから、 **[続ける]** を選択します。 **[CA のメール アドレス]** は、必要がないため空白のままにします。

    ![期待される証明書情報](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-csr-info.png)

1. **[名前を付けて保存]** に**証明書署名要求 (CSR) ファイル**の名前を入力し、 **[場所]** で保存場所を選択して **[保存]** を選択します。

    ![証明書のファイル名を選択する](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-save-csr.png)

    このアクションにより、選択した場所に **CSR ファイル**が保存されます。 既定の場所は **[デスクトップ]** です。 ファイル用に選択した場所を忘れないでください。

1. [iOS プロビジョニング ポータル](https://go.microsoft.com/fwlink/p/?LinkId=272456)の **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** ページに戻り、選択された **[プッシュ通知]** オプションまで下にスクロールし、 **[構成]** を選択して証明書を作成します。

    ![アプリ ID の編集ページ](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-edit-appid.png)

1. **[Apple Push Notification service TLS/SSL Certificates]\(Apple Push Notification Service の TLS/SSL 証明書\)** ウィンドウが表示されます。 **[Development TLS/SSL Certificate]\(開発 TLS/SSL 証明書\)** セクションで **[Create Certificate]\(証明書の作成\)** ボタンを選択します。

    ![アプリ ID の証明書の作成ボタン](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    **[Create a new Certificate]\(新しい証明書の作成\)** 画面が表示されます。

    > [!NOTE]
    > このチュートリアルでは開発証明書を使用します。 運用証明書の場合も同じ処理を行います。 通知の送信と同じ証明書の種類を使用するようにします。

1. **[Choose File]\(ファイルの選択\)** を選択して、**CSR ファイル**を保存した場所を参照し、証明書名をダブルクリックして読み込みます。 その後 **[続行]** を選択します。

1. ポータルで証明書が作成されたら、 **[Download]\(ダウンロード\)** ボタンを選択します。 この証明書を保存し、この保存場所を覚えておいてください。

    ![生成された証明書のダウンロード ページ](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    証明書がダウンロードされ、自分のコンピューターの**ダウンロード** フォルダーに保存されます。

    ![Downloads フォルダーの証明書ファイルを見つける](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [!NOTE]
    > 既定では、ダウンロードした開発証明書の名前は **aps_development.cer** になっています。

1. ダウンロードしたプッシュ証明書 **aps_development.cer** をダブルクリックします。 このアクションで、以下の図のように、新しい証明書がキーチェーンにインストールされます:

    ![新しい証明書が表示されたキーチェーン アクセス証明書リスト](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [!NOTE]
    > 証明書の名前は場合によって異なりますが、名前の前には **Apple Development iOS Push Services** が付き、適切なバンドル ID が関連付けられます。

1. キーチェーン アクセスの **[Certificates] (証明書)** カテゴリで、作成した新しいプッシュ証明書を **Control** + **クリック**します。 **[書き出す]** を選択し、ファイルに名前を付けて、**p12** 形式を選択します。次に、 **[保存]** を選択します。

    ![p12 形式として証明書をエクスポートする](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    パスワードを使用して証明書を保護することもできますが、パスワードはオプションです。 パスワードの作成を省略する場合は、 **[OK]** をクリックします。 エクスポートした p12 証明書のファイル名と場所を書き留めます。 これらは、APNs での認証を有効にするために使用されます。

    > [!NOTE]
    > 実際の p12 ファイルの名前と場所は、このチュートリアルの図に示されているものと異なる場合があります。

##### <a name="option-2-creating-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>オプション 2: トークンベースの認証に使用できる p8 証明書を作成する

1. 次の情報をメモしておきます。

    * **アプリ ID プレフィックス** (**チーム ID**)
    * **バンドル ID**

1. **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** に戻って **[Keys]\(キー\)** をクリックします。

   > [!NOTE]
   > **APNS** 用に構成されたキーが既にある場合は、作成直後にダウンロードした p8 証明書を再利用できます。 その場合、手順 **3**. から手順 **5**. は無視してかまいません。

1. [ **+** ] ボタン (または **[Create a key]\(キーの作成\)** ボタン) をクリックして新しいキーを作成します。
1. **[Key Name]\(キー名\)** に適切な値を入力し、 **[Apple Push Notifications service (APNS)]** オプションをオンにして、 **[続行]** をクリックし、次の画面で **[登録]** をクリックします。
1. **[ダウンロード]** をクリックして **p8** ファイル (*AuthKey_* で始まるファイル) を安全なローカル ディレクトリに移動し、 **[Done]\(完了\)** をクリックします。

   > [!NOTE]
   > p8 ファイルは必ず安全な場所に保管してください (さらにバックアップを保存すること)。 キーのダウンロード後は、サーバー コピーが削除されるため、再ダウンロードすることはできません。
  
1. **[キー]** で、作成したキー (または既存のキーを使用するように選択した場合はそのキー) をクリックします。
1. **[Key ID]\(キー ID\)** の値をメモしておきます。
1. 任意の適切なアプリケーション ([**Visual Studio Code**](https://code.visualstudio.com) など) で p8 証明書を開きます。 **-----BEGIN PRIVATE KEY-----** と **-----END PRIVATE KEY-----** で挟まれたキーの値をメモします。

    -----BEGIN PRIVATE KEY-----  
    <key_value>  
    -----END PRIVATE KEY-----

    > [!NOTE]
    > これは、後で **Notification Hub** の構成に使用する**トークン値**です。

以上の手順を終えると、次の情報が確認済みとなります。これらの情報は、後出の「[APNS 情報で通知ハブを構成する](#configure-your-notification-hub-with-apns-information)」で使用します。

* **チーム ID** (手順 1. を参照)
* **バンドル ID** (手順 1. を参照)
* **キー ID** (手順 7. を参照)
* **トークン値** (手順 8. で取得した p8 キー値)

### <a name="create-a-provisioning-profile-for-the-app"></a>アプリケーションのプロビジョニング プロファイルを作成する

1. [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) に戻り、 **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** を選択します。左側のメニューから **[Profiles]\(プロファイル\)** を選択し、 **[+]** を選択して新しいプロファイルを作成します。 **[Register a New Provisioning Profile]\(新しいプロビジョニング プロファイルの登録\)** 画面が表示されます。

1. **[Development]\(開発\)** で、プロビジョニング プロファイルの種類として **[iOS App Development]\(iOS アプリ開発\)** を選択し、 **[Continue]\(続行\)** を選択します。

    ![プロビジョニング プロファイル リスト](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

1. 次に、 **[App ID]\(アプリ ID\)** ドロップダウン リストで、自分が作成したアプリ ID を選択し、 **[Continue]\(続行\)** を選択します。

    ![アプリ ID を選択する](./media/notification-hubs-common-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)

1. **[Select certificates]\(証明書の選択\)** ウィンドウで、コード署名に使用する開発証明書を選択し、 **[Continue]\(続行\)** を選択します。

    > [!NOTE]
    > これは証明書であり、[前の手順](#creating-a-certificate-for-notification-hubs)で作成したプッシュ証明書ではありません。 これは開発証明書です。 このチュートリアルでは[前提条件](#prerequisites)であるため、存在しない場合は作成する必要があります。 開発者証明書は、[Apple Developer ポータル](https://developer.apple.com)、[Xcode](https://developer.apple.com/library/archive/documentation/ToolsLanguages/Conceptual/YourFirstAppStoreSubmission/ProvisionYourDevicesforDevelopment/ProvisionYourDevicesforDevelopment.html)、または [Visual Studio](https://docs.microsoft.com/xamarin/ios/get-started/installation/device-provisioning/) で作成できます。

1. **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** ページに戻り、左側のメニューから **[Profiles]\(プロファイル\)** を選択し、 **[+]** を選択して新しいプロファイルを作成します。 **[Register a New Provisioning Profile]\(新しいプロビジョニング プロファイルの登録\)** 画面が表示されます。

1. **[Select certificates]\(証明書の選択\)** ウィンドウで、作成した開発証明書を選択します。 その後 **[続行]** を選択します。

1. 次に、テストに使用するデバイスを選択し、 **[Continue]\(続行\)** を選択します。

1. 最後に、 **[Provisioning Profile Name]\(プロビジョニング プロファイル名\)** でプロファイルの名前を選択し、 **[Generate]\(生成\)** を選択します。

    ![プロビジョニング プロファイル名を選択する](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)

1. 新しいプロビジョニング プロファイルが作成されたら、 **[Download]\(ダウンロード\)** を選択します。 この保存場所を覚えておいてください。

1. プロビジョニング プロファイルの場所を参照し、それをダブルクリックして開発マシンにインストールします。
