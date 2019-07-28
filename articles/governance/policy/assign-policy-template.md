---
title: Resource Manager テンプレートを使用したポリシー割り当ての作成
description: この記事では、Resource Manager テンプレートを使用して、準拠していないリソースを特定するためのポリシー割り当てを作成する手順について説明します。
author: dcthegeek
ms.author: dacoulte
ms.date: 03/13/2019
ms.topic: quickstart
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: f31d6197c22be4d66e0610ad7914f541a45ed995
ms.sourcegitcommit: 59fd8dc19fab17e846db5b9e262a25e1530e96f3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2019
ms.locfileid: "65979571"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-a-resource-manager-template"></a>クイック スタート:Resource Manager テンプレートを使用して、準拠していないリソースを特定するためのポリシー割り当てを作成する

Azure のコンプライアンスを理解する第一歩は、リソースの状態を特定することです。
このクイックスタートでは、ポリシーの割り当てを作成して、マネージド ディスクを使用していない仮想マシンを特定するプロセスについて順を追って説明します。

このプロセスを終了すると、マネージド ディスクを使用していない仮想マシンを適切に特定できるようになります。 これらはポリシーの割り当てに "*準拠していません*"。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="create-a-policy-assignment"></a>ポリシー割り当てを作成する

このクイック スタートでは、ポリシー割り当てを作成し、"*マネージド ディスクを使用していない VM の監査*" という組み込みのポリシー定義を割り当てます。 使用できる組み込みポリシーの部分的な一覧については、[Azure Policy サンプル](./samples/index.md)に関する記事をご覧ください。

ポリシー割り当てを作成する方法はいくつかあります。 このクイック スタートでは、[クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/)を使用します。
テンプレートのコピーを次に示します。

[!code-json[policy-assignment](~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json)]

> [!NOTE]
> Azure Policy サービスは無料です。  詳細については、[Azure Policy の概要](./overview.md)に関するページを参照してください。

1. 次のイメージを選択して、Azure portal にサインインし、テンプレートを開きます。

   [![ポリシーのテンプレートを Azure にデプロイする](./media/assign-policy-template/deploy-to-azure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json)

1. 次の値を選択または入力します。

   | Name | 値 |
   |------|-------|
   | サブスクリプション | Azure サブスクリプションを選択します。 |
   | リソース グループ | **[新規作成]** を選択し、名前を指定して、 **[OK]** を選択します。 スクリーンショットでは、リソース グループ名は *mypolicyquickstart\<MMDD 形式の日付>rg* です。 |
   | Location | リージョンを選択します。 たとえば **[米国中部]** です。 |
   | ポリシーの割り当て名 | ポリシーの割り当て名を指定します。 必要に応じて、ポリシー定義の表示を使用できます。 たとえば、"**マネージド ディスクを使用していない VM の監査**" などです。 |
   | Rg Name \(RG 名\) | ポリシーを割り当てるリソース グループ名を指定します。 このクイック スタートでは、既定値 **[resourceGroup().name]** を使用します。 **[resourceGroup()](../../azure-resource-manager/resource-group-template-functions-resource.md#resourcegroup)** は、リソース グループを取得するテンプレート関数です。 |
   | ポリシー定義 ID | **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a** を指定します。 |
   | 上記の使用条件に同意する | (選択) |

1. **[購入]** を選択します。

いくつかのその他のリソース:

- その他のサンプル テンプレートについては、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)」を参照してください。
- テンプレート リファレンスを確認するには、[Azure テンプレート リファレンス](/azure/templates/microsoft.authorization/allversions)に関するページを参照してください。
- Resource Manager テンプレートを開発する方法については、[Azure Resource Manager のドキュメント](/azure/azure-resource-manager/)を参照してください。
- サブスクリプション レベルのデプロイについては、「[サブスクリプション レベルでリソース グループとリソースを作成する](../../azure-resource-manager/deploy-to-subscription.md)」を参照してください。

## <a name="identify-non-compliant-resources"></a>準拠していないリソースを特定する

ページの左側にある **[コンプライアンス]** を選択します。 次に、作成した "**Managed Disks を使用していない VM の監査**" ポリシー割り当てを見つけます。

![ポリシーのコンプライアンスの概要ページ](./media/assign-policy-template/policy-compliance.png)

この新しい割り当てに準拠していない既存のリソースがある場合、 **[準拠していないリソース]** の下に表示されます。

詳細については、「[コンプライアンスのしくみ](./how-to/get-compliance-data.md#how-compliance-works)」を参照してください。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

作成した割り当てを削除するには、次の手順のようにします。

1. Azure Policy ページの左側の **[コンプライアンス]** (または **[割り当て]** ) を選択し、作成した "**Audit VMs that do not use managed disks\(マネージド ディスクを使用しない VM の監査\)** " ポリシーの割り当てを見つけます。

1. "**マネージド ディスクを使用しない VM の監査**" ポリシー割り当てを右クリックし、 **[割り当ての削除]** を選択します。

   ![コンプライアンスの概要ページから割り当てを削除する](./media/assign-policy-template/delete-assignment.png)

## <a name="next-steps"></a>次の手順

このクイック スタートでは、組み込みのポリシー定義をスコープに割り当て、コンプライアンス レポートを評価しました。 ポリシー定義では、スコープ内のすべてのリソースが準拠していることが検証されて、準拠していないリソースが識別されます。

新しいリソースが準拠していることを検証するためのポリシーの割り当てについて詳しく学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ポリシーの作成と管理](./tutorials/create-and-manage.md)