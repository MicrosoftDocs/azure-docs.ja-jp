---
title: VNet とファイアウォールの背後にあるストレージ アカウントに対する監査
description: 仮想ネットワークとファイアウォールの背後にあるストレージ アカウントにデータベース イベントを書き込むように監査を構成する
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.topic: how-to
author: DavidTrigano
ms.author: datrigan
ms.reviewer: vanto
ms.date: 06/17/2020
ms.custom: azure-synapse
ms.openlocfilehash: 908c9f1d05c83eaa58f77b79a32d956898c35076
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93348255"
---
# <a name="write-audit-to-a-storage-account-behind-vnet-and-firewall"></a>VNet とファイアウォールの背後にあるストレージ アカウントに対して監査を書き込む
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


[Azure SQL Database](sql-database-paas-overview.md) および [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) の監査では、仮想ネットワークとファイアウォールの背後にある [Azure ストレージ アカウント](../../storage/common/storage-account-overview.md)への、データベース イベントの書き込みがサポートされています。

この記事では、このオプション向けに Azure SQL Database と Azure ストレージ アカウントを構成する 2 つの方法について説明します。 最初のものは Azure portal を使用し、2 番目のものは REST を使用します。

## <a name="background"></a>バックグラウンド

[Azure Virtual Network (VNet)](../../virtual-network/virtual-networks-overview.md) は、Azure 内のプライベート ネットワークの基本的な構成要素です。 VNet により、Azure Virtual Machines (VM) などのさまざまな種類の Azure リソースが、他の Azure リソース、インターネット、およびオンプレミスのネットワークと安全に通信することができます。 VNet は、自社のデータ センターにおける従来のネットワークと似ていますが、スケール、可用性、分離性など、Azure インフラストラクチャのさらなる利点を提供します。

VNet の概念やベスト プラクティスなどの詳細については、「[Azure Virtual Network とは](../../virtual-network/virtual-networks-overview.md)」を参照してください。

