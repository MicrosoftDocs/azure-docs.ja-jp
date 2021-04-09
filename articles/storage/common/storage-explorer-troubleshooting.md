---
title: Azure Storage Explorer トラブルシューティング ガイド | Microsoft Docs
description: Azure Storage Explorer のデバッグ手法の概要
services: storage
author: Deland-Han
manager: dcscontentpm
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/28/2020
ms.author: delhan
ms.openlocfilehash: 15df9b38abe35fe3eefad2fa160e1c1f16fe7aa7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102439461"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage Explorer トラブルシューティング ガイド

Microsoft Azure Storage Explorer は、Windows、macOS、Linux での Azure Storage データの操作を容易にするスタンドアロン アプリです。 このアプリは、Azure、各国のクラウド、および Azure Stack でホストされているストレージ アカウントに接続できます。

このガイドでは、Storage Explorer で一般に確認されている問題の解決方法を紹介しています。

## <a name="azure-rbac-permissions-issues"></a>Azure RBAC のアクセス許可に関する問題

Azure のロールベースのアクセス制御 ([Azure RBAC](../../role-based-access-control/overview.md)) を使用すると、アクセス許可のセットを "_ロール_" に結び付けることで、Azure リソースの非常にきめ細かなアクセス管理が可能になります。 ここでは、Storage Explorer で Azure RBAC を最適に動作させる方法について説明します。

### <a name="how-do-i-access-my-resources-in-storage-explorer"></a>Storage Explorer で自分のリソースにアクセスするにはどうすればいいですか?

Azure RBAC を使用したストレージ リソースへのアクセスに問題がある場合は、適切なロールが割り当てられていない可能性があります。 次のセクションでは、ストレージ リソースにアクセスするために Storage Explorer で現在必要なアクセス許可について説明します。 適切なロールまたはアクセス許可があるかどうかが不明な場合は、Azure アカウント管理者に問い合わせてください。

#### <a name="read-listget-storage-accounts-permissions-issue"></a>"読み取り:ストレージ アカウントの一覧表示/取得" アクセス許可の問題

ストレージ アカウントを一覧表示するためのアクセス許可が必要です。 このアクセス許可を取得するには、_閲覧者_ ロールが割り当てられている必要があります。

#### <a name="list-storage-account-keys"></a>ストレージ アカウント キーの一覧表示

Storage Explorer では、アカウント キーを使用して要求を認証することもできます。 _共同作成者_ ロールなどのより強力なロールを使用してアカウント キーにアクセスすることができます。

