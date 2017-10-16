---
title: "Azure Storage Explorer トラブルシューティング ガイド | Microsoft Docs"
description: "Azure の 2 つのデバッグ機能の概要"
services: virtual-machines
documentationcenter: 
author: Deland-Han
manager: cshepard
editor: 
ms.assetid: 
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/08/2017
ms.author: delhan
ms.openlocfilehash: e06c73c2c00b27178f8431b83b5c5a42110b6b1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-storage-explorer-troubleshooting-guide"></a>Azure Storage Explorer トラブルシューティング ガイド

Microsoft Azure Storage Explorer (プレビュー) は、Windows、macOS、Linux で Azure Storage データを容易に操作できるスタンドアロン アプリです。 このアプリは、Azure、ソブリン クラウド、および Azure Stack でホストされているストレージ アカウントに接続できます。

このガイドでは、Storage Explorer で確認されている一般的な問題の解決方法を紹介しています。

## <a name="sign-in-issues"></a>サインインの問題

Azure Active Directory (AAD) のアカウントのみがサポートされています。 ADFS アカウントを使用する場合、Storage Explorer へのサインインが機能しないことが予想されます。 続行する前に、アプリケーションを再起動して問題が解決されるかどうかを確認してください。

### <a name="error-self-signed-certificate-in-certificate-chain"></a>エラー: 証明書チェーンの自己署名証明書

このエラーが発生する原因はいくつかありますが、最も一般的な原因は次の 2 つです。

1. アプリが “透過型プロキシ” を介して接続されています。すなわち、サーバー (会社のサーバーなど) によって HTTPS トラフィックが遮断され、暗号化が解除され、その後自己署名証明書を使用して暗号化されています。

2. 受信した HTTPS メッセージに自己署名の SSL 証明書を挿入する、ウイルス対策ソフトウェアなどのアプリケーションが実行されています。

これらの問題のいずれかが発生すると、Storage Explorer は受信された HTTPS メッセージが改ざんされているかどうかを識別できなくなります。 自己署名証明書のコピーがある場合は、Storage Explorer にそのコピーを信頼させることができます。 証明書の挿入元がわからない場合は、次の手順に従って確認します。

