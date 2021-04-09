---
title: Azure Cosmos DB で TTL を使ってデータを期限切れにする
description: Microsoft Azure Cosmos DB では TTL を使って、一定期間経過後にシステムからドキュメントを自動的に消去することができます。
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/04/2020
ms.reviewer: sngun
ms.openlocfilehash: cf9d0aea9ab9e79a5f184a42e1bb785b6fb870a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93360090"
---
# <a name="time-to-live-ttl-in-azure-cosmos-db"></a>Azure Cosmos DB の Time to Live (TTL)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB は、**Time to Live** (TTL) を使用して、一定の期間が経過したらアイテムをコンテナーから自動的に削除する機能を提供します。 既定では、コンテナー レベルで Time to Live を設定し、項目ごとに値をオーバーライドできます。 コンテナー レベルまたは項目レベルで TTL を設定すると、項目が最後に変更されてからその期間が経過した後で、Azure Cosmos DB によってそれらの項目が自動的に削除されます。 Time to Live 値は秒数で構成します。 TTL を構成すると、TTL 値に基づいてシステムが自動的に期限切れアイテムを削除します。クライアント アプリケーションが明示的に発行する削除操作は必要ありません。 TTL の最大値は 2147483647 です。

期限切れアイテムの削除は、残っている[要求ユニット](request-units.md)、つまりユーザー要求によってまだ消費されていない要求ユニットを消費するバックグラウンド タスクです。 TTL の期限が切れた後であっても、要求のためにコンテナーが過負荷の状態で、使用できる RU が十分にない場合、データの削除は遅延します。 削除操作を実行するのに十分な RU が利用可能になると、データは削除されます。 たとえデータの削除が遅延しても、TTL の期限が切れた後は、どんなクエリによっても (どんな API によっても) データが返されることはありません。

> このコンテンツは Azure Cosmos DB トランザクション ストア TTL に関連付けられています。 [Azure Synapse Link](./synapse-link.md) 経由で NoETL HTAP シナリオを可能にする分析ストア TTL をお探しの場合、[こちら](./analytical-store-introduction.md#analytical-ttl)をクリックしてください。

## <a name="time-to-live-for-containers-and-items"></a>コンテナーおよび項目の Time to Live

Time to Live 値は秒数で設定され、項目が最後に変更された時間との差分として解釈されます。 コンテナーまたはコンテナー内の項目に対して Time to Live を設定できます。

1. **コンテナーの Time to Live** (`DefaultTimeToLive` を使用して設定):

   - 設定されていない (または null に設定されている) 場合、項目の期限が自動的に切れることはありません。

   - 値が存在し "-1" (無限) に設定されている場合、既定で項目は期限切れになりません。

   - 値が存在し、値が "*ゼロ以外の*" いずれかの数 ( *"n"* ) に設定されている場合、項目は最終変更時刻から *"n"* 秒後に期限切れになります。

2. **項目の Time to Live** (`ttl` を使用):

   - このプロパティを適用できるのは、`DefaultTimeToLive` が存在し、親コンテナーに対して null に設定されていない場合のみです。

   - 存在する場合、これが親コンテナーの `DefaultTimeToLive` 値をオーバーライドします。

## <a name="time-to-live-configurations"></a>Time to Live の構成

- TTL がコンテナーで *"n"* に設定されている場合、そのコンテナー内の項目は *n* 秒後に期限切れになります。  同じコンテナー内に独自の Time to Live が -1 (期限切れしないことを示す) に設定されている項目がある場合、または一部の項目の Time to Live 設定が異なる数値でオーバーライドされた場合、これらの項目はそれぞれに構成されている TTL 値に基づいて期限切れになります。

- TTL がコンテナーに設定されていない場合、そのコンテナー内の項目の Time to Live は無効になります。

- コンテナーの TTL が -1 に設定されている場合、そのコンテナー内にある Time to Live が n に設定されている項目は n 秒後に期限切れになり、その他の項目は期限切れになりません。

## <a name="examples"></a>例

このセクションでは、異なる Time to Live 値がコンテナーおよびアイテムに割り当てられたいくつかの例を示します。

### <a name="example-1"></a>例 1

コンテナーの TTL を null に設定 (DefaultTimeToLive = null)

|項目の TTL| 結果|
|---|---|
|ttl = null|TTL は無効です。 項目は無期限に設定されます (既定)。|
|ttl = -1|TTL は無効です。 項目は無期限に設定されます。|
|ttl = 2000|TTL は無効です。 項目は無期限に設定されます。|

### <a name="example-2"></a>例 2

コンテナーの TTL を -1 に設定 (DefaultTimeToLive = -1)

|項目の TTL| 結果|
|---|---|
|ttl = null|TTL は有効です。 項目は無期限に設定されます (既定)。|
|ttl = -1|TTL は有効です。 項目は無期限に設定されます。|
|ttl = 2000|TTL は有効です。 項目は、2000 秒後に期限切れになります。|

### <a name="example-3"></a>例 3

コンテナーの TTL を 1000 に設定 (DefaultTimeToLive = 1000)

|項目の TTL| 結果|
|---|---|
|ttl = null|TTL は有効です。 項目は、1,000 秒後に期限切れになります (既定)。|
|ttl = -1|TTL は有効です。 項目は無期限に設定されます。|
|ttl = 2000|TTL は有効です。 項目は、2000 秒後に期限切れになります。|

## <a name="next-steps"></a>次のステップ

Time to Live を構成する方法について次の記事をご覧ください。

- [Time to Live の構成](how-to-time-to-live.md)