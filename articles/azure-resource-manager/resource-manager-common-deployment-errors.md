---
title: "Azure へのデプロイで発生する一般的なエラーのトラブルシューティング | Microsoft Docs"
description: "Azure Resource Manager を使用した Azure へのリソースのデプロイ時に発生する一般的なエラーの解決方法について説明します。"
services: azure-resource-manager
documentationcenter: 
tags: top-support-issue
author: tfitzmac
manager: timlt
editor: tysonn
keywords: "デプロイのエラー、Azure へのデプロイ、Azure へのデプロイ"
ms.assetid: c002a9be-4de5-4963-bd14-b54aa3d8fa59
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/15/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: b31ecb83665208151e48f81e6148928bbf21d1b5
ms.lasthandoff: 03/15/2017


---
# <a name="troubleshoot-common-azure-deployment-errors-with-azure-resource-manager"></a>Azure Resource Manager を使用した Azure へのデプロイで発生する一般的なエラーのトラブルシューティング
このトピックでは、Azure へのデプロイで発生する可能性がある一般的なエラーの解決方法について説明します。

## <a name="two-types-of-errors"></a>2 種類のエラー
発生する可能性のあるエラーには、次の&2; 種類があります。

* 検証エラー
* デプロイ エラー

次の図は、サブスクリプションのアクティビティ ログを示しています。 2 つのデプロイで&3; つの操作が実行されています。 最初のデプロイでは、テンプレートは検証を通過しましたが、リソースの作成時 (**Write Deployments**) に失敗しました。 2 番目のデプロイでは、テンプレートが検証に失敗し、**Write Deployments** に進むことができませんでした。

![エラー コードの表示](./media/resource-manager-common-deployment-errors/show-activity-log.png)

検証エラーは、問題を引き起こすことを事前に判断できるシナリオで発生します。 これには、テンプレートに構文エラーがある場合や、サブスクリプション クォータを超えるリソースをデプロイしようとしている場合などがあります。 デプロイ エラーは、デプロイ プロセスで発生する状況に起因します。 たとえば、デプロイ中のリソースにアクセスしようとしている場合などです。

この&2; 種類のエラーでは、デプロイのトラブルシューティングに使用するエラー コードが返されます。 どちらの種類のエラーも[アクティビティ ログ](resource-group-audit.md)に表示されます。 ただし、検証エラーは、デプロイが開始されていないため、デプロイ履歴には表示されません。


## <a name="error-codes"></a>エラー コード

このトピックでは、次のエラー コードについて説明します。

