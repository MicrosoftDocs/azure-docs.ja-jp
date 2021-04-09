---
title: Java v4 SDK での Azure Cosmos DB サービス利用不可の例外をトラブルシューティングする
description: Java v4 SDK での Azure Cosmos DB サービス利用不可の例外を診断して修正する方法について説明します。
author: kushagrathapar
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 10/28/2020
ms.author: kuthapar
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 03c899307b00010ca87b279ed720b92946d3673c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340081"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-java-v4-sdk-service-unavailable-exceptions"></a>Azure Cosmos DB Java v4 SDK サービス利用不可の例外を診断してトラブルシューティングする
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Java v4 SDK は、Azure Cosmos DB に接続することができませんでした。

## <a name="troubleshooting-steps"></a>トラブルシューティングの手順
次の一覧には、サービス利用不可の例外の既知の原因と解決策が含まれています。

### <a name="the-required-ports-are-being-blocked"></a>必要なポートがブロックされている
[必要なすべてのポート](sql-sdk-connection-modes.md#service-port-ranges)が有効になっていることを確認します。

### <a name="client-side-transient-connectivity-issues"></a>クライアント側の一時的な接続に関する問題
サービス利用不可の例外は、タイムアウトの原因となっている一時的な接続の問題がある場合に発生する可能性があります。 一般的に、このシナリオに関連するスタック トレースには、`ServiceUnavailableException` エラーと診断詳細が含まれます。 次に例を示します。

```java
Exception in thread "main" ServiceUnavailableException{userAgent=azsdk-java-cosmos/4.6.0 Linux/4.15.0-1096-azure JRE/11.0.8, error=null, resourceAddress='null', requestUri='null', statusCode=503, message=Service is currently unavailable, please retry after a while. If this problem persists please contact support.: Message: "" {"diagnostics"}
```

[要求のタイムアウトのトラブルシューティングの手順](troubleshoot-request-timeout-java-sdk-v4-sql.md#troubleshooting-steps)に関するページに従って、問題を解決します。

### <a name="service-outage"></a>サービス停止
[[Azure の状態]](https://status.azure.com/status) を確認して、進行中の問題があるかどうかを確認します。


## <a name="next-steps"></a>次のステップ
* Azure Cosmos DB Java v4 SDK 使用時の問題を[診断してトラブルシューティングする](troubleshoot-java-sdk-v4-sql.md)。
* [Java v4 SDK](performance-tips-java-sdk-v4-sql.md) のパフォーマンス ガイドラインを確認する。