---
title: Azure Storage Explorer トラブルシューティング ガイド | Microsoft Docs
description: Azure Storage Explorer のデバッグ手法の概要
services: virtual-machines
author: Deland-Han
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 06/15/2018
ms.author: delhan
ms.openlocfilehash: 03cb3f2339dda1bf1dbb510b686882e924a98d74
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67118699"
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage Explorer トラブルシューティング ガイド

Microsoft Azure Storage Explorer は、Windows、macOS、Linux で Azure Storage データを容易に操作できるスタンドアロン アプリです。 このアプリは、Azure、National Clouds、および Azure Stack でホストされているストレージ アカウントに接続できます。

このガイドでは、Storage Explorer で確認されている一般的な問題の解決方法を紹介しています。

## <a name="role-based-access-control-permission-issues"></a>ロールベースのアクセス制御のアクセス許可に関する問題

[ロールベースのアクセス制御 (RBAC)](https://docs.microsoft.com/azure/role-based-access-control/overview) では、アクセス許可のセットを_ロール_に統合することで Azure リソースのアクセス権を詳細に管理できます。 ここでは、Storage Explorer で機能する RBAC を取得するためのいくつかの推奨事項を示します。

### <a name="what-do-i-need-to-see-my-resources-in-storage-explorer"></a>Storage Explorer でリソースを表示するには何が必要ですか?

RBAC を使用したストレージ リソースへのアクセスに関する問題が発生している場合は、適切なロールが割り当てられていない可能性があります。 次のセクションでは、Storage Explorer が現在ストレージ リソースにアクセスするために必要なアクセス許可について説明します。

適切なロールまたはアクセス許可があるかどうか不明な場合は、Azure アカウント管理者に問い合わせてください。

#### <a name="read-listget-storage-accounts"></a>Read:ストレージ アカウントの一覧表示/取得

ストレージ アカウントを一覧表示するためのアクセス許可が必要です。 このアクセス許可は、"閲覧者" ロールを割り当てることで取得できます。

#### <a name="list-storage-account-keys"></a>ストレージ アカウント キーの一覧表示

Storage Explorer では、アカウント キーを使用して要求を認証することもできます。 "共同作成者" ロールなどのより強力なロールでキーにアクセスすることができます。

> [!NOTE]
> アクセス キーは、それらを保持するすべてのユーザーに無制限のアクセス許可を付与します。 そのため、それをアカウント ユーザーに渡すことは一般にお勧めできません。 アクセス キーを取り消す必要がある場合は、[Azure portal](https://portal.azure.com/) で再生成することができます。

#### <a name="data-roles"></a>データ ロール

リソースからのデータの読み取りアクセス権を付与するロールを、少なくとも 1 つ割り当てる必要があります。 たとえば、BLOB を一覧表示したりダウンロードしたりする必要がある場合は、少なくとも "ストレージ BLOB データ閲覧者" ロールが必要になります。

### <a name="why-do-i-need-a-management-layer-role-to-see-my-resources-in-storage-explorer"></a>Storage Explorer でリソースを表示するには、管理レイヤーのロールが必要ですか?

Azure Storage には、_管理_と_データ_という 2 つのアクセスのレイヤーがあります。 サブスクリプションとストレージ アカウントには管理レイヤーを介してアクセスします。 コンテナー、BLOB、およびその他のデータ リソースには、データ レイヤーを介してアクセスします。 たとえば、Azure からストレージ アカウントの一覧を取得する場合は、管理エンドポイントに要求を送信します。 アカウント内の BLOB コンテナーの一覧が必要な場合は、適切なサービス エンドポイントに要求を送信します。

RBAC ロールには、管理またはデータ レイヤーへのアクセスのアクセス許可が含まれている場合があります。 たとえば、"閲覧者" ロールは、管理レイヤーのリソースへの読み取り専用アクセス権を付与します。

厳密に言えば、"閲覧者" ロールはデータ レイヤーのアクセス許可を提供せず、データ レイヤーにアクセスする必要はありません。

Storage Explorer は、Azure リソースに接続するために必要な情報を収集することにより、リソースに簡単にアクセスできるようにします。 たとえば、BLOB コンテナーを表示するために、Storage Explorer は、要求をコンテナーの一覧表示要求を BLOB サービス エンドポイントに送信します。 そのエンドポイントを取得するために、Storage Explorer は、アクセスできるサブスクリプションとストレージ アカウントの一覧を検索します。 ただし、サブスクリプションとストレージ アカウントを検索するためには、Storage Explorer は管理レイヤーにもアクセスする必要があります。

管理レイヤーのアクセス許可を付与するロールを持っていない場合、Storage Explorer は、データ レイヤーに接続するために必要な情報を取得できません。

### <a name="what-if-i-cant-get-the-management-layer-permissions-i-need-from-my-administrator"></a>管理レイヤーのアクセス許可を取得できない場合、管理者からは何が必要でしょうか?

現時点で RBAC 関連の解決策はまだありません。 回避策として、SAS URI を要求して[リソースにアタッチ](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=linux#attach-a-service-by-using-a-shared-access-signature-sas)することができます。

## <a name="error-self-signed-certificate-in-certificate-chain-and-similar-errors"></a>エラー:証明書チェーンの自己署名証明書 (および同様のエラー)

証明書のエラーは、次の 2 つの状況のいずれかで発生します。

1. アプリが “透過型プロキシ” を介して接続されています。すなわち、サーバー (会社のサーバーなど) によって HTTPS トラフィックが遮断され、暗号化が解除され、その後自己署名証明書を使用して暗号化されています。
2. ユーザーが受信する HTTPS メッセージに自己署名 SSL 証明書を挿入するアプリケーションを実行しています。 証明書を挿入するアプリケーションの例には、ウイルス対策およびネットワーク トラフィック検査ソフトウェアが含まれています。

Storage Explorer に自己署名証明書または信頼されない証明書が表示された場合、受信した HTTPS メッセージが変更されたかどうかを知ることができなくなります。 自己署名証明書のコピーがある場合は、次のステップを実行することによって、Storage Explorer にそのコピーを信頼させることができます。

1. Base-64 でエンコードされた X.509 (.cer) 証明書のコピーを取得します
2. **[編集]**  >  **[SSL 証明書]**  >  **[証明書のインポート]** の順にクリックし、ファイル ピッカーを使用して、.cer ファイルを検索して選択し、開きます

この問題は、複数の証明書 (ルート証明書および中間証明書) があることが原因になっている場合もあります。 エラーを解決するには両方の証明書を追加する必要があります。

証明書がどこから来ているかわからない場合は、それを見つけるために次の手順を試してみることができます。

1. Open SSL をインストールします。
    * [Windows](https://slproweb.com/products/Win32OpenSSL.html) (Light バージョンのいずれかで十分です)
    * Mac と Linux: オペレーティング システムに付属
2. Open SSL を実行します。
    * Windows: インストール ディレクトリを開き、 **/bin/** をクリックして、**openssl.exe** をダブルクリックします。
    * Mac と Linux: ターミナルで **openssl** を実行します。
3. `s_client -showcerts -connect microsoft.com:443` を実行します
4. 自己署名証明書を検索します。 どの証明書が自己署名済みかわからない場合は、件名 `("s:")` と発行者 `("i:")` が同じである任意の場所を探します。
5. 自己署名証明書が見つかったら、それぞれの証明書の **-----BEGIN CERTIFICATE-----** から **-----END CERTIFICATE-----** までのすべての内容をコピーして、新しい .cer ファイルに貼り付けます。
6. Storage Explorer を開き、 **[編集]**  >  **[SSL 証明書]**  >  **[証明書のインポート]** の順にクリックし、ファイル ピッカーを使用して、作成した .cer ファイルを検索して選択し、開きます。

前の手順を使用しても自己署名証明書が見つからない場合は、フィードバック ツールを使用して Microsoft にサポートを依頼してください。 また、`--ignore-certificate-errors` フラグを使用して、コマンド ラインから Storage Explorer を起動することも選択できます。 このフラグを指定して起動すると、Storage Explorer は証明書のエラーを無視します。

## <a name="sign-in-issues"></a>サインインの問題

### <a name="blank-sign-in-dialog"></a>空白のサインイン ダイアログ

サインイン ダイアログが空白になる原因で最もよくあるのは、Electron によってサポートされていないリダイレクトを実行するよう、ADFS が Storage Explorer に要求する場合です。 この問題を回避するには、サインインにデバイス コード フローを使用してみることができます。 これを行うには次の手順を実行します。

1. メニュー: [プレビュー] -> [Use Device Code Sign-In] (デバイス コード サインインを使用)。
2. [Connect]\(接続\) ダイアログを開きます (左側の垂直バーのプラグ アイコン、またはアカウント パネルの [Add Account]\(アカウントの追加\) を使用して)。
3. サインインする環境を選択します。
4. [サインイン] ボタンをクリックします。
5. 次のパネルの手順に従います。

既定のブラウザーが別のアカウントに既にサインインしているために、使用するアカウントへのサインインで問題が発生する場合は、次のどちらかのようにすることができます。

1. お使いのブラウザーのプライベート セッションに、リンクとコードを手動でコピーします。
2. 別のブラウザーに、リンクとコードを手動でコピーします。

### <a name="reauthentication-loop-or-upn-change"></a>再認証ループまたは UPN の変更

再認証ループに入っているか、またはいずれかのアカウントの UPN を変更している場合は、次の手順を試してください。

1. すべてのアカウントを削除した後、Storage Explorer を閉じます。
2. コンピューターから .IdentityService フォルダーを削除します。 Windows では、このフォルダーは `C:\users\<username>\AppData\Local` にあります。 Mac と Linux では、このフォルダーは、ユーザー ディレクトリのルートで見つけることができます。
3. Mac または Linux を使用している場合は、OS のキーストアから Microsoft.Developer.IdentityService エントリも削除する必要があります。 Mac では、キーストアは "Gnome Keychain" アプリケーションです。 Linux では、このアプリケーションは通常は "Keyring" という名前ですが、お使いのディストリビューションによっては名前が違うことがあります。

### <a name="conditional-access"></a>条件付きアクセス

Windows 10、Linux、または macOS 上で Storage Explorer が使用されている場合、条件付きアクセスはサポートされません。 これは、Storage Explorer によって使用される AAD ライブラリでの制限のためです。

## <a name="mac-keychain-errors"></a>Mac キーチェーン エラー

macOS のキーチェーンが、Storage Explorer の認証ライブラリの問題を引き起こす状態になることがあります。 この状態からキーチェーンを取得するには、以下の手順を実行します。

1. Storage Explorer を閉じます。
2. キーチェーンを開きます (**Cmd キーを押しながら Space キー**を押し、キーチェーンを入力し、Enter キーを押します)。
3. "ログイン" キーチェーンを選択します。
4. 南京錠アイコンをクリックして、キーチェーンをロックします (完了すると、鍵がかかった南京錠に表示が切り替わりますが、開いているアプリによっては数秒かかる場合があります)。

    ![image](./media/storage-explorer-troubleshooting/unlockingkeychain.png)

5. Storage Explorer を起動します。
6. サービス ハブがキーチェーンへのアクセスを求めている旨のメッセージを示すポップアップが表示されます。 それが表示されたら、Mac 管理者アカウントのパスワードを入力し、 **[常に許可]** ( **[常に許可]** が使用できない場合は **[許可]** ) をクリックします。
7. サインインを試します。

### <a name="general-sign-in-troubleshooting-steps"></a>サインインの一般的なトラブルシューティングの手順

* macOS を使用しており、[Waiting for authentication...] (認証の完了を待機しています) ダイアログの上にサインイン ウィンドウがいつまでも表示されない場合は、[この手順](#mac-keychain-errors)を試してください。
* Storage Explorer を再起動する
* 認証ウィンドウが空白の場合は、認証ダイアログ ボックスを閉じる前に少なくとも 1 分待機します。
* プロキシと証明書が、使用中のマシンと Storage Explorer の両方の設定用に正しく構成されていることを確認します。
* Windows を使用しており、同じコンピューター上で Visual Studio 2019 にアクセスできるときにサインインする場合は、Visual Studio 2019 にサインインしてみてください。 Visual Studio 2019 へのサインインに成功したら、Storage Explorer を開き、アカウント パネルでアカウントを確認できます。

これらの方法がいずれもうまくいかない場合、[GitHub で問題をオープンしてください](https://github.com/Microsoft/AzureStorageExplorer/issues)。

### <a name="missing-subscriptions-and-broken-tenants"></a>サブスクリプションの欠落とテナントの破損

サインインに成功した後もサブスクリプションを取得できない場合は、次のトラブルシューティング方法を試してください。

* お使いのアカウントに必要なサブスクリプションへのアクセス権があることを確認します。 使用しようとしている Azure 環境のポータルにサインインすることによって、アクセス権を確認できます。
* 適切な Azure 環境 (Azure、Azure China 21Vianet、Azure Germany、Azure US Government、またはカスタム環境) を使用してサインインしていることを確認します。
* プロキシの背後にいる場合は、Storage Explorer プロキシを正しく構成していることを確認します。
* アカウントを削除してから再度追加します。
* [詳細] リンクが存在する場合は、障害が発生しているテナントに対してどのようなエラー メッセージが報告されているかを確認します。 表示されているエラー メッセージをどうしたらよいかわからない場合は、どうぞお気軽に [GitHub で問題を開いてください](https://github.com/Microsoft/AzureStorageExplorer/issues)。

## <a name="cant-remove-attached-account-or-storage-resource"></a>接続されているアカウントまたはストレージ リソースを削除できない

接続されているアカウントまたはストレージ リソースを UI 経由で削除できない場合は、次のフォルダーを削除することにより、接続されているすべてのリソースを手動で削除できます。

* Windows: `%AppData%/StorageExplorer`
* macOS: `/Users/<your_name>/Library/Application Support/StorageExplorer`
* Linux: `~/.config/StorageExplorer`

> [!NOTE]
> 上記のフォルダーを削除する前に、Storage Explorer エクスプローラーを閉じます。

> [!NOTE]
> これまで SSL 証明書をインポートしたことがある場合、`certs` ディレクトリの内容をバックアップします。 後でバックアップを使用して、SSL 証明書を再インポートすることができます。

## <a name="proxy-issues"></a>プロキシの問題

最初に、入力した次の情報がすべて正しいことを確認します。

* プロキシの URL とポート番号
* プロキシで必要な場合はユーザー名とパスワード

> [!NOTE]
> Storage Explorer は、プロキシ設定を構成するためのプロキシ自動構成ファイルをサポートしていません。

### <a name="common-solutions"></a>一般的な解決方法

引き続き問題が発生する場合は、次のトラブルシューティング方法を試してください。

* プロキシを使用せずにインターネットに接続できる場合は、プロキシの設定を有効にしなくても Storage Explorer が動作することを確認します。 動作する場合は、プロキシの設定に問題があると考えられます。 プロキシ管理者と連携して問題を特定してください。
* プロキシ サーバーを使用する他のアプリケーションが期待どおりに動作するかを確認します。
* 使用しようとしている Azure 環境のポータルに接続できることを確認します。
* サービス エンドポイントからの応答を受信できるか確認します。 ブラウザーに、エンドポイント URL のいずれかを入力します。 接続できる場合は、InvalidQueryParameterValue または同様の XML の応答を受信します。
* プロキシ サーバーで Storage Explorer を使用しているユーザーが他にもいる場合は、それらのユーザーが接続できるかを確認します。 他のユーザーが接続できる場合は、プロキシ サーバー管理者にお問い合わせください。

### <a name="tools-for-diagnosing-issues"></a>問題診断ツール

ネットワーク ツール (Windows の場合の Fiddler など) がある場合は、次のように問題を診断できます。

* プロキシ経由で作業する必要がある場合は、プロキシ経由で接続するようにネットワーク ツールを構成する必要があります。
* ネットワーク ツールで使用されるポート番号を確認します。
* Storage Explorer のプロキシ設定として、ローカル ホストの URL とネットワーク ツールのポート番号を入力します。 入力が正しく行われると、Storage Explorer によって生成された、管理とサービス エンドポイントに対するネットワーク要求のログ記録が、ネットワーク ツールによって開始されます。 たとえば、ブラウザーで BLOB エンドポイントとして「 https://cawablobgrs.blob.core.windows.net/ 」と入力すると、リソースは存在するが、それにはアクセスできないことを示す次のような応答を受信します。

![コード サンプル](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>プロキシ サーバー管理者に問い合わせる

プロキシの設定が正しい場合は、プロキシ サーバー管理者に問い合わせて、

* プロキシによって Azure の管理またはリソース エンドポイントへのトラフィックがブロックされていないことを確認します。
* プロキシ サーバーで使用されている認証プロトコルを確認します。 Storage Explorer は現在、NTLM プロキシをサポートしていません。

## <a name="unable-to-retrieve-children-error-message"></a>"子を取得できません" エラー メッセージ

プロキシ経由で Azure に接続されている場合は、プロキシ設定が正しいことを確認します。 サブスクリプションまたはアカウントの所有者からリソースへのアクセス権が付与されている場合は、そのリソースへの読み取りまたは一覧表示のアクセス許可があることを確認してください。

## <a name="connection-string-doesnt-have-complete-configuration-settings"></a>接続文字列に完全な構成設定が含まれていない

このエラー メッセージが表示された場合は、Storage アカウントのキーを取得するために必要なアクセス許可がない可能性があります。 これが該当するかどうかを確認するには、ポータルにアクセスして、自分の Storage アカウントを見つけます。 Storage アカウントのノードを右クリックし、[ポータルで開く] をクリックすると、この操作をすばやく行うことができます。 この操作を実行したら、[アクセス キー] ブレードに移動します。 キーを表示するためのアクセス許可がない場合は、"アクセス権がありません" というメッセージを含むページが表示されます。 この問題を回避するには、他のユーザーからアカウント キーを入手し、名前とキーを使用して接続します。または、他のユーザーから Storage アカウントへの SAS を入手し、これを使用して Storage アカウントを接続することもできます。

アカウント キーが表示された場合は、Microsoft が問題の解決を手助けできるように、GitHub で問題を報告してください。

## <a name="issues-with-sas-url"></a>SAS URL の問題

SAS URL を使用してサービスに接続し、上記のエラーが発生する場合は、

* リソースの読み取りまたは一覧表示に必要なアクセス許可が URL で提供されているかを確認します。
* URL の有効期限が切れていないかを確認します。
* SAS URL がアクセス ポリシーに基づいている場合は、このアクセス ポリシーが失効されていないかを確認します。

無効な SAS URL を使用して誤ってアタッチし、デタッチできない場合は、次の手順を実行します。

1. Storage Explorer を実行している場合は、F12 キーを押して開発者ツール ウィンドウを開きます。
2. [アプリケーション] タブをクリックし、ローカル記憶域をクリックし、左側のツリーで file:// をクリックします。
3. 問題がある SAS URI のサービスの種類と関連付けられているキーを見つけます。 たとえば、BLOB コンテナーの SAS URI が不適切である場合は、`StorageExplorer_AddStorageServiceSAS_v1_blob` というキーを探します。
4. キーの値は JSON 配列にする必要があります。 不適切な URI に関連付けられたオブジェクトを見つけて削除します。
5. Ctrl キーを押しながら R キーを押して、Storage Explorer をリロードします。

## <a name="linux-dependencies"></a>Linux の依存関係

一般に、Linux で Storage Explorer を実行するには、次のパッケージが必要です。

* [.NET Core 2.0 ランタイム](https://docs.microsoft.com/dotnet/core/linux-prerequisites?tabs=netcore2x) 注: Storage Explorer バージョン 1.7.0 以前には .NET Core 2.0 が必要です。 新しいバージョンの .NET Core がインストールされている場合は、Storage Explorer に修正プログラムを適用する必要があります (下を参照)。 Storage Explorer 1.8.0 以降を実行している場合は、.NET Core 2.2 まで使用できます。 現時点で、2.2 を超えるバージョンは動作が検証されていません。
* `libgnome-keyring-common` と `libgnome-keyring-dev`
* `libgconf-2-4`

ご使用のディストリビューションによっては、他のパッケージのインストールも必要な場合があります。

Storage Explorer は、Ubuntu 18.04、16.04、および 14.04 で正式にサポートされています。 クリーンな状態のコンピューターでのインストール手順は次のとおりです。

# <a name="ubuntu-1804tab1804"></a>[Ubuntu 18.04](#tab/1804)

1. Storage Explorer のダウンロード
2. .NET Core ランタイムをインストールします。検証済みの最新バージョンは、[2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu18-04/runtime-2.0.8) です (新しいバージョンが既にインストールされている場合は、Storage Explorer の修正プログラムの適用が必要な場合があります。以下を参照してください)
3. `sudo apt-get install libgconf-2-4` を実行します。
4. `sudo apt install libgnome-keyring-common libgnome-keyring-dev` を実行します。

# <a name="ubuntu-1604tab1604"></a>[Ubuntu 16.04](#tab/1604)

1. Storage Explorer のダウンロード
2. .NET Core ランタイムをインストールします。検証済みの最新バージョンは、[2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu16-04/runtime-2.0.8) です (新しいバージョンが既にインストールされている場合は、Storage Explorer の修正プログラムの適用が必要な場合があります。以下を参照してください)
3. `sudo apt install libgnome-keyring-dev` を実行します。

# <a name="ubuntu-1404tab1404"></a>[Ubuntu 14.04](#tab/1404)

1. Storage Explorer のダウンロード
2. .NET Core ランタイムをインストールします。検証済みの最新バージョンは、[2.0.8](https://dotnet.microsoft.com/download/linux-package-manager/ubuntu14-04/runtime-2.0.8) です (新しいバージョンが既にインストールされている場合は、Storage Explorer の修正プログラムの適用が必要な場合があります。以下を参照してください)
3. `sudo apt install libgnome-keyring-dev` を実行します。

---

### <a name="patching-storage-explorer-for-newer-versions-of-net-core"></a>新しいバージョンの .NET Core への Storage Explorer 修正プログラムの適用 
2\.0 より新しいバージョンの .NET Core がインストールされていて、Storage Explorer バージョン 1.7.0 以前を実行している場合は、次の手順を実行して Storage Explorer の修正プログラムを適用する必要があります。
1. [nuget から](https://www.nuget.org/packages/StreamJsonRpc/1.5.43)バージョン 1.5.43 の StreamJsonRpc をダウンロードします。 ページの右側で "パッケージのダウンロード" リンクを探します。
2. パッケージをダウンロードした後、そのファイル拡張子を `.nupkg` から `.zip` に変更します
3. パッケージの解凍
4. `streamjsonrpc.1.5.43/lib/netstandard1.1/` に移動します
5. `StreamJsonRpc.dll` を Storage Explorer フォルダー内の次の場所にコピーします。
    1. `StorageExplorer/resources/app/ServiceHub/Services/Microsoft.Developer.IdentityService/`
    2. `StorageExplorer/resources/app/ServiceHub/Hosts/ServiceHub.Host.Core.CLR.x64/`

## <a name="open-in-explorer-from-azure-portal-doesnt-work"></a>Azure portal で [Explorer で開く] が機能しない

Azure Portal の [Explorer で開く] ボタンが機能しない場合は、互換性があるブラウザーを使用していることを確認してください。 互換性についてテスト済みのブラウザーは、次のとおりです｡
* Microsoft Edge
* Mozilla Firefox
* Google Chrome
* Microsoft Internet Explorer

## <a name="next-steps"></a>次の手順

上記の解決策がすべてうまくいかない場合は、[GitHub で問題をオープンしてください](https://github.com/Microsoft/AzureStorageExplorer/issues)。 また、左下隅にある [Report issue to GitHub (GitHub に問題をレポート)] ボタンを使用して、GitHub にすばやくアクセスすることもできます。

![フィードバック](./media/storage-explorer-troubleshooting/feedback-button.PNG)