仮想ネットワークの作成方法の詳細については、「[クイック スタート:Azure portal を使用した仮想ネットワークの作成](../../virtual-network/quick-create-portal.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

監査により VNet またはファイアウォールの背後にあるストレージ アカウントに書き込むためには、次の前提条件が満たされている必要があります。

> [!div class="checklist"]
>
> * 汎用 v2 ストレージ アカウント。 汎用 v1 または BLOB ストレージ アカウントを使用している場合は、[汎用 v2 ストレージ アカウントにアップグレードします](../../storage/common/storage-account-upgrade.md)。 詳細については、「[ストレージ アカウントの種類](../../storage/common/storage-account-overview.md#types-of-storage-accounts)」を参照してください。
> * ストレージ アカウントは、[論理 SQL サーバー](logical-servers.md)と同じサブスクリプションで、かつ同じ場所にある必要があります。
> * Azure ストレージ アカウントには `Allow trusted Microsoft services to access this storage account` が必要です。 これをストレージ アカウントの **ファイアウォールと仮想ネットワーク** に設定します。
> * 選択したストレージ アカウントに対する `Microsoft.Authorization/roleAssignments/write` アクセス許可が必要です。 詳細については、[Azure の組み込みロール](../../role-based-access-control/built-in-roles.md)に関するページを参照してください。

## <a name="configure-in-azure-portal"></a>Azure Portal での構成

お使いのサブスクリプションで、[Azure portal](https://portal.azure.com) に接続します。 リソース グループとサーバーに移動します。

1. [セキュリティ] の見出しの下にある **[監査]** をクリックします。 **[ON]** を選択します。

2. **[ストレージ]** を選択します。 ログを保存するストレージ アカウントを選択します。 ストレージ アカウントは、[前提条件](#prerequisites)に記載されている要件を満たしている必要があります。

3. **[容量の詳細]** を開きます

  > [!NOTE]
  > 選択したストレージ アカウントが VNet の背後にある場合は、次のメッセージが表示されます。
  >
  >`You have selected a storage account that is behind a firewall or in a virtual network. Using this storage requires to enable 'Allow trusted Microsoft services to access this storage account' on the storage account and creates a server managed identity with 'storage blob data contributor' RBAC.`
  >
  >このメッセージが表示されない場合、ストレージ アカウントは VNet の背後にはありません。

4. 保持期間の日数を選択します。 次に、 **[OK]** をクリックします 保持期間よりも古いログは削除されます。

5. 監査設定で **[保存]** を選択します。

VNet またはファイアウォールの背後にあるストレージ アカウントに書き込むように監査を構成できました。

## <a name="configure-with-rest-commands"></a>REST コマンドによる構成

Azure portal を使用する代わりに REST コマンドを使用して、VNet とファイアウォールの背後にあるストレージ アカウントにデータベース イベントを書き込むように監査を構成することもできます。

このセクションのサンプル スクリプトでは、スクリプトを実行する前にそれを更新する必要があります。 スクリプト内の次の値を置き換えます。

|値の例|サンプルの説明|
|:-----|:-----|
|`<subscriptionId>`| Azure サブスクリプション ID|
|`<resource group>`| Resource group|
|`<logical SQL Server>`| サーバー名|
|`<administrator login>`| [Administrator account] (管理者アカウント) |
|`<complex password>`| 管理者アカウント用の複雑なパスワード|

VNet またはファイアウォールの背後にあるストレージ アカウントにイベントを書き込むように SQL 監査を構成するには、次のようにします。

1. Azure Active Directory (Azure AD) に、サーバーを登録します。 PowerShell または REST API を使用します。

   **PowerShell**

   ```powershell
   Connect-AzAccount
   Select-AzSubscription -SubscriptionId <subscriptionId>
   Set-AzSqlServer -ResourceGroupName <your resource group> -ServerName <azure server name> -AssignIdentity
   ```

   [**REST API**](/rest/api/sql/servers/createorupdate):

   要求のサンプル

   ```html
   PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>?api-version=2015-05-01-preview
   ```

   要求本文

   ```json
   {
   "identity": {
              "type": "SystemAssigned",
              },
   "properties": {
     "fullyQualifiedDomainName": "<azure server name>.database.windows.net",
     "administratorLogin": "<administrator login>",
     "administratorLoginPassword": "<complex password>",
     "version": "12.0",
     "state": "Ready"
     }
   }
   ```

2. [Azure Portal](https://portal.azure.com) を開きます。 ストレージ アカウントに移動します。 **[アクセス制御 (IAM)]** を見つけて、 **[ロールの割り当ての追加]** をクリックします。 先ほどの手順で Azure Active Directory (Azure AD) に登録したデータベースをホストするサーバーに、**ストレージ BLOB データ共同作成者** Azure ロールを割り当てます。

   > [!NOTE]
   > 所有者特権を持つメンバーのみが、この手順を実行できます。 さまざまな Azure の組み込みロールについては、「[Azure 組み込みロール](../../role-based-access-control/built-in-roles.md)」を参照してください。

3. *storageAccountAccessKey* を指定せずに、[サーバーの BLOB 監査ポリシー](/rest/api/sql/server%20auditing%20settings/createorupdate)を構成します。

   要求のサンプル

   ```html
     PUT https://management.azure.com/subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Sql/servers/<azure server name>/auditingSettings/default?api-version=2017-03-01-preview
   ```

   要求本文

   ```json
   {
     "properties": {
      "state": "Enabled",
      "storageEndpoint": "https://<storage account>.blob.core.windows.net"
     }
   }
   ```

## <a name="using-azure-powershell"></a>Azure PowerShell の使用

- [データベース監査ポリシーを作成または更新する (Set-AzSqlDatabaseAudit)](/powershell/module/az.sql/set-azsqldatabaseaudit)
- [サーバー監査ポリシーを作成または更新する (Set-AzSqlServerAudit)](/powershell/module/az.sql/set-azsqlserveraudit)

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの使用

次の例に示すように、[Azure Resource Manager](../../azure-resource-manager/management/overview.md) テンプレートを使用して、仮想ネットワークとファイアウォールの背後にあるストレージ アカウントにデータベース イベントを書き込むように監査を構成できます。

> [!IMPORTANT]
> 仮想ネットワークとファイアウォールの背後でストレージ アカウントを使用するには、**isStorageBehindVnet** パラメーターを true に設定する必要があります。

- [BLOB ストレージに監査ログを書き込むように監査機能を有効にした Azure SQL Server をデプロイする](https://azure.microsoft.com/resources/templates/201-sql-auditing-server-policy-to-blob-storage)

> [!NOTE]
> リンクされたサンプルは、外部の公開リポジトリにあり、保証なしに "手を加えず" に提供され、Microsoft サポート プログラム/サービスのサポート対象ではありません。

## <a name="next-steps"></a>次のステップ

* [PowerShell を使用して、仮想ネットワーク サービス エンドポイントと、Azure SQL Database の仮想ネットワーク規則を作成する。](scripts/vnet-service-endpoint-rule-powershell-create.md)
* [仮想ネットワーク規則:REST API を使用した操作](/rest/api/sql/virtualnetworkrules)
* [サーバーの仮想ネットワーク サービス エンドポイントとルールを使用する](vnet-service-endpoint-rule-overview.md)
