---
title: Azure ハイブリッド特典
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Azure SQL Database と SQL Managed Instance の割引に既存の SQL Server ライセンスを使用します。
services: sql-database
ms.service: sql-db-mi
ms.subservice: features
ms.custom: sqldbrb=4
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: sashan, moslake
ms.date: 02/16/2021
ms.openlocfilehash: f7a37e761e37e295bbb92e442b1813ebded2a7cd
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "104955280"
---
# <a name="azure-hybrid-benefit---azure-sql-database--sql-managed-instance"></a>Azure ハイブリッド特典 - Azure SQL Database と SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

仮想コアベースの購入モデルのプロビジョニングされたコンピューティング レベルでは、[Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/)を使用して、既存のライセンスを Azure SQL Database と Azure SQL Managed Instance の割引料金で交換できます。 この Azure 特典では、ソフトウェア アシュアランスを含む SQL Server ライセンスを使用して、SQL Database と SQL Managed Instance で最大 30% 以上節約できます。 [Azure ハイブリッド特典](https://azure.microsoft.com/pricing/hybrid-benefit/) ページには、割引金額を決定するための計算機能があります。  Azure ハイブリッド特典は Azure SQL Database サーバーレスには適用されないことにご注意ください。

> [!NOTE]
> Azure ハイブリッド特典に変更してもダウンタイムは必要ありません。

![仮想コアの価格体系](./media/azure-hybrid-benefit/pricing.png)

## <a name="choose-a-license-model"></a>ライセンス モデルを選択する

Azure ハイブリッド特典では、SQL Server データベース エンジン自体には既存の SQL Server ライセンスを使用して、基になる Azure インフラストラクチャに対してのみ支払うか (基本コンピューティングの価格)、または基になるインフラストラクチャと SQL Server ライセンスの両方に対して支払うか (ライセンス込みの価格) を選択できます。

ライセンス モデルは、Azure portal で選択または変更できます。 
- 新しいデータベースの場合は、作成時に **[基本]** タブの **[データベースの構成]** を選択し、コストを削減するオプションを選択します。
- 既存のデータベースの場合は、 **[設定]** メニューの **[構成]** を選択し、コストを削減するオプションを選択します。

次のいずれかの API を使用して、新規または既存のデータベースを構成することもできます。

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell を使用してライセンスの種類を設定または更新するには:

- [New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)
- [Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)
- [New-AzSqlInstance](/powershell/module/az.sql/new-azsqlinstance)
- [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI を使用してライセンスの種類を設定または更新するには:

- [az sql db create](/cli/azure/sql/db#az-sql-db-create)
- [az sql mi create](/cli/azure/sql/mi#az-sql-mi-create)
- [az sql mi update](/cli/azure/sql/mi#az-sql-mi-update)

# <a name="rest-api"></a>[REST API](#tab/rest)

REST API を使用してライセンスの種類を設定または更新するには:

- [データベース - 作成または更新](/rest/api/sql/databases/createorupdate)
- [データベース - 更新](/rest/api/sql/databases/update)
- [Managed Instances - Create Or Update](/rest/api/sql/managedinstances/createorupdate)
- [Managed Instances - Update](/rest/api/sql/managedinstances/update)

* * *


### <a name="azure-hybrid-benefit-questions"></a>Azure ハイブリッド特典に関する質問

#### <a name="are-there-dual-use-rights-with-azure-hybrid-benefit-for-sql-server"></a>SQL Server 向け Azure ハイブリッド特典では、二重使用権がサポートされていますか。

確実かつシームレスに移行を行うために、180 日間はライセンスの二重使用権があります。 この 180 日の期間が経過すると、SQL Server ライセンスは、SQL Database のクラウドでしか使用できなくなります。 オンプレミスとクラウドでの二重使用権はなくなります。

#### <a name="how-does-azure-hybrid-benefit-for-sql-server-differ-from-license-mobility"></a>SQL Server 向け Azure ハイブリッド特典とライセンス モビリティの違いは何ですか。

Microsoft では、ソフトウェア アシュアランス付きの SQL Server をご利用のお客様に対して、ライセンス モビリティの特典を提供しています。 これにより、パートナーの共有サーバーにご自分のライセンスを再割り当てすることができます。 この特典は、Azure IaaS および AWS EC2 でご利用いただけます。

SQL Server 向け Azure ハイブリッド特典は、次の 2 つの重要な点において、ライセンス モビリティとは異なります。

- 高度に仮想化されたワークロードを Azure に移動することで、コスト面でメリットがもたらされます。 SQL Server Enterprise Edition のお客様は、高度に仮想化されたアプリケーション用に、オンプレミスで所有しているすべてのコアに対し、Azure の General Purpose SKU で 4 つのコアを取得できます。 ライセンス モビリティの場合、仮想化されたワークロードをクラウドに移動しても、コスト上のメリットは特にありません。
- SQL Server と高い互換性がある、Azure (SQL Managed Instance) の PaaS 移行先 が提供されます。

#### <a name="what-are-the-specific-rights-of-the-azure-hybrid-benefit-for-sql-server"></a>SQL Server 向け Azure ハイブリッド特典では、具体的にはどのような権限が付与されますか。

SQL Database および SQL Managed Instance のお客様には、SQL Server 向け Azure ハイブリッド特典によって次の権限が付与されます。

|ライセンス フットプリント|SQL Server 向け Azure ハイブリッド特典の内容|
|---|---|
|SA を含む SQL Server Enterprise Edition の中核的なお客様|<li>Hyperscale、General Purpose または Business Critical SKU で基本料金を支払うことができる</li><br><li>オンプレミスの 1 コア = Hyperscale SKU の 4 コア</li><br><li>オンプレミスの 1 コア = General Purpose SKU の 4 コア</li><br><li>オンプレミスの 1 コア = Business Critical SKU の 1 コア</li>|
|SA を含む SQL Server Standard Edition の中核的なお客様|<li>Hyperscale、General Purpose または Business Critical SKU で基本料金を支払うことができる</li><br><li>オンプレミスの 1 コア = Hyperscale SKU の 1 コア</li><br><li>オンプレミスの 1 コア = General Purpose SKU の 1 コア</li><br><li>オンプレミスの 4 コア = Business Critical SKU の 1 コア</li>|
|||


## <a name="next-steps"></a>次のステップ

- Azure SQL デプロイ オプションの選択については、[Azure SQL での適切なデプロイ オプションの選択](azure-sql-iaas-vs-paas-what-is-overview.md)に関するページを参照してください。
- SQL Database と SQL Managed Instance の機能比較については、[SQL Database と SQL Managed Instance の機能比較](database/features-comparison.md)に関するページを参照してください。
