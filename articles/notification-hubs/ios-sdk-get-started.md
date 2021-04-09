---
title: Azure Notification Hubs と iOS SDK を使用して iOS アプリにプッシュ通知を送信する
description: このチュートリアルでは、Azure Notification Hubs と Apple Push Notification Service を使用して iOS デバイスにプッシュ通知を送信する方法について学習します。
author: sethmanheim
ms.author: sethm
ms.date: 10/30/2020
ms.topic: tutorial
ms.service: notification-hubs
ms.reviewer: thsomasu
ms.lastreviewed: 06/01/2020
ms.openlocfilehash: c18814230eda6624e8ab1040797c66108f630e30
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581739"
---
# <a name="tutorial-send-push-notifications-to-ios-apps-using-azure-notification-hubs"></a>チュートリアル:Azure Notification Hubs を使用して iOS アプリにプッシュ通知を送信する

このチュートリアルでは、[Apple Push Notification service (APNS)](https://developer.apple.com/library/content/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html#//apple_ref/doc/uid/TP40008194-CH8-SW1) を介して iOS デバイスにプッシュ通知を送信するために Azure Notification Hubs を設定し、資格情報を構成する方法について説明します。 

このチュートリアルを実行することは、以降の Objective C および Swift iOS チュートリアルの前提条件であり、以下の手順を取り上げています。

- 証明書の署名要求ファイルを生成する。
- アプリをプッシュ通知用に要求する。
- アプリケーションのプロビジョニング プロファイルを作成する。
- 通知ハブを作成します。
- APNS 情報を利用して通知ハブを構成する。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、アクティブな Azure アカウントが必要です。 アカウントがない場合は、無料試用アカウントを数分で作成することができます。 詳細については、「[Azure の無料試用版サイト](https://azure.microsoft.com/free/)」を参照してください。

以下も必要となります。

- アクティブな [Apple Developer](https://developer.apple.com/) アカウント。
- [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532) を実行しており、有効な開発者証明書がキーチェーンにインストールされている Mac。
- iOS バージョン 10 以降を実行している iPhone または iPad。
- [Apple ポータル](https://developer.apple.com/)に登録され、自分の証明書に関連付けられているお使いの物理デバイス。

このサービスに慣れていない場合は、[Azure Notification Hubs の概要](notification-hubs-push-notification-overview.md)に関するページを必ず読んでください。

> [!NOTE]
> 通知ハブは、[サンドボックス] 認証モードのみを使用するように構成されます。 運用環境のワークロードにはこの認証モードを使用しないでください。

## <a name="generate-the-certificate-signing-request-file"></a>証明書の署名要求ファイルを生成する

Apple Push Notification Service (APNS) では、証明書を使用してプッシュ通知を認証します。 次の手順に従って、通知を送受信するために必要なプッシュ証明書を作成します。 これらの概念の詳細については、[Apple Push Notification Service](https://developer.apple.com/library/archive/documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/APNSOverview.html) の公式ドキュメントを参照してください。

証明書署名要求 (CSR) ファイルを生成します。これは、Apple が署名済みのプッシュ証明書を生成するために使用します。

1. Mac で、キーチェーン アクセス ツールを実行します。 これは、Launchpad の **ユーティリティ** フォルダーまたは **その他** フォルダーから開くことができます。

2. **[キーチェーン アクセス]** を選択し、 **[証明書アシスタント]** を展開して、 **[認証局に証明書を要求]** を選択します。

   :::image type="content" source="media/ios-sdk-get-started/image1.png" alt-text="[認証局に証明書を要求] メニュー オプションが強調表示されたスクリーンショット。":::

   > [!NOTE]
   > 既定では、キーチェーン アクセスによってリスト内の最初の項目が選択されます。 これは、 **[証明書]** カテゴリで、 **[Apple Worldwide Developer Relations Certification Authority]** が一覧の最初の項目ではない場合に問題になることがあります。 CSR (証明書署名要求) を生成する前に、キー以外の項目があること、または **[Apple Worldwide Developer Relations Certification Authority]** キーが選択されていることを確認します。

3. **[ユーザーのメール アドレス]** を選択し、 **[通称]** の値を入力します。 **[ディスクに保存]** を指定したことを確認してから、 **[続ける]** を選択します。 **[CA のメール アドレス]** は、必要がないため空白のままにします。

   :::image type="content" source="media/ios-sdk-get-started/image2.png" alt-text="必要な証明書情報":::

4. **[名前]** に CSR ファイルの名前を入力し、 **[場所]** で保存先を選択してから、 **[保存]** を選択します。

   :::image type="content" source="media/ios-sdk-get-started/image3.png" alt-text="ファイル名を選択する":::

   このアクションにより、選択した場所に CSR ファイルが保存されます。 既定の場所は **[デスクトップ]** です。 ファイル用に選択した場所を忘れないでください。

次に、アプリを Apple に登録し、プッシュ通知を有効にして、エクスポートした CSR をアップロードしてプッシュ証明書を作成します。

## <a name="register-your-app-for-push-notifications"></a>アプリケーションをプッシュ通知に登録する

プッシュ通知を iOS アプリに送信するには、アプリケーションを Apple に登録すると共に、プッシュ通知にも登録します。

1. ご自分のアプリをまだ登録していない場合は、Apple Developer Center の [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) に移動します。 自分の Apple ID を使用してポータルにサインインし、 **[Identifiers]\(識別子\)** を選択します。 次に、 **[+]** を選択して、新しいアプリを登録します。

   :::image type="content" source="media/ios-sdk-get-started/image4.png" alt-text="アプリ ID のページ":::

2. **[Register a New Identifier]\(新しい識別子の登録\)** 画面で、 **[App IDs]\(アプリ ID\)** を選択します。 その後 **[続行]** を選択します。

   :::image type="content" source="media/ios-sdk-get-started/image5.png" alt-text="新しい ID の登録ページ":::

3. 新しいアプリで次の 3 つの値を更新してから、 **[Continue]\(続行\)** を選択します。

   - **説明**:アプリのわかりやすい名前を入力します。
   - **[Bundle ID]\(バンドル ID\)** : [アプリ ディストリビューション ガイド](https://help.apple.com/xcode/mac/current/#/dev91fe7130a)の説明のとおりに、 **<組織 ID>.<製品名>** の形式のバンドル ID を入力します。 <**組織 ID**> と <**製品名**> の値は Xcode プロジェクトを作成する際に使用する組織 ID と製品名に一致させる必要があります。 次のスクリーンショットでは、**NotificationHubs** という値が組織 ID として使用され、**GetStarted** という値が製品名として使用されています。 Xcode で正しい発行プロファイルが使用されるように、 **[Bundle Identifier]\(バンドル識別子\)** の値はご自分の Xcode プロジェクトの値と一致させるようにしてください。

      :::image type="content" source="media/ios-sdk-get-started/image6.png" alt-text="アプリ ID を登録する":::

   - **[Push Notifications]\(プッシュ通知\)** : **[Capabilities]\(機能\)** セクションの **[Push Notifications]\(プッシュ通知\)** オプションをオンにします。

      :::image type="content" source="media/ios-sdk-get-started/image7.png" alt-text="新しいアプリ ID を登録する":::

      このアクションにより、アプリ ID が生成され、その情報を確認するよう求められます。 **[Continue]\(続行\)** を選択し、 **[Register]\(登録\)** を選択して新しいアプリ ID を確認します。

      :::image type="content" source="media/ios-sdk-get-started/image8.png" alt-text="新しいアプリ ID を確認する":::

      **[Register]\(登録\)** を選択すると、新しいアプリ ID が **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** ページに 1 行の項目として表示されます。

4. **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** ページの **[Identifiers]\(識別子\)** で、先ほど作成したアプリ ID の行項目を探し、その行を選択すると **[Edit your App ID Configuration]\(App ID 構成の編集\)** 画面が表示されます。

## <a name="create-a-certificate-for-notification-hubs"></a>Notification Hubs の証明書を作成する

> [!NOTE]
> iOS 13 のリリースでは、トークン ベースの認証を使用したサイレント通知のみを受信できます。 APNS 資格情報に証明書ベースの認証を使用している場合は、トークンベースの認証を使用するように切り替える必要があります。

通知ハブを **APNs** と連携させるには、証明書が必要です。 これには、次の 2 とおりの方法があります。

- Notification Hubs に直接アップロードできる **.p12** ファイルを作成します。

- [トークンベースの認証](notification-hubs-push-notification-http2-token-authentication.md) (より新しいアプローチ) に使用できる **.p8** ファイルを作成します。

「[APNS のトークンベース (HTTP/2) 認証](notification-hubs-push-notification-http2-token-authentication.md)」に記載されているように、2 番目のオプションには、証明書を使用する場合と比べて多くの利点があります。 ただし、ここでは両方のアプローチの手順を取り上げます。

### <a name="option-1-create-a-p12-push-certificate-that-can-be-uploaded-directly-to-notification-hubs"></a>オプション 1: Notification Hubs に直接アップロードできる .p12 プッシュ証明書を作成する

1. チェック マークが付いた **[Push Notifications]\(プッシュ通知\)** オプションまで下へスクロールします。証明書を作成するために **[Configure]\(構成\)** を選択します。

   :::image type="content" source="media/ios-sdk-get-started/image9.png" alt-text="App ID":::

2. **[Apple Push Notification service SSL Certificates]\(Apple Push Notification Service の SSL 証明書\)** ウィンドウが表示されます。 **[Development SSL Certificate]\(開発 SSL 証明書\)** セクションで **[Create Certificate]\(証明書の作成\)** ボタンを選択します。

   :::image type="content" source="media/ios-sdk-get-started/image10.png" alt-text="証明書を作成する":::

   **[Create a new Certificate]\(新しい証明書の作成\)** 画面が表示されます。

   > [!NOTE]
   > このチュートリアルでは開発証明書を使用します。 運用証明書の場合も同じ処理を行います。 通知を送信する際と同じ証明書の種類を使用するようにします。

3. **[Choose File]\(ファイルの選択\)** を選択して、最初のタスクで CSR ファイルを保存した場所を参照し、名前をダブルクリックして証明書を読み込みます。 その後 **[続行]** を選択します。

4. ポータルで証明書が作成されたら、 **[Download]\(ダウンロード\)** ボタンを選択します。 この証明書を保存し、この保存場所を覚えておいてください。

   :::image type="content" source="media/ios-sdk-get-started/image11.png" alt-text="証明書をダウンロードする":::

   証明書がダウンロードされ、ご自分の **[Download]\(ダウンロード\)** フォルダーに保存されます。

   :::image type="content" source="media/ios-sdk-get-started/image12.png" alt-text="証明書ファイルを見つける":::

   既定では、ダウンロードした開発証明書の名前は **aps_development.cer** になっています。

5. ダウンロードしたプッシュ証明書 **aps\_development.cer** をダブルクリックします。 このアクションで、以下の図のように、新しい証明書がキーチェーンにインストールされます:

   :::image type="content" source="media/ios-sdk-get-started/image13.png" alt-text="キーチェーン アクセス":::

   証明書の名前は場合によって異なりますが、名前の前には **Apple Development iOS Push Services** が付けられます。

6. Keychain Access の **[Certificates]** カテゴリで、作成した新しいプッシュ証明書を右クリックします。 **[書き出す]** を選択し、ファイルに名前を付けて、 **.p12** 形式を選択します。次に、 **[保存]** を選択します。

   :::image type="content" source="media/ios-sdk-get-started/image14.png" alt-text="証明書のエクスポート":::

   パスワードを使用して証明書を保護することもできますが、これはオプションです。 パスワードの作成を省略する場合は、 **[OK]** をクリックします。 エクスポートした .p12 証明書のファイル名と場所を書き留めます。 これらは、APNS での認証を有効にするために使用されます。

   > [!NOTE]
   > 実際の .p12 ファイルの名前と場所は、このチュートリアルの図に示されているものと異なる場合があります。

### <a name="option-2-create-a-p8-certificate-that-can-be-used-for-token-based-authentication"></a>オプション 2:トークンベースの認証に使用できる .p8 証明書を作成する

1. 次の情報をメモしておきます。

   - **アプリ ID プレフィックス** (**チーム ID**)
   - **バンドル ID**

2. **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** に戻って **[Keys]\(キー\)** をクリックします。 **APNS** 用に構成されたキーが既にある場合は、作成直後にダウンロードした .p8 証明書を再利用できます。 その場合、手順 3. ～ 5. は無視できます。

3. [ **+** ] ボタン (または **[Create a key]\(キーの作成\)** ボタン) をクリックして新しいキーを作成します。

4. **[Key Name]\(キー名\)** に適切な値を入力し、 **[Apple Push Notifications service (APNS)]** オプションをオンにして、 **[Continue]\(続行\)** をクリックし、次の画面で **[Register]\(登録\)** をクリックします。

5. **[Download]\(ダウンロード\)** をクリックして **.p8** ファイル (`AuthKey_` で始まるファイル) を安全なローカル ディレクトリに移動し、 **[Done]\(完了\)** をクリックします。

   > [!IMPORTANT]
   > .p8 ファイルは必ず安全な場所に保管してください (さらにバックアップを保存すること)。 キーは、ダウンロード後にサーバー コピーが削除されるため、再ダウンロードすることはできません。

6. **[Keys]\(キー\)** で、先ほど作成したキー (または既存のキーを使用するように選択した場合はそのキー) をクリックします。

7. **[Key ID]\(キー ID\)** の値をメモしておきます。

8. [Visual Studio Code](https://code.visualstudio.com/) などの適切な自分のお好みのアプリケーションで、自分の .p8 証明書を開き、キーの値をメモします。 **-----BEGIN PRIVATE KEY-----** と **-----END PRIVATE KEY-----** で挟まれた値が該当します。

   ```p8
   -----BEGIN PRIVATE KEY-----
   <key_value>
   -----END PRIVATE KEY-----
   ```

   これは、後で Notification Hubs の構成に使用するトークン値です。

これらの手順の最後に、後出の「[APNS 情報で通知ハブを構成する](#configure-the-notification-hub-with-apns-information)」で使用するための次の情報が必要になります。

- **チーム ID** (手順 1. を参照)
- **バンドル ID** (手順 1. を参照)
- **キー ID** (手順 7. を参照)
- **トークン値**(.p8 キー値、手順 8 を参照)

## <a name="create-a-provisioning-profile"></a>プロビジョニング プロファイルを作成する

1. [iOS Provisioning Portal](https://go.microsoft.com/fwlink/p/?LinkId=272456) に戻り、 **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** を選択します。左側のメニューから **[Profiles]\(プロファイル\)** を選択し、 **[+]** を選択して新しいプロファイルを作成します。 **[Register a New Provisioning Profile]\(新しいプロビジョニング プロファイルの登録\)** 画面が表示されます。

2. **[Development]\(開発\)** で、プロビジョニング プロファイルの種類として **[iOS App Development]\(iOS アプリ開発\)** を選択し、 **[Continue]\(続行\)** を選択します。

   :::image type="content" source="media/ios-sdk-get-started/image15.png" alt-text="プロビジョニング プロファイルの一覧":::

3. 次に、 **[App ID]\(アプリ ID\)** ドロップダウン リストで自分が作成したアプリ ID を選択し、 **[Continue]\(続行\)** を選択します。

   :::image type="content" source="media/ios-sdk-get-started/image16.png" alt-text="アプリ ID を選択する":::

4. **[Select certificates]\(証明書の選択\)** ウィンドウで、コード署名に使用する開発証明書を選択し、 **[Continue]\(続行\)** を選択します。 この証明書は、作成したプッシュ証明書ではありません。 存在しない場合は、作成する必要があります。 証明書が存在する場合は、次の手順に進みます。 開発証明書が存在しない場合に証明書を作成するには:

   1. "**No Certificates are available (利用可能な証明書がありません)** " と表示されたら、 **[Create Certificate]\(証明書の作成\)** を選択します。
   2. **[Software]\(ソフトウェア\)** セクションで、 **[Apple Development]\(Apple 開発\)** を選択します。 その後 **[続行]** を選択します。
   3. **[Create a New Certificate]\(新しい証明書の作成\)** 画面で、 **[Choose File]\(ファイルの選択\)** を選択します。
   4. 先ほど作成した **証明書署名要求** 証明書を参照して選択し、 **[Open]\(開く\)** を選択します。
   5. **[続行]** をクリックします。
   6. 開発証明書をダウンロードします。また、それを保存した場所を覚えておきます。

5. **[Certificates, Identifiers & Profiles]\(証明書、識別子、およびプロファイル\)** ページに戻り、左側のメニューから **[Profiles]\(プロファイル\)** を選択し、 **[+]** を選択して新しいプロファイルを作成します。 **[Register a New Provisioning Profile]\(新しいプロビジョニング プロファイルの登録\)** 画面が表示されます。

6. **[Select certificates]\(証明書の選択\)** ウィンドウで、先ほど作成した開発証明書を選択します。 その後 **[続行]** を選択します。

7. 次に、テストに使用するデバイスを選択し、 **[Continue]\(続行\)** を選択します。

8. 最後に、 **[Provisioning Profile Name]\(プロビジョニング プロファイル名\)** でプロファイルの名前を選択し、 **[Generate]\(生成\)** を選択します。

   :::image type="content" source="media/ios-sdk-get-started/image17.png" alt-text="プロビジョニング プロファイル名を選択する":::

9. 新しいプロビジョニング プロファイルが作成されたら、 **[Download]\(ダウンロード\)** を選択します。 それを保存した場所を覚えておきます。

10. プロビジョニング プロファイルの場所を参照し、それをダブルクリックして Xcode 開発マシンにインストールします。

## <a name="create-a-notification-hub"></a>通知ハブを作成する

このセクションでは、通知ハブを作成し、.p12 プッシュ証明書またはトークンベースの認証を使用して、APNS での認証を構成します。 既に作成した通知ハブを使用する場合は、手順 5. に進んでください。

1. [Azure portal](https://portal.azure.com/) にサインインします。

2. 左側のメニューで **[すべてのサービス]** を選択し、 **[モバイル]** セクションの **[Notification Hubs]** を選択します。 サービス名の横にある星のアイコンを選択して、左側のメニューにある **[お気に入り]** セクションにサービスを追加します。 **Notification Hubs** を **[お気に入り]** に追加し、それを選択します。

   :::image type="content" source="media/ios-sdk-get-started/image18.png" alt-text="Azure Portal":::

3. **[Notification Hubs]** ページで、ツール バーの **[追加]** を選択します。

   :::image type="content" source="media/ios-sdk-get-started/image19.png" alt-text="[追加] ツール バー ボタン":::

4. **[Notification Hub]** ページで、次の手順のようにします。

   1. **[通知ハブ]** に名前を入力します。
   2. **[新しい名前空間の作成]** に名前を入力します。 1 つの名前空間には、1 つ以上の通知ハブが含まれます。
   3. **[場所]** ドロップダウン リストから値を選択します。 この値では、通知ハブを作成したい場所を指定します。
   4. **[リソース グループ]** で既存のリソース グループを選択するか、新しいリソース グループを作成します。
   5. **［作成］** を選択します

   :::image type="content" source="media/ios-sdk-get-started/image20.png" alt-text="プロパティを設定する":::

5. **[通知]** (ベルのアイコン) を選択し、**[リソースに移動]** を選択します。 **[Notification Hubs]** ページの一覧を最新の情報に更新して、お使いの通知ハブを選択することもできます。

   :::image type="content" source="media/ios-sdk-get-started/image21.png" alt-text="ポータルの通知":::

6. リストから **[アクセス ポリシー]** を選択します。 使用可能な 2 つの接続文字列を書き留めてください。 これらは、後でプッシュ通知を処理するために必要になります。

   > [!IMPORTANT]
   > アプリケーションで **DefaultFullSharedAccessSignature** ポリシーを使用しないでください。 これの使用が意図されているのはバックエンドだけです。

   :::image type="content" source="media/ios-sdk-get-started/image22.png" alt-text="接続文字列":::

## <a name="configure-the-notification-hub-with-apns-information"></a>APNS 情報を利用して通知ハブを構成する

**[Notification Services]** で **[Apple (APNS)]** を選択し、先ほど「[Notification Hubs の証明書を作成する](#create-a-certificate-for-notification-hubs)」セクションで選択した方法に応じて適切な手順に従います。

> [!NOTE]
> **[アプリケーション モード]** の **[Production] (運用)** は、ストアからアプリを購入したユーザーにプッシュ通知を送信する場合にのみ使用します。

### <a name="option-1-use-a-p12-push-certificate"></a>オプション 1: .p12 プッシュ証明書を使用する

1. **[Certificate]** を選択します。

2. ファイル アイコンを選択します。

3. 先ほどエクスポートした .p12 ファイルを選択し、 **[Open]\(開く\)** を選択します。

4. 必要に応じて、適切なパスワードを指定します。

5. **[サンドボックス]** モードを選択します。

   :::image type="content" source="media/ios-sdk-get-started/image23.png" alt-text="構成":::

6. **[保存]** を選択します。

### <a name="option-2-use-token-based-authentication"></a>オプション 2:トークン ベースの認証を使用する

1. **[トークン]** を選択します。

2. 先ほど取得した次の値を入力します。

   - **キー ID**
   - **バンドル ID**
   - **チーム ID**
   - **トークン**

3. **[サンドボックス]** を選択します。

4. **[保存]** を選択します。

これで、APNS での通知ハブの構成が完了しました。 アプリを登録してプッシュ通知を送信するために必要な接続文字列も用意できました。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure で通知ハブを作成して構成し、それを、Apple Push Notification Service (APNS) を使用してアプリケーションに通知を送信できるように構成しました。 次に、サンプルの iOS アプリケーションを作成し、Azure portal を通じて送信されるプッシュ通知を受信できるように Azure Notification Hubs SDK と統合しします。 選択する言語に基づいた次のチュートリアルに進みます。

- [チュートリアル:Azure Notification Hubs を使用して iOS アプリにプッシュ通知を送信する](ios-sdk-current.md)
