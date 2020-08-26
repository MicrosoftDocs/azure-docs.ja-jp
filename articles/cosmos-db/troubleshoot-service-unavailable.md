---
title: Azure Cosmos DB サービス利用不可の例外をトラブルシューティングする
description: Cosmos DB サービス利用不可の例外を診断して修正する方法
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b9c24ee3b94be86ccf9d27b928c42fc194800a3b
ms.sourcegitcommit: 25bb515efe62bfb8a8377293b56c3163f46122bf
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87987377"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable"></a>Azure Cosmos DB サービス利用不可の例外を診断してトラブルシューティングする
SDK は Azure Cosmos DB サービスに接続できませんでした。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
次の一覧には、サービス利用不可の例外の既知の原因と解決策が含まれています。

### <a name="1-the-required-ports-are-being-blocked"></a>1.必要なポートがブロックされている
[必要なすべてのポート](performance-tips-dotnet-sdk-v3-sql.md#networking)が有効になっていることを確認します。

### <a name="2-client-side-transient-connectivity-issues"></a>2.クライアント側の一時的な接続に関する問題
サービス利用不可の例外は、タイムアウトの原因となっている一時的な接続の問題がある場合に発生する可能性があります。 一般的に、このシナリオに関連するスタック トレースには `TransportException` が含まれています。次に例を示します。

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

[要求のタイムアウトのトラブルシューティング](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps)に関するページに従って、問題を解決します。

### <a name="3-service-outage"></a>3.サービス停止
[[Azure Status]\(Azure の状態\)](https://status.azure.com/status) を確認して、進行中の問題があるか確認します。


## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK の使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)
* [.NET V3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET V2](performance-tips.md) のパフォーマンス ガイドラインについて学習する