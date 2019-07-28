---
title: インクルード ファイル
description: インクルード ファイル
services: storage
author: markmcgeeataquent
ms.service: storage
ms.topic: include
ms.date: 04/06/2018
ms.author: mimig
ms.custom: include file
ms.openlocfilehash: 93bb0a853199b8b1fc38b8cc903af0a434cab034
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/18/2019
ms.locfileid: "67181714"
---
現在 Azure Table Storage を使用している場合、Azure Cosmos DB Table API に移行することにより次のメリットが得られます。

| | Azure Table Storage | Azure Cosmos DB Table API |
| --- | --- | --- |
| Latency | 高速だが、待ち時間の上限はなし。 | 読み取りと書き込みの待ち時間は数ミリ秒であり、世界中のどこでもあらゆるスケールで 99 パーセンタイルの 10 ミリ秒未満の読み取り待ち時間と 15 ミリ秒未満の書き込み待ち時間でサポート。 |
| スループット | 可変スループット モデル。 テーブルには、20,000 操作/秒のスケーラビリティの制限あり。 | SLA によって保証された、[テーブルごとの専用の予約済みスループット](../articles/cosmos-db/request-units.md)を備えた高いスケーラビリティ。 アカウントにはスループットの上限がなく、テーブルあたり 10,000, 000 操作/秒以上に対応。 |
| グローバル配信 | 高可用性のために 1 つの読み取り可能なセカンダリ読み取りリージョンをオプションで備えた単一リージョン。 フェールオーバーを開始できない。 | 1 から 30 超のリージョンにわたる[ターンキー グローバル分散](../articles/cosmos-db/distribute-data-globally.md)。 いつでも、世界中どこにでも、[自動フェールオーバーと手動フェールオーバー](../articles/cosmos-db/high-availability.md)を実行可能。 |
| インデックス作成 | PartitionKey と RowKey のプライマリ インデックスのみ。 セカンダリ インデックスなし。 | すべてのプロパティに対する自動および完全なインデックス作成。インデックス管理なし。 |
| クエリ | クエリの実行では、プライマリ キーにインデックスを使用し、それ以外の場合はスキャンする。 | クエリは、クエリ時間の短縮のためにプロパティの自動インデックス作成を利用できる。 |
| 整合性 | プライマリ リージョン内では厳密な整合性。 セカンダリ リージョン内では最終的な整合性。 | アプリケーションのニーズに基づいて、可用性、待ち時間、スループット、および整合性のトレードオフを行う[明確に定義された 5 つの整合性レベル](../articles/cosmos-db/consistency-levels.md)。 |
| 価格 | ストレージ最適化。 | スループット最適化。 |
| SLA | 99.99% の可用性。 | すべての単一リージョンのアカウントとすべてのマルチリージョンのアカウントに対して緩やかな整合性で 99.99% の可用性 SLA、すべてのマルチリージョンのデータベース アカウントに 99.999% の読み取り可用性、一般公開に対する[業界トップレベルの包括的な SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/)。 |