> [!NOTE]
> アクセス キーは、それらを保持するすべてのユーザーに無制限のアクセス許可を付与します。 そのため、これらのキーをアカウント ユーザーに渡すことはお勧めしません。 アクセス キーを取り消す必要がある場合は、[Azure portal](https://portal.azure.com/) で再生成できます。

#### <a name="data-roles"></a>データ ロール

リソースからデータを読み取るためのアクセス権を付与するロールが少なくとも 1 つ割り当てられている必要があります。 たとえば、BLOB を一覧表示したりダウンロードしたりする場合は、少なくとも _ストレージ BLOB データ閲覧者_ ロールが必要になります。

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Storage Explorer でリソースを表示するには、管理レイヤーのロールが必要ですか?

Azure Storage には、_管理_ と _データ_ という 2 つのアクセスのレイヤーがあります。 サブスクリプションとストレージ アカウントには管理レイヤーを介してアクセスします。 コンテナー、BLOB、およびその他のデータ リソースには、データ レイヤーを介してアクセスします。 たとえば、Azure からストレージ アカウントの一覧を取得する場合は、管理エンドポイントに要求を送信します。 アカウント内の BLOB コンテナーの一覧が必要な場合は、適切なサービス エンドポイントに要求を送信します。

Azure ロールでは、管理レイヤーまたはデータ レイヤーにアクセスするためのアクセス許可を与えることができます。 たとえば、閲覧者ロールは、管理レイヤー リソースへの読み取り専用アクセス権を付与します。

厳密に言えば、閲覧者ロールは、データ レイヤーのアクセス許可を提供せず、データ レイヤーにアクセスするためには必要ありません。

Storage Explorer を使用すると、Azure リソースに接続するために必要な情報を収集することで、リソースに簡単にアクセスできます。 たとえば、BLOB コンテナーを表示するために、Storage Explorer は BLOB サービス エンドポイントに "コンテナーの一覧表示" 要求を送信します。 そのエンドポイントを取得するために、Storage Explorer は、アクセスできるサブスクリプションとストレージ アカウントの一覧を検索します。 サブスクリプションとストレージ アカウントを検索するには、Storage Explorer は管理レイヤーにもアクセスする必要があります。

管理レイヤーのアクセス許可を付与するロールを持っていない場合、Storage Explorer は、データ レイヤーに接続するために必要な情報を取得できません。

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>管理レイヤーのアクセス許可を取得できない場合、管理者からは何が必要でしょうか?

BLOB コンテナー、ADLS Gen2 コンテナーまたはディレクトリ、またはキューにアクセスする場合、Azure 資格情報を利用してそれらのリソースにアタッチできます。

1. [接続] ダイアログを開きます。
1. 接続先のリソースの種類を選択します。
1. **[Azure Active Directory (Azure AD) を使用してサインインする]** を選択します。 **[次へ]** を選択します。
1. アタッチしているリソースに関連付けられているユーザー アカウントとテナントを選択します。 **[次へ]** を選択します。
1. リソースの URL を入力し、接続の一意の表示名を入力します。 **[次へ]** 、 **[接続]** の順に選択します。

その他のリソースの種類については現在、Azure RBAC 関連のソリューションはありません。 回避策として、SAS URL を要求し、次の手順に従ってリソースにアタッチすることができます。

1. [接続] ダイアログを開きます。
1. 接続先のリソースの種類を選択します。
1. **[Shared Access Signature (SAS)]** を選択します。 **[次へ]** を選択します。
1. 受け取った SAS URL を入力し、接続の一意の表示名を入力します。 **[次へ]** 、 **[接続]** の順に選択します。
 
リソースへのアタッチの詳細については、「[個々のリソースにアタッチする](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=linux#attach-to-an-individual-resource)」を参照してください。

### <a name="recommended-azure-built-in-roles"></a>推奨される Azure 組み込みロール

Storage Explorer を使用するために必要なアクセス許可を提供できる Azure 組み込みロールがいくつかあります。 そうしたロールの一部を以下に示します。
- [所有者](../../role-based-access-control/built-in-roles.md#owner):リソースへのアクセスを含め、すべてを管理します。
- [共同作成者](../../role-based-access-control/built-in-roles.md#contributor):リソースへのアクセスを除き、すべてを管理します。
- [閲覧者](../../role-based-access-control/built-in-roles.md#reader):リソースを読み取って一覧表示します。
- [ストレージ アカウント共同作業者](../../role-based-access-control/built-in-roles.md#storage-account-contributor): ストレージ アカウントの完全な管理。
- [ストレージ BLOB データ所有者](../../role-based-access-control/built-in-roles.md#storage-blob-data-owner):Azure Storage の BLOB コンテナーおよびデータに対するフル アクセス。
- [ストレージ BLOB データ共同作成者](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor):Azure Storage コンテナーと BLOB の読み取り、書き込み、削除を行います。
- [ストレージ BLOB データ閲覧者](../../role-based-access-control/built-in-roles.md#storage-blob-data-reader):Azure Storage コンテナーと BLOB の読み取りと一覧表示を行います。

> [!NOTE]
> アカウントキーへのアクセスは、所有者、共同作成者、ストレージ アカウント共同作成者の各ロールによって許可されます。

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>エラー:証明書チェーンの自己署名証明書 (および同様のエラー)

証明書のエラーは、通常、次のいずれかの状況で発生します。

- アプリは "_透過プロキシ_" 経由で接続されます。 つまり、サーバー (会社のサーバーなど) は HTTPS トラフィックを傍受し、暗号化を解除した後、自己署名証明書を使用して暗号化します。
- 受信した HTTPS メッセージに自己署名 TLS/SSL 証明書を挿入するアプリケーションを実行しています。 証明書を挿入するアプリケーションの例としては、ウイルス対策およびネットワーク トラフィック検査ソフトウェアなどがあります。

Storage Explorer は自己署名証明書または信頼されない証明書が表示されると、受信した HTTPS メッセージが変更されているかどうかを認識できなくなります。 自己署名証明書のコピーがある場合は、次の手順に従って、それを信頼するように Storage Explorer に指示できます。

1. Base-64 でエンコードされた X.509 (.cer) 証明書のコピーを取得します。
2. **[編集]**  >  **[SSL 証明書]**  >  **[証明書のインポート]** の順に移動し、ファイル ピッカーを使用して .cer ファイルを検索し、選択して開きます。

この問題は、複数の証明書 (ルートと中間) がある場合にも発生することがあります。 エラーを解決するには、両方の証明書を追加する必要があります。

証明書の発行元がわからない場合は、次の手順に従って確認します。

1. OpenSSL をインストールします。
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html):任意の Light バージョンで十分です。
    * Mac と Linux:お使いのオペレーティング システムに付属しているはずです。
2. OpenSSL を実行します。
    * Windows: インストール ディレクトリを開き、 **/bin/** を選択し、**openssl.exe** をダブルクリックします。
    * Mac と Linux:ターミナルから `openssl` を実行します。
3. `s_client -showcerts -connect microsoft.com:443` を実行します。
4. 自己署名証明書を検索します。 どの証明書が自己署名かわからない場合は、Subject (発行先) `("s:")` と Issuer (発行元) `("i:")` が同じであるものをすべてメモします。
5. 自己署名証明書が見つかったら、証明書ごとに、`-----BEGIN CERTIFICATE-----` から `-----END CERTIFICATE-----` までのすべての内容をコピーして、新しい .cer ファイルに貼り付けます。
6. Storage Explorer を開いて、 **[編集]**  >  **[SSL 証明書]**  >  **[証明書のインポート]** の順に移動します。 次に、ファイル ピッカーを使用して、作成した .cer ファイルを検索し、選択して開きます。

この手順で自己署名証明書が見つからない場合は、フィードバック ツールを使用して Microsoft にご連絡ください。 また、`--ignore-certificate-errors` フラグを使用して、コマンド ラインから Storage Explorer を開くこともできます。 このフラグを指定して開くと、Storage Explorer は証明書のエラーを無視します。

## <a name="sign-in-issues"></a>サインインの問題

### <a name="blank-sign-in-dialog-box"></a>空白のサインイン ダイアログ

空白のサインイン ダイアログ ボックスが表示されるのは、多くの場合、Active Directory フェデレーション サービス (AD FS) が Storage Explorer に対して、リダイレクトを実行するよう指示した場合です。これは、Electron ではサポートされていません。 この問題を回避するには、サインインにデバイス コード フローを使用してみることができます。 これを行うには、次のステップに従います。

1. 左側の垂直ツールバーで、 **[設定]** を開きます。 [設定] パネルで、 **[アプリケーション]**  >  **[サインイン]** にアクセスします。 **[デバイス コード フロー サインインの使用]** を有効にします。
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
4. 南京錠アイコンを選択してキーチェーンをロックします。 (南京錠は、プロセスの完了時にロックされているように見えます。 開いているアプリによっては、数秒かかることがあります。)

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

Storage Explorer では、プロキシ サーバーを経由した Azure Storage リソースへの接続がサポートされています。 プロキシを経由して Azure に接続する際に問題が発生した場合、推奨事項を以下に示します。

> [!NOTE]
> Storage Explorer では、プロキシサーバーでの基本認証のみがサポートされています。 NTLM などの他の認証方法はサポートされていません。

> [!NOTE]
> Storage Explorer は、プロキシ設定を構成するためのプロキシ自動構成ファイルをサポートしていません。

### <a name="verify-storage-explorer-proxy-settings"></a>Storage Explorer のプロキシ設定を確認する

**[アプリケーション] → [プロキシ] → [プロキシ構成]** 設定では、Storage Explorer によってどのソースからプロキシ構成を取得するかを決定します。

[環境変数を使用する] を選択する場合は、`HTTPS_PROXY` または `HTTP_PROXY` 環境変数を必ず設定してください (環境変数には大文字と小文字の区別があるため、必ず正しい変数を設定してください)。 これらの変数が定義されていないか無効である場合、プロキシは Storage Explorer で使用されません。 環境変数を変更したら、Storage Explorer を再起動します。

[Use app proxy settings]\(アプリのプロキシ設定を使用する\) を選択する場合は、アプリ内プロキシ設定が正しいことを確認します。

### <a name="steps-for-diagnosing-issues"></a>問題を診断するための手順

引き続き問題が発生する場合は、次のトラブルシューティング方法をお試しください。

1. プロキシを使用せずにインターネットに接続できる場合は、プロキシの設定を有効にしなくても Storage Explorer が動作することを確認します。 Storage Explorer が正常に接続されている場合は、ご利用のプロキシ サーバーに問題があるおそれがあります。 管理者と連携して問題を特定してください。
2. プロキシ サーバーを使用する他のアプリケーションが想定どおりに動作することを確認します。
3. 使用しようとしている Azure 環境のポータルに接続できることを確認します。
4. サービス エンドポイントからの応答を受信できるか確認します。 ブラウザーに、エンドポイント URL のいずれかを入力します。 接続することができる場合は、`InvalidQueryParameterValue` または同様の XML の応答が送られてきます。
5. 同じプロキシ サーバーで Storage Explorer を使用している他の誰かが接続できるかどうかを確認します。 できる場合は、プロキシ サーバー管理者に連絡する必要があります。

### <a name="tools-for-diagnosing-issues"></a>問題診断ツール

Fiddler などのネットワーク ツールを使用すると、問題の診断が容易になります。

1. ネットワーク ツールを、ローカル ホスト上で実行されるプロキシ サーバーとして構成します。 実際のプロキシの内側で作業を続ける必要がある場合は、プロキシ経由で接続するようにネットワーク ツールを構成することが必要な場合があります。
2. ネットワーク ツールで使用されるポート番号を確認します。
3. ローカル ホストとネットワーク ツールのポート番号 ("localhost:8888" など) を使用するように Storage Explorer のプロキシ設定を構成します。
 
設定が正しく行われると、Storage Explorer によって生成されたネットワーク要求が管理およびサービス エンドポイントにネットワーク ツールによってログされます。
 
ネットワーク ツールによって Storage Explorer のトラフィックがログされていないようである場合は、そのツールを別のアプリケーションでテストしてみてください。 たとえば、ご利用のストレージ リソースのいずれかのエンドポイント URL (`https://contoso.blob.core.windows.net/` など) を入力すると、次のような応答が送られてきます。

  ![コード サンプル](./media/storage-explorer-troubleshooting/4022502_en_2.png)

  リソースにアクセスできなくても、この応答があれば、それが存在することになります。

ネットワーク ツールに他のアプリケーションからのトラフィックしか表示されない場合、Storage Explorer のプロキシ設定を調整することが必要な場合があります。 それ以外の場合は、ツールの設定を調整することが必要なことがあります。

### <a name="contact-proxy-server-admin"></a>プロキシ サーバー管理者に問い合わせる

プロキシの設定が正しい場合、プロキシ サーバー管理者に連絡して次のことを行うことが必要な場合があります。

* プロキシによって Azure の管理エンドポイントまたはリソース エンドポイントへのトラフィックがブロックされていないことを確認します。
* プロキシ サーバーで使用されている認証プロトコルを確認します。 Storage Explorer では、基本認証プロトコルのみがサポートされます。 Storage Explorer で NTLM プロキシはサポートされていません。

## <a name="unable-to-retrieve-children-error-message"></a>"子を取得できません" エラー メッセージ

プロキシ経由で Azure に接続されている場合は、プロキシ設定が正しいことを確認します。

サブスクリプションまたはアカウントの所有者からリソースへのアクセス権が付与されている場合は、該当するリソースの読み取りまたは一覧表示の権限があることを確認してください。

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>接続文字列に完全な構成設定が含まれていない

このエラー メッセージが表示された場合は、ストレージ アカウントのキーを取得するために必要なアクセス許可を持っていない可能性があります。 このケースに該当することを確認するには、ポータルに移動してストレージ アカウントを見つけます。 これを行うには、ストレージ アカウントのノードを右クリックし、 **[ポータルで開く]** を選択します。 次に、 **[アクセス キー]** ブレードに移動します。 キーを表示するためのアクセス許可がない場合は、"アクセス権がありません" というメッセージが表示されます。 この問題を回避するには、他のユーザーからアカウント キーを入手し、名前とキーを使用してアタッチします。または、他のユーザーからストレージ アカウントへの SAS を入手し、これを使用してストレージ アカウントを接続することもできます。

アカウント キーが表示される場合は、Microsoft が問題の解決を手助けできるように、GitHub でイシューを報告してください。

## <a name="error-occurred-while-adding-new-connection-typeerror-cannot-read-property-version-of-undefined"></a>新しい接続の追加中にエラーが発生しました: (Error occurred while adding new connection:)TypeError:Cannot read property 'version' of undefined (未定義のプロパティ ' version' を読み取ることができません)

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

# <a name="windows"></a>[Windows](#tab/Windows)

1. **[スタート]** メニューで **[資格情報マネージャー]** を検索して開きます。
2. **[Windows 資格情報]** に移動します。
3. **[汎用資格情報]** で、`<connection_type_key>/<corrupted_connection_name>` キー (たとえば、`StorageExplorer_CustomConnections_Accounts_v1/account1`) が含まれているエントリを探します。
4. これらのエントリを削除してから、接続をもう一度追加してください。

# <a name="macos"></a>[macOS](#tab/macOS)

1. Spotlight (コマンド キーとスペース キー) を開き、 **[キーチェーン アクセス]** を検索します。
2. `<connection_type_key>/<corrupted_connection_name>` キー (たとえば、`StorageExplorer_CustomConnections_Accounts_v1/account1`) が含まれているエントリを探します。
3. これらのエントリを削除してから、接続をもう一度追加してください。

# <a name="linux"></a>[Linux](#tab/Linux)

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

### <a name="snap"></a>スナップ

Storage Explorer 1.10.0 以降は、Snap Store からスナップとして入手できます。 Storage Explorer スナップは、すべての依存関係を自動的にインストールし、スナップの新しいバージョンが利用可能になると更新されます。 Storage Explorer スナップのインストールは、推奨されるインストール方法です。

Storage Explorer では、パスワード マネージャーを使用する必要があります。Storage Explorer が正しく機能するためには、これを手動で接続しなければならない場合があります。 次のコマンドを実行して、Storage Explorer をシステムのパスワード マネージャーに接続できます。

```bash
snap connect storage-explorer:password-manager-service :password-manager-service
```

### <a name="targz-file"></a>.tar.gz ファイル

アプリケーションを tar.gz ファイルとしてダウンロードすることもできますが、依存関係は手動でインストールする必要があります。

.tar.gz ダウンロードで提供されている Storage Explorer は、次の Ubuntu バージョンでのみサポートされています。 Storage Explorer は他の Linux ディストリビューションでも動作することがありますが、正式にはサポートされていません。

- Ubuntu 20.04 x64
- Ubuntu 18.04 x64
- Ubuntu 16.04 x64

Storage Explorer を使用するには、お使いのシステムに .NET Core をインストールする必要があります。 .NET Core 2.1 を推奨していますが、Storage Explorer は 2.2 でも動作します。

> [!NOTE]
> Storage Explorer バージョン 1.7.0 以前には .NET Core 2.0 が必要です。 新しいバージョンの .NET Core がインストールされている場合は、[Storage Explorer に修正プログラムを適用](#patching-storage-explorer-for-newer-versions-of-net-core)する必要があります。 Storage Explorer 1.8.0 以降を実行している場合は、少なくとも .NET Core 2.1 が必要になります。

# <a name="ubuntu-2004"></a>[Ubuntu 20.04](#tab/2004)

1. Storage Explorer .tar.gz ファイルをダウンロードします。
2. [.NET Core Runtime](/dotnet/core/install/linux) をインストールします。
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/20.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1804"></a>[Ubuntu 18.04](#tab/1804)

1. Storage Explorer .tar.gz ファイルをダウンロードします。
2. [.NET Core Runtime](/dotnet/core/install/linux) をインストールします。
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```

# <a name="ubuntu-1604"></a>[Ubuntu 16.04](#tab/1604)

1. Storage Explorer .tar.gz ファイルをダウンロードします。
2. [.NET Core Runtime](/dotnet/core/install/linux) をインストールします。
   ```bash
   wget https://packages.microsoft.com/config/ubuntu/16.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb; \
     sudo dpkg -i packages-microsoft-prod.deb; \
     sudo apt-get update; \
     sudo apt-get install -y apt-transport-https && \
     sudo apt-get update && \
     sudo apt-get install -y dotnet-runtime-2.1
   ```
---

Storage Explorer で必要なライブラリの多くは、Canonical の標準 Ubuntu インストールにプレインストールされています。 カスタム環境の場合、一部のライブラリが不足することがあります。 Storage Explorer を起動できない場合、お使いのシステムに次のパッケージがインストールされていることを確認することをお勧めします。

- iproute2
- libasound2
- libatm1
- libgconf2-4
- libnspr4
- libnss3
- libpulse0
- libsecret-1-0
- libx11-xcb1
- libxss1
- libxtables11
- libxtst6
- xdg-utils

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

## <a name="next-steps"></a>次のステップ

これらの解決策のいずれもうまくいかない場合は、[GitHub でイシューを開いてください](https://github.com/Microsoft/AzureStorageExplorer/issues)。 これは、左下隅にある **[Report issue to GitHub]\(GitHub にイシューを報告する\)** ボタンを選択して実行することもできます。

![フィードバック](./media/storage-explorer-troubleshooting/feedback-button.PNG)