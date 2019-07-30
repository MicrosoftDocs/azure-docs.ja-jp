---
title: Azure Resource Manager テンプレートを使用した Service Bus メッセージング名前空間の作成 | Microsoft Docs
description: Azure Resource Manager テンプレートを使用して Service Bus メッセージング名前空間を作成します。
services: service-bus-messaging
documentationcenter: .net
author: spelluru
manager: timlt
editor: ''
ms.assetid: dc0d6482-6344-4cef-8644-d4573639f5e4
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 06/21/2019
ms.author: spelluru
ms.openlocfilehash: 4162775153a48dc8ea28e06f7c99f9927b9c602a
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444766"
---
# <a name="create-a-service-bus-namespace-by-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用した Service Bus 名前空間の作成

Azure Resource Manager テンプレートをデプロイして Service Bus 名前空間を作成する方法について説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。 テンプレートの作成の詳細については、「[Azure Resource Manager のドキュメント](/azure/azure-resource-manager/)」をご覧ください。

Service Bus 名前空間を作成するために、次のテンプレートも使用できます。

* [キューを含んだ Service Bus 名前空間を作成する](./service-bus-resource-manager-namespace-queue.md)
* [トピックとサブスクリプションを含んだ Service Bus 名前空間を作成する](./service-bus-resource-manager-namespace-topic.md)
* [キューと承認規則を含んだ Service Bus 名前空間を作成する](./service-bus-resource-manager-namespace-auth-rule.md)
* [トピック、サブスクリプション、ルールを含んだ Service Bus の名前空間を作成する](./service-bus-resource-manager-namespace-topic-with-rule.md)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料アカウントを作成](https://azure.microsoft.com/free/)してください。

## <a name="create-a-service-bus-namespace"></a>Service Bus 名前空間の作成を作成する

このクイック スタートでは、[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/)から[既存の Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/blob/master/101-servicebus-create-namespace/azuredeploy.json)を使用します。

[!code-json[create-azure-service-bus-namespace](~/quickstart-templates/101-servicebus-create-namespace/azuredeploy.json)]

テンプレートのその他のサンプルについては、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Servicebus&pageNumber=1&sort=Popular)」をご覧ください。

テンプレートをデプロイして Service Bus 名前空間を作成するには:

1. 次のコード ブロックの **[試してみる]** を選択し、指示に従って Azure Cloud Shell にサインインします。

    ```azurepowershell-interactive
    $serviceBusNamespaceName = Read-Host -Prompt "Enter a name for the service bus namespace to be created"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${serviceBusNamespaceName}rg"
    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-servicebus-create-namespace/azuredeploy.json"

    New-AzResourceGroup -Name $resourceGroupName -Location $location
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -serviceBusNamespaceName $serviceBusNamespaceName

    Write-Host "Press [ENTER] to continue ..."
    ```

    リソース グループ名は、**rg** が追加された Service Bus 名前空間名です。

2. **[コピー]** を選択し、PowerShell スクリプトがコピーされます。
3. シェル コンソールを右クリックし、 **[貼り付け]** を選択します。

イベント ハブが作成されるまでしばらく時間がかかります。

## <a name="verify-the-deployment"></a>デプロイを検証する

デプロイされた Service Bus 名前空間を確認するには、Azure portal からリソース グループを開くか、次の Azure PowerShell スクリプトを使用します。 Cloud Shell がまだ開いている場合は、次のスクリプトの 1 番目と 2 番目の行をコピー/実行する必要はありません。

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Get-AzServiceBusNamespace -ResourceGroupName $resourceGroupName -Name $serviceBusNamespaceName

Write-Host "Press [ENTER] to continue ..."
```

このチュートリアルでは、テンプレートをデプロイするために Azure PowerShell を使用します。 テンプレートのその他のデプロイ方法については、以下をご覧ください。

* [Azure portal を使用する方法](../azure-resource-manager/resource-group-template-deploy-portal.md)。
* [Azure CLI を使用する方法](../azure-resource-manager/resource-group-template-deploy-cli.md)。
* [REST API を使用する方法](../azure-resource-manager/resource-group-template-deploy-rest.md)。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

Azure リソースが不要になったら、リソース グループを削除して、デプロイしたリソースをクリーンアップします。 Cloud Shell がまだ開いている場合は、次のスクリプトの 1 番目と 2 番目の行をコピー/実行する必要はありません。

```azurepowershell-interactive
$serviceBusNamespaceName = Read-Host -Prompt "Enter the same service bus namespace name used earlier"
$resourceGroupName = "${serviceBusNamespaceName}rg"

Remove-AzResourceGroup -ResourceGroupName $resourceGroupName

Write-Host "Press [ENTER] to continue ..."
```

## <a name="next-steps"></a>次の手順

この記事では、Service Bus 名前空間を作成しました。 キュー、トピック/サブスクリプションを作成して使用する方法については、他のクイック スタートを参照してください。

* [Service Bus キューの使用](service-bus-dotnet-get-started-with-queues.md)
* [Service Bus トピックの概要](service-bus-dotnet-how-to-use-topics-subscriptions.md)
