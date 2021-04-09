---
title: クイックスタート - Azure Resource Manager テンプレートを使用して Azure Analysis Services サーバー リソースを作成する
description: Azure Resource Manager テンプレートを使用して Azure Analysis Services サーバー リソースを作成する方法についてのクイックスタート。
author: minewiskan
ms.author: owend
tags: azure-resource-manager
ms.service: azure-analysis-services
ms.topic: quickstart
ms.date: 08/31/2020
ms.custom: subject-armqs, references_regions
ms.openlocfilehash: 3e776bf41420d38a1b208ce11a6a34e97fa92a15
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "89230791"
---
# <a name="quickstart-create-a-server---arm-template"></a>クイック スタート:サーバーの作成 - ARM テンプレート

このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、Azure サブスクリプションに Analysis Services サーバー リソースを作成する方法について説明します。

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

[![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

## <a name="prerequisites"></a>前提条件

* **Azure サブスクリプション**: [Azure 無料試用版](https://azure.microsoft.com/offers/ms-azr-0044p/)でアカウントを作成します。
* **Azure Active Directory**: サブスクリプションは、Azure Active Directory テナントに関連付けられている必要があります。 また、その Azure Active Directory でアカウントを使用して Azure にサインインしている必要があります。 詳細については、「[認証とユーザーのアクセス許可](analysis-services-manage-users.md)」を参照してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-analysis-services-create/)からのものです。

:::code language="json" source="~/quickstart-templates/101-analysis-services-create/azuredeploy.json":::

このテンプレートには、1 つの [Microsoft.AnalysisServices/servers](/azure/templates/microsoft.analysisservices/servers) リソースがファイアウォール規則と共に定義されています。

## <a name="deploy-the-template"></a>テンプレートのデプロイ

1. Azure にサインインして、テンプレートを開くには、次の [Azure へのデプロイ] リンクを選択します。 このテンプレートを使用して、Analysis Services サーバー リソースを作成し、必須のプロパティと省略可能なプロパティを指定します。

   [![Azure へのデプロイ](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-analysis-services-create%2Fazuredeploy.json)

2. 次の値を選択または入力します。

    特に明記されていない場合は、既定値を使用してください。

    * **サブスクリプション**:Azure サブスクリプションを選択します。
    * **[リソース グループ]** : **[新規作成]** をクリックし、新しいリソース グループの一意の名前を入力します。
    * **[場所]** :リソース グループに作成したリソースの既定の場所を選択します。
    * **[サーバー名]** : サーバー リソースの名前を入力します。 
    * **[場所]** :Analysis Services の場合は無視してください。 場所は、[Server Location]\(サーバーの場所\) で指定します。
    * **[Server location]\(サーバーの場所\)** : Analysis Services サーバーの場所を入力します。 通常は、リソース グループに対して指定した既定の場所と同じリージョンになりますが、必須ではありません。 たとえば、「**米国中北部**」を入力します。 サポートされているリージョンについては、[リージョンごとの Analysis Services の利用の可否](analysis-services-overview.md#availability-by-region)を参照してください。
    * **[SKU 名]** : 作成する Analysis Services サーバーの SKU 名を入力します。 次の中から選択します。B1、B2、D1、S0、S1、S2、S3、S4、S8v2、S9v2。 SKU の利用の可否は、リージョンによって異なります。 評価とテストには、S0 または D1 をお勧めします。
    * **Capacity**:クエリ レプリカのスケールアウト インスタンスの総数を入力します。 複数インスタンスのスケールアウトは、一部のリージョンに限りサポートされます。
    * **[ファイアウォール設定]** : サーバー用に定義するインバウンド ファイアウォール規則を入力します。 指定しなかった場合、ファイアウォールは無効になります。
    * **[Backup Blob Container Uri]\(バックアップ BLOB コンテナー URI\)** : 読み取り、書き込み、リストの各アクセス許可を持つプライベート Azure Blob Storage コンテナーの SAS URI を入力します。 [バックアップと復元](analysis-services-backup.md)を使用する場合のみ必須です。
    * **上記の使用条件に同意する**: 選択。

3. **[購入]** を選択します。 サーバーが正常にデプロイされると、次の通知が表示されます。

   ![ARM テンプレート、デプロイのポータル通知](./media/analysis-services-create-template/notification.png)

## <a name="validate-the-deployment"></a>デプロイの検証

Azure portal または Azure PowerShell を使用して、リソース グループとサーバー リソースが作成されたことを確認します。

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
(Get-AzResource -ResourceType "Microsoft.AnalysisServices/servers" -ResourceGroupName $resourceGroupName).Name
 Write-Host "Press [ENTER] to continue..."
```

---

## <a name="clean-up-resources"></a>リソースをクリーンアップする

不要になったら、Azure portal、Azure CLI、Azure PowerShell のいずれかを使用してリソース グループとサーバー リソースを削除します。

# <a name="cli"></a>[CLI](#tab/CLI)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName &&
echo "Press [ENTER] to continue ..."
```

# <a name="powershell"></a>[PowerShell](#tab/PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
Remove-AzResourceGroup -Name $resourceGroupName
Write-Host "Press [ENTER] to continue..."
```

---

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、ARM テンプレートを使用して、新しいリソース グループと Azure Analysis Services サーバー リソースを作成しました。 テンプレートを使用してサーバー リソースを作成したら、以下のことを検討してください。

> [!div class="nextstepaction"]
> [クイック スタート: サーバーのファイアウォールの構成 - ポータル](analysis-services-qs-firewall.md)   
