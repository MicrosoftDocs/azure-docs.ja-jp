---
title: Azure SQL Database - 汎用と Business Critical | Microsoft Docs
description: この記事では、仮想コアの購入レベルにおける汎用サービス レベルと Business Critical サービス レベルについて説明します。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
manager: craigg
ms.date: 02/23/2019
ms.openlocfilehash: 067ea8eee297eb8572bd37e240b8d13afe458ca7
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59785121"
---
# <a name="azure-sql-database-service-tiers"></a>Azure SQL Database サービス レベル

Azure SQL Database は、インフラストラクチャに障害が発生した場合でも 99.99% の可用性を確保するために、クラウド環境に合わせて調整された SQL Server データベース エンジン アーキテクチャに基づいています。 Azure SQL Database で使用されているアーキテクチャ モデルは 3 つあります。

- ほとんどの一般的なワークロード向けに設計された [General Purpose](sql-database-service-tier-general-purpose.md)。
- 1 つの読み取り可能レプリカを使用する低待機時間ワークロード向けに設計された [Business Critical](sql-database-service-tier-business-critical.md)。
- 複数の読み取り可能レプリカを使用する非常に大規模なデータベース (最大 100 TB) 向けに設計された [Hyperscale](sql-database-service-tier-hyperscale.md)。

この記事では、仮想コアベースの購入モデルにおける汎用サービス レベルと Business Critical サービス レベルに対するストレージおよびバックアップに関する考慮事項について説明します。

> [!NOTE]
> 仮想コアベースの購入モデルにおけるハイパースケール サービス レベルの詳細については、[ハイパースケール サービス レベル](sql-database-service-tier-hyperscale.md)に関するページを参照してください。 仮想コアベースの購入モデルと DTU ベースの購入モデルとの比較については、[Azure SQL Database の購入モデルとリソース](sql-database-purchase-models.md)に関する記事をご覧ください。

## <a name="data-and-log-storage"></a>データとログのストレージ

以下、具体例に沿って説明します。

- 割り当てられたストレージは、データ ファイル (MDF) およびログ ファイル (LDF) によって使用されます。
- 各単一データベースのコンピューティング サイズで、最大データベース サイズがサポートされ、既定の最大サイズは 32 GB です。
- 必要な単一データベース サイズ (MDF のサイズ) を構成するとき、LDF をサポートするために、その 30% の追加ストレージが自動的に追加されます
- Managed Instance でのストレージ サイズは、32 GB の倍数で指定する必要があります。
- 10 GB と、サポートされている最大サイズの間で、任意の単一データベース サイズを選択できます
  - 標準または汎用サービス レベルのストレージの場合は、10 GB 単位でサイズを増減します
  - Premium または Business Critical レベルのストレージの場合は、250 GB 単位でサイズを増減します
- General Purpose サービス レベルでは、`tempdb` には、接続されている SSD を使用します。このストレージ コストは、仮想コア価格に含まれます。
- Business Critical サービス レベルでは、`tempdb` は、接続されている SSD を MDF ファイルおよび LDF ファイルと共有します。tempDB ストレージ コストは、仮想コア価格に含まれます。

> [!IMPORTANT]
> MDF および LDF に割り当てられている合計ストレージに対して課金されます。

MDF および LDF の現在の合計サイズを監視するには、[sp_spaceused](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-spaceused-transact-sql) を使用します。 個々の MDF ファイルおよび LDF ファイルの現在のサイズを監視するには、[sys.database_files](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-database-files-transact-sql) を使用します。

> [!IMPORTANT]
> 場合によっては、未使用領域を再利用できるようにデータベースを縮小する必要があります。 詳細については、「[Manage file space in Azure SQL Database](sql-database-file-space-management.md)」(Azure SQL Database でファイル領域を管理する) を参照してください。

## <a name="backups-and-storage"></a>バックアップとストレージ

データベース バックアップのストレージは、SQL Database のポイントインタイム リストア (PITR) および[長期リテンション期間 (LTR)](sql-database-long-term-retention.md) 機能をサポートするために割り当てられます。 このストレージはデータベースごとに別個に割り当てられ、データベース料金ごとに 2 つが個々に課金されます。

- **PITR**:個々のデータベース バックアップが、[RA-GRS ストレージ](../storage/common/storage-designing-ha-apps-with-ragrs.md)に自動的にコピーされます。 ストレージのサイズは、新しいバックアップが作成されるにつれ、動的に増加します。  このストレージは、毎週の完全バックアップ、毎日の差分バックアップ、5 分ごとにコピーされるトランザクション ログ バックアップによって使用されます。 ストレージの使用量は、データベースの変化率とリテンション期間によって異なります。 リテンション期間は、データベースごとに 7 ～ 35 日の範囲内で別々に構成できます。 データ サイズと同量の最小ストレージ容量が、追加料金なしで提供されます。 ほとんどのデータベースでは、この容量で十分に 7 日間のバックアップを格納できます。
- **LTR**:SQL Database には、最大 10 年の完全バックアップの長期リテンション期間を構成するオプションが用意されています。 LTR ポリシーが有効になっている場合、これらのバックアップは、RA-GRS ストレージに自動的に格納されますが、バックアップがコピーされる頻度は制御できます。 さまざまなコンプライアンス要件を満たすために、毎週、毎月、毎年のバックアップに対して異なったリテンション期間を選択することができます。 この構成によって、LTR バックアップに使用されるストレージ容量が定義されます。 LTR ストレージのコストは、LTR 料金計算ツールを使用して見積もることができます。 詳細については、「[長期保存](sql-database-long-term-retention.md)」をご覧ください。

## <a name="next-steps"></a>次の手順

- 汎用サービス レベルと Business Critical サービスレベルの単一データベースに対して選択できる具体的なコンピューティング サイズとストレージ サイズの詳細については、[単一データベースに対する SQL Database の仮想コアベースのリソース制限](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)に関するページを参照してください
- 汎用サービス レベルと Business Critical サービスレベルのエラスティック プールに対して選択できる具体的なコンピューティング サイズとストレージ サイズの詳細については、[エラスティック プールに対する SQL Database の仮想コアベースのリソース制限](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)に関するページを参照してください。
