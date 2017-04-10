---
title: "Azure リソースを新しいサブスクリプションまたはリソース グループに移動する | Microsoft Docs"
description: "Azure Resource Manager を使用して、リソースを新しいリソース グループまたはサブスクリプションに移動します。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 892d24199be5065ee54d46863cca2fd958db3236
ms.lasthandoff: 03/30/2017


---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>新しいリソース グループまたはサブスクリプションへのリソースの移動
このトピックでは、リソースを新しいサブスクリプションまたは同じサブスクリプション内の新しいリソース グループに移動する方法について説明します。 リソースの移動には、ポータル、PowerShell、Azure CLI、または REST API を使用できます。 このトピックの移動操作は、Azure サポートの支援を受けなくても利用できます。

リソースを移動する場合は、その操作の間、ソース グループとターゲット グループの両方がロックされます。 これらのリソース グループに対する書き込み操作および削除操作は、移動が完了するまでブロックされます。 このロックはリソース グループでリソースを追加、更新、削除できなくなることを意味しますが、リソースが停止されるわけではありません。 たとえば、SQL Server とそのデータベースを新しいリソース グループに移動する場合、そのデータベースを使用するアプリケーションにダウンタイムは発生しません。 これまでどおり、データベースの読み取りと書き込みを行うことができます。 

リソースの場所を変更することはできません。 リソースを移動しても、新しいリソース グループに移動されるだけです。 新しいリソース グループは別の場所に存在する場合もありますが、リソース自体の場所は変更されません。

> [!NOTE]
> この記事では、既存の Azure アカウント プラン内のリソースを移動する方法について説明します。 実際に Azure アカウント プランを変更 (従量課金制から前払いにアップグレードするなど) しながら既存のリソースの処理を継続する場合は、 [別の Azure サブスクリプション プランへの切り替え](../billing/billing-how-to-switch-azure-offer.md)に関するページをご覧ください。 
> 
> 

## <a name="checklist-before-moving-resources"></a>リソースの移動前のチェック リスト
リソースを移動する前に実行すべき重要な手順がいくつかあります。 これらの条件を確認することにより、エラーの発生を回避できます。

