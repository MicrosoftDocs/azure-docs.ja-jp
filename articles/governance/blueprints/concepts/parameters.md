---
title: パラメーターを使用して動的ブループリントを作成する
description: 静的パラメーターと動的パラメーターについて、およびこれらを使用して、セキュリティで保護された動的ブループリントを作成する方法について説明します。
ms.date: 01/27/2021
ms.topic: conceptual
ms.openlocfilehash: 5dbf7ec02e89eac791ec3e17202a5ab13a04b81d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98918536"
---
# <a name="creating-dynamic-blueprints-through-parameters"></a>パラメーターを使用して動的ブループリントを作成する

さまざまなアーティファクト (リソース グループ、Azure Resource Manager テンプレート (ARM テンプレート)、ポリシー、またはロールの割り当てなど) で完全に定義されたブループリントは、Azure 内のオブジェクトの迅速な作成と一貫性のある作成を提供します。 これらの再利用可能なデザイン パターンとコンテナーの柔軟な使用を可能にするため、Azure Blueprint ではパラメーターをサポートしています。 パラメーターを使用することで、ブループリントによってデプロイされたアーティファクトでプロパティを変更するため、定義と割り当ての最中の柔軟性が得られます。

単純な例が、リソース グループ アーティファクトです。 リソース グループを作成すると、そのグループは指定する必要がある 2 つの必須値 (名前と場所) を持ちます。 リソース グループをブループリントに追加するときに、パラメーターが存在していなかった場合は、ブループリントを使用するたびに名前と場所を定義することになります。 この繰り返しでブループリントを使用するたびに、同じリソース グループ内にアーティファクトが作成されることになります。 そのリソース グループ内でリソースが重複し、競合が発生する場合があります。

> [!NOTE]
> 2 つの異なるブループリントに同じ名前のリソース グループを含めることには問題はありません。
> ブループリントに含まれているリソース グループが既に存在している場合、ブループリントは関連するアーティファクトをそのリソース グループ内に引き続き作成します。 サブスクリプション内に同じ名前とリソースの種類を持つ 2 つのリソースが存在することはできないため、これにより競合が発生する可能性があります。

この問題の解決策は、パラメーターです。 Azure Blueprints では、サブスクリプションへの割り当て中に、アーティファクトの各プロパティの値を定義できます。 パラメーターにより、1 つのサブスクリプション内で競合することなく、リソース グループとその他のリソースを作成するブループリントを再利用することができます。

## <a name="blueprint-parameters"></a>ブループリントのパラメーター

