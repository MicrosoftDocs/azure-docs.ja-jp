---
title: テンプレートを使用した新しいポリシーの割り当て
description: このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用して、ポリシー割り当てを作成し、準拠していないリソースを特定します。
ms.date: 08/17/2020
ms.topic: quickstart
ms.custom: subject-armqs
ms.openlocfilehash: c32f833520a811d47129196ae4d67c92bad7e21f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "89651428"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-by-using-an-arm-template"></a>ARM テンプレートを使用して、準拠していないリソースを特定するためのポリシー割り当てを作成する

Azure のコンプライアンスを理解する第一歩は、リソースの状態を特定することです。
このクイックスタートでは、Azure Resource Manager テンプレート (ARM テンプレート) を使用してポリシー割り当てを作成し、マネージド ディスクを使用していない仮想マシンを特定するプロセスについて順を追って説明します。 このプロセスを終了すると、マネージド ディスクを使用していない仮想マシンを適切に特定できるようになります。 これらはポリシーの割り当てに "_準拠していません_"。

[!INCLUDE [About Azure Resource Manager](../../../includes/resource-manager-quickstart-introduction.md)]

環境が前提条件を満たしていて、ARM テンプレートの使用に慣れている場合は、 **[Azure へのデプロイ]** ボタンを選択します。 Azure portal でテンプレートが開きます。

:::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure Policy を Azure に割り当てるための ARM テンプレートをデプロイするボタン。" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="review-the-template"></a>テンプレートを確認する

このクイック スタートでは、ポリシー割り当てを作成し、"_マネージド ディスクを使用していない VM の監査_" という組み込みのポリシー定義を割り当てます。 使用できる組み込みポリシーの部分的な一覧については、[Azure Policy サンプル](./samples/index.md)に関する記事をご覧ください。

このクイックスタートで使用されるテンプレートは [Azure クイックスタート テンプレート](https://azure.microsoft.com/resources/templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/)からのものです。

:::code language="json" source="~/quickstart-templates/101-azurepolicy-assign-builtinpolicy-resourcegroup/azuredeploy.json":::

テンプレート内に定義されているリソース:

- [Microsoft.Authorization/policyAssignments](/azure/templates/microsoft.authorization/policyassignments)

## <a name="deploy-the-template"></a>テンプレートのデプロイ

> [!NOTE]
> Azure Policy サービスは無料です。 詳細については、[Azure Policy の概要](./overview.md)に関するページを参照してください。

1. 次のイメージを選択して、Azure portal にサインインし、テンプレートを開きます。

   :::image type="content" source="../../media/template-deployments/deploy-to-azure.svg" alt-text="Azure Policy を Azure に割り当てるための ARM テンプレートをデプロイするボタン。" border="false" link="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-azurepolicy-assign-builtinpolicy-resourcegroup%2Fazuredeploy.json":::

1. 次の値を選択または入力します。

   | 名前 | 値 |
   |------|-------|
   | サブスクリプション | Azure サブスクリプションを選択します。 |
   | Resource group | **[新規作成]** を選択し、名前を指定して、 **[OK]** を選択します。 スクリーンショットでは、リソース グループ名は _mypolicyquickstart\<Date in MMDD\>rg_ です。 |
   | 場所 | リージョンを選択します。 たとえば **[米国中部]** です。 |
   | ポリシーの割り当て名 | ポリシーの割り当て名を指定します。 必要に応じて、ポリシー定義の表示を使用できます。 たとえば、"_マネージド ディスクを使用していない VM の監査_" などです。 |
   | Rg Name \(RG 名\) | ポリシーを割り当てるリソース グループ名を指定します。 このクイック スタートでは、既定値 **[resourceGroup().name]** を使用します。 **[resourceGroup()](../../azure-resource-manager/templates/template-functions-resource.md#resourcegroup)** は、リソース グループを取得するテンプレート関数です。 |
   | ポリシー定義 ID | **/providers/Microsoft.Authorization/policyDefinitions/0a914e76-4921-4c19-b460-a2d36003525a** を指定します。 |
   | 上記の使用条件に同意する | (選択) |

1. **[購入]** を選択します。

いくつかのその他のリソース:

- その他のサンプル テンプレートについては、「[Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Authorization&pageNumber=1&sort=Popular)」を参照してください。
- テンプレート リファレンスを確認するには、[Azure テンプレート リファレンス](/azure/templates/microsoft.authorization/allversions)に関するページを参照してください。
- ARM テンプレートを開発する方法については、[Azure Resource Manager のドキュメント](../../azure-resource-manager/management/overview.md)を参照してください。
- サブスクリプション レベルのデプロイについては、「[サブスクリプション レベルでリソース グループとリソースを作成する](../../azure-resource-manager/templates/deploy-to-subscription.md)」を参照してください。

## <a name="validate-the-deployment"></a>デプロイの検証

ページの左側にある **[コンプライアンス]** を選択します。 次に、作成した "_Managed Disks を使用していない VM の監査_" ポリシー割り当てを見つけます。

:::image type="content" source="./media/assign-policy-template/policy-compliance.png" alt-text="ポリシーのコンプライアンスのページにあるコンプライアンスの詳細のスクリーンショット。" border="false":::

この新しい割り当てに準拠していない既存のリソースがある場合、**[準拠していないリソース]** の下に表示されます。

詳細については、「[コンプライアンスのしくみ](./how-to/get-compliance-data.md#how-compliance-works)」を参照してください。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

作成した割り当てを削除するには、次の手順のようにします。

1. Azure Policy ページの左側の **[コンプライアンス]** (または **[割り当て]**) を選択し、作成した "_Audit VMs that do not use managed disks\(マネージド ディスクを使用しない VM の監査\)_" ポリシーの割り当てを見つけます。

1. "_マネージド ディスクを使用しない VM の監査_" ポリシー割り当てを右クリックし、**[割り当ての削除]** を選択します。

   :::image type="content" source="./media/assign-policy-template/delete-assignment.png" alt-text="[コンプライアンス] ページからコンテキスト メニューを使用して割り当てを削除するスクリーンショット。" border="false":::

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、組み込みのポリシー定義をスコープに割り当て、コンプライアンス レポートを評価しました。 ポリシー定義では、スコープ内のすべてのリソースが準拠していることが検証されて、準拠していないリソースが識別されます。

新しいリソースが準拠していることを検証するためのポリシーの割り当てについて詳しく学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [ポリシーの作成と管理](./tutorials/create-and-manage.md)