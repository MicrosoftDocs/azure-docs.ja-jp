---
title: Azure Cosmos DB サービス利用不可の例外をトラブルシューティングする
description: Azure Cosmos DB サービス利用不可の例外を診断して修正する方法について説明します。
author: j82w
ms.service: cosmos-db
ms.date: 08/06/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 749592b778612c6903c9c15e336de3fb00978199
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88870869"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-service-unavailable-exceptions"></a>Azure Cosmos DB サービス利用不可の例外を診断してトラブルシューティングする
SDK は、Azure Cosmos DB に接続することができませんでした。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
次の一覧には、サービス利用不可の例外の既知の原因と解決策が含まれています。

### <a name="the-required-ports-are-being-blocked"></a>必要なポートがブロックされている
[必要なすべてのポート](performance-tips-dotnet-sdk-v3-sql.md#networking)が有効になっていることを確認します。

### <a name="client-side-transient-connectivity-issues"></a>クライアント側の一時的な接続に関する問題
サービス利用不可の例外は、タイムアウトの原因となっている一時的な接続の問題がある場合に発生する可能性があります。 一般的に、このシナリオに関連するスタック トレースには、`TransportException` エラーが含まれます。 次に例を示します。

```C#
TransportException: A client transport error occurred: The request timed out while waiting for a server response. 
(Time: xxx, activity ID: xxx, error code: ReceiveTimeout [0x0010], base error: HRESULT 0x80131500
```

[要求のタイムアウトのトラブルシューティングの手順](troubleshoot-dot-net-sdk-request-timeout.md#troubleshooting-steps)に関するページに従って、問題を解決します。

### <a name="service-outage"></a>サービス停止
[[Azure の状態]](https://status.azure.com/status) を確認して、進行中の問題があるかどうかを確認します。


## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB .NET SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-dot-net-sdk.md)。
* [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) と [.NET v2](performance-tips.md) のパフォーマンス ガイドラインを確認する。