1. 移動元と移動先のサブスクリプションが同じ [Active Directory テナント](../active-directory/active-directory-howto-tenant.md)に存在している必要があります。 両方のサブスクリプションに同じテナント ID があることを確認するには、Azure PowerShell または Azure CLI を使用します。

  Azure PowerShell では、次を使用します。

  ```powershell
  (Get-AzureRmSubscription -SubscriptionName "Example Subscription").TenantId
  ```

  Azure CLI 2.0 では、次を使用します。

  ```azurecli
  az account show --subscription "Example Subscription" --query tenantId
  ```

  移動元と移動先のサブスクリプションのテナント ID が同じでない場合は、サブスクリプションのディレクトリの変更を試みることができます。 ただし、このオプションは、(組織アカウントではなく) Microsoft アカウントでログインしているサービス管理者のみが使用できます。 ディレクトリの変更を試みるには、[クラシック ポータル](https://manage.windowsazure.com/)にログインし、**[設定]** を選択して、サブスクリプションを選択します。 **[ディレクトリの編集]** アイコンを使用できる場合は、そのアイコンを選択して、関連付けられている Active Directory を変更します。 

  ![ディレクトリを編集する](./media/resource-group-move-resources/edit-directory.png) 

  このアイコンが使用できない場合は、サポートに連絡して、リソースを新しいテナントに移動する必要があります。

2. サービスでリソースの移動機能を有効にする必要があります。 このトピックで、リソースの移動を有効にするサービスと、リソースの移動を有効にしないサービスを示します。
3. 移動するリソースのリソース プロバイダーについて、移動先のサブスクリプションに登録する必要があります。 登録しないと、 **リソースの種類についてサブスクリプションへの登録が行われていない**ことを示すエラーが発生します。 この問題は、リソースを新しいサブスクリプションに移動するが、そのサブスクリプションがそのリソースの種類で使用されたことがない場合に発生する可能性があります。 登録ステータスを確認し、リソース プロバイダーを登録する方法については、「 [リソース プロバイダーと種類](resource-manager-supported-services.md#resource-providers-and-types)」を参照してください。

## <a name="when-to-call-support"></a>サポートに問い合わせる場合
このトピックで説明するセルフサービス操作を使用すれば、ほとんどのリソースを移動できます。 次の場合にセルフサービス操作を使用します。

* Resource Manager のリソースを移動する。
* [クラシック デプロイメントの制限事項](#classic-deployment-limitations)に従って、クラシック リソースを移動する。 

次の場合はサポートに問い合わせる必要があります。

* リソースを新しい Azure アカウント (および Active Directory テナント) に移動する。
* クラシック リソースを移動するときに制限事項に関連する問題が発生した。

## <a name="services-that-enable-move"></a>移動が可能なサービス
現在、新しいリソース グループへの移動と新しいサブスクリプションへの移動の両方が可能なサービスは、次のとおりです。

* API Management
* App Service アプリ (Web Apps) - 「 [App Service の制限事項](#app-service-limitations)
* Automation
* Batch

* Bing Maps
* CDN
* Cloud Services - 「 [クラシック デプロイメントの制限事項](#classic-deployment-limitations)
* Cognitive Services
* Content Moderator
* Data Catalog
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* DocumentDB
* Event Hubs
* HDInsight クラスター - 「[HDInsight の制限事項](#hdinsight-limitations)」を参照
* IoT Hub
* Key Vault 
* ロード バランサー
* Logic Apps
* Machine Learning
* Media Services
* Mobile Engagement
* Notification Hubs
* Operational Insights
* Operations Management
* Power BI
* Redis Cache
* Scheduler
* Search
* Server Management
* Service Bus
* Service Fabric
* Storage
* Storage (クラシック) - 「 [クラシック デプロイメントの制限事項](#classic-deployment-limitations)
* Stream Analytics
* SQL Database サーバー - データベースとサーバーは同じリソース グループ内に存在する必要があります。 SQL Server を移動すると、そのデータベースもすべて移動されます。
* Traffic Manager
* Virtual Machines - 証明書が Key Vault に格納されている場合、新しいサブスクリプションへの移動はサポートされません
* Virtual Machines (クラシック) - 「 [クラシック デプロイメントの制限事項](#classic-deployment-limitations)
* Virtual Network - 現在、ピアリングされた Virtual Network は、VNet ピアリングを無効にするまで移動することはできません。 無効にすれば、Virtual Network を正常に移動し、VNet ピアリングを有効にできるようになります。
* VPN Gateway 

 
## <a name="services-that-do-not-enable-move"></a>移動が不可能なサービス
現在、リソースの移動が不可能なサービスは、次のとおりです。

* AD Hybrid Health Service
* Application Gateway
* Application Insights
* BizTalk Services
* Container Service
* ExpressRoute
* DevTest ラボ - 同じサブスクリプション内の新しいリソース グループへの移動が有効になっています。ただし、サブスクリプション間の移動は有効になっていません。
* Dynamics LCS
* Recovery Services コンテナー - Recovery Services コンテナーに関連付けられているコンピューティング リソース、ネットワーク リソース、ストレージ リソースも移動できません。「[Recovery Services の制限事項](#recovery-services-limitations)」をご覧ください。
* セキュリティ
* 証明書が Key Vault に格納されている Virtual Machines
* Managed Disks を使用する Virtual Machines
* Managed Disks を使用する Virtual Machines の可用性セット
* Managed Disks を使用する Virtual Machines スケール セット
* Managed Disks
* Managed Disks から作成されたイメージ
* Managed Disks から作成されたスナップショット
* Virtual Machines スケール セット
* Virtual Networks (クラシック) - 「 [クラシック デプロイメントの制限事項](#classic-deployment-limitations)
* Marketplace のリソースから作成された Virtual Machines は、サブスクリプション間で移動できません。 現在のサブスクリプションでリソースをプロビジョニング解除し、新しいサブスクリプションにデプロイし直す必要があります。

## <a name="app-service-limitations"></a>App Service の制限事項
App Service アプリを使用している場合、App Service プランのみを移動することはできません。 App Service アプリを移動するには、次のオプションがあります。

* App Service プランとそのリソース グループ内の他のすべての App Service リソースを、まだ App Service リソースが含まれていない新しいリソース グループに移動する。 この要件により、App Service プランに関連付けられていない App Service リソースも移動する必要があります。 
* アプリを別のリソース グループに移動し、元のリソース グループにも App Service プランをすべて保持する。

元のリソース グループに Application Insights のリソースも含まれている場合、現在 Application Insights では移動操作を実行できないため、このリソースを移動できません。 App Service アプリを移動する際に Application Insights のリソースも含めると、移動操作自体が失敗します。 ただし、アプリが正常に動作するために、Application Insights と App Service プランがそのアプリと同じリソース グループ内に存在する必要はありません。

たとえば、リソース グループに次のものが含まれているとします。

* **plan-a** と **app-insights-a** に関連付けられた **web-a**
* **plan-b** と **app-insights-b** に関連付けられた **web-b**

オプションは次のとおりです。

* **web-a**、**plan-a**、**web-b**、**plan-b** を移動する
* **web-a** と **web-b** を移動する
* **web-a**
* **web-b**

これ以外のすべての組み合わせでは、移動できないリソースの種類 (Application Insights) の移動、または App Service プランの移動時に残しておくことができないリソースの種類 (すべての種類の App Service リソース) の保持のどちらかが行われます。

Web アプリがその App Service プランとは異なるリソース グループに存在するが、その両方を新しいリソース グループに移動する場合、移動を 2 段階で行う必要があります。 次に例を示します。

* **web-group** に存在する **web-a**
* **plan-group** に存在する **plan-a**
* **web-a** と **plan-a** を **combined-group** に配置しようとしている

この移動を実行するには、次の順序で 2 つの移動操作を個別に実行します。

1. **web-a** を **plan-group** に移動します
2. **web-a** と **plan-a** を **combined-group** に移動します

App Service 証明書は、新しいリソース グループまたはサブスクリプションに問題なく移動できます。 ただし、お使いのアプリに、外部から購入してアップロードした SSL 証明書が含まれている場合は、Web アプリを移動する前に証明書を削除する必要があります。 たとえば、次の手順を実行できます。

1. アップロードした証明書を Web アプリから削除します
2. Web アプリを移動します
3. Web アプリに証明書をアップロードします

## <a name="recovery-services-limitations"></a>Recovery Services の制限事項
Azure Site Recovery では、障害復旧の設定に使用されるストレージ リソース、ネットワーク リソース、またはコンピューティング リソースは移動できません。 

たとえば、ストレージ アカウント (Storage1) へのオンプレミス コンピューターのレプリケーションが設定済みで、Azure へのフェールオーバー後、保護されたコンピューターを、Azure 仮想ネットワーク (Network1) に接続された仮想マシン (VM1) として使用する必要があるとします。 こうした Azure リソース、つまり Storage1、VM1、および Network1 はどれも、同じサブスクリプション内のリソース グループ間、またはサブスクリプション間で移動することはできません。

## <a name="hdinsight-limitations"></a>HDInsight の制限事項

HDInsight クラスターは、新しいサブスクリプションまたはリソース グループに移動できます。 ただし、HDInsight クラスターにリンクされているネットワーク リソース (仮想ネットワーク、NIC、ロード バランサーなど) をサブスクリプション間で移動することはできません。 また、クラスターの仮想マシンに接続されている NIC を新しいリソース グループに移動することはできません。

HDInsight クラスターを新しいサブスクリプションに移動するときは、まず、他のリソース (ストレージ アカウントなど) を移動します。 その後、HDInsight クラスターを単独で移動します。

## <a name="classic-deployment-limitations"></a>クラシック デプロイメントの制限事項
クラシック モデルを使用してデプロイされるリソースを移動するためのオプションは、リソースをサブスクリプション内で移動するか、新しいサブスクリプションに移動するかによって異なります。 

### <a name="same-subscription"></a>同じサブスクリプション
リソースを同じサブスクリプション内のリソース グループ間で移動する場合は、次の制限が適用されます。

* Virtual Networks (クラシック) を移動することはできません。
* Virtual Machines (クラシック) はクラウド サービスで移動する必要があります。 
* クラウド サービスは、移動にその仮想マシンがすべて含まれている場合にのみ移動できます。
* 一度に移動できるクラウド サービスは 1 つだけです。
* 一度に移動できるストレージ アカウント (クラシック) は 1 つだけです。
* ストレージ アカウント (クラシック) は、仮想マシンまたはクラウド サービスと同じ操作では移動できません。

クラシック リソースを同じサブスクリプション内の新しリソース グループに移動する場合は、[ポータル](#use-portal)、[Azure PowerShell](#use-powershell)、[Azure CLI](#use-azure-cli)、または [REST API](#use-rest-api) の標準の移動操作を使用します。 Resource Manager のリソースの移動に使用した方法と同じ操作を使用します。

### <a name="new-subscription"></a>新しいサブスクリプション
リソースを新しいサブスクリプションに移動する場合は、次の制限が適用されます。

* サブスクリプション内のすべてのクラシック リソースは、同じ操作で移動する必要があります。
* 対象のサブスクリプションには、他のクラシック リソースは含めないでください。
* クラシック リソースの場合、移動は、別の REST API を通じてのみ要求できます。 標準の Resource Manager の移動コマンドは、クラシック リソースを新しいサブスクリプションに移動する場合は機能しません。

クラシック リソースを新しいサブスクリプションに移動する場合は、ポータルまたはクラシック リソース固有の REST 操作のいずれかを使用する必要があります。 ポータルを使用したクラシック リソースの移動については、「[Use portal (ポータルの使用)](#use-portal)」を参照してください。 REST を使用するには、次の手順を実行します。

1. 移動元のサブスクリプションがサブスクリプション間の移動に参加できることを確認します。 次の操作を行います。

  ```HTTP   
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```
   
     要求本文は次のようになります。

  ```json 
  {
    "role": "source"
  }
  ```
  
     検証操作の応答は次のような形式になります。

  ```json 
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. 移動先のサブスクリプションがサブスクリプション間の移動に参加できることを確認します。 次の操作を行います。

  ```HTTP 
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     要求本文は次のようになります。

  ```json 
  {
    "role": "target"
  }
  ```
   
     応答は移動元のサブスクリプションの検証と同じ形式になります。
3. 両方のサブスクリプションが検証に合格し、すべてのクラシック リソースをあるサブスクリプションから別のサブスクリプションに移動する場合は、次の操作を行います。

  ```HTTP 
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    要求本文は次のようになります。

  ```json 
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

この操作には数分かかる場合があります。 

## <a name="use-portal"></a>ポータルの使用
リソースを移動するには、そのリソースが含まれるリソース グループを選択し、**[移動]** を選択します。

![リソースの移動](./media/resource-group-move-resources/select-move.png)

リソースを新しいリソース グループに移動するか新しいサブスクリプションに移動するかを選択します。

移動するリソースを、移動先のリソース グループを選択します。 そのリソースのスクリプトを更新する必要があること確認し、 **[OK]**を選択します。 前の手順でサブスクリプションの編集アイコンを選択した場合は、移動先のサブスクリプションも選択する必要があります。

![移動先の選択](./media/resource-group-move-resources/select-destination.png)

**[通知]**に、移動操作が実行されていることが表示されます。

![移動の状態の表示](./media/resource-group-move-resources/show-status.png)

完了すると、結果が表示されます。

![移動の結果の表示](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>PowerShell の使用
既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、`Move-AzureRmResource` コマンドを実行します。

最初の例では、1 つのリソースを新しいリソース グループに移動する方法を示します。

```powershell
$resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId
```

2 番目の例では、複数のリソースを新しいリソース グループに移動する方法を示します。

```powershell
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

新しいサブスクリプションに移動する場合は、`DestinationSubscriptionId` パラメーターの値を含めます。

指定したリソースの移動を確認するように求められます。

```powershell
Confirm
Are you sure you want to move these resources to the resource group
'/subscriptions/{guid}/resourceGroups/newRG' the resources:

/subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/serverFarms/exampleplan
/subscriptions/{guid}/resourceGroups/destinationgroup/providers/Microsoft.Web/sites/examplesite
[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): y
```

## <a name="use-azure-cli-20"></a>Azure CLI 2.0 の使用
既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、`az resource move` コマンドを使用します。 移動するリソースのリソース ID を指定します。 リソース ID は次のコマンドを使用して取得できます。

```azurecli
az resource show -g sourceGroup -n storagedemo --resource-type "Microsoft.Storage/storageAccounts" --query id
```

次の例は、ストレージ アカウントを新しいリソース グループに移動する方法を示しています。 `--ids` パラメーターには、移動するリソース ID のスペース区切りリストを指定します。

```azurecli
az resource move --destination-group newgroup --ids "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo"
```

新しいサブスクリプションに移動するには、`--destination-subscription-id` パラメーターを指定します。

## <a name="use-azure-cli-10"></a>Azure CLI 1.0 の使用
既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、`azure resource move` コマンドを使用します。 移動するリソースのリソース ID を指定します。 リソース ID は次のコマンドを使用して取得できます。

```azurecli
azure resource list -g sourceGroup --json
```

次の形式が返されます。

```azurecli
[
  {
    "id": "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo",
    "name": "storagedemo",
    "type": "Microsoft.Storage/storageAccounts",
    "location": "southcentralus",
    "tags": {},
    "kind": "Storage",
    "sku": {
      "name": "Standard_RAGRS",
      "tier": "Standard"
    }
  }
]
```

次の例は、ストレージ アカウントを新しいリソース グループに移動する方法を示しています。 `-i` パラメーターには、移動するリソース ID のコンマ区切りリストを指定します。

```azurecli
azure resource move -i "/subscriptions/{guid}/resourceGroups/sourceGroup/providers/Microsoft.Storage/storageAccounts/storagedemo" -d "destinationGroup"
```

指定したリソースの移動を確認するように求められたら、

## <a name="use-rest-api"></a>REST API を使用する
既存のリソースを別のリソース グループまたはサブスクリプションに移動するには、次のコマンドを実行します。

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 
```

要求の本文で、ターゲット リソース グループと、移動するリソースを指定します。 REST による移動操作の詳細については、「 [リソースの移動](https://msdn.microsoft.com/library/azure/mt218710.aspx)」を参照してください。

## <a name="next-steps"></a>次のステップ
* サブスクリプションを管理するための PowerShell コマンドレットについては、「 [Resource Manager での Azure PowerShell の使用](powershell-azure-resource-manager.md)」を参照してください。
* サブスクリプションを管理するための Azure CLI コマンドについては、「 [リソース マネージャーでの Azure CLI の使用](xplat-cli-azure-resource-manager.md)」を参照してください。
* サブスクリプションを管理するためのポータル機能については、 [Azure Portal を使用したリソースの管理](resource-group-portal.md)に関するページをご覧ください。
* リソースを論理的に整理する方法については、「 [タグを使用したリソースの整理](resource-group-using-tags.md)」を参照してください。


