---
author: stevestein
ms.service: sql-database
ms.subservice: operations
ms.topic: include
ms.date: 08/23/2019
ms.author: sstein
ms.openlocfilehash: 3cc8e836d39a38f950e15d31aeb3e3acdca45acc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84031753"
---
## <a name="create-and-manage-azure-sql-resources-with-the-azure-portal"></a>Azure portal で Azure SQL リソースを作成して管理する

Azure portal には、SQL 仮想マシンを含む[すべての Azure SQL リソース](https://go.microsoft.com/fwlink/?linkid=2100641)を管理できる単一ページが用意されています。

**[Azure SQL]** ページにアクセスするには、Azure portal のメニューの **[Azure SQL]** を選択するか、任意のページで **[Azure SQL]** を検索して選択します。

> [!NOTE]
> **Azure SQL** からは、Azure SQL Database の単一データベースとプールされたデータベース、それらをホストする論理 SQL サーバー、SQL Managed Instances、SQL 仮想マシンなど、Azure portal であらゆる SQL リソースに簡単にアクセスするための手段が提供されます。  [Azure SQL](../azure-sql-iaas-vs-paas-what-is-overview.md) はサービスでもリソースでもなく、SQL 関連サービスの集合です。 

既存のリソースを管理するには、一覧から目的の項目を選択します。 新しい Azure SQL リソースを作成するには、 **[+ 追加]** を選択します。 

![ポータルの Azure SQL のページ](./media/sql-database-create-manage-portal/add-azure-sql-resources.png)

**[+ 追加]** を選択した後、任意のタイルで **[詳細の表示]** を選択して、さまざまなオプションに関する追加情報を表示します。

![データベース タイルの詳細](./media/sql-database-create-manage-portal/single-sql-database-deployment-options.png)

詳細については、次の情報を参照してください。

- [単一データベースを作成する](../database/single-database-create-quickstart.md)
- [エラスティック プールの作成](../database/elastic-pool-overview.md#creating-a-new-sql-database-elastic-pool-using-the-azure-portal)
- [マネージド インスタンスを作成する](../managed-instance/instance-create-quickstart.md)
- [SQL 仮想マシンを作成する](../virtual-machines/windows/sql-vm-create-portal-quickstart.md)