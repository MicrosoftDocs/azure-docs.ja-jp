---
title: Azure Resource Manager テンプレートを使用して Azure Cache for Redis をデプロイする
description: Azure Resource Manager テンプレート (ARM テンプレート) を使用して Azure Cache for Redis リソースをデプロイする方法について説明します。 テンプレートは、一般的なシナリオ向けに用意されています。
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.custom: subject-armqs
ms.date: 08/18/2020
ms.openlocfilehash: 8bd9a45ec7c43d9338dec184afd784d2b163c410
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92735963"
---
# <a name="quickstart-create-an-azure-cache-for-redis-using-an-arm-template"></a>クイックスタート: ARM テンプレートを使用して Azure Cache for Redis を作成する

Azure Cache for Redis をデプロイする Azure Resource Manager テンプレート (ARM テンプレート) を作成する方法について説明します。 キャッシュを既存のストレージ アカウントで使用することで、診断データを保持することができます。 さらに、デプロイ対象のリソースを定義する方法と、デプロイの実行時に指定されるパラメーターを定義する方法についても説明します。 このテンプレートは、独自のデプロイに使用することも、要件に合わせてカスタマイズすることもできます。 現時点では、診断設定は、サブスクリプションの同じリージョン内のすべてのキャッシュで共有されます。 領域内の 1 つのキャッシュを更新すると、領域内の他のすべてのキャッシュに反映されます。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**:Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。
* **ストレージ アカウント**:これを作成するには、「[Azure Storage アカウントの作成](../storage/common/storage-account-create.md?tabs=azure-portal)」を参照してください。 ストレージ アカウントは、診断データに使用されます。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-redis-cache/)からのものです。

:::code language="json" source="~/quickstart-templates/101-redis-cache/azuredeploy.json":::

このテンプレートでは、次のリソースが定義されています。

* [Microsoft.Cache/Redis](/azure/templates/microsoft.cache/redis)
* [Microsoft.Insights/diagnosticsettings](/azure/templates/microsoft.insights/diagnosticsettings)

新しい [Premium レベル](cache-overview.md#service-tiers)の Resource Manager テンプレートも利用できます。

* [クラスタリングを使用する Premium Azure Cache for Redis の作成](https://azure.microsoft.com/resources/templates/201-redis-premium-cluster-diagnostics/)
* [データの永続化を使用する Premium Azure Cache for Redis の作成](https://azure.microsoft.com/resources/templates/201-redis-premium-persistence/)
* [仮想ネットワークにデプロイされる Premium Redis Cache の作成](https://azure.microsoft.com/resources/templates/201-redis-premium-vnet/)

最新のテンプレートを確認する場合は、「[Azure クイックスタート テンプレート](https://azure.microsoft.com/documentation/templates/)」で _Azure Cache for Redis_ を検索してください。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインし、テンプレートを開くには次の画像を選択します。

    [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-redis-cache%2Fazuredeploy.json)
1. 次の値を選択または入力します。

    * **[サブスクリプション]** : データ共有とその他のリソースの作成に使用する Azure サブスクリプションを選択します。
    * **[リソース グループ]** : **[新規作成]** を選択して新しいリソース グループを作成するか、既存のリソース グループを選択します。
    * **場所**: リソース グループの場所を選択します。 ストレージ アカウントと Redis Cache は同じリージョンに存在する必要があります。 既定では、Redis Cache はリソース グループと同じ場所を使用します。 そのため、ストレージ アカウントと同じ場所を指定します。
    * **Redis Cache 名**: Redis Cache の名前を入力します。
    * **既存の診断ストレージ アカウント**: ストレージ アカウントのリソース ID を入力します。 構文は `/subscriptions/&lt;SUBSCRIPTION ID>/resourceGroups/&lt;RESOURCE GROUP NAME>/providers/Microsoft.Storage/storageAccounts/&lt;STORAGE ACCOUNT NAME>` です。

    残りの設定については既定値を使用します。
1. **[上記の使用条件に同意する]** を選択し、 **[購入]** を選択します。

## <a name="review-deployed-resources"></a>デプロイされているリソースを確認する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 作成した Redis Cache を開きます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、リソース グループを削除します。これにより、リソース グループ内のリソースが削除されます。

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the resource group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure Cache for Redis をデプロイする Azure Resource Manager テンプレートを作成する方法について説明しました。 Azure Web アプリと Azure Cache for Redis をデプロイする Azure Resource Manager のテンプレートを作成する方法については、「[テンプレートを使用して Web アプリと Azure Cache for Redis を作成する](./cache-web-app-arm-with-redis-cache-provision.md)」を参照してください。
