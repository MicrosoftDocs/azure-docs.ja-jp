---
title: 用語集
titleSuffix: Azure SQL
description: Azure SQL Database、Azure SQL Managed Instance、Azure VM 上の SQL で使用する用語集です。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: sqldbrb=4
ms.devlang: ''
ms.topic: reference
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/09/2020
ms.openlocfilehash: 8bd1e312463cf89ae30d54bc4a32a497fc7a9a78
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98249654"
---
# <a name="azure-sql-database-glossary-of-terms"></a>Azure SQL Database 用語集
[!INCLUDE[appliesto-asf](includes/appliesto-asf.md)]

## <a name="azure-sql-database"></a>Azure SQL データベース

|Context|期間|詳細情報|
|:---|:---|:---|
|Azure サービス|Azure SQL Database または SQL Database|[Azure SQL Database](database/sql-database-paas-overview.md)|
|購入モデル|DTU ベースの購入モデル|[DTU ベースの購入モデル](database/service-tiers-dtu.md)|
||仮想コアベースの購入モデル|[仮想コアベースの購入モデル](database/service-tiers-vcore.md)|
|デプロイ オプション |単一データベース|[単一データベース](database/single-database-overview.md)|
||エラスティック プール|[エラスティック プール](database/elastic-pool-overview.md)|
|サービス階層|Basic、Standard、Premium、General Purpose、Hyperscale、Business Critical|仮想コア モデルのサービス レベルについては、「[SQL Database サービス レベル](database/service-tiers-vcore.md#service-tiers)」を参照してください。 DTU モデルのサービス階層については、[DTU モデル](database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers)に関するページを参照してください。|
|コンピューティング レベル|サーバーレス コンピューティング|[サーバーレス コンピューティング](database/service-tiers-vcore.md#compute-tiers)
||プロビジョニング済みコンピューティング|[プロビジョニング済みコンピューティング](database/service-tiers-vcore.md#compute-tiers)
|コンピューティング世代|Gen5、M シリーズ、Fsv2 シリーズ、DC シリーズ|[ハードウェアの世代](database/service-tiers-vcore.md#hardware-generations)
|サーバー エンティティ| サーバー |[論理 SQL サーバー](database/logical-servers.md)|
|リソースの種類|仮想コア|単一データベース、エラスティック プール用のコンピューティング リソースに提供される CPU コア。 |
||コンピューティング サイズとストレージ容量|コンピューティング サイズとは、単一データベースまたはエラスティック プールで使用できる CPU、メモリ、およびその他の非ストレージ関連のリソースの最大量です。  ストレージ サイズとは、単一データベースまたはエラスティック プールに使用可能なストレージの量です。 仮想コア モデルのサイズ設定オプションについては、[仮想コア単一データベース](database/resource-limits-vcore-single-databases.md)と[仮想コア エラスティック プール](database/resource-limits-vcore-elastic-pools.md)に関するページを参照してください。  (../managed-instance/resource-limits.md)。  DTU モデルのサイズ設定オプションについては、[DTU 単一データベース](database/resource-limits-dtu-single-databases.md)と [DTU エラスティック プール](database/resource-limits-dtu-elastic-pools.md)に関するページを参照してください。

## <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance

|Context|期間|詳細情報|
|:---|:---|:---|
|Azure サービス|Azure SQL Managed Instance|[SQL Managed Instance](managed-instance/sql-managed-instance-paas-overview.md)|
|購入モデル|仮想コアベースの購入モデル|[仮想コアベースの購入モデル](database/service-tiers-vcore.md)|
|デプロイ オプション |単一インスタンス|[単一インスタンス](managed-instance/sql-managed-instance-paas-overview.md)|
||インスタンス プール (プレビュー)|[インスタンス プール](managed-instance/instance-pools-overview.md)|
|サービス階層|General Purpose、Business Critical|[SQL Managed Instance のサービス レベル](managed-instance/sql-managed-instance-paas-overview.md#service-tiers)|
|コンピューティング レベル|プロビジョニング済みコンピューティング|[プロビジョニング済みコンピューティング](database/service-tiers-vcore.md#compute-tiers)|
|コンピューティング世代|第 5 世代|[ハードウェアの世代](database/service-tiers-vcore.md#hardware-generations)
|サーバー エンティティ|Managed Instance またはインスタンス| SQL Managed Instance はそれ自体がサーバーであるため、該当なし |
|リソースの種類|仮想コア|SQL Managed Instance 用のコンピューティング リソースに提供される CPU コア。|
||コンピューティング サイズとストレージ容量|コンピューティング サイズとは、SQL Managed Instance 用の CPU、メモリ、およびその他の非ストレージ関連のリソースの最大量です。  ストレージ サイズとは、SQL Managed Instance で使用可能なストレージの量です。  サイズ設定オプションについては、[SQL Managed Instance](managed-instance/resource-limits.md) に関するページを参照してください。 |
