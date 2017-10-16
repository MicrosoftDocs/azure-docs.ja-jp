---
title: "キー値ストアとしての Azure Cosmos DB - コストの概要 | Microsoft Docs"
description: "Azure Cosmos DB をキー値ストアとして使用する場合のコストの低さについて説明します。"
keywords: "キー値ストア"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
tags: 
documentationcenter: 
ms.assetid: 7f765c17-8549-4509-9475-46394fc3a218
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/28/2017
ms.author: mimig
ms.openlocfilehash: 33eef1b51a5ee00b0fa67096030ed9ce92cf768e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cosmos-db-as-a-key-value-store--cost-overview"></a>キー値ストアとしての Azure Cosmos DB - コストの概要

Azure Cosmos DB は、高可用性で大規模なアプリケーションを簡単に構築するための、マルチモデルなグローバル分散データベース サービスです。 既定では、Azure Cosmos DB が取り込むすべてのデータのインデックスが効率よく自動作成されます。 そのため、どのようの種類のデータであっても、高速かつ一貫性のある[SQL](documentdb-sql-query.md) (および [JavaScript](programming.md)) クエリが実行されます。 

この記事では、キー/値ストアとして Azure Cosmos DB を使用し、シンプルな書き込みと読み取り操作を行う際のコストについて説明しています。 書き込み操作には、ドキュメントの挿入、置換、削除、および upsert などがあります。 Azure Cosmos DB は 99.99% という可用性の高さに加えて、99 パーセンタイルで、読み取り時の待機時間は 10 ms 未満、書き込み (インデックス設定済みの場合) 時の待機時間は 15 ms 未満を実現します。 

## <a name="why-we-use-request-units-rus"></a>要求ユニット (RU) を使用する理由

Azure Cosmos DB のパフォーマンスは、パーティションにプロビジョニングされた[要求ユニット](request-units.md) (RU) の量に基づいています。 このプロビジョニングの粒度は 1 秒で、RU/秒と RU/分の単位で購入 ([時間単位での課金と混同しないでください](https://azure.microsoft.com/pricing/details/cosmos-db/))。 RU は、アプリケーションに必要とされるスループットのプロビジョニングを簡略化するための通貨と考えるべきです。 お客様は読み取り容量ユニットと書き込み容量ユニットの間の違いを考慮する必要はありません。 RU の単一通貨モデルにより、プロビジョニングされた容量を読み取りと書き込みの間で共有する際の効率が上がります。 容量がプロビジョニングされるというモデルによって、予測可能で一貫性のあるスループットをサービスが提供でき、待機時間が短くなり、可用性が高くなります。 最後に、RU を使用してスループットをモデル化しながらも、プロビジョニングされる各 RU には定義された量のリソース (メモリ、コア) があります。 RU/秒は、単なる IOPS ではありません。

グローバル分散データベース システムである Cosmos DB は、高可用性に加えて、待機時間、スループット、整合性に関する SLA を提供している唯一の Azure サービスです。 お客様がプロビジョニングするスループットは、ご利用の Cosmos DB データベース アカウントに関連付けられている各リージョンに適用されます。 読み取りに関しては、Cosmos DB では明確に定義された複数の[整合性レベル](consistency-levels.md)が用意されており、その中から選択できます。 

次の表では、ドキュメント サイズ (1 KB と 100 KB) に基づいて、読み取りおよび書き込みトランザクションを実行するのに必要な RU 数を示しています。

|アイテムのサイズ|読み取り 1 回|書き込み 1 回|
|-------------|------|-------|
|1 KB|1 RU|5 RU|
|100 KB|10 RU|50 RU|

## <a name="cost-of-reads-and-writes"></a>読み取りと書き込みのコスト

1,000 RU/秒をプロビジョニングする場合は、360 万 RU/時間となり、その 1 時間のコストは 0.08 ドルになります (米国とヨーロッパの場合)。 サイズが 1 KB のドキュメントでは、プロビジョニングしたスループットを使用して、360 万回の読み取りまたは 72 万回の書き込み (360 万 RU / 5) が可能です。 100 万回の読み取りおよび書き込みに当てはめると、コストは 0.022 ドル/読み取り 100 万回 (0.08 ドル/ 3.6)、0.111 ドル/書き込み 100 万回 (0.08 ドル/ 0.72) となります。 下記の表にあるように、100 万回あたりのコストが最低となります。

|アイテムのサイズ|読み取り 100 万回|書き込み 100 万回|
|-------------|-------|--------|
|1 KB|0.022 ドル|0.111 ドル|
|100 KB|0.222 ドル|1.111 ドル|


基本的な BLOB またはオブジェクト ストア サービスの多くでは、読み取りトランザクション 100 万回につき 0.40 ドル、書き込みトランザクション 100 万回につき 5 ドルが課金されます。 最適な方法で使用した場合は、Cosmos DB では他のソリューションと比べて最大で 98% のコストを削減できます (1 KB のトランザクションの場合)。

## <a name="next-steps"></a>次のステップ

Azure Cosmos DB のリソース プロビジョニングの最適化に関する新しい記事をお見逃しなく。 Microsoft の [RU 計算ツール](https://www.documentdb.com/capacityplanner)を自由にご利用ください。

