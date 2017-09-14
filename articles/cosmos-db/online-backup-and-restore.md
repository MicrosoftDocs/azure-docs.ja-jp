---
title: "Azure Cosmos DB でのオンライン バックアップと復元 | Microsoft Docs"
description: "Azure Cosmos DB データベースで自動バックアップと復元を実行する方法について説明します。"
keywords: "バックアップと復元、オンライン バックアップ"
services: cosmos-db
documentationcenter: 
author: RahulPrasad16
manager: jhubbard
editor: monicar
ms.assetid: 98eade4a-7ef4-4667-b167-6603ecd80b79
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/28/2017
ms.author: raprasa
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 89dc30c5475786d89554f5ec9e10e555267e6d78
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="automatic-online-backup-and-restore-with-azure-cosmos-db"></a>Azure Cosmos DB での自動オンライン バックアップと復元
Azure Cosmos DB は、すべてのデータのバックアップを、一定の間隔で自動的に取得します。 自動バックアップは、データベース操作のパフォーマンスにも可用性にも影響を与えずに取得されます。 すべてのバックアップは別のストレージ サービスに保存され、これらのバックアップは、局地的な障害の発生時に回復機能を提供するためにグローバルにレプリケートされます。 自動バックアップは、Cosmos DB コンテナーを誤って削除した後のデータの回復やディザスター リカバリー ソリューションを必要とするシナリオを想定しています。  

この記事では、データの冗長性と Cosmos DB での可用性の簡単な要約から始めた後、バックアップについて説明します。 

