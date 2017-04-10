---
title: "論理的に整理するための Azure リソースのタグ付け | Microsoft Docs"
description: "タグを適用して、課金や管理のために Azure リソースを整理する方法を示します。"
services: azure-resource-manager
documentationcenter: 
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 003a78e5-2ff8-4685-93b4-e94d6fb8ed5b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: AzurePortal
ms.devlang: na
ms.topic: article
ms.date: 02/15/2017
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: 0e1ee94504ebff235c1da9128e0ac68c2b28bc59
ms.openlocfilehash: 2f56314769d90a1f0f9ebb5ece9c8e54b23b8936
ms.lasthandoff: 02/21/2017


---
# <a name="use-tags-to-organize-your-azure-resources"></a>タグを使用した Azure リソースの整理
[!INCLUDE [resource-manager-tag-introduction](../../includes/resource-manager-tag-introduction.md)]

> [!NOTE]
> タグを適用できるのは、リソース マネージャーの操作をサポートするリソースのみです。 仮想マシン、仮想ネットワーク、またはストレージをクラシック デプロイ モデル (クラシック ポータルなど) を使用して作成した場合、そのリソースにタグを適用することはできません。 タグ付けをサポートするには、Resource Manager を介してこれらのリソースを再デプロイします。 その他のすべてのリソースでは、タグ付けがサポートされています。
> 
> 

## <a name="ensure-tag-consistency-with-policies"></a>ポリシーを使用してタグの一貫性を確保する

リソース ポリシーを使用して、組織の標準的なルールを作成することができます。 リソースが適切な値でタグ付けされることを保証するポリシーを作成することができます。 詳細については、「[タグに関するリソース ポリシーを適用する](resource-manager-policy-tags.md)」を参照してください。

## <a name="templates"></a>テンプレート

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>ポータル
[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="powershell"></a>PowerShell
[!INCLUDE [resource-manager-tag-resources-powershell](../../includes/resource-manager-tag-resources-powershell.md)]

## <a name="azure-cli-20"></a>Azure CLI 2.0

Azure CLI 2.0 では、リソースとリソース グループにタグを追加し、タグの値を使用してリソースをクエリすることができます。

リソースまたはリソース グループにタグを適用するたびに、そのリソースまたはリソース グループの既存のタグが上書きされます。 したがって、リソースまたはリソース グループに保持する必要がある既存のタグがあるかどうかに基づいて、異なるアプローチを使用する必要があります。 その方法を次に示します。

* タグのないリソース グループにタグを追加する。

  ```azurecli
  az group update -n TagTestGroup --set tags.Environment=Test tags.Dept=IT
  ```

* タグのないリソースにタグを追加する。

  ```azurecli
  az resource tag --tags Dept=IT Environment=Test -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
  ``` 

既にタグ付けされているリソースにタグを追加するには、最初に既存のタグを取得します。 

```azurecli
az resource show --query tags --output list -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
```

次の形式が返されます。

```
Dept        : Finance
Environment : Test
```

既存のタグをリソースを再適用し、新しいタグを追加します。

```azurecli
az resource tag --tags Dept=Finance Environment=Test CostCenter=IT -g TagTestGroup -n storageexample --resource-type "Microsoft.Storage/storageAccounts"
``` 

特定のタグを持つリソース グループを取得するには、`az group list` を使用します。

```azurecli
az group list --tag Dept=IT
```

特定のタグと値を持つすべてのリソースを取得するには、`az resource list` を使用します。

```azurecli
az resource list --tag Dept=Finance
```

## <a name="azure-cli-10"></a>Azure CLI 1.0
[!INCLUDE [resource-manager-tag-resources-cli](../../includes/resource-manager-tag-resources-cli.md)]

## <a name="rest-api"></a>REST API
ポータルと PowerShell のどちらも、バックグラウンドで [リソース マネージャーの REST AP](https://docs.microsoft.com/rest/api/resources/) を使用します。 別の環境にタグ付けを統合する必要がある場合、リソース ID に対する GET 操作でタグを取得し、PATCH 呼び出しでタグのセットを更新できます。

## <a name="tags-and-billing"></a>タグと課金
タグを使用して、課金データをグループ化できます。 たとえば、異なる組織向けに複数の VM を実行している場合は、タグを使用して、コスト センターごとに課金データをグループ化します。 また、タグを使用すると、運用環境で実行されている VM の課金データなどの、ランタイム環境ごとにコストを分類することもできます。


タグに関する情報は、 [Azure Resource Usage API と RateCard API](../billing/billing-usage-rate-card-overview.md) から、あるいはコンマ区切り値 (CSV) ファイルから取得できます。 使用状況ファイルは [Azure アカウント ポータル](https://account.windowsazure.com/)または [EA ポータル](https://ea.azure.com)からダウンロードします。 課金情報へのプログラムによるアクセスの詳細については、「 [Microsoft Azure リソースの消費を把握する](../billing/billing-usage-rate-card-overview.md)」を参照してください。 REST API の操作については、「 [Azure Billing REST API Reference (Azure Billing REST API リファレンス)](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c)」を参照してください。


課金のタグがサポートされているサービスの使用状況 CSV ファイルをダウンロードした場合、タグは「 **Tags** 」列に表示されます。 詳細については、「[Microsoft Azure の課金内容の確認](../billing/billing-understand-your-bill.md)」を参照してください。

![課金タグを参照してください](./media/resource-group-using-tags/billing_csv.png)

## <a name="next-steps"></a>次のステップ
* カスタマイズしたポリシーを使用して、サブスクリプションの制約と規則を適用できます。 定義するポリシーには、すべてのリソースが特定のタグに値が指定されていることが必要になる場合があります。 詳細については、「 [ポリシーを使用したリソース管理とアクセス制御](resource-manager-policy.md)」を参照してください。
* リソースのデプロイ時に Azure PowerShell を使用する方法の概要については、「 [Azure リソース マネージャーでの Windows PowerShell の使用](powershell-azure-resource-manager.md)」をご覧ください。
* リソースのデプロイ時に Azure CLI を使用する方法の概要については、「 [Azure リソース管理での Mac、Linux、および Windows 用 Azure CLI の使用](xplat-cli-azure-resource-manager.md)」をご覧ください。
* ポータルの使用方法の概要については、「 [Azure ポータルを使用した Azure リソースの管理](resource-group-portal.md)  
* 企業が Resource Manager を使用してサブスクリプションを効果的に管理する方法については、「[Azure enterprise scaffold - prescriptive subscription governance (Azure エンタープライズ スキャフォールディング - サブスクリプションの規範的な管理)](resource-manager-subscription-governance.md)」を参照してください。


