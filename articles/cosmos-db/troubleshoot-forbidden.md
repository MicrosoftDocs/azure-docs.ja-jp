---
title: Azure Cosmos DB の 禁止例外をトラブルシューティングする
description: 禁止例外を診断して修正する方法について説明します。
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 0854e5d2c323df695460908c45714673756328c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99971892"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Azure Cosmos DB の 禁止例外を診断してトラブルシューティングする
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

HTTP 状態コード 403 は、要求の完了が禁止されていることを表します。

## <a name="firewall-blocking-requests"></a>要求をブロックするファイアウォール
このシナリオでは、次のようなエラーが表示されるのが一般的です。

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>解決策
現在の[ファイアウォール設定](how-to-configure-firewall.md)が正しいことと、接続しようとしている IP またはネットワークが含まれていることを確認します。
最近更新した場合は、変更が **適用されるまでに最大 15 分** かかる可能性があることに注意してください。

## <a name="next-steps"></a>次のステップ
* [IP ファイアウォール](how-to-configure-firewall.md)を構成します。
* [仮想ネットワーク](how-to-configure-vnet-service-endpoint.md)からのアクセスを構成します。
* [プライベート エンドポイント](how-to-configure-private-endpoints.md)からのアクセスを構成します。
