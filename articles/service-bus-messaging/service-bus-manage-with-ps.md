---
title: "PowerShell で Azure Service Bus リソースを管理する | Microsoft ドキュメント"
description: "PowerShell モジュールで Service Bus リソースを作成し、管理する"
services: service-bus-messaging
documentationcenter: .NET
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 
ms.service: service-bus-messaging
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/28/2017
ms.author: sethm
ms.translationtype: HT
ms.sourcegitcommit: a0b98d400db31e9bb85611b3029616cc7b2b4b3f
ms.openlocfilehash: 1520cea72069be5d7bb078a3770d61fade26b853
ms.contentlocale: ja-jp
ms.lasthandoff: 08/29/2017

---
# <a name="use-powershell-to-manage-service-bus-resources"></a>PowerShell モジュールで Service Bus リソースを管理する

Microsoft Azure PowerShell は、Azure サービスのデプロイメントと管理を制御し自動化するために使用できるスクリプティング環境です。 この記事では、[Service Bus Resource Manager の PowerShell モジュール](/powershell/module/azurerm.servicebus) を使用して、Service Bus エンティティ (名前空間、キュー、トピック、およびサブスクリプション) を、ローカルの Azure PowerShell コンソールまたはスクリプトでプロビジョニングおよび管理する方法について説明します。

Azure Resource Manager テンプレートでも Service Bus エンティティを管理できます。 詳細については、[「Azure Resource Manager テンプレートを使用して Service Bus リソースを作成する」](service-bus-resource-manager-overview.md)の記事を参照してください。

## <a name="prerequisites"></a>前提条件

開始する前に、次の前提条件を満たす必要があります。

* Azure サブスクリプション。 サブスクリプションの入手方法の詳細については、[購入オプション][purchase options]、[メンバー プラン][member offers]、または[無料アカウント][free account]に関するページをご覧ください。
* Azure PowerShell を搭載するコンピューター 手順については、「[Azure PowerShell コマンドレットの使用開始](/powershell/azure/get-started-azureps)」をご覧ください。
* PowerShell スクリプト、NuGet パッケージ、.NET Framework の一般的理解。

## <a name="get-started"></a>作業開始

最初の手順では、PowerShell を使用して、Azure アカウントと Azure サブスクリプションにログインします。 [「Get started with Azure PowerShell cmdlets (Azure PowerShell コマンドレットの概要)」](/powershell/azure/get-started-azureps)の手順に従って、Azure アカウントにログインし、Azure サブスクリプションのリソースを取得し、アクセスします。

## <a name="provision-a-service-bus-namespace"></a>Service Bus 名前空間のプロビジョニング

Service Bus 名前空間を使用する場合は、次のコマンドレットを使用できます。[Get AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespace)、[New-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespace)、[Remove-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespace)、および[Set-AzureRmServiceBusNamespace](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespace)

この例では、スクリプトでローカル変数 `$Namespace` と `$Location` を作成します。

* `$Namespace` は操作する Service Bus 名前空間の名前です。
* `$Location` は名前空間をプロビジョニングするデータ センターを識別します。
* `$CurrentNamespace` では取得 (または作成) する参照名前空間を保存します。

実際のスクリプトでは `$Namespace` と `$Location` はパラメーターとして渡すことができます。

スクリプトのこの部分では、次を行います。

1. 指定された名前の Service Bus 名前空間の取得しようとします。
2. 名前空間が見つかると、記述されているものを報告します。
3. 名前空間が見つからない場合、名前空間を作成し、新しく作成した名前空間を取得します。
   
    ``` powershell
    # Query to see if the namespace currently exists
    $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
   
    # Check if the namespace already exists or needs to be created
    if ($CurrentNamespace)
    {
        Write-Host "The namespace $Namespace already exists in the $Location region:"
        # Report what was found
        Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
    }
    else
    {
        Write-Host "The $Namespace namespace does not exist."
        Write-Host "Creating the $Namespace namespace in the $Location region..."
        New-AzureRmServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace -Location $Location
        $CurrentNamespace = Get-AzureRMServiceBusNamespace -ResourceGroup $ResGrpName -NamespaceName $Namespace
        Write-Host "The $Namespace namespace in Resource Group $ResGrpName in the $Location region has been successfully created."
                
    }
    ```

### <a name="create-a-namespace-authorization-rule"></a>名前空間の承認規則の作成

次の例では、コマンドレットの [New-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/new-azurermservicebusnamespaceauthorizationrule)、[Get AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/get-azurermservicebusnamespaceauthorizationrule)、[Set-AzureRmServiceBusNamespaceAuthorizationRule](/powershell/module/azurerm.servicebus/set-azurermservicebusnamespaceauthorizationrule)、および [Remove-AzureRmServiceBusNamespaceAuthorizationRule ](/powershell/module/azurerm.servicebus/remove-azurermservicebusnamespaceauthorizationrule) を使用して承認規則の名前空間を管理する方法を示しています。