## <a name="high-availability-with-cosmos-db---a-recap"></a>Cosmos DB の高可用性 - まとめ
Cosmos DB は、[グローバルに分散される](distribute-data-globally.md)ように設計されています。複数の Azure リージョン間のスループットを、ポリシーに基づくフェールオーバーと透過的なマルチホーム API と共にスケーリングすることができます。 [SLA の 99.99% の可用性](https://azure.microsoft.com/support/legal/sla/cosmos-db)を提供するデータベース システムとして、Cosmos DB のすべての書き込みは、クライアントに受信確認を行う前にローカル データ センター内のレプリカのクォーラムによってローカル ディスクに永続的にコミットされます。 Cosmos DB の高可用性はローカル ストレージに依存しており、外部ストレージ テクノロジには依存していません。 さらに、データベース アカウントが 1 つ以上の Azure リージョンに関連付けられている場合、書き込みは他のリージョンにもレプリケートされます。 スループットとデータへのアクセスの待機時間が短くなるようにスケーリングするために、データベース アカウントに任意の数のリージョンを関連付けることができます。 各読み取りリージョンで、(レプリケートされた) データは、レプリカ セット間で永続化されます。  

次の図に示すように、1 つの Cosmos DB コンテナーが[水平方向にパーティション分割](partition-data.md)されます。 次の図では、“パーティション” は円で示され、各パーティションは、レプリカ セットによって高可用性を保持します。 これは、単一の Azure リージョン内でのローカルな分散です (X 軸で示されています)。 さらに、各パーティション (および対応するレプリカ セット) は、データベース アカウントに関連付けられている複数のリージョン (たとえば、この図では、米国東部、米国西部、インド中部という 3 つのリージョン) にグローバルに分散されます。 “パーティション セット” は、各リージョンのデータの複数のコピーで構成されるエンティティであり、グローバルに分散されるエンティティです (Y 軸で示されています)。 データベース アカウントに関連付けられているリージョンに優先順位を割り当てることができ、障害が発生した場合、Cosmos DB は、次のリージョンに透過的にフェールオーバーします。 アプリケーションのエンド ツー エンドの可用性をテストするために、フェールオーバーを手動でシミュレートできます。  

次の図は、Cosmos DB の高度な冗長性を示しています。

![Cosmos DB の高度な冗長性](./media/online-backup-and-restore/redundancy.png)

![Cosmos DB の高度な冗長性](./media/online-backup-and-restore/global-distribution.png)

## <a name="full-automatic-online-backups"></a>自動化されたオンラインの完全バックアップ
コンテナーまたはデータベースを削除してしまいました。 Cosmos DB では、データだけでなく、データのバックアップも、冗長性と局地的な障害からの回復力が高められています。 これらの自動化されたバックアップは、現時点では 4 時間ごとに取得され、常時最新の 2 つのバックアップが保存されます。 データが誤って削除された場合や破損した場合は、8 時間以内に [Azure サポートにお問い合わせ](https://azure.microsoft.com/support/options/)ください。 

バックアップは、データベース操作のパフォーマンスにも可用性にも影響を与えずに取得されます。 Cosmos DB は、バックアップをバックグラウンドで取得し、プロビジョニング済みの RU を消費したり、パフォーマンスに影響を与えたり、データベースの可用性に影響を与えたりすることはありません。 

Cosmos DB の中に保存されるデータとは異なり、自動バックアップは、Azure Blob Storage サービスに保存されます。 短い待機時間/効率的なアップロードを保証するために、バックアップのスナップショットは、Cosmos DB データベース アカウントの現在の書き込みリージョンと同じリージョンの Azure Blob Storage インスタンスにアップロードされます。 局地的な災害からの回復では、Azure Blob Storage のバックアップ データの各スナップショットが、地理冗長ストレージ (GRS) 経由でもう一度レプリケートされます。 次の図は、Cosmos DB コンテナー全体 (この例では米国西部の 3 つのプライマリ コレクションのすべて) が、米国西部の Azure BLOB ストレージ アカウントにバックアップされた後、米国東部に GRS 経由でレプリケートされます。 

次の図は、GRS Azure Storage 内のすべての Cosmos DB エンティティの定期的な完全バックアップを示しています。

![GRS Azure Storage 内のすべての Cosmos DB エンティティの定期的な完全バックアップ](./media/online-backup-and-restore/automatic-backup.png)

## <a name="backup-retention-period"></a>バックアップのリテンション期間
前述のように、Azure Cosmos DB では 4 時間ごとにデータのスナップショットが取得され、すべてのパーティションについて最新の 2 つのスナップショットが30 日間保持されます。 法令遵守規定に基づいて、スナップショットは 90 日後に消去されます。

独自のスナップショットを保持する場合は、Azure Cosmos DB の JSON へのエクスポート オプション ([データ移行ツール](import-data.md#export-to-json-file)) を使用して、追加のバックアップをスケジュールすることができます。 

## <a name="restoring-a-database-from-an-online-backup"></a>オンライン バックアップからのデータベースの復元
データベースまたはコレクションを誤って削除した場合は、[サポート チケットを申請する](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)か、[Azure サポートに問い合わせる](https://azure.microsoft.com/support/options/)ことで、最新の自動バックアップからデータを復元できます。 データ破損の問題が原因でデータベースを復元する必要がある場合は、「[データ破損の処理](#handling-data-corruption)」を参照し、破損したデータがバックアップに達するのを防ぐために追加の手順を実行する必要があります。 バックアップの特定のスナップショットを復元するには、復元するスナップショットのバックアップ サイクル期間中のデータが Cosmos DB から入手可能である必要があります。

## <a name="handling-data-corruption"></a>データ破損の処理
Azure Cosmos DB では、システム内のすべてのパーティションの、最新の 2 つのバックアップが保持されます。 このモデルは、コンテナー (ドキュメントのコレクション、グラフ、テーブル)、またはデータベースが誤って削除された場合に最新のいずれかのバージョンを復元できるため非常に優れています。 ただし、ユーザーがデータの破損の問題を招いた場合、Azure Cosmos DB がデータの破損を認識しないことがあるため、破損がバックアップに達している可能性があります。 破損が検出されたらすぐに破損したコンテナー (コレクション/グラフ/テーブル) を削除し、破損したデータでバックアップが上書きされないように保護してください。 最新のバックアップが 4 時間前のものである可能性があるため、[Change Feed](change-feed.md) を適用して、コンテナーを削除する前に過去 4 時間分のデータをキャプチャして保存することができます。

## <a name="next-steps"></a>次のステップ

複数のデータ センターにデータベースをレプリケートする場合は、[Cosmos DB を使用したデータのグローバル分散](distribute-data-globally.md)に関するページを参照してください。 

Azure サポートに問い合わせる場合は、 [Azure ポータルからチケットを申請します](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。


