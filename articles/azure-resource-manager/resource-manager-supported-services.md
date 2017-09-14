---
title: "Azure リソース プロバイダーとリソースの種類 | Microsoft Docs"
description: "リソース マネージャーをサポートするリソース プロバイダー、そのスキーマと利用可能な API バージョン、およびリソースをホストできるリージョンについて説明します。"
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 3c7a6fe4-371a-40da-9ebe-b574f583305b
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: tomfitz
ms.translationtype: HT
ms.sourcegitcommit: a678700884b612cad6281eb8f3b74ce63a0ebb69
ms.openlocfilehash: 6a9128f45d4199404019cee594842d59c7f1aaf3
ms.contentlocale: ja-jp
ms.lasthandoff: 07/26/2017

---

# <a name="resource-providers-and-types"></a>リソース プロバイダーと種類

リソースをデプロイするときに、リソース プロバイダーと種類に関する情報を取得しなければならないケースは少なくありません。 この記事では、次のことについて説明します。

* Azure のすべてのリソース プロバイダーを表示する
* リソース プロバイダーの登録ステータスを確認する
* リソース プロバイダーを登録する
* リソース プロバイダーのリソースの種類を表示する
* リソースの種類の有効な場所を表示する
* リソースの種類の有効な API のバージョンを表示する

これらの手順は、ポータル、Powershell、または Azure CLI を介して実行することができます。

## <a name="powershell"></a>PowerShell

Azure ですべてのリソース プロバイダーおよびサブスクリプションの登録状態を表示するには、次のコマンドを使用します。

```powershell
Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
```

これは次のような結果を返します。

