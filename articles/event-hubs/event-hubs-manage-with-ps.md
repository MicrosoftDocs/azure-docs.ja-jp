---
title: "PowerShell を使用した Azure Event Hubs リソースの管理 | Microsoft Docs"
description: "PowerShell モジュールを使用した Event Hubs の作成および管理"
services: event-hubs
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: event-hubs
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/15/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: 1e6fb68d239ee3a66899f520a91702419461c02b
ms.openlocfilehash: 2b49c01153b1104612e6ebf9c88566fc40d1f635
ms.contentlocale: ja-jp
ms.lasthandoff: 08/16/2017

---
# <a name="use-powershell-to-manage-event-hubs-resources"></a>PowerShell を使用した Event Hubs リソースの管理

Microsoft Azure PowerShell は、Azure サービスのデプロイメントと管理を制御し自動化するために使用できるスクリプティング環境です。 この記事では、[Event Hubs Resource Manager の PowerShell モジュール](/powershell/module/azurerm.eventhub) で、ローカルの Azure PowerShell コンソールまたはスクリプトを使用して Event Hubs エンティティ (名前空間、個々のイベント ハブ、およびコンシューマー グループ) をプロビジョニングおよび管理する方法について説明します。

Azure Resource Manager テンプレートを使用して Event Hubs リソースを管理することもできます。 詳細については、記事「[イベント ハブとコンシューマー グループを含んだ Event Hubs 名前空間を Azure Resource Manager テンプレートで作成する](event-hubs-resource-manager-namespace-event-hub.md)」を参照してください。

## <a name="prerequisites"></a>前提条件

開始する前に、次の条件が必要です。

* Azure サブスクリプション。 サブスクリプションの入手方法の詳細については、[購入オプション][purchase options]、[メンバー プラン][member offers]、または[無料アカウント][free account]に関するページをご覧ください。
* Azure PowerShell を搭載するコンピューター 手順については、「[Azure PowerShell コマンドレットの使用開始](/powershell/azure/get-started-azureps)」をご覧ください。
* PowerShell スクリプト、NuGet パッケージ、.NET Framework の一般的理解。

## <a name="get-started"></a>作業開始

最初の手順では、PowerShell を使用して、Azure アカウントと Azure サブスクリプションにログインします。 [「Get started with Azure PowerShell」](/powershell/azure/get-started-azureps) (Azure PowerShell コマンドレットの概要) の手順に従って Azure アカウントにログインし、Azure サブスクリプションのリソースを取得し、アクセスします。

## <a name="provision-an-event-hubs-namespace"></a>Event Hubs 名前空間のプロビジョニング

Event Hubs の名前空間を使用した操作では、[Get AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/get-azurermeventhubnamespace)、[New-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/new-azurermeventhubnamespace)、[Remove-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/remove-azurermeventhubnamespace)、および [Set-AzureRmEventHubNamespace](/powershell/module/azurerm.eventhub/set-azurermeventhubnamespace) コマンドレットを使用できます。

この例では、スクリプトでローカル変数 `$Namespace` と `$Location` を作成します。

* `$Namespace` は操作するEvent Hubs 名前空間の名前です。
* `$Location` は名前空間をプロビジョニングするデータセンターを識別します。
* `$CurrentNamespace` では取得 (または作成) する参照名前空間を保存します。

実際のスクリプトでは `$Namespace` と `$Location` はパラメーターとして渡すことができます。

スクリプトのこの部分では、次を行います。

1. 指定した名前で Event Hubs 名前空間の取得を試行します。
2. 名前空間が見つかると、記述されているものを報告します。
3. 名前空間が見つからない場合、名前空間を作成し、新しく作成した名前空間を取得します。

    ```powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
    }
    ```

## <a name="create-an-event-hub"></a>イベント ハブの作成

イベント ハブを作成するには、前のセクションのスクリプトを使用して名前空間の確認を実行します。 次に、New-[AzureRmEventHub](/powershell/module/azurerm.eventhub/new-azurermeventhub) コマンドレットを使用してイベント ハブを作成します。

```powershell
# Check if event hub already exists
$CurrentEH = Get-AzureRMEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName

if($CurrentEH)
{
    Write-Host "The event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $EventHubName event hub does not exist."
    Write-Host "Creating the $EventHubName event hub in the $Location region..."
    New-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -Location $Location -MessageRetentionInDays 3
    $CurrentEH = Get-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $EventHubName event hub in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="create-a-consumer-group"></a>コンシューマー グループの作成

イベント ハブ内でコンシューマー グループを作成するには、前のセクションのスクリプトを使用して名前空間とイベント ハブの確認を実行します。 次に、[New-AzureRmEventHubConsumerGroup](/powershell/module/azurerm.eventhub/new-azurermeventhubconsumergroup) コマンドレットを使用して、イベント ハブ内でコンシューマー グループを作成します。 次に例を示します。

```powershell
# Check if consumer group already exists
$CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -ErrorAction Ignore

if($CurrentCG)
{
    Write-Host "The consumer group $ConsumerGroupName in event hub $EventHubName already exists in the $Location region:"
    # Report what was found
    Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
}
else
{
    Write-Host "The $ConsumerGroupName consumer group does not exist."
    Write-Host "Creating the $ConsumerGroupName consumer group in the $Location region..."
    New-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
    $CurrentCG = Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
    Write-Host "The $ConsumerGroupName consumer group in event hub $EventHubName in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

#### <a name="set-user-metadata"></a>ユーザー メタデータの設定

前セクションのスクリプトを実行後、[Set-AzureRmServiceBusQueue](/powershell/module/azurerm.eventhub/set-azurermeventhubconsumergroup) コマンドレットを使用して、コンシューマー グループのプロパティを次の例のように更新します。

```powershell
# Set some user metadata on the CG
Write-Host "Setting the UserMetadata field to 'Testing'"
Set-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName -UserMetadata "Testing"
# Show result
Get-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
```

## <a name="remove-event-hub"></a>イベント ハブの削除

作成したイベント ハブを削除するには、`Remove-*` コマンドレットを、次の例のように使用できます。

```powershell
# Clean up
Remove-AzureRmEventHubConsumerGroup -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName -ConsumerGroupName $ConsumerGroupName
Remove-AzureRmEventHub -ResourceGroupName $ResGrpName -NamespaceName $Namespace -EventHubName $EventHubName
Remove-AzureRmEventHubNamespace -ResourceGroupName $ResGrpName -NamespaceName $Namespace
```

## <a name="next-steps"></a>次のステップ

- Event Hubs の Resource Manager の PowerShell モジュールに関する全ドキュメントは、[ここ](/powershell/module/azurerm.eventhub)から参照してください。 このページには、利用可能なすべてのコマンドレットが一覧されています。
- Azure Resource Manager テンプレートの使用の詳細については、記事「[イベント ハブとコンシューマー グループを含んだ Event Hubs 名前空間を Azure Resource Manager テンプレートで作成する](event-hubs-resource-manager-namespace-event-hub.md)」を参照してください。
- [Event Hubs .NET 管理ライブラリ](event-hubs-management-libraries.md)に関する情報。

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/

