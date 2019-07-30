---
title: General Purpose サービス レベル - Azure SQL Database | Microsoft Docs
description: Azure SQL Database General Purpose レベルについて説明します
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: ef64fa1285efc2c271d8d7f37bdecabd67242cc0
ms.sourcegitcommit: de47a27defce58b10ef998e8991a2294175d2098
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/15/2019
ms.locfileid: "67874971"
---
# <a name="general-purpose-service-tier---azure-sql-database"></a>General Purpose サービス レベル - Azure SQL Database

> [!NOTE]
> 仮想コアベースの購入モデルでの General Purpose サービス レベルは、DTU ベースの購入モデルでは Standard サービス レベルと呼ばれます。 仮想コアベースの購入モデルと DTU ベースの購入モデルとの比較については、[Azure SQL Database の購入モデルとリソース](sql-database-purchase-models.md)に関する記事をご覧ください。

は、インフラストラクチャに障害が発生した場合でも 99.99% の可用性を確保するために、クラウド環境に適合された SQL Server データベース エンジン アーキテクチャに基づいています。 Azure SQL Database では 3 つのサービス レベルが使用されており、それぞれアーキテクチャ モデルが異なります。 これらのサービス レベルは次のとおりです。

- 汎用
- Business Critical
- ハイパースケール

General Purpose サービス レベルのアーキテクチャ モデルは、コンピューティングとストレージの分離に基づきます。 このアーキテクチャ モデルでは、データベース ファイルが透過的にレプリケートされ、基盤となるインフラストラクチャの障害が発生した場合にデータ損失が発生しないことが保証されている Azure Blob Storage の高可用性と信頼性に依存しています。

次の図は、計算レイヤーとストレージ レイヤーが分離されている Standard アーキテクチャ モデルの 4 ノードを示しています。

![計算とストレージの分離](media/sql-database-managed-instance/general-purpose-service-tier.png)

General Purpose サービス レベルのアーキテクチャ モデルには、2 つのレイヤーがあります。

- ステートレス計算レイヤー。`sqlserver.exe` プロセスを実行しており、一時的なデータとキャッシュ データのみが含まれています (プラン キャッシュ、バッファー プール、列のストア プールなど)。 このステートレス SQL Server ノードは、プロセスの初期化、ノードの正常性の制御、および他の場所へのフェールオーバーを必要に応じて実行する Azure Service Fabric によって操作されます。
- ステートフル データ レイヤー。データベース ファイル (.mdf/.ldf) は Azure Blob Storage に保存されています。 Azure Blob Storage では、データベース ファイル内にあるレコードのデータが消失しないことが保証されています。 Azure Storage には、データの可用性と冗長性が組み込まれています。そのため、たとえ SQL Server プロセスがクラッシュしても、ログ ファイルのレコードやデータ ファイルのページはすべて維持されます。

データベース エンジンまたはオペレーティング システムがアップグレードされた場合、基となるインフラストラクチャの一部で障害が発生した場合、または SQL Server プロセスで重大な問題が検出された場合、Azure Service Fabric は、ステートレス SQL Server プロセスを別のステートレス計算ノードに移行します。 フェールオーバー時間を最小限に抑えるために、プライマリ ノードのフェールオーバーが発生した場合に新しい計算サービスの実行を待機している一連のスペア ノードがあります。 Azure Storage レイヤーのデータは影響を受けず、データ/ログ ファイルは、新しく初期化された SQL Server プロセスにアタッチされます。 このプロセスは、99.99% の可用性を保証していますが、移行時間や、新しい SQL Server ノードの起動にコールド キャッシュを使用することが原因で、実行中の大きなワークロードに対しては、パフォーマンス上の影響が若干生じる場合があります。

## <a name="when-to-choose-this-service-tier"></a>このサービス レベルを選択する場合

General Purpose サービス レベルは、ほとんどの一般的なワークロード向けに設計されている Azure SQL Database の既定のサービス レベルです。 ほとんどのケースで、Azure SQL IaaS に匹敵する、ストレージの待ち時間が 5 から 10 ミリ秒で SLA が 99.99% のフル マネージド データベース エンジンが必要な場合は、General Purpose レベルをお勧めします。

## <a name="next-steps"></a>次の手順

- [Managed Instance](sql-database-managed-instance-resource-limits.md#service-tier-characteristics) の General Purpose/Standard レベル、[仮想コア モデル](sql-database-vcore-resource-limits-single-databases.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)または [DTU モデル](sql-database-dtu-resource-limits-single-databases.md#single-database-storage-sizes-and-compute-sizes)の単一データベース、[仮想コア モデル](sql-database-vcore-resource-limits-elastic-pools.md#general-purpose-service-tier-storage-sizes-and-compute-sizes)および [DTU モデル](sql-database-dtu-resource-limits-elastic-pools.md#standard-elastic-pool-limits)のエラスティック プールのリソース特性 (コア数、IO、メモリ) を確認します。
- [Business Critical](sql-database-service-tier-business-critical.md) レベルと [Hyperscale](sql-database-service-tier-hyperscale.md) レベルについて学びます。
- [Service Fabric](../service-fabric/service-fabric-overview.md) について学びます。
- 高可用性およびディザスター リカバリーのためのその他のオプションについては、[ビジネス継続性](sql-database-business-continuity.md)に関するページを参照してください。
