---
title: "テンプレートを使用した Azure Service Bus のリソースの作成 | Microsoft Docs"
description: "Azure Resource Manager テンプレートを使用して Service Bus リソースの作成を自動化する"
services: service-bus-messaging
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 24f6a207-0fa4-49cf-8a58-963f9e2fd655
ms.service: service-bus-messaging
ms.devlang: tbd
ms.topic: article
ms.tgt_pltfrm: dotnet
ms.workload: na
ms.date: 01/18/2017
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: ca66a344ea855f561ead082091c6941540b1839d
ms.openlocfilehash: a9832411884f67c06635261a3e144de14ebbb4b8


---
# <a name="create-service-bus-resources-using-azure-resource-manager-templates"></a>Azure Resource Manager テンプレートを使用して Service Bus リソースを作成する
この記事では、Azure Resource Manager テンプレート、PowerShell、Service Bus リソース プロバイダーを使用して Service Bus リソースを作成し、デプロイする方法について説明します。

Azure Resource Manager テンプレートを使用すると、ソリューションでデプロイするリソースを定義し、さまざまな環境用の値を入力できるパラメーターと変数を指定できます。 テンプレートは、JSON、およびデプロイの値を構築するときの式で構成されます。 Azure Resource Manager テンプレートの作成の詳細と、テンプレート形式の説明については、[「Azure Resource Manager のテンプレートの作成」](../azure-resource-manager/resource-group-authoring-templates.md)を参照してください。

> [!NOTE]
> この記事の例では、Azure Resource Manager を使用して Service Bus の名前空間とメッセージング エンティティ (キュー) を作成する方法について説明します。 他のテンプレート例については、「[Azure クイックスタート テンプレート][Azure Quickstart Templates gallery]」ギャラリーで "Service Bus" を検索してください。
>
>

## <a name="service-bus-resource-manager-templates"></a>Service Bus Resource Manager テンプレート
これらの Service Bus Azure Resource Manager テンプレートは、ダウンロードしてデプロイすることができます。 次のリンクをクリックすると、それぞれの詳細情報と、GitHub のテンプレートを参照できます。

* [Service Bus 名前空間の作成](service-bus-resource-manager-namespace.md)
* [キューを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-queue.md)
* [トピックとサブスクリプションを含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-topic.md)
* [キューと承認規則を含んだ Service Bus 名前空間を作成する](service-bus-resource-manager-namespace-auth-rule.md)
* [トピック、サブスクリプション、ルールを含んだ Service Bus の名前空間を作成する](service-bus-resource-manager-namespace-topic-with-rule.md)

## <a name="deploy-with-powershell"></a>PowerShell でデプロイする
次の手順では、PowerShell を使用して、Azure Resource Manager テンプレートをデプロイします。**標準**レベルの Service Bus 名前空間と、名前空間内にキューが作成されます。 この例は、[キューを使用した Service Bus 名前空間の作成](https://github.com/Azure/azure-quickstart-templates/tree/master/201-servicebus-create-queue)テンプレートに基づいています。 ワークフローの概要は次のとおりです。

1. PowerShell をインストールします。
2. テンプレートと (必要に応じて) パラメーター ファイルを作成します。
3. PowerShell で Azure アカウントにログインします。
4. リソース グループが存在しない場合は、新しいリソース グループを作成します。
5. デプロイをテストします。
6. 必要に応じて、デプロイ モードを設定します。
7. テンプレートをデプロイします。

Azure Resource Manager テンプレートのデプロイの詳細については、[Azure Resource Manager テンプレートを使用したリソースのデプロイ][Deploy resources with Azure Resource Manager templates]に関する記事をご覧ください。

### <a name="install-powershell"></a>PowerShell をインストールする
「[Azure PowerShell コマンドレットの使用開始](/powershell/azureps-cmdlets-docs)」の手順に従って、Azure PowerShell をインストールします。

### <a name="create-a-template"></a>テンプレートの作成
GitHub から [201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.json) テンプレートを複製またはコピーします。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Service Bus namespace"
            }
        },
        "serviceBusQueueName": {
            "type": "string",
            "metadata": {
                "description": "Name of the Queue"
            }
        },
        "serviceBusApiVersion": {
            "type": "string",
            "defaultValue": "2015-08-01",
            "metadata": {
                "description": "Service Bus ApiVersion used by the template"
            }
        }
    },
    "variables": {
        "location": "[resourceGroup().location]",
        "sbVersion": "[parameters('serviceBusApiVersion')]",
        "defaultSASKeyName": "RootManageSharedAccessKey",
        "authRuleResourceId": "[resourceId('Microsoft.ServiceBus/namespaces/authorizationRules', parameters('serviceBusNamespaceName'), variables('defaultSASKeyName'))]"
    },
    "resources": [{
        "apiVersion": "[variables('sbVersion')]",
        "name": "[parameters('serviceBusNamespaceName')]",
        "type": "Microsoft.ServiceBus/Namespaces",
        "location": "[variables('location')]",
        "kind": "Messaging",
        "sku": {
            "name": "StandardSku",
            "tier": "Standard"
        },
        "resources": [{
            "apiVersion": "[variables('sbVersion')]",
            "name": "[parameters('serviceBusQueueName')]",
            "type": "Queues",
            "dependsOn": [
                "[concat('Microsoft.ServiceBus/namespaces/', parameters('serviceBusNamespaceName'))]"
            ],
            "properties": {
                "path": "[parameters('serviceBusQueueName')]"
            }
        }]
    }],
    "outputs": {
        "NamespaceConnectionString": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryConnectionString]"
        },
        "SharedAccessPolicyPrimaryKey": {
            "type": "string",
            "value": "[listkeys(variables('authRuleResourceId'), variables('sbVersion')).primaryKey]"
        }
    }
}
```

### <a name="create-a-parameters-file-optional"></a>パラメーター ファイルを作成する (省略可能)
省略可能なパラメーター ファイルを使用するには、[201-servicebus-create-queue](https://github.com/Azure/azure-quickstart-templates/blob/master/201-servicebus-create-queue/azuredeploy.parameters.json) ファイルをコピーします。 `serviceBusNamespaceName` の値を、このデプロイで作成する Service Bus 名前空間の名前で置き換えます。また、`serviceBusQueueName` の値を、作成するキューの名前で置き換えます。

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "serviceBusNamespaceName": {
            "value": "<myNamespaceName>"
        },
        "serviceBusQueueName": {
            "value": "<myQueueName>"
        },
        "serviceBusApiVersion": {
            "value": "2015-08-01"
        }
    }
}
```