1. Open SSL をインストールします。

    - [Windows](https://slproweb.com/products/Win32OpenSSL.html) (Light バージョンのいずれかで十分です)

    - Mac と Linux: オペレーティング システムに付属

2. Open SSL を実行します。

    - Windows: インストール ディレクトリを開き、**/bin/** をクリックして、**openssl.exe** をダブルクリックします。
    - Mac と Linux: ターミナルで **openssl** を実行します。

3. s_client -showcerts -connect microsoft.com:443 を実行します。

4. 自己署名証明書を検索します。 どれが自己署名かわからない場合は、証明書の発行先 ("s:") と証明書の発行者 ("i:") が同じものを検索します。

5. 自己署名証明書が見つかったら、それぞれの証明書の **-----BEGIN CERTIFICATE-----** から **-----END CERTIFICATE-----** までのすべての内容をコピーして、新しい .cer ファイルに貼り付けます。

6. Storage Explorer を開き、**[編集]** > **[SSL 証明書]** > **[証明書のインポート]** の順にクリックし、ファイル ピッカーを使用して、作成した .cer ファイルを検索して選択し、開きます。

上記の手順で自己署名証明書が見つからない場合は、フィードバック ツールを使用して Microsoft にお問い合わせください。

### <a name="unable-to-retrieve-subscriptions"></a>サブスクリプションを取得できない

正常にサインインした後にサブスクリプションを取得できない場合は、次の手順に従って問題を解決します。

- Azure Portal にサインインして、お使いのアカウントにサブスクリプションへのアクセス権があることを確認します。

- 適切な環境 (Azure、Azure China、Azure Germany、Azure US Government、またはカスタム環境/Azure Stack) を使用してサインインしていることを確認します。

- プロキシの内側にいる場合は、Storage Explorer のプロキシが適切に構成されていることを確認します。

- アカウントを削除してから再度追加します。

- ルート ディレクトリ (C:\Users\ContosoUser) から次のファイルを削除して、アカウントを再度追加します。

    - .adalcache

    - .devaccounts

    - .extaccounts

- サインイン時にエラー メッセージが表示される場合は、F12 キーを押して開発者ツールのコンソールを開きます。

![開発者ツール](./media/storage-explorer-troubleshooting/4022501_en_2.png)

### <a name="unable-to-see-the-authentication-page"></a>認証ページが表示されない

認証ページが表示されない場合は、次の手順に従って問題を解決します。

- 接続速度によっては、サインイン ページの読み込みに時間がかかる場合があります。認証ダイアログ ボックスを閉じる前に少なくとも 1 分はお待ちください。

- プロキシの内側にいる場合は、Storage Explorer のプロキシが適切に構成されていることを確認します。

- F12 キーを押して開発者コンソールを表示します。 開発者コンソールからの応答を確認して、認証が機能しない原因の手がかりを見つけられるかどうかを調べます。

### <a name="cannot-remove-account"></a>アカウントを削除できない

アカウントを削除できない場合、または再認証のリンクが役に立たない場合は、次の手順に従って問題を解決します。

- ルート ディレクトリから次のファイルを削除して、アカウントを再度追加します。

    - .adalcache

    - .devaccounts

    - .extaccounts

- SAS にアタッチされた Storage リソースを削除する場合は、次のファイルを削除します。

    - %AppData%/StorageExplorer フォルダー (Windows の場合)

    - /Users/<your_name>/Library/Applicaiton SUpport/StorageExplorer (Mac の場合)

    - ~/.config/StorageExplorer (Linux の場合)

> [!NOTE]
>  これらのファイルを削除すると、すべての資格情報を再入力する必要があります。

## <a name="proxy-issues"></a>プロキシの問題

最初に、入力した次の情報がすべて正しいことを確認します。

- プロキシの URL とポート番号

- プロキシで必要な場合はユーザー名とパスワード

### <a name="common-solutions"></a>一般的な解決方法

まだ問題が発生する場合は、次の手順に従って問題を解決します。

- プロキシを使用せずにインターネットに接続できる場合は、プロキシの設定を有効にしなくても Storage Explorer が動作することを確認します。 動作する場合は、プロキシの設定に問題があると考えられます。 プロキシ管理者と連携して問題を特定してください。

- プロキシ サーバーを使用する他のアプリケーションが期待どおりに動作するかを確認します。

- Web ブラウザーを使用して Microsoft Azure Portal に接続できるかを確認します。

- サービス エンドポイントからの応答を受信できるか確認します。 ブラウザーに、エンドポイント URL のいずれかを入力します。 接続できる場合は、InvalidQueryParameterValue または同様の XML の応答を受信します。

- プロキシ サーバーで Storage Explorer を使用しているユーザーが他にもいる場合は、それらのユーザーが接続できるかを確認します。 他のユーザーが接続できる場合は、プロキシ サーバー管理者にお問い合わせください。

### <a name="tools-for-diagnosing-issues"></a>問題診断ツール

ネットワーク ツール (Windows 用 Fiddler など) をお持ちの場合は、次のように問題を診断できる場合があります。

- プロキシ経由で作業する必要がある場合は、プロキシ経由で接続するようにネットワーク ツールを構成する必要があります。

- ネットワーク ツールで使用されるポート番号を確認します。

- Storage Explorer のプロキシ設定として、ローカル ホストの URL とネットワーク ツールのポート番号を入力します。 入力が正しく行われると、Storage Explorer によって生成された、管理とサービス エンドポイントに対するネットワーク要求のログ記録が、ネットワーク ツールによって開始されます。 たとえば、BLOB エンドポイントの場合はブラウザーに「https://cawablobgrs.blob.core.windows.net/」と入力すると、リソースは存在するもののアクセスできないことを示す次のようなメッセージが表示されます。

![コード サンプル](./media/storage-explorer-troubleshooting/4022502_en_2.png)

### <a name="contact-proxy-server-admin"></a>プロキシ サーバー管理者に問い合わせる

プロキシの設定が正しい場合は、プロキシ サーバー管理者に問い合わせて、

- プロキシによって、Azure の管理またはリソース エンドポイントへのトラフィックがブロックされていないことを確認します。

- プロキシ サーバーで使用されている認証プロトコルを確認します。 Storage Explorer は、現在 NTLM プロキシをサポートしていません。

## <a name="unable-to-retrieve-children-error-message"></a>"子を取得できません" エラー メッセージ

プロキシ経由で Azure に接続している場合は、プロキシの設定が正しいことを確認します。 サブスクリプションまたはアカウント所有者からリソースへのアクセス権が付与された場合は、該当するリソースの読み取りまたは一覧表示の権限があることを確認してください。

### <a name="issues-with-sas-url"></a>SAS URL の問題
SAS URL を使用してサービスに接続し、上記のエラーが発生する場合は、

- リソースの読み取りまたは一覧表示に必要なアクセス許可が URL で提供されているかを確認します。

- URL の有効期限が切れていないかを確認します。

- SAS URL がアクセス ポリシーに基づいている場合は、このアクセス ポリシーが失効されていないかを確認します。

## <a name="next-steps"></a>次のステップ

上記の方法で問題が解決しなかった場合は、フィードバック ツールを使用してメールでお問い合わせください。その際は Azure サポートから問題解決のご連絡を差し上げられるように、問題についてできる限り詳しくご記入ください。

これを行うには、**[ヘルプ]** メニューをクリックし、**[フィードバックを送信する]** をクリックします。

![フィードバック](./media/storage-explorer-troubleshooting/4022503_en_1.png)
