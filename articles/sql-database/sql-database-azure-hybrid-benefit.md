---
title: Azure ハイブリッド特典
description: 既存の SQL Server ライセンスを SQL Database の割引に使用します。
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake, carlrab
ms.date: 11/13/2019
ms.openlocfilehash: 394c674da63bbda643246c3d61fb670d6ac8d1f7
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/08/2019
ms.locfileid: "74928619"
---
# <a name="azure-hybrid-benefit"></a>Azure ハイブリッド特典

仮想コアベースの購入モデルのプロビジョニングされたコンピューティング レベルでは、[SQL Server 向けの Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を使用して、既存のライセンスを SQL Database の割引料金で交換できます。 この Azure 特典では、ソフトウェア アシュアランスを含むオンプレミスの SQL Server ライセンスを使用して、Azure SQL Database について最大 30 % 節約できます。 

> [!NOTE]
> Azure ハイブリッド特典に変更してもダウンタイムは必要ありません。

![価格](./media/sql-database-service-tiers/pricing.png)

## <a name="choose-a-license-model"></a>ライセンス モデルを選択する

Azure ハイブリッド特典では、SQL データベース エンジン自体には既存の SQL Server ライセンスを使用して、基になる Azure インフラストラクチャに対してのみ支払うか (基本コンピューティングの価格)、または基になるインフラストラクチャと SQL Server ライセンスの両方に対して支払うか (ライセンス込みの価格) を選択できます。

Azure Portal または次のいずれかの API を使用して、ライセンス モデルを選択または変更できます。

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してライセンスの種類を設定または更新するには:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してライセンスの種類を設定または更新するには:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql db update](/cli/azure/sql/db#az-sql-db-update)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-apitabrest"></a>[REST API](#tab/rest)

REST API を使用してライセンスの種類を設定または更新するには:

- [データベース - 作成または更新](/rest/api/sql/databases/createorupdate)
- [データベース - 更新](/rest/api/sql/databases/update)
- [Managed Instances - Create Or Update](/rest/api/sql/managedinstances/createorupdate)
- [Managed Instances - Update](/rest/api/sql/managedinstances/update)

* * *

## <a name="next-steps"></a>次の手順

- SQL Database デプロイ オプションの選択については、「[Azure SQL で適切なデプロイ オプションを選択する](sql-database-paas-vs-sql-server-iaas.md)」を参照してください。
- SQL Database 機能の比較については、「[Azure SQL Database の機能](sql-database-features.md)」を参照してください。
