---
title: Storage Analytics ログを使用した待ち時間のトラブルシューティング
description: Azure Storage Analytics ログを使用して待ち時間の問題の特定とトラブルシューティングを行って、クライアント アプリケーションを最適化します。
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 1e6033f9a8f4cecd2429eca67a3d58e54d7ae1f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99221110"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>Storage Analytics ログを使用した待ち時間のトラブルシューティング

診断とトラブルシューティングは、Azure Storage を使用してクライアント アプリケーションを構築し、サポートするうえでの重要なスキルです。

Azure アプリケーションの分散型の性質により、エラーやパフォーマンスの両方の問題に対する診断とトラブルシューティングは、従来の環境で実施するよりも複雑になる場合があります。

次の手順では、Azure Storage Analytics ログを使用して待ち時間の問題の特定とトラブルシューティングを行って、クライアント アプリケーションを最適化する方法を示します。

## <a name="recommended-steps"></a>推奨される手順

1. [Storage Analytics のログ](./manage-storage-analytics-logs.md#download-storage-logging-log-data)をダウンロードします。

2. 次の PowerShell スクリプトを使用して、未加工の形式のログを表形式に変換します。

   ```Powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv “REPLACE THIS WITH FILE PATH” -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. 次に示すように、スクリプトによって GUI ウィンドウが起動され、情報を列でフィルター処理できます。

   ![Storage Analytics ログ パーサー ウィンドウ](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)
 
4. "operation-type" に基づいてログ エントリを絞り込み、問題の期間中に作成されたログ エントリを探します。

   ![operation-type のログ エントリ](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. この問題が発生した期間中では、次の値が重要になります。

   * Operation-type = GetBlob
   * request-status = SASNetworkError
   * End-to-End-Latency-In-Ms = 8453
   * Server-Latency-In-Ms = 391

   エンドツーエンドの待ち時間は、次の式を使用して計算されます。

   * エンドツーエンドの待ち時間 = サーバーの待ち時間 + クライアントの待ち時間

   ログ エントリを使用してクライアントの待ち時間を計算します。

   * クライアントの待ち時間 = エンドツーエンドの待ち時間 - サーバーの待ち時間

        例:8453 – 391 = 8062 ms

   次の表は、長い待ち時間の OperationType と RequestStatus の結果に関する情報を示しています。

   | BLOB の種類 |RequestStatus=<br>Success|RequestStatus=<br>(SAS)NetworkError|推奨|
   |---|---|---|---|
   |GetBlob|はい|いいえ|[**GetBlob 操作:** RequestStatus = Success](#getblob-operation-requeststatus--success)|
   |GetBlob|いいえ|はい|[**GetBlob 操作:** RequestStatus = (SAS)NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|はい|いいえ|[**Put 操作:** RequestStatus = Success](#put-operation-requeststatus--success)|
   |PutBlob|いいえ|はい|[**Put 操作:** RequestStatus = (SAS)NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>状態の結果

### <a name="getblob-operation-requeststatus--success"></a>GetBlob 操作:RequestStatus = Success

「推奨される手順」の手順 5 の説明に従って、次の値を確認します。

* エンドツーエンドの待ち時間
* サーバーの待ち時間
* クライアントの待ち時間

**RequestStatus = Success** の **GetBlob 操作** において **最大時間** が **クライアントの待ち時間** で費やされている場合、これは Azure Storage がクライアントにデータを書き込むために大量の時間を費やしていることを示します。 この待ち時間は、クライアント側の問題を示しています。

**推奨事項:**

* クライアントのコードを調査します。
* Wireshark、Microsoft Message Analyzer、または Tcping を使用して、クライアントからネットワーク接続の問題を調査します。 

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>GetBlob 操作:RequestStatus = (SAS)NetworkError

「推奨される手順」の手順 5 の説明に従って、次の値を確認します。

* エンドツーエンドの待ち時間
* サーバーの待ち時間
* クライアントの待ち時間

**RequestStatus = (SAS)NetworkError** の **GetBlob 操作** において **最大時間** が **クライアントの待ち時間** で費やされている場合、最も一般的な問題は、ストレージ サービスでタイムアウトが期限切れになる前にクライアントが切断されていることです。

**推奨事項:**

* クライアントのコードを調べて、Storage サービスからクライアントが切断した理由とタイミングを把握してください。
* Wireshark、Microsoft Message Analyzer、または Tcping を使用して、クライアントからネットワーク接続の問題を調査します。 

### <a name="put-operation-requeststatus--success"></a>Put 操作:RequestStatus = Success

「推奨される手順」の手順 5 の説明に従って、次の値を確認します。

* エンドツーエンドの待ち時間
* サーバーの待ち時間
* クライアントの待ち時間

**RequestStatus = Success** の **Put 操作** において **最大時間** が **クライアントの待ち時間** で費やされている場合、これはクライアントが Azure Storage にデータを送信するのに時間がかかっていることを示します。 この待ち時間は、クライアント側の問題を示しています。

**推奨事項:**

* クライアントのコードを調査します。
* Wireshark、Microsoft Message Analyzer、または Tcping を使用して、クライアントからネットワーク接続の問題を調査します。 

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Put 操作:RequestStatus = (SAS)NetworkError

「推奨される手順」の手順 5 の説明に従って、次の値を確認します。

* エンドツーエンドの待ち時間
* サーバーの待ち時間
* クライアントの待ち時間

**RequestStatus = (SAS)NetworkError** の **PutBlob 操作** において **最大時間** が **クライアントの待ち時間** で費やされている場合、最も一般的な問題は、ストレージ サービスでタイムアウトが期限切れになる前にクライアントが切断されていることです。

**推奨事項:**

* クライアントのコードを調べて、Storage サービスからクライアントが切断した理由とタイミングを把握してください。
* Wireshark、Microsoft Message Analyzer、または Tcping を使用して、クライアントからネットワーク接続の問題を調査します。