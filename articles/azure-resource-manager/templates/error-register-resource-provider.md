---
title: リソース プロバイダーの登録エラー
description: Azure Resource Manager でのリソースのデプロイ時の Azure リソース プロバイダーの登録エラーを解決する方法について説明します。
ms.topic: troubleshooting
ms.date: 02/15/2019
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 143cf03a33739f43a29af94fc2f8a336fb3aef8f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89076669"
---
# <a name="resolve-errors-for-resource-provider-registration"></a>リソース プロバイダーの登録エラーの解決

この記事では、これまでサブスクリプションで使用したことがないリソース プロバイダーを使用する際に発生する可能性のあるエラーについて説明します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="symptom"></a>症状

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

サポートされる場所や API バージョンがエラー メッセージに提示されます。 提示されたいずれかの値を使用するようにテンプレートを変更してください。 ほとんどのプロバイダーは、Azure portal またはご使用のコマンド ライン インターフェイスによって自動的に登録されますが、登録されない場合もあります。 まだ使ったことがないリソース プロバイダーについては、手動で登録しなければならない場合があります。

または、仮想マシンの自動シャットダウンを無効にすると、次のようなエラー メッセージを受け取ることがあります。

```
Code: AuthorizationFailed
Message: The client '<identifier>' with object id '<identifier>' does not have authorization to perform action 'Microsoft.Compute/virtualMachines/read' over scope ...
```

## <a name="cause"></a>原因

これらのエラーの原因として、次のいずれかの理由が考えられます。

* サブスクリプションに対して必要なリソース プロバイダーが登録されていない
* リソース タイプでサポートされた API バージョンに該当しない
* リソース タイプでサポートされた場所に該当しない
* VM を自動シャットダウンするには、Microsoft.DevTestLab リソース プロバイダーを登録する必要があります。

## <a name="solution-1---powershell"></a>解決策 1 - PowerShell

PowerShell では、**Get-AzResourceProvider** を使用して登録の状態を確認します。

```powershell
Get-AzResourceProvider -ListAvailable
```

プロバイダーを登録するには、**Register-AzResourceProvider** を使用し、登録するリソース プロバイダーの名前を指定します。

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.Cdn
```

特定のタイプのリソースでサポートされている場所を取得するには、次のコマンドを使用します。

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
```

特定のタイプのリソースでサポートされている API バージョンを取得するには、次のコマンドを使用します。

```powershell
((Get-AzResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
```

## <a name="solution-2---azure-cli"></a>解決策 2 - Azure CLI

プロバイダーが登録されているかどうかを確認するには、 `az provider list` コマンドを使用します。

```azurecli-interactive
az provider list
```

リソース プロバイダーを登録するには、 `az provider register` コマンドを使用し、登録する *名前空間* を指定します。

```azurecli-interactive
az provider register --namespace Microsoft.Cdn
```

特定のリソースの種類に対してサポートされている場所と API バージョンを確認するには、次のコマンドを使用します。

```azurecli-interactive
az provider show -n Microsoft.Web --query "resourceTypes[?resourceType=='sites'].locations"
```

## <a name="solution-3---azure-portal"></a>解決策 3 - Azure Portal

ポータルを利用すると、登録の状態を確認したり、リソース プロバイダー名前空間を登録したりできます。

1. Portal で **[すべてのサービス]** を選択します。

   ![[すべてのサービス] を選択する](./media/error-register-resource-provider/select-all-services.png)

1. **[サブスクリプション]** を選択します。

   ![[サブスクリプション] を選択する](./media/error-register-resource-provider/select-subscriptions.png)

1. サブスクリプションの一覧から、リソース プロバイダーの登録に使用するサブスクリプションを選択します。

   ![リソース プロバイダーを登録するサブスクリプションを選択する](./media/error-register-resource-provider/select-subscription-to-register.png)

1. サブスクリプションについては、**[リソース プロバイダー]** を選択します。

   ![[リソース プロバイダー] を選択する](./media/error-register-resource-provider/select-resource-provider.png)

1. リソース プロバイダーの一覧を確認し、必要に応じて **[登録]** リンクを選択して、デプロイするタイプのリソース プロバイダーを登録します。

   ![List resource providers](./media/error-register-resource-provider/list-resource-providers.png)