* [AccountNameInvalid](#accountnameinvalid)
* [承認に失敗する](#authorization-failed)
* [BadRequest](#badrequest)
* [DeploymentFailed](#deploymentfailed)
* [DisallowedOperation](#disallowedoperation)
* [InvalidContentLink](#invalidcontentlink)
* [InvalidTemplate](#invalidtemplate)
* [MissingSubscriptionRegistration](#noregisteredproviderfound)
* [NotFound](#notfound)
* [NoRegisteredProviderFound](#noregisteredproviderfound)
* [OperationNotAllowed](#quotaexceeded)
* [ParentResourceNotFound](#parentresourcenotfound)
* [QuotaExceeded](#quotaexceeded)
* [RequestDisallowedByPolicy](#requestdisallowedbypolicy)
* [ResourceNotFound](#notfound)
* [SkuNotAvailable](#skunotavailable)
* [StorageAccountAlreadyExists](#storagenamenotunique)
* [StorageAccountAlreadyTaken](#storagenamenotunique)

### <a name="deploymentfailed"></a>DeploymentFailed

このエラー コードは、デプロイに関する一般的なエラーを示しますが、トラブルシューティングの開始が必要なエラー コードではありません。 実際に問題の解決に役立つエラー コードは、通常、このエラーの&1; つ下のレベルにあります。 たとえば、次の図は、デプロイ エラーの下にある **RequestDisallowedByPolicy** エラー コードを示しています。

![エラー コードの表示](./media/resource-manager-common-deployment-errors/error-code.png)

### <a name="skunotavailable"></a>SkuNotAvailable

リソース (通常は仮想マシン) をデプロイするときに、次のエラー コードとエラー メッセージが表示される場合があります。

```
Code: SkuNotAvailable
Message: The requested tier for resource '<resource>' is currently not available in location '<location>' 
for subscription '<subscriptionID>'. Please try another tier or deploy to a different location.
```

このエラーは、選択したリソースの SKU (VM サイズなど) が、選択した地域で利用できない場合に発生します。 この問題を解決するには、リージョンで利用可能な SKU を特定する必要があります。 ポータルまたは REST 操作のいずれかを使用して、利用可能な SKU を検索できます。

- [ポータル](https://portal.azure.com)を使用するには、ポータルにログインし、インターフェイスからリソースを追加します。 値を設定するときに、そのリソースで利用可能な SKU が表示されます。 デプロイを完了する必要はありません。

    ![利用可能な SKU](./media/resource-manager-common-deployment-errors/view-sku.png)

- 仮想マシンの REST API を使用するには、次の要求を送信します。

  ```HTTP 
  GET
  https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Compute/skus?api-version=2016-03-30
  ```

  次の形式で利用可能な SKU とリージョンを返します。

  ```json
  {
    "value": [
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A0",
        "tier": "Standard",
        "size": "A0",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      {
        "resourceType": "virtualMachines",
        "name": "Standard_A1",
        "tier": "Standard",
        "size": "A1",
        "locations": [
          "eastus"
        ],
        "restrictions": []
      },
      ...
    ]
  }    
  ```

UI に表示されたリージョン (またはビジネス ニーズを満たすその他のリージョン) に適切な SKU が見つからない場合は、[Azure サポート](https://portal.azure.com/#create/Microsoft.Support)にお問い合わせください。

### <a name="disallowedoperation"></a>DisallowedOperation

```
Code: DisallowedOperation
Message: The current subscription type is not permitted to perform operations on any provider 
namespace. Please use a different subscription.
```

このエラーが発生した場合は、Azure Active Directory を除くすべての Azure サービスへのアクセスが許可されていないサブスクリプションを使用していることになります。 クラシック ポータルにアクセスする必要があるものの、リソースのデプロイが許可されていないような場合に、サブスクリプションがこのような状態になることがあります。 この問題を解決するには、リソースをデプロイする権限のあるサブスクリプションを使用する必要があります。  

PowerShell で使用可能なサブスクリプションを表示するには、次のコマンドを使用します。

```powershell
Get-AzureRmSubscription
```

さらに、現在のサブスクリプションを設定するには、次のコマンドを使用します。

```powershell
Set-AzureRmContext -SubscriptionName {subscription-name}
```

Azure CLI 2.0 で使用可能なサブスクリプションを表示するには、次のコマンドを使用します。

```azurecli
az account list
```

さらに、現在のサブスクリプションを設定するには、次のコマンドを使用します。

```azurecli
az account set --subscription {subscription-name}
```

### <a name="invalidtemplate"></a>InvalidTemplate
このエラーは、さまざまな種類のエラーが原因となって発生する場合があります。

- 構文エラー

   テンプレートの検証に失敗したことを示すエラー メッセージが表示された場合、テンプレートに構文エラーが存在する可能性があります。

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed
  ```

   テンプレートの式は複雑になることもあり、このエラーが発生することは珍しくありません。 たとえば、ストレージ アカウントの次の名前の割り当てには、1 組の角かっこ、3 つの関数、3 組のかっこ、1 組の一重引用符、および&1; つのプロパティが含まれています。

  ```json
  "name": "[concat('storage', uniqueString(resourceGroup().id))]",
  ```

   一致する構文を指定しないと、テンプレートによって、意図したものとは異なる値が生成されます。

   この種類のエラーが発生したら、式の構文を慎重に確認してください。 [Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) や [Visual Studio Code](resource-manager-vs-code.md) など、構文エラーの指摘が可能な JSON エディターの使用を検討してください。

- セグメントの長さが不適切

   テンプレートが無効であるために発生するエラーは他にもあります。たとえばリソース名が正しい形式になっていないとエラーが発生します。

  ```
  Code=InvalidTemplate
  Message=Deployment template validation failed: 'The template resource {resource-name}'
  for type {resource-type} has incorrect segment lengths.
  ```

   ルート レベルのリソースは、名前に含まれるセグメントの数がリソース タイプのセグメントの数よりも&1; つ少なくなっている必要があります。 各セグメントは、スラッシュで区別されます。 次の例は、type のセグメント数が&2; つで、name のセグメント数が&1; つなので、**有効な名前**です。

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "myHostingPlanName",
    ...
  }
  ```

   しかし次の例は、name と type のセグメント数が同じであるため、 **有効な名前ではありません** 。

  ```json
  {
    "type": "Microsoft.Web/serverfarms",
    "name": "appPlan/myHostingPlanName",
    ...
  }
  ```

   子のリソースに関しては、type と name のセグメント数が一致します。 このセグメントの数は理に適っています。子の完全な name と type には親の name と type が含まれるためです。 したがって、完全な name のセグメント数は、やはり完全な type よりも&1; つ少なくなります。

  ```json
  "resources": [
      {
          "type": "Microsoft.KeyVault/vaults",
          "name": "contosokeyvault",
          ...
          "resources": [
              {
                  "type": "secrets",
                  "name": "appPassword",
                  ...
              }
          ]
      }
  ]
  ```

   複数のリソース プロバイダーに対して適用される Resource Manager タイプでは、セグメントを正しく指定するために注意が必要となります。 たとえば、リソース ロックを Web サイトに適用するためには、type が&4; つのセグメントで構成されている必要があります。 したがって name のセグメント数は&3; つになります。

  ```json
  {
      "type": "Microsoft.Web/sites/providers/locks",
      "name": "[concat(variables('siteName'),'/Microsoft.Authorization/MySiteLock')]",
      ...
  }
  ```

- copy のインデックスが不要

   この **InvalidTemplate** エラーは、**copy** 要素がサポートされていないテンプレートの部分にこの要素を適用すると発生します。 copy 要素を適用できるのは、リソース タイプだけです。 リソース タイプ内のプロパティに copy を適用することはできません。 たとえば、copy を仮想マシンに適用することはできますが、仮想マシンの OS ディスクに適用することはできません。 場合によっては、子リソースを親リソースに変換して、copy のループを作成することができます。 copy の使用について詳しくは、「 [Azure Resource Manager でリソースの複数のインスタンスを作成する](resource-group-create-multiple.md)」をご覧ください。

- パラメーターが無効

   パラメーターに使用できる値がテンプレートで指定されている場合に、それ以外の値を指定すると、次のようなエラー メッセージが表示されます。

  ```
  Code=InvalidTemplate;
  Message=Deployment template validation failed: 'The provided value {parameter value}
  for the template parameter {parameter name} is not valid. The parameter value is not
  part of the allowed values
  ``` 

   テンプレートで許可された値を十分に確認し、デプロイの際はその値を用いてください。

- 循環依存関係が検出されました

   このエラーは、リソース同士の依存関係がデプロイの開始を妨げるときに受け取ります。 相互依存関係の組み合わせにより、2 つ以上のリソースが同じく待機しているその他のリソースを待機します。 たとえば、resource1 が resource3 に依存、resource2 が resource1 に依存、resource3 が resource2 している場合などです。 通常、この問題は不要な依存関係を削除することによって解決できます。 依存関係に関するエラーのトラブルシューティングのヒントについて詳しくは、「[デプロイの順序の確認](#check-deployment-sequence)」をご覧ください。

<a id="notfound" />
### <a name="notfound-and-resourcenotfound"></a>NotFound と ResourceNotFound
解決できないリソースの名前がテンプレートに含まれている場合、次のようなエラーが表示されます。

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

不足しているリソースをテンプレートにデプロイする場合は、依存関係を追加する必要があるかどうかを確認してください。 Resource Manager は、可能であれば複数のリソースを並列して作成することで、デプロイを最適化しています。 リソースを順番にデプロイする必要がある場合は、テンプレートで **dependsOn** 要素を使用して、他のリソースへの依存関係を作成してください。 たとえば、Web アプリをデプロイする場合は、App Service プランが存在する必要があります。 Web アプリが App Service プランに依存していることを指定しなかった場合、Resource Manager では同時に両方のリソースが作成されます。 また、App Service プランのリソースが見つからないというエラーが発生します。これは、Web アプリにプロパティを設定しようとしたときに、そのリソースがまだ存在しないためです。 このエラーは、Web アプリに依存関係を設定することで回避します。

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

依存関係に関するエラーのトラブルシューティングのヒントについて詳しくは、「[デプロイの順序の確認](#check-deployment-sequence)」をご覧ください。

また、このエラーは、そのリソースがデプロイ先のリソース グループとは異なるリソース グループに存在する場合にも表示されます。 この場合は、[resourceId 関数](resource-group-template-functions.md#resourceid)を使用して、リソースの完全修飾名を取得します。

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

解決できないリソースで [reference](resource-group-template-functions.md#reference) または [listKeys](resource-group-template-functions.md#listkeys) 関数を使用しようとした場合、次のエラーが発生します。

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

**reference** 関数を含む式を検索してください。 その際、パラメーターの値が正しいかどうかを十分に確認してください。

### <a name="parentresourcenotfound"></a>ParentResourceNotFound

あるリソースが別のリソースの親である場合、子リソースを作成する前に親リソースが存在する必要があります。 まだ存在しない場合、次のエラーが発生します。

```
Code=ParentResourceNotFound;
Message=Can not perform requested operation on nested resource. Parent resource 'exampleserver' not found."
```

子リソースの名前には、親の名前が含まれます。 たとえば、SQL Database は次のように定義できます。

```json
{
  "type": "Microsoft.Sql/servers/databases",
  "name": "[concat(variables('databaseServerName'), '/', parameters('databaseName'))]",
  ...
```

ただし、親リソースに依存関係を指定しなかった場合、子リソースは親の前にデプロイされる可能性があります。 このエラーを解決するには、依存関係を含めます。

```json
"dependsOn": [
    "[variables('databaseServerName')]"
]
```

<a id="storagenamenotunique" />
### <a name="storageaccountalreadyexists-and-storageaccountalreadytaken"></a>StorageAccountAlreadyExists と StorageAccountAlreadyTaken
ストレージ アカウントには、Azure 全体で一意であるリソースの名前を指定する必要があります。 一意の名前を指定しないと、次のようなエラーが発生します。

```
Code=StorageAccountAlreadyTaken
Message=The storage account named mystorage is already taken.
```

一意の名前を作成するには、使用している命名規則に、 [uniqueString](resource-group-template-functions.md#uniquestring) 関数の結果を連結します。

```json
"name": "[concat('storage', uniqueString(resourceGroup().id))]",
"type": "Microsoft.Storage/storageAccounts",
```

ストレージ アカウントをデプロイするとき、ご利用のサブスクリプションに既に存在するストレージ アカウントと同じ名前を使用したうえで別の場所にデプロイすると、ストレージ アカウントが既に別の場所に存在しているというエラーが表示されます。 既存のストレージ アカウントを削除するか、既存のストレージ アカウントと同じ場所を指定してください。

### <a name="accountnameinvalid"></a>AccountNameInvalid
**AccountNameInvalid** エラーは、禁止文字を含むストレージ アカウント名を付けようとしたときに発生します。 ストレージ アカウント名の長さは 3 ～ 24 文字で、数字と小文字のみを使用する必要があります。 [uniqueString](resource-group-template-functions.md#uniquestring) 関数は 13 文字を返します。 **uniqueString** の結果にプレフィックスを連結する場合は、11 文字以下のプレフィックスを指定してください。

### <a name="badrequest"></a>BadRequest

プロパティに無効な値を指定した場合、BadRequest 状態が発生することがあります。 たとえば、ストレージ アカウントに誤った SKU 値を指定した場合、デプロイは失敗します。 プロパティの有効な値を判定するには、デプロイするリソース タイプの [REST API](/rest/api) を確認します。

<a id="noregisteredproviderfound" />
### <a name="noregisteredproviderfound-and-missingsubscriptionregistration"></a>NoRegisteredProviderFound と MissingSubscriptionRegistration
リソースをデプロイするときに、次のエラー コードとメッセージが表示される場合があります。

```
Code: NoRegisteredProviderFound
Message: No registered resource provider found for location {location}
and API version {api-version} for type {resource-type}.
```

または、次のようなメッセージが表示される場合があります。

```
Code: MissingSubscriptionRegistration
Message: The subscription is not registered to use namespace {resource-provider-namespace}
```

これらのエラーの原因として、次の&3; つの理由のいずれかが考えられます。

1. サブスクリプションに対してリソース プロバイダーが登録されていない
2. リソース タイプでサポートされた API バージョンに該当しない
3. リソース タイプでサポートされた場所に該当しない

サポートされる場所や API バージョンがエラー メッセージに提示されます。 提示されたいずれかの値を使用するようにテンプレートを変更してください。 ほとんどのプロバイダーは、Azure ポータルまたはご使用のコマンド ライン インターフェイスによって自動的に登録されますが、登録されない場合もあります。 まだ使ったことがないリソース プロバイダーについては、手動で登録しなければならない場合があります。 リソース プロバイダーの詳細については、PowerShell か Azure CLI で確認します。

**ポータル**

ポータルを利用すると、登録の状態を確認したり、リソース プロバイダー名前空間を登録したりできます。

1. サブスクリプションについては、**[リソース プロバイダー]** を選択します。

   ![リソース プロバイダーの選択](./media/resource-manager-common-deployment-errors/select-resource-provider.png)

2. リソース プロバイダーの一覧を確認し、必要に応じて **[登録]** リンクを選択して、デプロイするタイプのリソース プロバイダーを登録します。

   ![リソース プロバイダーの一覧](./media/resource-manager-common-deployment-errors/list-resource-providers.png)

**PowerShell**

登録の状態を確認するには、 **Get-AzureRmResourceProvider**を使用します。

```powershell
Get-AzureRmResourceProvider -ListAvailable
```

プロバイダーを登録するには、 **Register-AzureRmResourceProvider** を使用し、登録するリソース プロバイダーの名前を指定します。

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Cdn
```

特定のタイプのリソースでサポートされている場所を取得するには、次のコマンドを使用します。

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

特定のタイプのリソースでサポートされている API バージョンを取得するには、次のコマンドを使用します。

```powershell
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

**Azure CLI**

プロバイダーが登録されているかどうかを確認するには、 `azure provider list` コマンドを使用します。

```azurecli
az provider list
```

リソース プロバイダーを登録するには、 `azure provider register` コマンドを使用し、登録する *名前空間* を指定します。

```azurecli
az provider register --namespace Microsoft.Cdn
```

特定のリソースの種類に対してサポートされている場所と API バージョンを確認するには、次のコマンドを使用します。

```azurecli
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

<a id="quotaexceeded" />
### <a name="quotaexceeded-and-operationnotallowed"></a>QuotaExceeded および OperationNotAllowed
デプロイがクォータを超過した場合に問題が起こることがあります。この問題は、リソース グループ、サブスクリプション、アカウント、その他のスコープごとに起こる可能性があります。 たとえば、ご利用のサブスクリプションの構成により、リージョンごとのコア数が制限されている場合があります。 上限を超えたコア数の仮想マシンをデプロイしようとすると、クォータを超過したというエラーが発生します。
詳細については、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../azure-subscription-service-limits.md)」を参照してください。

コアのサブスクリプションのクォータを確認するには、Azure CLI で `azure vm list-usage` コマンドを使用します。 次に、無料試用版アカウントのコア クォータが 4 である例を示します。

```azurecli
az vm list-usage --location "South Central US"
```

次のような結果が返されます。

```azurecli
[
  {
    "currentValue": 0,
    "limit": 2000,
    "name": {
      "localizedValue": "Availability Sets",
      "value": "availabilitySets"
    }
  },
  ...
]
```

米国西部リージョンで&5; 個以上のコアを作成するテンプレートをデプロイした場合、次のようなデプロイ エラーが発生します。

```
Code=OperationNotAllowed
Message=Operation results in exceeding quota limits of Core.
Maximum allowed: 4, Current in use: 4, Additional requested: 2.
```

また、PowerShell では、 **Get-AzureRmVMUsage** コマンドレットを使用できます。

```powershell
Get-AzureRmVMUsage
```

次のような結果が返されます。

```powershell
...
CurrentValue : 0
Limit        : 4
Name         : {
                 "value": "cores",
                 "localizedValue": "Total Regional Cores"
               }
Unit         : null
...
```

このような場合はポータルに移動し、ファイルをデプロイするリージョンのクォータを増加させるように、サポートに問題を報告してください。

> [!NOTE]
> リソース グループの場合、クォータはサブスクリプション全体ではなく個々 のリージョンに対するものであることに注意してください。 米国西部に 30 のコアをデプロイする必要がある場合は、米国西部に 30 のリソース マネージャーのコアを要求する必要があります。 アクセスできるリージョンのいずれかで 30 のコアをデプロイする必要がある場合は、すべてのリージョンで 30 の Resource Manager コアを要求する必要があります。
>
>

### <a name="invalidcontentlink"></a>InvalidContentLink
次のようなエラー メッセージが発生することがあります。

```
Code=InvalidContentLink
Message=Unable to download deployment content from ...
```

これは、入れ子になった無効なテンプレートにリンクしようとしたことが原因と考えられます。 入れ子になったテンプレートに指定した URI を十分に確認してください。 ストレージ アカウントにテンプレートがある場合は、URI がアクセス可能であることを確認してください。 場合によっては、SAS トークンを渡す必要があります。 詳細については、「 [Azure リソース マネージャーでのリンクされたテンプレートの使用](resource-group-linked-templates.md)」を参照してください。

### <a name="requestdisallowedbypolicy"></a>RequestDisallowedByPolicy
このエラーは、デプロイ時に実行しようとしているアクションを禁止するリソース ポリシーがサブスクリプションに含まれている場合に発生します。 エラー メッセージでポリシー識別子を探します。

```
Policy identifier(s): '/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition'
```

**PowerShell** では、そのポリシー識別子を **Id** パラメーターとして指定して、デプロイをブロックしたポリシーの詳細を取得します。

```powershell
(Get-AzureRmPolicyDefinition -Id "/subscriptions/{guid}/providers/Microsoft.Authorization/policyDefinitions/regionPolicyDefinition").Properties.policyRule | ConvertTo-Json
```

**Azure CLI 2.0** では、ポリシー定義の名前を指定します。

```azurecli
az policy definition show --name regionPolicyAssignment
```

ポリシーについては、「 [ポリシーを使用したリソース管理とアクセス制御](resource-manager-policy.md)」をご覧ください。

### <a name="authorization-failed"></a>承認に失敗する
リソースをデプロイしようとしたアカウントまたはサービス プリンシパルに、そのアクションを実行するアクセス権がなかった場合、デプロイ時にエラーが発生することがあります。 Azure Active Directory では、どの ID がどのリソースにアクセスできるかをユーザーまたは管理者が高い精度で制御することができます。 たとえば、アカウントに閲覧者ロールが割り当てられている場合、リソースを作成することはできません。 この場合、承認が失敗したことを示すエラー メッセージが表示されます。

ロールベースのアクセス制御の詳細については、「 [Azure のロールベースのアクセス制御](../active-directory/role-based-access-control-configure.md)」を参照してください。

## <a name="troubleshooting-tricks-and-tips"></a>トラブルシューティングのコツとヒント

### <a name="enable-debug-logging"></a>デバッグ ログの有効化
要求、応答、またはこの両方をログに記録することで、デプロイの処理に関する有益な情報を見つけることができます。

- PowerShell

   PowerShell では、**DeploymentDebugLogLevel** パラメーターを All、ResponseContent、または RequestContent に設定します。

  ```powershell
  New-AzureRmResourceGroupDeployment -ResourceGroupName examplegroup -TemplateFile c:\Azure\Templates\storage.json -DeploymentDebugLogLevel All
  ```

   次のコマンドレットを使用して、要求の内容を確認します。

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.request | ConvertTo-Json
  ```

   次のコマンドレットを使用して、応答の内容を確認します。

  ```powershell
  (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName storageonly -ResourceGroupName startgroup).Properties.response | ConvertTo-Json
  ```

   この情報は、テンプレートの値が正しく設定されているかどうかを確認するのに役立ちます。

- Azure CLI 2.0

   次のコマンドでデプロイ操作を確認します。

  ```azurecli
  az group deployment operation list --resource-group ExampleGroup --name vmlinux
  ```

- 入れ子になったテンプレート

   入れ子になったテンプレートのデバッグ情報をログに記録するには、**debugSetting** 要素を使用します。

  ```json
  {
      "apiVersion": "2016-09-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri": "{template-uri}",
              "contentVersion": "1.0.0.0"
          },
          "debugSetting": {
             "detailLevel": "requestContent, responseContent"
          }
      }
  }
  ```


### <a name="create-a-troubleshooting-template"></a>トラブルシューティング用テンプレートの作成
場合によっては、テンプレートのトラブルシューティングの最も簡単な方法は、テンプレートの一部をテストすることです。 エラーの原因と考えられる部分に注目できる簡略化されたテンプレートを作成できます。 たとえば、リソースの参照時にエラーが発生しているとします。 この場合、テンプレート全体を処理するのではなく、問題の原因と考えられる部分を返すテンプレートを作成します。 これにより、適切なパラメーターを渡しているかどうか、テンプレート関数を正しく使用しているかどうか、求めているリソースを取得しているかどうかを確認できます。

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageName": {
        "type": "string"
    },
    "storageResourceGroup": {
        "type": "string"
    }
  },
  "variables": {},
  "resources": [],
  "outputs": {
    "exampleOutput": {
        "value": "[reference(resourceId(parameters('storageResourceGroup'), 'Microsoft.Storage/storageAccounts', parameters('storageName')), '2016-05-01')]",
        "type" : "object"
    }
  }
}
```

または、設定の誤った依存関係に関連すると思われるデプロイ エラーが発生していると仮定します。 テンプレートをテストするには、これを簡略化されたテンプレートに分解します。 最初に、1 つのリソース (SQL Server など) のみをデプロイするテンプレートを作成します。 そのリソースを正しく定義したことを確認したら、それに依存するリソース (SQL Database など) を追加します。 これら&2; つのリソースを正しく定義したことを確認したら、その他の依存リソース (監査ポリシーなど) を追加します。 各テスト デプロイの合間に、リソース グループを削除して、依存関係を適切にテストしていることを確認します。 

### <a name="check-deployment-sequence"></a>デプロイの順序の確認

リソースが予期しない順序でデプロイされると、多数のデプロイ エラーが発生します。 これらのエラーは、依存関係が正しく設定されていない場合に発生します。 必要な依存関係がないと、あるリソースが別のリソースの値を使用しようとしても、そのリソースがまだ存在しません。 リソースが見つからなかったことを示すエラー メッセージが表示されます。 リソースのデプロイ時間はそれぞれ異なるため、この種のエラーは断続的に発生する可能性があります。 たとえば、最初のリソースのデプロイの試行は成功しました。これは、必要なリソースがランダムで時間内に完了したためです。 しかし、2 回目の試行は失敗しました。これは、必要なリソースが時間内に完了しなかったためです。 

また、不要な依存関係を設定するのは望ましくありません。 不要な依存関係があると、相互に依存していないリソースを並行してデプロイすることを妨げるため、デプロイ時間が長くなります。 さらに、循環依存関係が作成されてデプロイがブロックされる恐れがあります。 あるリソースが同じテンプレートにデプロイされている場合、[参照](resource-group-template-functions.md#reference)関数はパラメーターとして指定するそのリソースに対する暗黙的な依存関係を作成します。 このため、**dependsOn** プロパティで指定したよりも多くの依存関係が作成されることがあります。 [resourceId](resource-group-template-functions.md#resourceid) 関数は暗黙的な依存関係を作成しません。また、リソースの存在を検証することもしません。

依存関係の問題が発生した場合は、リソースのデプロイ順序を把握する必要があります。 デプロイ操作の順序を確認するには、次の手順に従います。

1. リソース グループのデプロイ履歴を選択します。

   ![デプロイ履歴の選択](./media/resource-manager-common-deployment-errors/select-deployment.png)

2. 履歴からデプロイを選択し、**[イベント]** を選択します。

   ![デプロイ イベントの選択](./media/resource-manager-common-deployment-errors/select-deployment-events.png)

3. 各リソースのイベントの順序を調べます。 各操作の状態に注意してください。 たとえば、次の図には、並列でデプロイされた&3; つのストレージ アカウントが示されています。 3 つとも同時に開始されていることがわかります。

   ![並列デプロイ](./media/resource-manager-common-deployment-errors/deployment-events-parallel.png)

   次の図には、並列でデプロイされていない&3; つのストレージ アカウントが示されています。 2 つ目のストレージ アカウントは最初のストレージ アカウントに依存し、3 つ目のストレージ アカウントは&2; つ目のストレージ アカウントに依存します。 したがって、最初のストレージ アカウントの開始、受け入れ、完了の後に次のストレージ アカウントが開始されます。

   ![連続デプロイ](./media/resource-manager-common-deployment-errors/deployment-events-sequence.png)

実際のシナリオはかなり複雑になることがありますが、デプロイが開始および完了したタイミングは同じ手法を使用して検出できます。 デプロイ イベントの順序が想定と異なっていないかを確認します。 異なる場合は、そのリソースの依存関係を調べてください。

Resource Manager では、テンプレートの検証中に循環依存関係を識別します。 具体的に循環依存関係が存在することを示すエラー メッセージが返されます。 循環依存関係を解決するには:

1. テンプレートで、循環依存関係が検出されたリソースを検索します。 
2. **dependsOn** プロパティと**参照**関数の使用を確認し、そのリソースが依存しているリソースを調べます。 
3. それらのリソースが依存しているリソースを確認します。 元のリソースに依存するリソースを見つけるまで依存関係を辿ります。
5. 循環依存関係に関連するリソースについては、すべての **dependsOn** プロパティを調べ、不要な依存関係を特定します。 それら不要な依存関係を削除します。 依存関係が必要であるかどうか確証がない場合は、削除してみてください。 
6. テンプレートを再デプロイします。

**dependsOn** プロパティから値を削除すると、テンプレートをデプロイするときにエラーが発生することがあります。 エラーが発生した場合は、テンプレートの依存関係を元に戻します。 

このアプローチで循環依存関係が解決しない場合は、デプロイ ロジックの一部 (拡張機能や構成設定など) を子リソースに移行することを検討してください。 それらの子リソースを、循環依存関係に関連するリソースの後にデプロイするように構成します。 たとえば、2 つの仮想マシンをデプロイする場合を考えてみます。それぞれ互いに参照するプロパティを設定する必要があるとします。 この仮想マシンは、次の順序でデプロイできます。

1. vm1
2. vm2
3. vm1 の拡張機能は vm1 と vm2 に依存します。 拡張機能は vm2 から取得した値を vm1 に設定します。
4. vm2 の拡張機能は vm1 と vm2 に依存します。 拡張機能は vm1 から取得した値を vm2 に設定します。

このアプローチは App Service アプリでも有効です。 構成値をアプリ リソースの子リソースに移動することを検討してください。 2 つの Web アプリは次の順序でデプロイできます。

1. webapp1
2. webapp2
3. webapp1 の config は webapp1 と webapp2 に依存します。 アプリの設定には webapp2 からの値が含まれています。
4. webapp2 の config は webapp1 と webapp2 に依存します。 アプリの設定には webapp1 からの値が含まれています。

## <a name="troubleshooting-other-services"></a>その他のサービスのトラブルシューティング
前のデプロイ エラー コードで問題をトラブルシューティングできなかった場合、各 Azure サービスに関するもう少し詳しいトラブルシューティング ガイダンスを探すことができます。

次の表に、Virtual Machines のトラブルシューティング トピックを示します。

| エラー | 記事 |
| --- | --- |
| カスタム スクリプト拡張機能のエラー |[Windows VM 拡張機能のエラー](../virtual-machines/virtual-machines-windows-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />または<br />[Linux VM 拡張機能のエラー](../virtual-machines/virtual-machines-linux-extensions-troubleshoot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| OS イメージのプロビジョニング エラー |[新しい Windows VM のエラー](../virtual-machines/virtual-machines-windows-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />または<br />[新しい Linux VM のエラー](../virtual-machines/virtual-machines-linux-troubleshoot-deployment-new-vm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| 割り当ての失敗 |[Windows VM の割り当てエラー](../virtual-machines/virtual-machines-windows-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />または<br />[Linux VM の割り当てエラー](../virtual-machines/virtual-machines-linux-allocation-failure.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| 接続を試行するときの Secure Shell (SSH) エラー |[Linux VM への Secure Shell 接続](../virtual-machines/virtual-machines-linux-troubleshoot-ssh-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| VM で実行されているアプリケーションへの接続時に発生するエラー |[Windows VM で実行中のアプリケーション](../virtual-machines/virtual-machines-windows-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)<br />または<br />[Linux VM で実行中のアプリケーション](../virtual-machines/virtual-machines-linux-troubleshoot-app-connection.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) |
| リモート デスクトップ接続のエラー |[Windows VM へのリモート デスクトップ接続](../virtual-machines/virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| 再デプロイすることで解決する接続エラー |[新しい Azure ノードへの仮想マシンの再デプロイ](../virtual-machines/virtual-machines-windows-redeploy-to-new-node.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) |
| クラウド サービスのエラー |[クラウド サービスのデプロイの問題](../cloud-services/cloud-services-troubleshoot-deployment-problems.md) |

次の表に、その他の Azure サービスのトラブルシューティング トピックを示します。 リソースのデプロイまたは構成に関連した問題を中心に掲載しています。 リソースに関して実行時に発生する問題のトラブルシューティングについてご不明な点がある場合は、対応する Azure サービスのドキュメントを参照してください。

| サービス | 記事 |
| --- | --- |
| Automation |[Azure Automation の共通エラーのトラブルシューティングのヒント](../automation/automation-troubleshooting-automation-errors.md) |
| Azure Stack |[Microsoft Azure Stack のトラブルシューティング](../azure-stack/azure-stack-troubleshooting.md) |
| Data Factory |[Data Factory のトラブルシューティング](../data-factory/data-factory-troubleshoot.md) |
| Service Fabric |[Azure Service Fabric アプリケーションの監視と診断](../service-fabric/service-fabric-diagnostics-overview.md) |
| Site Recovery |[仮想マシンおよび物理サーバーの保護の監視とトラブルシューティング](../site-recovery/site-recovery-monitoring-and-troubleshooting.md) |
| Storage |[Microsoft Azure Storage の監視、診断、およびトラブルシューティング](../storage/storage-monitoring-diagnosing-troubleshooting.md) |
| StorSimple |[StorSimple デバイスのデプロイメントのトラブルシューティング](../storsimple/storsimple-troubleshoot-deployment.md) |
| SQL Database |[Azure SQL Database との接続に関する一般的な問題のトラブルシューティング](../sql-database/sql-database-troubleshoot-common-connection-issues.md) |
| SQL Data Warehouse |[Azure SQL Data Warehouse のトラブルシューティング](../sql-data-warehouse/sql-data-warehouse-troubleshoot.md) |

## <a name="next-steps"></a>次のステップ
* 監査アクションについては、「 [リソース マネージャーの監査操作](resource-group-audit.md)」をご覧ください。
* デプロイ時にエラーが発生した場合の対応については、 [デプロイ操作の確認](resource-manager-deployment-operations.md)に関するページを参照してください。