詳しくは、「[パラメーター](../azure-resource-manager/resource-group-template-deploy.md#parameters)」のトピックをご覧ください。

### <a name="log-in-to-azure-and-set-the-azure-subscription"></a>Azure にログインして Azure サブスクリプションを設定する
PowerShell プロンプトから、次のコマンドを実行します。

```powershell
Login-AzureRmAccount
```

Azure アカウントにログオンするように求められます。 ログオンしたら、次のコマンドを実行して、使用できるサブスクリプションを確認します。

```powershell
Get-AzureRMSubscription
```

このコマンドを実行すると、使用できる Azure サブスクリプションの一覧が返されます。 現在のセッションのサブスクリプションを選択するには、次のコマンドを実行します。 `<YourSubscriptionId>` は、使用する Azure サブスクリプションの GUID に置き換えてください。

```powershell
Set-AzureRmContext -SubscriptionID <YourSubscriptionId>
```

### <a name="set-the-resource-group"></a>リソース グループを設定する
既存のリソース グループがない場合は、**New-AzureRmResourceGroup** コマンドで新しいリソース グループを作成します。 使用するリソース グループの名前と場所を指定します。 次に例を示します。

```powershell
New-AzureRmResourceGroup -Name MyDemoRG -Location "West US"
```

成功した場合、新しいリソース グループの概要が表示されます。

```powershell
ResourceGroupName : MyDemoRG
Location          : westus
ProvisioningState : Succeeded
Tags              :
ResourceId        : /subscriptions/<GUID>/resourceGroups/MyDemoRG
```

### <a name="test-the-deployment"></a>展開をテスト
デプロイを検証するには、`Test-AzureRmResourceGroupDeployment` コマンドレットを実行します。 デプロイをテストする場合、デプロイの実行時と同様に、必要なパラメーターを正確に指定します。

```powershell
Test-AzureRmResourceGroupDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="create-the-deployment"></a>デプロイを作成する
新しいデプロイを作成するには、`New-AzureRmResourceGroupDeployment` コマンドレットを実行し、プロンプトに従って必要なパラメーターを指定します。 パラメーターには、デプロイの名前、リソース グループの名前、テンプレート ファイルのパスまたは URL が含まれます。 **Mode** パラメーターを指定しない場合、**Incremental** の既定値が使用されます。 詳細については、[「増分デプロイと完全デプロイ」](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)を参照してください。

次のコマンドを実行すると、PowerShell ウィンドウに&3; つのパラメーターを指定するように求められます。

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

代わりにパラメーター ファイルを使用するには、次のコマンドを使用します。

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -TemplateParameterFile <path to parameters file>\azuredeploy.parameters.json
```

また、デプロイ コマンドレットを実行するときに、インライン パラメーターを使用することもできます。 コマンドは次のとおりです。

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json -parameterName "parameterValue"
```

[完全](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments)デプロイを実行するには、**Mode** パラメーターを **Complete** に設定します。

```powershell
New-AzureRmResourceGroupDeployment -Name MyDemoDeployment -Mode Complete -ResourceGroupName MyDemoRG -TemplateFile <path to template file>\azuredeploy.json
```

### <a name="verify-the-deployment"></a>デプロイを検証する
リソースが正常にデプロイされると、デプロイの概要が PowerShell ウィンドウに表示されます。

```powershell
DeploymentName    : MyDemoDeployment
ResourceGroupName : MyDemoRG
ProvisioningState : Succeeded
Timestamp         : 4/19/2016 10:38:30 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    serviceBusNamespaceName  String             <namespaceName>
                    serviceBusQueueName  String                 <queueName>
                    serviceBusApiVersion  String                2015-08-01

```

## <a name="next-steps"></a>次のステップ
Azure Resource Manager テンプレートをデプロイする基本のワークフローとコマンドが表示されました。 詳細については、次のリンクを参照してください。

* [Azure Resource Manager の概要][Azure Resource Manager overview]
* [Resource Manager テンプレートと Azure PowerShell を使用したリソースのデプロイ][Deploy resources with Azure Resource Manager templates]
* [Azure リソース マネージャーのテンプレートの作成](../azure-resource-manager/resource-group-authoring-templates.md)

[Azure Resource Manager overview]: ../azure-resource-manager/resource-group-overview.md
[Deploy resources with Azure Resource Manager templates]: ../azure-resource-manager/resource-group-template-deploy.md
[Azure Quickstart Templates gallery]: https://azure.microsoft.com/documentation/templates/?term=service+bus



<!--HONumber=Jan17_HO4-->