```powershell
ProviderNamespace                RegistrationState
-------------------------------- ------------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

リソース プロバイダーの登録によって、サブスクリプションがリソース プロバイダーで機能するように構成します。 登録の範囲は常にサブスクリプションです。 既定では、多くのリソース プロバイダーが自動的に登録されます。 ただし、一部、手動で登録する必要があるリソース プロバイダーもあります。 リソース プロバイダーを登録するには、リソース プロバイダーの `/register/action` 操作を実行するための権限が必要です。 この操作は、共同作成者ロールと所有者ロールに含まれます。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

これは次のような結果を返します。

```powershell
ProviderNamespace : Microsoft.Batch
RegistrationState : Registering
ResourceTypes     : {batchAccounts, operations, locations, locations/quotas}
Locations         : {West Europe, East US, East US 2, West US...}
```

サブスクリプション内に特定のリソース プロバイダーからのリソース タイプがまだある場合、そのリソース プロバイダーの登録を解除することはできません。

特定のリソース プロバイダーの情報を表示するには、次のコマンドを使用します。

```powershell
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
```

これは次のような結果を返します。

```powershell
{ProviderNamespace : Microsoft.Batch
RegistrationState : Registered
ResourceTypes     : {batchAccounts}
Locations         : {West Europe, East US, East US 2, West US...}

...
```

リソース プロバイダーのリソースの種類を表示するには、次のコマンドを使用します。

```powershell
(Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes.ResourceTypeName
```

次のような結果が返されます。

```powershell
batchAccounts
operations
locations
locations/quotas
```

API バージョンは、リリース プロバイダーがリリースする REST API のバージョンに一致します。 リソース プロバイダーは、新しい機能を有効にすると、REST API の新しいバージョンをリリースします。 

リソースの種類の使用可能な API バージョンを取得するには、次のコマンドを使用します。

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).ApiVersions
```

次のような結果が返されます。

```powershell
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

リソース マネージャーはすべてのリージョンでサポートされていますが、デプロイするリソースはすべてのリージョンではサポートされていない場合があります。 さらに、サブスクリプションでの制限により、リソースをサポートする一部のリージョンを使用できない場合があります。 

リソースの種類のサポートされている場所を取得するには、次のコマンドを使用します。

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch).ResourceTypes | Where-Object ResourceTypeName -eq batchAccounts).Locations
```

次のような結果が返されます。

```powershell
West Europe
East US
East US 2
West US
...
```

## <a name="azure-cli"></a>Azure CLI
Azure ですべてのリソース プロバイダーおよびサブスクリプションの登録状態を表示するには、次のコマンドを使用します。

```azurecli
az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
```

これは次のような結果を返します。

```azurecli
Provider                         Status
-------------------------------- ----------------
Microsoft.ClassicCompute         Registered
Microsoft.ClassicNetwork         Registered
Microsoft.ClassicStorage         Registered
Microsoft.CognitiveServices      Registered
...
```

リソース プロバイダーの登録によって、サブスクリプションがリソース プロバイダーで機能するように構成します。 登録の範囲は常にサブスクリプションです。 既定では、多くのリソース プロバイダーが自動的に登録されます。 ただし、一部、手動で登録する必要があるリソース プロバイダーもあります。 リソース プロバイダーを登録するには、リソース プロバイダーの `/register/action` 操作を実行するための権限が必要です。 この操作は、共同作成者ロールと所有者ロールに含まれます。

```azurecli
az provider register --namespace Microsoft.Batch
```

登録が進行中であることを示すメッセージが返されます。

サブスクリプション内に特定のリソース プロバイダーからのリソース タイプがまだある場合、そのリソース プロバイダーの登録を解除することはできません。

特定のリソース プロバイダーの情報を表示するには、次のコマンドを使用します。

```azurecli
az provider show --namespace Microsoft.Batch
```

これは次のような結果を返します。

```azurecli
{
    "id": "/subscriptions/####-####/providers/Microsoft.Batch",
    "namespace": "Microsoft.Batch",
    "registrationsState": "Registering",
    "resourceTypes:" [
        ...
    ]
}
```

リソース プロバイダーのリソースの種類を表示するには、次のコマンドを使用します。

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[*].resourceType" --out table
```

次のような結果が返されます。

```azurecli
Result
---------------
batchAccounts
operations
locations
locations/quotas
```

API バージョンは、リリース プロバイダーがリリースする REST API のバージョンに一致します。 リソース プロバイダーは、新しい機能を有効にすると、REST API の新しいバージョンをリリースします。 

リソースの種類の使用可能な API バージョンを取得するには、次のコマンドを使用します。

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].apiVersions | [0]" --out table
```

次のような結果が返されます。

```azurecli
Result
---------------
2017-05-01
2017-01-01
2015-12-01
2015-09-01
2015-07-01
```

リソース マネージャーはすべてのリージョンでサポートされていますが、デプロイするリソースはすべてのリージョンではサポートされていない場合があります。 さらに、サブスクリプションでの制限により、リソースをサポートする一部のリージョンを使用できない場合があります。 

リソースの種類のサポートされている場所を取得するには、次のコマンドを使用します。

```azurecli
az provider show --namespace Microsoft.Batch --query "resourceTypes[?resourceType=='batchAccounts'].locations | [0]" --out table
```

次のような結果が返されます。

```azurecli
Result
---------------
West Europe
East US
East US 2
West US
...
```

## <a name="portal"></a>ポータル

Azure ですべてのリソース プロバイダおよびサブスクリプションの登録状態を表示するには、**[サブスクリプション]** を選択します。

![サブスクリプションの選択](./media/resource-manager-supported-services/select-subscriptions.png)

表示するサブスクリプションを選択します。

![サブスクリプションの指定](./media/resource-manager-supported-services/subscription.png)

**[リソース プロバイダー]** を選択し、利用可能なリソース プロバイダーの一覧を表示します。

![リソース プロバイダーの表示](./media/resource-manager-supported-services/show-resource-providers.png)

リソース プロバイダーの登録によって、サブスクリプションがリソース プロバイダーで機能するように構成します。 登録の範囲は常にサブスクリプションです。 既定では、多くのリソース プロバイダーが自動的に登録されます。 ただし、一部、手動で登録する必要があるリソース プロバイダーもあります。 リソース プロバイダーを登録するには、リソース プロバイダーの `/register/action` 操作を実行するための権限が必要です。 この操作は、共同作成者ロールと所有者ロールに含まれます。 リソース プロバイダーを登録するには、**[登録]** を選択します。

![リソース プロバイダーの登録](./media/resource-manager-supported-services/register-provider.png)

サブスクリプション内に特定のリソース プロバイダーからのリソース タイプがまだある場合、そのリソース プロバイダーの登録を解除することはできません。

特定のリソース プロバイダーの情報を表示するには、**[その他のサービス]** を選択します。

![[その他のサービス] の選択](./media/resource-manager-supported-services/more-services.png)

**リソース エクスプローラー**を検索し、使用可能なオプションから選択します。

![リソース エクスプローラーの選択](./media/resource-manager-supported-services/select-resource-explorer.png)

**[プロバイダー]** を選択します。

![プロバイダーの選択](./media/resource-manager-supported-services/select-providers.png)

表示するリソース プロバイダーとリソースの種類を選択します。

![リソースの種類の選択](./media/resource-manager-supported-services/select-resource-type.png)

リソース マネージャーはすべてのリージョンでサポートされていますが、デプロイするリソースはすべてのリージョンではサポートされていない場合があります。 さらに、サブスクリプションでの制限により、リソースをサポートする一部のリージョンを使用できない場合があります。 リソース エクスプローラーでは、リソースの種類の有効な場所が表示されます。

![場所の表示](./media/resource-manager-supported-services/show-locations.png)

API バージョンは、リリース プロバイダーがリリースする REST API のバージョンに一致します。 リソース プロバイダーは、新しい機能を有効にすると、REST API の新しいバージョンをリリースします。 リソース エクスプローラーでは、リソースの種類の有効 API のバージョンが表示されます。

![API のバージョンの表示](./media/resource-manager-supported-services/show-api-versions.png)

## <a name="next-steps"></a>次のステップ
* リソース マネージャーのテンプレートの作成の詳細については、 [Azure リソース マネージャーのテンプレートの作成](resource-group-authoring-templates.md)に関するページを参照してください。
* リソースをデプロイする方法を確認するには、「 [Azure リソース マネージャーのテンプレートを使用したアプリケーションのデプロイ](resource-group-template-deploy.md)」を参照してください。
* リソース プロバイダーの操作を表示するには、「[Azure REST API](/rest/api/)」を参照してください。


