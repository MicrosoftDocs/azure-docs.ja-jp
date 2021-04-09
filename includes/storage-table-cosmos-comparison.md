---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: MarkMcGeeAtAquent
ms.service: storage
ms.topic: include
ms.date: 01/08/2021
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: a7e34f077ce1b2541168df40f2806fdb24a63a79
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98050759"
---
現在 Azure Table Storage を使用している場合、Azure Cosmos DB Table API に移行することにより次のメリットが得られます。

|特徴量 | Azure Table Storage | Azure Cosmos DB Table API |
| --- | --- | --- |
| Latency | 高速だが、待ち時間の上限はなし。 | 読み取りと書き込みの待ち時間は数ミリ秒であり、世界中のどこでもあらゆるスケールで 99 パーセンタイルの 10 ミリ秒未満の読み取り待ち時間と 15 ミリ秒未満の書き込み待ち時間でサポート。 |
| スループット | 可変スループット モデル。 テーブルには、20,000 操作/秒のスケーラビリティの制限あり。 | SLA によって保証された、[テーブルごとの専用の予約済みスループット](../articles/cosmos-db/request-units.md)を備えた高いスケーラビリティ。 アカウントにはスループットの上限がなく、テーブルあたり 10,000, 000 操作/秒以上に対応 (プロビジョニングされたスループット モード)。 |
| グローバル配信 | 高可用性のために 1 つの読み取り可能なセカンダリ読み取りリージョンをオプションで備えた単一リージョン。 フェールオーバーを開始できない。 | 1 から 30 超のリージョンにわたる[ターンキー グローバル分散](../articles/cosmos-db/distribute-data-globally.md)。 いつでも、世界中どこにでも、[自動フェールオーバーと手動フェールオーバー](../articles/cosmos-db/high-availability.md)を実行可能。 |
| インデックス作成 | PartitionKey と RowKey のプライマリ インデックスのみ。 セカンダリ インデックスなし。 | すべてのプロパティに対する自動および完全なインデックス作成。インデックス管理なし。 |
| クエリ | クエリの実行では、プライマリ キーにインデックスを使用し、それ以外の場合はスキャンする。 | クエリは、クエリ時間の短縮のためにプロパティの自動インデックス作成を利用できる。 |
| 一貫性 | プライマリ リージョン内では厳密な整合性。 セカンダリ リージョン内では最終的な整合性。 | アプリケーションのニーズに基づいて、可用性、待ち時間、スループット、および整合性のトレードオフを行う[明確に定義された 5 つの整合性レベル](../articles/cosmos-db/consistency-levels.md)。 |
| 価格 | 使用量ベース。 | [使用量ベース](../articles/cosmos-db/serverless.md)と[プロビジョニング済みの容量](../articles/cosmos-db/set-throughput.md)の両方のモードで使用できます。 |
| SLA | 99.99% の可用性。 | すべての単一リージョンのアカウントとすべてのマルチリージョンのアカウントに対して緩やかな整合性で 99.99% の可用性 SLA、すべてのマルチリージョンのデータベース アカウントに 99.999% の読み取り可用性、一般公開に対する[業界トップレベルの包括的な SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)。 |
