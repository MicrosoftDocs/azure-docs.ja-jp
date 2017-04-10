---
title: "DocumentDB での自動オンライン バックアップと復元 |Microsoft Docs"
description: "NoSQL データベースの自動バックアップと復元を Azure DocumentDB で実行する方法について説明します。"
keywords: "バックアップと復元、オンライン バックアップ"
services: documentdb
documentationcenter: 
author: RahulPrasad16
manager: jhubbard
editor: monicar
ms.assetid: 98eade4a-7ef4-4667-b167-6603ecd80b79
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/06/2017
ms.author: raprasa
translationtype: Human Translation
ms.sourcegitcommit: b5419efbaf51476cfc662c8aa814001e2757b4b7
ms.openlocfilehash: db7b24c049153b6622f50fd9934611d48c98a1e8


---
# <a name="automatic-online-backup-and-restore-with-documentdb"></a>DocumentDB での自動オンライン バックアップと復元
Azure DocumentDB は、すべてのデータのバックアップを、一定の間隔で自動的に取得します。 自動バックアップは、NoSQL データベース操作のパフォーマンスにも可用性にも影響を与えずに取得されます。 すべてのバックアップは別のストレージ サービスに保存され、これらのバックアップは、局地的な障害の発生時に回復機能を提供するためにグローバルにレプリケートされます。 自動バックアップは、DocumentDB コレクションを誤って削除した後のデータの回復や障害復旧ソリューションを必要とするシナリオを想定しています。  

この記事では、データの冗長性と DocumentDB での可用性の簡単な要約から始めた後、バックアップについて説明します。 

## <a name="high-availability-with-documentdb---a-recap"></a>DocumentDB の高可用性 - まとめ
DocumentDB は、[グローバルに分散される](documentdb-distribute-data-globally.md)ように設計されています。複数の Azure リージョン間のスループットを、ポリシーに基づくフェールオーバーと透過的なマルチホーム API と共にスケーリングすることができます。 [SLA の&99;.99% の可用性](https://azure.microsoft.com/support/legal/sla/documentdb/v1_0/)を提供するデータベース システムとして、DocumentDB のすべての書き込みは、クライアントに受信確認を行う前にローカル データ センター内のレプリカのクォーラムによってローカル ディスクに永続的にコミットされます。 DocumentDB の高可用性はローカル ストレージに依存しており、外部ストレージ テクノロジには依存していません。 さらに、データベース アカウントが&1; つ以上の Azure リージョンに関連付けられている場合、書き込みは他のリージョンにもレプリケートされます。 スループットとデータへのアクセスの待機時間が短くなるようにスケーリングするために、データベース アカウントに任意の数のリージョンを関連付けることができます。 各読み取りリージョンで、(レプリケートされた) データは、レプリカ セット間で永続化されます。  

次の図に示すように、1 つの DocumentDB コレクションが [水平方向にパーティション分割](documentdb-partition-data.md)されます。 次の図では、“パーティション” は円で示され、各パーティションは、レプリカ セットによって高可用性を保持します。 これは、単一の Azure リージョン内でのローカルな分散です (X 軸で示されています)。 さらに、各パーティション (および対応するレプリカ セット) は、データベース アカウントに関連付けられている複数のリージョン (たとえば、この図では、米国東部、米国西部、インド中部という&3; つのリージョン) にグローバルに分散されます。 “パーティション セット” は、各リージョンのデータの複数のコピーで構成されるエンティティであり、グローバルに分散されるエンティティです (Y 軸で示されています)。 データベース アカウントに関連付けられているリージョンに優先順位を割り当てることができ、障害が発生した場合、DocumentDB は、次のリージョンに透過的にフェールオーバーします。 アプリケーションのエンド ツー エンドの可用性をテストするために、フェールオーバーを手動でシミュレートできます。  

次の図は、DocumentDB の高度な冗長性を示しています。

![DocumentDB の高度な冗長性](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-redundancy.png)

![DocumentDB の高度な冗長性](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-global-distribution.png)

## <a name="full-automatic-online-backups"></a>自動化されたオンラインの完全バックアップ
コレクションまたはデータベースを削除してしまいました。 DocumentDB では、データだけでなく、データのバックアップも、冗長性と局地的な障害からの回復力が高められています。 これらの自動化されたバックアップは、現時点では 4 時間ごとに取得され、常時最新の 2 つのバックアップが保存されます。 データが誤って削除された場合や破損した場合は、8 時間以内に [Azure サポートにお問い合わせ](https://azure.microsoft.com/support/options/)ください。 

バックアップは、データベース操作のパフォーマンスにも可用性にも影響を与えずに取得されます。 DocumentDB は、バックアップをバックグラウンドで取得し、プロビジョニング済みの RU を消費したり、パフォーマンスに影響を与えたり、NoSQL データベースの可用性に影響を与えたりすることはありません。 

DocumentDB の中に保存されるデータとは異なり、自動バックアップは、Azure Blob Storage サービスに保存されます。 短い待機時間/効率的なアップロードを保証するために、バックアップのスナップショットは、DocumentDB データベース アカウントの現在の書き込みリージョンと同じリージョンの Azure Blob Storage インスタンスにアップロードされます。 局地的な災害からの回復では、Azure Blob Storage のバックアップ データの各スナップショットが、地理冗長ストレージ (GRS) 経由でもう一度レプリケートされます。 次の図は、DocumentDB コレクション全体 (この例では米国西部の&3; つのプライマリ コレクションのすべて) が、米国西部の Azure Blob Storage アカウントにバックアップされた後、米国東部に GRS 経由でレプリケートされます。 

次の図は、GRS Azure Storage 内のすべての DocumentDB エンティティの定期的な完全バックアップを示しています。

![GRS Azure Storage 内のすべての DocumentDB エンティティの定期的な完全バックアップ](./media/documentdb-online-backup-and-restore/azure-documentdb-nosql-database-automatic-backup.png)

## <a name="retention-period-for-a-given-snapshot"></a>特定のスナップショットの保有期間
前述のように、データのスナップショットは定期的に取得され、最新のスナップショットは、法令遵守規定に基づいて 90 日間保持された後、最終的に消去されます。 コレクションまたはアカウントが削除された場合、DocumentDB は、最後のバックアップを 90 日間保存します。

## <a name="restore-database-from-the-online-backup"></a>オンライン バックアップからデータベースを復元する
データを誤って削除した場合は、[サポート チケットを申請する](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)か、[Azure サポートに問い合わせる](https://azure.microsoft.com/support/options/)ことで、最後の自動バックアップからデータを復元できます。 バックアップの特定のスナップショットを復元するには、少なくとも復元するスナップショットのバックアップ サイクル期間中のデータが DocumentDB から入手可能である必要があります。

## <a name="next-steps"></a>次のステップ
複数のデータ センターに NoSQL データベースをレプリケートする場合は、「 [DocumentDB を使用したデータのグローバル分散](documentdb-distribute-data-globally.md)」を参照します。 

Azure サポートに問い合わせる場合は、 [Azure ポータルからチケットを申請します](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)。




<!--HONumber=Feb17_HO1-->


