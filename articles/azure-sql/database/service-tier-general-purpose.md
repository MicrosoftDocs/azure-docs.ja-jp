---
title: 汎用のサービス階層
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Azure SQL Database と Azure SQL Managed Instance の General Purpose サービス レベルについて説明します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/07/2019
ms.openlocfilehash: ee218253309995e721c97f4a7f7b4547b32f7c36
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "85986643"
---
# <a name="general-purpose-service-tier---azure-sql-database-and-azure-sql-managed-instance"></a>General Purpose サービス レベル - Azure SQL Database と Azure SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

> [!NOTE]
> 仮想コアベースの購入モデルでの General Purpose サービス レベルは、DTU ベースの購入モデルでは Standard サービス レベルと呼ばれます。 仮想コアベースの購入モデルと DTU ベースの購入モデルとの比較については、[購入モデルとリソース](purchasing-models.md)に関する記事を参照してください。

Azure SQL Database と Azure SQL Managed Instance は、インフラストラクチャに障害が発生した場合でも 99.99% の可用性を確保するために、クラウド環境に適合された SQL Server データベース エンジン アーキテクチャに基づいています。 

Azure SQL Database と SQL Managed Instance では、次の 2 つのサービス レベルが使用されています。 

- General Purpose
- Business Critical

Azure SQL Database には 3 つ目のサービス レベルもありますが、現在、Azure SQL Managed Instance には使用できません。

- ハイパースケール

General Purpose サービス レベルのアーキテクチャ モデルは、コンピューティングとストレージの分離に基づきます。 このアーキテクチャ モデルでは、データベース ファイルが透過的にレプリケートされ、基盤となるインフラストラクチャの障害が発生した場合にデータ損失が発生しないことが保証されている Azure Blob Storage の高可用性と信頼性に依存しています。

次の図は、計算レイヤーとストレージ レイヤーが分離されている Standard アーキテクチャ モデルの 4 ノードを示しています。

![計算とストレージの分離](./media/service-tier-general-purpose/general-purpose-service-tier.png)

General Purpose サービス レベルのアーキテクチャ モデルには、2 つのレイヤーがあります。

- ステートレス計算レイヤー。`sqlservr.exe` プロセスを実行しており、一時的なデータとキャッシュ データのみが含まれています (プラン キャッシュ、バッファー プール、列のストア プールなど)。 このステートレス ノードは、プロセスの初期化、ノードの正常性の制御、および他の場所へのフェールオーバーを必要に応じて実行する Azure Service Fabric によって操作されます。
- ステートフル データ レイヤー。データベース ファイル (.mdf/.ldf) は Azure Blob Storage に保存されています。 Azure Blob Storage では、データベース ファイル内にあるレコードのデータが消失しないことが保証されています。 Azure Storage には、データの可用性と冗長性が組み込まれており、たとえプロセスがクラッシュしても、ログ ファイルのレコードやデータ ファイルのページがすべて確実に維持されます。

データベース エンジンまたはオペレーティング システムがアップグレードされた場合、基となるインフラストラクチャの一部で障害が発生した場合、または `sqlservr.exe` プロセスで重大な問題が検出された場合、Azure Service Fabric は、ステートレス プロセスを別のステートレス計算ノードに移行します。 フェールオーバー時間を最小限に抑えるために、プライマリ ノードのフェールオーバーが発生した場合に新しい計算サービスの実行を待機している一連のスペア ノードがあります。 Azure Storage レイヤーのデータは影響を受けず、データおよびログ ファイルは、新しく初期化されたプロセスにアタッチされます。 このプロセスは、99.99% の可用性を保証していますが、移行時間や、新しいノードの起動にコールド キャッシュを使用することが原因で、実行中の大きなワークロードに対しては、パフォーマンス上の影響が若干生じる場合があります。

## <a name="when-to-choose-this-service-tier"></a>このサービス レベルを選択する場合

General Purpose サービス レベルは、Azure SQL Database および Azure SQL Managed Instance の既定のサービス レベルであり、ほとんどの一般的なワークロード向けに設計されています。 ほとんどのケースで、Azure 仮想マシン上の SQL Server に匹敵する、ストレージの待ち時間が 5 から 10 ミリ秒で SLA が 99.99% のフル マネージド データベース エンジンが必要な場合は、General Purpose レベルをお勧めします。

## <a name="next-steps"></a>次のステップ

- [SQL Managed Instance](../managed-instance/resource-limits.md#service-tier-characteristics) の General Purpose および Standard レベル、[仮想コア モデル](resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen4)または [DTU モデル](resource-limits-dtu-single-databases.md#single-database-storage-sizes-and-compute-sizes)の単一データベース、[仮想コア モデル](resource-limits-vcore-elastic-pools.md#general-purpose---provisioned-compute---gen4)および [DTU モデル](resource-limits-dtu-elastic-pools.md#standard-elastic-pool-limits)のエラスティック プールのリソース特性 (コア数、I/O、メモリ) を確認します。
- [Business Critical](service-tier-business-critical.md) レベルと [Hyperscale](service-tier-hyperscale.md) レベルについて学びます。
- [Service Fabric](../../service-fabric/service-fabric-overview.md) について学びます。
- 高可用性およびディザスター リカバリーのためのその他のオプションについては、[ビジネス継続性](business-continuity-high-availability-disaster-recover-hadr-overview.md)に関するページを参照してください。
