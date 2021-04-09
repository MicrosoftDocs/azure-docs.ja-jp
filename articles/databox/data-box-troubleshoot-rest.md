---
title: REST インターフェイスを使用するための Azure Data Box のトラブルシューティング | Microsoft Docs
description: REST インターフェイスを介してデータをコピーした場合に Azure Data Box で見られる問題のトラブルシューティングを行う方法について説明します。
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 01/25/2021
ms.author: alkohli
ms.openlocfilehash: 17b8d6de198746a79a50c4fbda805b364212e3c4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98796052"
---
# <a name="troubleshoot-issues-related-to-azure-data-box-blob-storage"></a>Azure Data Box BLOB ストレージに関連する問題のトラブルシューティング

この記事では、データをコピーするために、Data Box の REST インターフェイスを介して Data Box Blob ストレージを使用している場合に検出される可能性のある問題のトラブルシューティングの方法について詳しく説明します。 これらの問題は、Azure Storage Explorer、AzCopy、または Python 用 Microsoft Azure Storage ライブラリなどのアプリケーションやクライアント ライブラリで Data Box Blob ストレージを使用している場合に発生します。

## <a name="errors-seen-in-azure-storage-explorer"></a>Azure Storage Explorer で見られるエラー

このセクションでは、Data Box Blob ストレージで Azure Storage Explorer を使用している場合に直面するいくつかの問題について詳しく説明します。