```powershell
# Query to see if rule exists
$CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

# Check if the rule already exists or needs to be created
if ($CurrentRule)
{
    Write-Host "The $AuthRule rule already exists for the namespace $Namespace."
}
else
{
    Write-Host "The $AuthRule rule does not exist."
    Write-Host "Creating the $AuthRule rule for the $Namespace namespace..."
    New-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule -Rights @("Listen","Send")
    $CurrentRule = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
    Write-Host "The $AuthRule rule for the $Namespace namespace has been successfully created."

    Write-Host "Setting rights on the namespace"
    $authRuleObj = Get-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule

    Write-Host "Remove Send rights"
    $authRuleObj.Rights.Remove("Send")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Add Send and Manage rights to the namespace"
    $authRuleObj.Rights.Add("Send")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj
    $authRuleObj.Rights.Add("Manage")
    Set-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthRuleObj $authRuleObj

    Write-Host "Show value of primary key"
    $CurrentKey = Get-AzureRmServiceBusNamespaceKey -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
        
    Write-Host "Remove this authorization rule"
    Remove-AzureRmServiceBusNamespaceAuthorizationRule -ResourceGroup $ResGrpName -NamespaceName $Namespace -AuthorizationRuleName $AuthRule
}
```

## <a name="create-a-queue"></a>キューを作成する

キューまたはトピックを作成するには、前のセクションのスクリプトを使用して名前空間の確認を実行します。 その後、次のようにキューを作成します。

```powershell
# Check if queue already exists
$CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName

if($CurrentQ)
{
    Write-Host "The queue $QueueName already exists in the $Location region:"
}
else
{
    Write-Host "The $QueueName queue does not exist."
    Write-Host "Creating the $QueueName queue in the $Location region..."
    New-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -EnablePartitioning $True
    $CurrentQ = Get-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName
    Write-Host "The $QueueName queue in Resource Group $ResGrpName in the $Location region has been successfully created."
}
```

### <a name="modify-queue-properties"></a>キューのプロパティを変更する

前セクションのスクリプト実行後、[Set-AzureRmServiceBusQueue](/powershell/module/azurerm.servicebus/set-azurermservicebusqueue) コマンドレットを次の例のように使用して、キューのプロパティを更新します。

```powershell
$CurrentQ.DeadLetteringOnMessageExpiration = $True
$CurrentQ.MaxDeliveryCount = 7
$CurrentQ.MaxSizeInMegabytes = 2048
$CurrentQ.EnableExpress = $True

Set-AzureRmServiceBusQueue -ResourceGroup $ResGrpName -NamespaceName $Namespace -QueueName $QueueName -QueueObj $CurrentQ
```

## <a name="provisioning-other-service-bus-entities"></a>その他の Service Bus エンティティのプロビジョニング

[Service Bus PowerShell モジュール](/powershell/module/azurerm.servicebus) を使用してトピックやサブスクリプションなどの他のエンティティをプロビジョニングできます。 これらのコマンドレットは、前のセクションで説明したキュー作成コマンドレットに構文的に似ています。

## <a name="next-steps"></a>次のステップ

- Service Bus Manager PowerShell モジュールの全ドキュメントを[ここ](/powershell/module/azurerm.servicebus)から参照してください。 このページには、利用可能なすべてのコマンドレットが一覧されています。
- Azure Resource Manager テンプレートの使用の詳細については、「[Azure Resource Manager テンプレートを使用して Service Bus リソースを作成する](service-bus-resource-manager-overview.md)」の記事を参照してください。
- [Service Bus .NET 管理ライブラリ](service-bus-management-libraries.md)に関する情報。

これ以外の Service Bus エンティティを管理する方法については、次のブログ投稿で説明されています。

* [PowerShell スクリプトを使用してService Bus キュー、トピック、サブスクリプションを作成する方法 (ブログの投稿)](http://blogs.msdn.com/b/paolos/archive/2014/12/02/how-to-create-a-service-bus-queues-topics-and-subscriptions-using-a-powershell-script.aspx)
* [PowerShell スクリプトを使用して Service Bus の名前空間と Event Hub を作成する方法 (ブログの投稿)](http://blogs.msdn.com/b/paolos/archive/2014/12/01/how-to-create-a-service-bus-namespace-and-an-event-hub-using-a-powershell-script.aspx)
* [Service Bus PowerShell Scripts](https://code.msdn.microsoft.com/Service-Bus-PowerShell-a46b7059)

<!--Anchors-->

[purchase options]: http://azure.microsoft.com/pricing/purchase-options/
[member offers]: http://azure.microsoft.com/pricing/member-offers/
[free account]: http://azure.microsoft.com/pricing/free-trial/

