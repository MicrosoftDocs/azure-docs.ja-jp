---
title: Azure Storage Explorer トラブルシューティング ガイド | Microsoft Docs
description: Azure Storage Explorer のデバッグ手法の概要
services: virtual-machines
author: Deland-Han
manager: dcscontentpm
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: ad73520c99dd5418fd4444b3f41d7d1e22f8d52f
ms.sourcegitcommit: c79aa93d87d4db04ecc4e3eb68a75b349448cd17
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/18/2019
ms.locfileid: "71090828"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage Explorer トラブルシューティング ガイド

Microsoft Azure Storage Explorer は、Windows、macOS、Linux での Azure Storage データの操作を容易にするスタンドアロン アプリです。 このアプリは、Azure、各国のクラウド、および Azure Stack でホストされているストレージ アカウントに接続できます。

このガイドでは、Storage Explorer で一般に確認されている問題の解決方法を紹介しています。

## <a name="rbac-permissions-issues"></a>RBAC のアクセス許可の問題

ロールベースのアクセス制御 [RBAC](https://docs.microsoft.com/azure/role-based-access-control/overview) は、アクセス許可のセットを_ロール_に結合することで、Azure リソースの非常にきめ細かなアクセス管理を可能にします。 ここでは、Storage Explorer で RBAC を最適に動作させる方法について説明します。

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Storage Explorer で自分のリソースにアクセスするにはどうすればいいですか?

RBAC を使用したストレージ リソースへのアクセスに問題がある場合は、適切なロールが割り当てられていない可能性があります。 次のセクションでは、ストレージ リソースにアクセスするために Storage Explorer で現在必要なアクセス許可について説明します。 適切なロールまたはアクセス許可があるかどうかが不明な場合は、Azure アカウント管理者に問い合わせてください。

#### <a name="read-listget-storage-accounts-permissions-issue"></a>"読み取り:ストレージ アカウントの一覧表示/取得" アクセス許可の問題

ストレージ アカウントを一覧表示するためのアクセス許可が必要です。 このアクセス許可を取得するには、_閲覧者_ ロールが割り当てられている必要があります。

#### <a name="list-storage-account-keys"></a>ストレージ アカウント キーの一覧表示

Storage Explorer では、アカウント キーを使用して要求を認証することもできます。 _共同作成者_ ロールなどのより強力なロールを使用してアカウント キーにアクセスすることができます。

> [!NOTE]
> アクセス キーは、それらを保持するすべてのユーザーに無制限のアクセス許可を付与します。 そのため、これらのキーをアカウント ユーザーに渡すことはお勧めしません。 アクセス キーを取り消す必要がある場合は、[Azure portal](https://portal.azure.com/) で再生成できます。

#### <a name="data-roles"></a>データ ロール

リソースからデータを読み取るためのアクセス権を付与するロールが少なくとも 1 つ割り当てられている必要があります。 たとえば、BLOB を一覧表示したりダウンロードしたりする場合は、少なくとも_ストレージ BLOB データ閲覧者_ ロールが必要になります。

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Storage Explorer でリソースを表示するには、管理レイヤーのロールが必要ですか?

Azure Storage には、_管理_と_データ_という 2 つのアクセスのレイヤーがあります。 サブスクリプションとストレージ アカウントには管理レイヤーを介してアクセスします。 コンテナー、BLOB、およびその他のデータ リソースには、データ レイヤーを介してアクセスします。 たとえば、Azure からストレージ アカウントの一覧を取得する場合は、管理エンドポイントに要求を送信します。 アカウント内の BLOB コンテナーの一覧が必要な場合は、適切なサービス エンドポイントに要求を送信します。

RBAC ロールには、管理レイヤーまたはデータ レイヤーにアクセスするためのアクセス許可が含まれている場合があります。 たとえば、閲覧者ロールは、管理レイヤー リソースへの読み取り専用アクセス権を付与します。

厳密に言えば、閲覧者ロールは、データ レイヤーのアクセス許可を提供せず、データ レイヤーにアクセスするためには必要ありません。

Storage Explorer を使用すると、Azure リソースに接続するために必要な情報を収集することで、リソースに簡単にアクセスできます。 たとえば、BLOB コンテナーを表示するために、Storage Explorer は BLOB サービス エンドポイントに "コンテナーの一覧表示" 要求を送信します。 そのエンドポイントを取得するために、Storage Explorer は、アクセスできるサブスクリプションとストレージ アカウントの一覧を検索します。 サブスクリプションとストレージ アカウントを検索するには、Storage Explorer は管理レイヤーにもアクセスする必要があります。

管理レイヤーのアクセス許可を付与するロールを持っていない場合、Storage Explorer は、データ レイヤーに接続するために必要な情報を取得できません。

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>管理レイヤーのアクセス許可を取得できない場合、管理者からは何が必要でしょうか?

現在、この問題に対する RBAC 関連のソリューションはありません。 回避策として、SAS URI を要求して[リソースにアタッチ](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#use-a-sas-uri)することができます。

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>エラー:証明書チェーンの自己署名証明書 (および同様のエラー)

証明書のエラーは、通常、次のいずれかの状況で発生します。

- アプリが "_透過プロキシ_" 経由で接続されています。つまり、サーバー (会社のサーバーなど) はHTTPS トラフィックを傍受し、暗号化を解除した後、自己署名証明書を使用して暗号化します。
- 受信した HTTPS メッセージに自己署名 SSL 証明書を挿入するアプリケーションを実行しています。 証明書を挿入するアプリケーションの例としては、ウイルス対策およびネットワーク トラフィック検査ソフトウェアなどがあります。

Storage Explorer は自己署名証明書または信頼されない証明書が表示されると、受信した HTTPS メッセージが変更されているかどうかを認識できなくなります。 自己署名証明書のコピーがある場合は、次の手順に従って、それを信頼するように Storage Explorer に指示できます。

1. Base-64 でエンコードされた X.509 (.cer) 証明書のコピーを取得します。
2. **[編集]**  >  **[SSL 証明書]**  >  **[証明書のインポート]** の順に移動し、ファイル ピッカーを使用して .cer ファイルを検索し、選択して開きます。

この問題は、複数の証明書 (ルートと中間) がある場合にも発生することがあります。 エラーを解決するには、両方の証明書を追加する必要があります。

証明書の発行元がわからない場合は、次の手順に従って確認します。

1. OpenSSL をインストールします。
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html):任意の Light バージョンで十分です。
    * Mac と Linux:お使いのオペレーティング システムに付属しているはずです。
2. OpenSSL を実行します。
    * Windows:インストール ディレクトリを開き、 **/bin/** を選択し、**openssl.exe** をダブルクリックします。
    * Mac と Linux:ターミナルから `openssl` を実行します。
3. `s_client -showcerts -connect microsoft.com:443` を実行します。
4. 自己署名証明書を検索します。 どの証明書が自己署名かわからない場合は、Subject (発行先) `("s:")` と Issuer (発行元) `("i:")` が同じであるものをすべてメモします。
5. 自己署名証明書が見つかったら、証明書ごとに、`-----BEGIN CERTIFICATE-----` から `-----END CERTIFICATE-----` までのすべての内容をコピーして、新しい .cer ファイルに貼り付けます。
6. Storage Explorer を開いて、 **[編集]**  >  **[SSL 証明書]**  >  **[証明書のインポート]** の順に移動します。 次に、ファイル ピッカーを使用して、作成した .cer ファイルを検索し、選択して開きます。

この手順で自己署名証明書が見つからない場合は、フィードバック ツールを使用して Microsoft にご連絡ください。 また、`--ignore-certificate-errors` フラグを使用して、コマンド ラインから Storage Explorer を開くこともできます。 このフラグを指定して開くと、Storage Explorer は証明書のエラーを無視します。

## <a name="sign-in-issues"></a>サインインの問題

### <a name="blank-sign-in-dialog-box"></a>空白のサインイン ダイアログ

空白のサインイン ダイアログ ボックスが表示されるのは、多くの場合、Active Directory フェデレーション サービス (AD FS) が Storage Explorer に対して、リダイレクトを実行するよう指示した場合です。これは、Electron ではサポートされていません。 この問題を回避するには、サインインにデバイス コード フローを使用してみることができます。 これを行うには、次のステップに従います。

1. メニューで、 **[プレビュー]**  >  **[Use Device Code Sign-In]\(デバイス コード サインインの使用\)** の順に移動します。
2. **[接続]** ダイアログを開きます (左側の垂直バーのプラグ アイコン、またはアカウント パネルの **[アカウントの追加]** を使用)。
3. サインインする環境を選択します。
4. **[サインイン]** を選択します。
5. 次のパネルの手順に従います。

既定のブラウザーが別のアカウントに既にサインインしているために、使用したいアカウントにサインインできない場合は、次のいずれかを実行してください。

- お使いのブラウザーのプライベート セッションに、リンクとコードを手動でコピーします。
- 別のブラウザーに、リンクとコードを手動でコピーします。

### <a name="reauthentication-loop-or-upn-change"></a>再認証ループまたは UPN の変更

再認証ループに入っているか、またはいずれかのアカウントの UPN を変更している場合は、次の手順を実行してください。

1. すべてのアカウントを削除した後、Storage Explorer を閉じます。
2. コンピューターから .IdentityService フォルダーを削除します。 Windows では、このフォルダーは `C:\users\<username>\AppData\Local` にあります。 Mac と Linux では、このフォルダーは、ユーザー ディレクトリのルートで見つけることができます。
3. Mac または Linux を実行している場合は、オペレーティング システムのキーストアから Microsoft.Developer.IdentityService エントリも削除する必要があります。 Mac では、キーストアは *Gnome Keychain* アプリケーションです。 Linux では、このアプリケーションは通常は _Keyring_ という名前ですが、お使いのディストリビューションによっては名前が違うことがあります。

### <a name="conditional-access"></a>条件付きアクセス

Storage Explorer によって使用される Azure AD ライブラリの制限により、Windows 10、Linux、または macOS で Storage Explorer を使用する場合、条件付きアクセスはサポートされません。

## <a name="mac-keychain-errors"></a>Mac キーチェーン エラー

macOS のキーチェーンは、Storage Explorer 認証ライブラリの問題を引き起こす状態になることがあります。 キーチェーンをこの状態から抜け出させるには、次の手順を実行します。

1. Storage Explorer を閉じます。
2. キーチェーンを開きます (コマンド キーと Space キーを押し、「**keychain**」と入力して、Enter キーを押す)。
3. "ログイン" キーチェーンを選択します。
4. 南京錠アイコンを選択してキーチェーンをロックします。 (プロセスが完了すると、南京錠はロックされているように見えます。 開いているアプリによっては、数秒かかる場合があります。)

    ![南京錠アイコン](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. ストレージ エクスプローラーを開きます。
6. "サービス ハブがキーチェーンへのアクセスを要求しています" というようなメッセージが表示されます。 Mac 管理者アカウントのパスワードを入力し、 **[常に許可]** ( **[常に許可]** が使用できない場合は **[許可]** ) を選択します。
7. サインインを試します。

### <a name="general-sign-in-troubleshooting-steps"></a>サインインの一般的なトラブルシューティングの手順

* macOS を使用しており、 **[Waiting for authentication]\(認証の完了を待機しています\)** ダイアログ ボックスの上にサインイン ウィンドウが表示されない場合は、[この手順](#mac-keychain-errors)を試してください。
* Storage Explorer を再起動します。
* 認証ウィンドウが空白の場合は、認証ダイアログ ボックスを閉じる前に少なくとも 1 分待機します。
* プロキシと証明書の設定が、使用中のマシンと Storage Explorer の両方で適切に構成されていることを確認します。
* Windows を実行しており、同じコンピューター上の Visual Studio 2019、およびサインイン資格情報にアクセスできる場合は、Visual Studio 2019 にサインインしてみてください。 Visual Studio 2019 へのサインインに成功したら、Storage Explorer を開き、アカウント パネルでアカウントを確認できます。

これらの方法がいずれもうまくいかない場合は、[GitHub でイシューを開いてください](https://github.com/Microsoft/AzureStorageExplorer/issues)。

### <a name="missing-subscriptions-and-broken-tenants"></a>サブスクリプションの欠落とテナントの破損

サインインに成功した後もサブスクリプションを取得できない場合は、次のトラブルシューティング方法を試してください。

* お使いのアカウントに必要なサブスクリプションへのアクセス権があることを確認します。 使用しようとしている Azure 環境のポータルにサインインすることによって、アクセス権を確認できます。
* 適切な Azure 環境 (Azure、Azure China 21Vianet、Azure Germany、Azure US Government、またはカスタム環境) を使用してサインインしていることを確認します。
* プロキシ サーバーの背後にいる場合は、Storage Explorer プロキシを正しく構成していることを確認します。
* アカウントを削除してから再度追加します。
* [More information]\(詳細\) リンクがある場合は、障害が発生しているテナントに対してどのエラー メッセージが報告されているかを確認します。 エラー メッセージへの対処方法がわからない場合は、遠慮なく [GitHub でイシューを開いてください](https://github.com/Microsoft/AzureStorageExplorer/issues)。

## <a name="cant-remove-an-attached-account-or-storage-resource"></a>アタッチされているアカウントまたはストレージのリソースを削除できない

アタッチされているアカウントまたはストレージのリソースを UI 経由で削除できない場合は、次のフォルダーを削除することにより、接続されているすべてのリソースを手動で削除できます。

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> これらのフォルダーを削除する前に、Storage Explorer を閉じます。

> [!NOTE]
> 今までに SSL 証明書をインポートしたことがある場合は、`certs` ディレクトリの内容をバックアップします。 後でバックアップを使用して、SSL 証明書を再インポートすることができます。

## <a name="proxy-issues"></a>プロキシの問題

まず、入力した次の情報が正しいことを確認します。

* プロキシの URL とポート番号
* プロキシで必要な場合はユーザー名とパスワード

> [!NOTE]
> Storage Explorer は、プロキシ設定を構成するためのプロキシ自動構成ファイルをサポートしていません。

### <a name="common-solutions"></a>一般的な解決方法

引き続き問題が発生する場合は、次のトラブルシューティング方法を試してください。

* プロキシを使用せずにインターネットに接続できる場合は、プロキシの設定を有効にしなくても Storage Explorer が動作することを確認します。 動作する場合は、プロキシの設定に問題があると考えられます。 管理者と連携して問題を特定してください。
* プロキシ サーバーを使用する他のアプリケーションが想定どおりに動作することを確認します。
* 使用しようとしている Azure 環境のポータルに接続できることを確認します。
* サービス エンドポイントからの応答を受信できるか確認します。 ブラウザーに、エンドポイント URL のいずれかを入力します。 接続できる場合は、InvalidQueryParameterValue または同様の XML の応答を受信するはずです。
* プロキシ サーバーで Storage Explorer を使用しているユーザーが他にもいる場合は、それらのユーザーが接続できるかを確認します。 できる場合は、プロキシ サーバー管理者に連絡する必要があります。

### <a name="tools-for-diagnosing-issues"></a>問題診断ツール

ネットワーク ツール (Windows の場合の Fiddler など) がある場合は、次のように問題を診断できます。

* プロキシ経由で作業する必要がある場合は、プロキシ経由で接続するようにネットワーク ツールを構成する必要があります。
* ネットワーク ツールで使用されるポート番号を確認します。
* Storage Explorer のプロキシ設定として、ローカル ホストの URL とネットワーク ツールのポート番号を入力します。 この操作を正しく行うと、お使いのネットワーク ツールは、Storage Explorer が管理エンドポイントとサービスエンドポイントに対して行ったネットワーク要求のログ記録を開始します。 たとえば、ブラウザーで BLOB エンドポイントに対して「`https://cawablobgrs.blob.core.windows.net/`」を入力すると、次のような応答が返されます。

  ![サンプル コード](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  この応答は、アクセスできなくても、リソースが存在することを示しています。

### <a name="contact-proxy-server-admin"></a>プロキシ サーバー管理者に問い合わせる

プロキシの設定が正しい場合は、プロキシ サーバー管理者に連絡して次のことを行う必要があります。

* プロキシによって Azure の管理エンドポイントまたはリソース エンドポイントへのトラフィックがブロックされていないことを確認します。
* プロキシ サーバーで使用されている認証プロトコルを確認します。 Storage Explorer は現在、NTLM プロキシをサポートしていません。

## <a name="unable-to-retrieve-children-error-message"></a>"子を取得できません" エラー メッセージ

プロキシ経由で Azure に接続されている場合は、プロキシ設定が正しいことを確認します。 サブスクリプションまたはアカウントの所有者からリソースへのアクセス権が付与されている場合は、そのリソースへの読み取りまたは一覧表示のアクセス許可があることを確認してください。

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>接続文字列に完全な構成設定が含まれていない

このエラー メッセージが表示された場合は、ストレージ アカウントのキーを取得するために必要なアクセス許可を持っていない可能性があります。 このケースに該当することを確認するには、ポータルに移動してストレージ アカウントを見つけます。 これを行うには、ストレージ アカウントのノードを右クリックし、 **[ポータルで開く]** を選択します。 次に、 **[アクセス キー]** ブレードに移動します。 キーを表示するためのアクセス許可がない場合は、"アクセス権がありません" というメッセージが表示されます。 この問題を回避するには、他のユーザーからアカウント キーを入手し、名前とキーを使用してアタッチします。または、他のユーザーからストレージ アカウントへの SAS を入手し、これを使用してストレージ アカウントを接続することもできます。

アカウント キーが表示される場合は、Microsoft が問題の解決を手助けできるように、GitHub でイシューを報告してください。

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>Error occurred while adding new connection:TypeError:Cannot read property 'version' of undefined (新しい接続の追加中にエラーが発生しました: TypeError: 未定義のプロパティ 'version' を読み取れません)

カスタム接続を追加しようとしたときにこのエラー メッセージが表示される場合は、ローカル資格情報マネージャーに格納されている接続データが破損している可能性があります。 この問題を回避するには、破損したローカル接続を削除してから、再度追加してください。

1. Storage Explorer を開始します。 メニューから、 **[ヘルプ]**  >  **[開発者ツールの切り替え]** に移動します。
2. 開いたウィンドウの **[アプリケーション]** タブで、 **[ローカル ストレージ]** (左側) > **[file://]** に移動します。
3. 問題が発生している接続の種類に応じて、そのキーを検索し、その値をテキスト エディターにコピーします。 値は、次のようなカスタム接続名の配列です。
    * ストレージ アカウント
        * `StorageExplorer_CustomConnections_Accounts_v1`
    * BLOB コンテナー
        * `StorageExplorer_CustomConnections_Blobs_v1`
        * `StorageExplorer_CustomConnections_Blobs_v2`
    * ファイル共有
        * `StorageExplorer_CustomConnections_Files_v1`
    * キュー
        * `StorageExplorer_CustomConnections_Queues_v1`
    * テーブル
        * `StorageExplorer_CustomConnections_Tables_v1`
4. 現在の接続名を保存したら、開発者ツールの値を `[]` に設定します。

破損していない接続を維持する場合は、次の手順を使用して、破損した接続を特定できます。 既存の接続がすべて失われてもかまわない場合は、この手順を省略し、プラットフォーム固有の指示に従って接続データを消去できます。

1. テキスト エディターから開発者ツールに各接続名を再追加し、接続がまだ機能しているかどうかを確認します。
2. 接続が正常に機能している場合は、破損していないため、そのままにしておくことができます。 接続が機能していない場合は、その値を開発者ツールから削除し、後で再追加できるように記録しておきます。
3. すべての接続を検査するまで繰り返します。

すべての接続を処理した後、再追加されていないすべての接続名に対して、破損したデータ (存在する場合) をクリアし、Storage Explorer の標準手順を使用してそれらを再び追加する必要があります。

# <a name="windowstabwindows"></a>[Windows](#tab/Windows)

1. **[スタート]** メニューで **[資格情報マネージャー]** を検索して開きます。
2. **[Windows 資格情報]** に移動します。
3. **[汎用資格情報]** で、`<connection_type_key>/<corrupted_connection_name>` キー (たとえば、`StorageExplorer_CustomConnections_Accounts_v1/account1`) が含まれているエントリを探します。
4. これらのエントリを削除してから、接続をもう一度追加してください。

# <a name="macostabmacos"></a>[macOS](#tab/macOS)

1. Spotlight (コマンド キーとスペース キー) を開き、 **[キーチェーン アクセス]** を検索します。
2. `<connection_type_key>/<corrupted_connection_name>` キー (たとえば、`StorageExplorer_CustomConnections_Accounts_v1/account1`) が含まれているエントリを探します。
3. これらのエントリを削除してから、接続をもう一度追加してください。

# <a name="linuxtablinux"></a>[Linux](#tab/Linux)

ローカル資格情報の管理は、Linux ディストリビューションによって異なります。 Linux ディストリビューションにローカル資格情報管理用の組み込み GUI ツールが用意されていない場合は、サードパーティ製のツールをインストールしてローカル資格情報を管理できます。 たとえば、Linux のローカル資格情報を管理するためのオープンソース GUI である [Seahorse](https://wiki.gnome.org/Apps/Seahorse/) を使用できます。

1. ローカル資格情報管理ツールを開き、保存されている資格情報を検索します。
2. `<connection_type_key>/<corrupted_connection_name>` キー (たとえば、`StorageExplorer_CustomConnections_Accounts_v1/account1`) が含まれているエントリを探します。
3. これらのエントリを削除してから、接続をもう一度追加してください。
---

これらの手順を実行した後もこのエラーが発生する場合、または接続の破損の原因と思われることを共有したい場合は、GitHub のページで[イシューを開いてください](https://github.com/microsoft/AzureStorageExplorer/issues)。

## <a name="issues-with-sas-url"></a>SAS URL の問題

SAS URL を使用してサービスに接続し、エラーが発生する場合は、次の手順を実行してください。

* リソースの読み取りまたは一覧表示に必要なアクセス許可が URL で提供されているかを確認します。
* URL の有効期限が切れていないかを確認します。
* SAS URL がアクセス ポリシーに基づいている場合は、このアクセス ポリシーが失効されていないかを確認します。

無効な SAS URL を使用して誤ってアタッチし、デタッチできなくなった場合は、次の手順を実行します。

1. Storage Explorer を実行している場合は、F12 キーを押して開発者ツール ウィンドウを開きます。
2. **[アプリケーション]** タブで、左側のツリーから **[ローカル ストレージ]**  >  **[file://]** の順に選択します。
3. 問題がある SAS URI のサービスの種類と関連付けられているキーを見つけます。 たとえば、BLOB コンテナーの SAS URI が不適切である場合は、`StorageExplorer_AddStorageServiceSAS_v1_blob` というキーを探します。
4. キーの値は JSON 配列にする必要があります。 不適切な URI に関連付けられたオブジェクトを見つけて削除します。
5. Ctrl キーを押しながら R キーを押して、Storage Explorer をリロードします。

## <a name="linux-dependencies"></a>Linux の依存関係

Storage Explorer 1.10.0 以降は、Snap Store からスナップとして入手できます。 Storage Explorer スナップは、すべての依存関係を自動的にインストールし、スナップの新しいバージョンが利用可能になると更新されます。 Storage Explorer スナップのインストールは、推奨されるインストール方法です。

Storage Explorer では、パスワード マネージャーを使用する必要があります。Storage Explorer が正しく機能するためには、これを手動で接続しなければならない場合があります。 次のコマンドを実行して、Storage Explorer をシステムのパスワード マネージャーに接続できます。

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

アプリケーションを tar.gz ファイルとしてダウンロードすることもできますが、依存関係は手動でインストールする必要があります。

> [!IMPORTANT]
> .tar.gz のダウンロードで提供されている Storage Explorer は、Ubuntu ディストリビューションでのみサポートされています。 他のディストリビューションについては検証されておらず、代替または追加のパッケージが必要となる場合があります。

Linux 用の Storage Explorer の場合、次のパッケージが最も一般的な要件となります。

* [.NET Core 2.0 ランタイム](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x)
* `libgconf-2-4`
* `libgnome-keyring0` または `libgnome-keyring-dev`
* `libgnome-keyring-common`

> [!NOTE]
> Storage Explorer バージョン 1.7.0 以前には .NET Core 2.0 が必要です。 新しいバージョンの .NET Core がインストールされている場合は、[Storage Explorer に修正プログラムを適用](#patching-storage-explorer-for-newer-versions-of-net-core)する必要があります。 Storage Explorer 1.8.0 以降を実行している場合は、.NET Core 2.2 まで使用できます。 現時点で、2.2 を超えるバージョンは動作が検証されていません。

# <a name="ubuntu-1904tab1904"></a>[Ubuntu 19.04](#tab/1904)

1. Storage Explorer をダウンロードします。
2. [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu19-04/runtime-current) をインストールします。
3. 次のコマンドを実行します。
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring0
   ```

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. Storage Explorer をダウンロードします。
2. [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-current) をインストールします。
3. 次のコマンドを実行します。
   ```bash
   sudo apt-get install libgconf-2-4 libgnome-keyring-common libgnome-keyring0
   ```

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. Storage Explorer をダウンロードします。
2. [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-current) をインストールします。
3. 次のコマンドを実行します。
   ```bash
   sudo apt install libgnome-keyring-dev
   ```

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. Storage Explorer をダウンロードします。
2. [.NET Core Runtime](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-current) をインストールします。
3. 次のコマンドを実行します。
   ```bash
   sudo apt install libgnome-keyring-dev
   ```
---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>新しいバージョンの .NET Core への Storage Explorer 修正プログラムの適用

Storage Explorer 1.7.0 以前の場合は、Storage Explorer で使用されている .NET Core のバージョンに対して修正プログラムを適用しなければならない場合があります。

1. [NuGet から](https://www.nuget.org/packages/StreamJsonRpc/1.5.43)バージョン 1.5.43 の StreamJsonRpc をダウンロードします。 ページの右側で "パッケージのダウンロード" リンクを探します。
2. パッケージをダウンロードした後、そのファイル拡張子を `.nupkg` から `.zip` に変更します。
3. パッケージを解凍します。
4. `streamjsonrpc.1.5.43/lib/netstandard1.1/` フォルダーを開きます。
5. `StreamJsonRpc.dll` を Storage Explorer フォルダー内の次の場所にコピーします。
   * `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
   * `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-the-azure-portal-doesnt-work"></a>Azure portal で [Explorer で開く] が機能しない

Azure portal の **[Explorer で開く]** ボタンが機能しない場合は、互換性があるブラウザーを使用していることを確認してください。 互換性についてテスト済みのブラウザーは、次のとおりです｡
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>次の手順

これらの解決策のいずれもうまくいかない場合は、[GitHub でイシューを開いてください](https://github.com/Microsoft/AzureStorageExplorer/issues)。 これは、左下隅にある **[Report issue to GitHub]\(GitHub にイシューを報告する\)** ボタンを選択して実行することもできます。

![フィードバック](./media/storage-explorer-troubleshooting/feedback-button.PNG)