|エラー メッセージ  |推奨される操作 |
|---------|---------|
|Unable to retrieve child resources. (子リソースを取得できません。) The value for one of the HTTP headers is not in the correct format. (いずれかの HTTP ヘッダーの値の形式が正しくありません。)|**[編集]** メニューから **[Target Azure Stack APIs]\(Azure Stack API を対象とする\)** を選択します。 <br>Azure Storage Explorer を再起動します。|
|`getaddrinfo ENOTFOUND <accountname>.blob.<serialnumber>.microsoftdatabox.com` |エンドポイント名 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` が次の場所にあるホスト ファイルに追加されていることを確認します。 <li>`C:\Windows\System32\drivers\etc\hosts` (Windows) </li><li> `/etc/hosts` (Linux)</li>|
|Unable to retrieve child resources. (子リソースを取得できません。) <br>Details: self-signed certificate (詳細: 自己署名証明書) |次の手順に従って、ご使用のデバイス用の TLS/SSL 証明書を Azure Storage Explorer にインポートします。 <li>Azure portal から証明書をダウンロードします。 詳細については、[証明書のダウンロード](data-box-deploy-copy-data-via-rest.md#download-certificate)に関する記事を参照してください。</li><li>**[編集]** メニューから、 **[SSL 証明書]** 、 **[証明書のインポート]** の順に選択します。</li>|

## <a name="errors-seen-in-azcopy-for-windows"></a>AzCopy for Windows で見られるエラー

このセクションでは、Data Box Blob ストレージで AzCopy for Windows を使用している場合に直面するいくつかの問題について詳しく説明します。

|エラー メッセージ  |推奨される操作 |
|---------|---------|
|AzCopy コマンドが 1 分間応答を停止しているように見えた後、次のメッセージが表示されます。 <br>Failed to enumerate directory https://… (ディレクトリ https://… の列挙に失敗しました。) リモート名を解決できませんでした `<accountname>.blob.<serialnumber>.microsoftdatabox.com`|エンドポイント名 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` が `C:\Windows\System32\drivers\etc\hosts` にあるホスト ファイルに追加されていることを確認します。|
|AzCopy コマンドが 1 分間応答を停止しているように見えた後、次のメッセージが表示されます。 <br>Error parsing source location. (ソースの場所の解析中にエラーが発生しました。) 基になる接続が閉じられました。SSL/TLS のセキュリティで保護されているチャネルに対する信頼関係を確立できませんでした。|ご使用のデバイス用の TLS/SSL 証明書をシステムの証明書ストアにインポートします。 詳細については、[証明書のダウンロード](data-box-deploy-copy-data-via-rest.md#download-certificate)に関する記事を参照してください。|


## <a name="errors-seen-in-azcopy-for-linux"></a>AzCopy for Linux で見られるエラー

このセクションでは、Data Box Blob ストレージで AzCopy for Linux を使用している場合に直面するいくつかの問題について詳しく説明します。

|エラー メッセージ  |推奨される操作 |
|---------|---------|
|AzCopy コマンドが 20 分間応答を停止しているように見えた後、次のメッセージが表示されます。 <br>Error parsing source location `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`. (ソースの場所の解析中にエラーが発生しました: `https://<accountname>.blob.<serialnumber>.microsoftdatabox.com/<cntnr>`) このようなデバイスまたはアドレスはありません|エンドポイント名 `<accountname>.blob.<serialnumber>.microsoftdatabox.com` が `/etc/hosts` にあるホスト ファイルに追加されていることを確認します。|
|AzCopy コマンドが 20 分間応答を停止しているように見えた後、次のメッセージが表示されます。 <br>Error parsing source location… (ソースの場所の解析中にエラーが発生しました…) The SSL connection could not be established. (SSL 接続を確立できませんでした。)|ご使用のデバイス用の TLS/SSL 証明書をシステムの証明書ストアにインポートします。 詳細については、[証明書のダウンロード](data-box-deploy-copy-data-via-rest.md#download-certificate)に関する記事を参照してください。|

## <a name="errors-seen-in-azure-storage-library-for-python"></a>Python 用 Azure Storage ライブラリで見られるエラー

このセクションでは、データ コピーに Linux クライアントを使用して Data Box Disk をデプロイする際に直面するいくつかの重要な問題について詳しく説明します。

|エラー メッセージ  |推奨される操作 |
|---------|---------|
|The value for one of the HTTP headers is not in the correct format. (いずれかの HTTP ヘッダーの値の形式が正しくありません。) |インストールしたバージョンの Python 用 Azure Storage ライブラリが Data Box でサポートされていません。 Azure Data Box Blob ストレージの要件で、サポートされるバージョンを確認してください。|
|… [SSL:CERTIFICATE_VERIFY_FAILED] …|Python を実行する前に、REQUESTS_CA_BUNDLE 環境変数を Base64 でエンコードされた TLS 証明書ファイルのパスに設定してください ([証明書のダウンロード](data-box-deploy-copy-data-via-rest.md#download-certificate)方法を参照してください)。 <br>次に例を示します。<br>`export REQUESTS_CA_BUNDLE=/tmp/mycert.cer` <br>`python` <br>あるいは、証明書をシステムの証明書ストアに追加し、この環境変数をそのストアのパスに設定します。 <br> Ubuntu の場合の例: <br>`export REQUESTS_CA_BUNDLE=/etc/ssl/certs/ca-certificates.crt` <br>`python`|


## <a name="common-errors"></a>一般的なエラー

これらのエラーは、いずれかのアプリケーションに固有のものではありません。

|エラー メッセージ  |推奨される操作 |
|---------|---------|
|The connection times out. (接続がタイムアウトになりました。) |Data Box デバイスにサインインし、デバイスがロック解除されていることを確認します。 デバイスは再起動される度に、ユーザーがサインインするまでロックされた状態になります。|
|REST API 認証が次のエラーで失敗します。サーバーが要求を認証できませんでした。 認証ヘッダーの値が、署名を含め正しく形成されていることを確認してください。 ErrorCode:AuthenticationFailed. |これが発生する理由の 1 つは、デバイスの時刻が Azure の時刻と同期されていない場合です。 時間のずれが大きい場合、REST API 経由で Data Box にデータをコピーしようとすると、REST API 認証が中断されます。 この状況では、送信 UDP 123 ポートを開き、`time.windows.com` へのアクセスを許可することができます。 デバイスの時刻が Azure の時刻と同期されると、認証は成功するはずです。 |

## <a name="next-steps"></a>次のステップ

- [Data Box Blob ストレージのシステム要件](data-box-system-requirements-rest.md)を確認します。