REST API を使って、パラメーターをブループリント自体に作成できます。 これらのパラメーターは、サポートされている各アーティファクトの各パラメーターと異なります。 ブループリントでパラメーターを作成すると、そのブループリント内のアーティファクトで使用できます。 リソース グループの名前付けのプレフィックスはその一例です。 アーティファクトは、ブループリント パラメーターを使用して、"ほぼ動的な" パラメーターを作成できます。 パラメーターは割り当て中にも定義できるので、このパターンでは一貫性を考慮して、名前規則に従うことができます。 手順については、静的パラメーターの設定の「[Blueprint level parameter](#blueprint-level-parameter)」(ブループリント レベルのパラメーター) を参照してください。

### <a name="using-securestring-and-secureobject-parameters"></a>secureString パラメーターと secureObject パラメーターを使用する

ARM テンプレート "_アーティファクト_" は **secureString** と **secureObject** 型のパラメーターをサポートしていますが、Azure Blueprints は Azure Key Vault と接続するためにそれぞれを必要とします。 このセキュリティ手法では、ブループリントとシークレットを一緒に格納する安全性の低い手法を防止して、安全なパターンの使用を促します。 Azure Blueprints ではこのセキュリティ手法をサポートし、ARM テンプレートの "_アーティファクト_" にあるいずれかのセキュリティで保護されたパラメーターの包含を検出します。 検出された安全なパラメーターごとに次の Key Vault プロパティの割り当て時に、プロンプトが表示されます。

- Key Vault リソース ID
- Key Vault シークレット名
- Key Vault シークレット バージョン

ブループリント割り当てにおいて **システム割り当てマネージド ID** が使用されている場合、参照された Key Vault はブループリント定義が割り当てられている同じサブスクリプションに存在する _必要があります_。

ブループリント割り当てにおいて **ユーザー割り当てマネージド ID** が使用されている場合、参照された Key Vault は一元化されたサブスクリプションに存在する _可能性があります_。 ブループリント割り当て前に、マネージド ID には Key Vault に対する適切な権利が付与されている必要があります。

> [!IMPORTANT]
> いずれの場合も、Key Vault では **アクセス ポリシー** ページに構成されている **テンプレート デプロイの Azure Resource Manager にアクセスできる** 必要があります。 この機能を有効にする方法については、Key Vault の「[テンプレートのデプロイを有効にする](../../../azure-resource-manager/managed-applications/key-vault-access.md#enable-template-deployment)」を参照してください。

Azure Key Vault の詳細については、[Key Vault の概要](../../../key-vault/general/overview.md)ページを参照してください。

## <a name="parameter-types"></a>パラメーターの種類

### <a name="static-parameters"></a>静的パラメーター

どのブループリントを使用しても、静的な値を使用しているアーティファクトがデプロイされるので、ブループリントの定義の中で定義されたパラメーター値は、**静的パラメーター** と呼ばれます。 リソース グループの例では、リソース グループの名前には有効ではありませんが、場所には有効です。 ブループリントのすべての割り当てでは、割り当て時に呼び出されたものが何であれ、同じ場所にリソース グループが作成されます。 この柔軟性により、必要なときに定義するものと、割り当て時に変更できるものを選択できるようになります。

#### <a name="setting-static-parameters-in-the-portal"></a>ポータルで静的パラメーターを設定する

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側のページから **[ブループリントの定義]** を選択します。

1. 既存のブループリントを選択し、 **[ブループリントを編集する]** を選択するか、 **[+ ブループリントを作成する]** を選択して、 **[基本]** タブに情報を入力します。

1. **[Next:アーティファクト]** を選択するか、 **[アーティファクト]** タブを選択します。

1. パラメーター オプションを持つブループリントに追加されたアーティファクトの **[パラメーター]** 列に、 **[X of Y parameters populated]\(X/Y のパラメーターが設定されました\)** が表示されます。 パラメーターを編集するには、そのアーティファクト行を選択します。

   :::image type="content" source="../media/parameters/parameter-column.png" alt-text="ブループリントの定義と、[X of Y parameters populated]\(X/Y のパラメーターが設定されました\) のメッセージが強調表示されたスクリーンショット。" border="false":::

1. **[成果物の編集]** ページには、選択したアーティファクトに適した値のオプションが表示されます。 アーティファクトの各パラメーターには、タイトル、値ボックス、チェックボックスがあります。 **静的パラメーター** にするには、チェックボックスをオフに設定します。 次の例では、 _[場所]_ のチェックボックスがオフで、 _[リソース グループ名]_ のチェックボックスはオンになっているため、場所のみが **静的パラメーター** です。

   :::image type="content" source="../media/parameters/static-parameter.png" alt-text="ブループリント アーティファクトの静的パラメーターのスクリーンショット。" border="false":::

#### <a name="setting-static-parameters-from-rest-api"></a>REST API で静的パラメーターを設定する

各 REST API URI には、独自の値で置き換える必要のある変数があります。

- `{YourMG}` - 実際の管理グループの名前に置き換えます
- `{subscriptionId}` - サブスクリプション ID で置き換えます

##### <a name="blueprint-level-parameter"></a>ブループリント レベルのパラメーター

REST API を使用してブループリントを作成するときに、[ブループリントのパラメーター](#blueprint-parameters)を作成することができます。 これを行うには、次の REST API URI と本文の形式を使用します。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint?api-version=2018-11-01-preview
  ```

- 要求本文

  ```json
  {
      "properties": {
          "description": "This blueprint has blueprint level parameters.",
          "targetScope": "subscription",
          "parameters": {
              "owners": {
                  "type": "array",
                  "metadata": {
                      "description": "List of AAD object IDs that is assigned Owner role at the resource group"
                  }
              }
          },
          "resourceGroups": {
              "storageRG": {
                  "description": "Contains the resource template deployment and a role assignment."
              }
          }
      }
  }
  ```

ブループリント レベルのパラメーターを作成すると、そのブループリントに追加されるアーティファクトで使用できるようになります。
次の REST API の例では、ブループリントでロールの割り当てアーティファクトを作成し、ブループリント レベルのパラメーターを使用します。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/roleOwner?api-version=2018-11-01-preview
  ```

- 要求本文

  ```json
  {
      "kind": "roleAssignment",
      "properties": {
          "resourceGroup": "storageRG",
          "roleDefinitionId": "/providers/Microsoft.Authorization/roleDefinitions/8e3af657-a8ff-443c-a75c-2fe8c4bcb635",
          "principalIds": "[parameters('owners')]"
      }
  }
  ```

この例では、`[parameters('owners')]` の値を利用することで、**principalIds** プロパティで **owners** ブループリント レベルのパラメーターを使用しています。 ブループリント レベルのパラメーターを使用してアーティファクト上にパラメーターを設定しても、やはり **静的パラメーター** の 1 例になります。 ブループリント レベルのパラメーターは、ブループリントの割り当て中に設定することはできず、割り当てごとに同じ値になります。

##### <a name="artifact-level-parameter"></a>アーティファクト レベルのパラメーター

アーティファクトでの **静的パラメーター** の作成は似ていますが、`parameters()` 関数を使用する代わりに、直接値を受け取ります。 次の例では、2 つの静的パラメーター **tagName** と **tagValue** を作成します。 各値は直接提供され、関数呼び出しは使用しません。

- REST API URI

  ```http
  PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{YourMG}/providers/Microsoft.Blueprint/blueprints/MyBlueprint/artifacts/policyStorageTags?api-version=2018-11-01-preview
  ```

- 要求本文

  ```json
  {
      "kind": "policyAssignment",
      "properties": {
          "description": "Apply storage tag and the parameter also used by the template to resource groups",
          "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/49c88fc8-6fd1-46fd-a676-f12d1d3a4c71",
          "parameters": {
              "tagName": {
                  "value": "StorageType"
              },
              "tagValue": {
                  "value": "Premium_LRS"
              }
          }
      }
  }
  ```

### <a name="dynamic-parameters"></a>動的パラメーター

静的パラメーターの逆が **動的パラメーター** です。 このパラメーターは、ブループリント上に定義されるのではなく、ブループリントの割り当てのたびに定義されます。 リソース グループの例では、リソース グループ名には、**動的パラメーター** の使用が有効です。 ブループリントの割り当てごとに、別の名前を指定します。 ブルー プリントの関数の一覧は、「[blueprint functions](../reference/blueprint-functions.md)」 (ブループリント関数) を参照してください。

#### <a name="setting-dynamic-parameters-in-the-portal"></a>ポータルで動的パラメーターを設定する

1. 左側のウィンドウにある **[すべてのサービス]** を選択します。 **[ブループリント]** を探して選択します。

1. 左側のページから **[ブループリントの定義]** を選択します。

1. 割り当てるブループリントを右クリックします。 **[ブループリントの割り当て]** を選択するか、割り当てるブループリントを選択して、 **[ブループリントの割り当て]** ボタンを使用します。

1. **[ブループリントの割り当て]** ページで、 **[アーティファクト パラメーター]** セクションを見つけます。 少なくとも 1 つ以上の **動的パラメーター** を持つアーティファクトごとに、アーティファクトと構成オプションが表示されます。 ブループリントを割り当てる前に、パラメーターに必要な値を指定します。 次の例では、_Name_ がブループリント割り当てを完了するために定義する必要がある **動的パラメーター** です。

   :::image type="content" source="../media/parameters/dynamic-parameter.png" alt-text="ブループリントの割り当て時に動的パラメーターを設定するスクリーンショット。" border="false":::

#### <a name="setting-dynamic-parameters-from-rest-api"></a>REST API で動的パラメーターを設定する

割り当て時に **動的パラメーター** を設定するには、値を直接指定します。 [ パラメータ ()](../reference/blueprint-functions.md#parameters) などの関数を使用する代わりに、指定した値が適切な文字列になります。 リソース グループのアーティファクトは、"テンプレート名" と **name** および **location** プロパティで定義されます。 含まれているアーティファクトのその他すべてのパラメーターは、**parameters** の下で **\<name\>** と **value** のキーの組で定義されます。 割り当て時に指定されていない動的パラメーターにブループリントを構成すると、割り当ては失敗します。

- REST API URI

  ```http
  PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Blueprint/blueprintAssignments/assignMyBlueprint?api-version=2018-11-01-preview
  ```

- 要求本文

  ```json
  {
      "properties": {
          "blueprintId": "/providers/Microsoft.Management/managementGroups/{YourMG}  /providers/Microsoft.Blueprint/blueprints/MyBlueprint",
          "resourceGroups": {
              "storageRG": {
                  "name": "StorageAccount",
                  "location": "eastus2"
              }
          },
          "parameters": {
              "storageAccountType": {
                  "value": "Standard_GRS"
              },
              "tagName": {
                  "value": "CostCenter"
              },
              "tagValue": {
                  "value": "ContosoIT"
              },
              "contributors": {
                  "value": [
                      "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
                      "38833b56-194d-420b-90ce-cff578296714"
                  ]
                },
              "owners": {
                  "value": [
                      "44254d2b-a0c7-405f-959c-f829ee31c2e7",
                      "316deb5f-7187-4512-9dd4-21e7798b0ef9"
                  ]
              }
          }
      },
      "identity": {
          "type": "systemAssigned"
      },
      "location": "westus"
  }
  ```

## <a name="next-steps"></a>次のステップ

- [ブループリント関数](../reference/blueprint-functions.md)のリスト を参照してください。
- [ブループリントのライフサイクル](./lifecycle.md)を参照する。
- [ブループリントの優先順位](./sequencing-order.md)のカスタマイズを参照する。
- [ブループリントのリソース ロック](./resource-locking.md)の使用方法を調べる。
- [既存の割り当ての更新](../how-to/update-existing-assignments.md)方法を参照する。
- ブループリントの割り当て時の問題を[一般的なトラブルシューティング](../troubleshoot/general.md)で解決する。