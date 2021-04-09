---
title: チュートリアル:コンプライアンスを強制するポリシーを作成する
description: このチュートリアルでは、標準の強制、コストの制御、セキュリティの維持、および企業全体の設計原則の適用を行うポリシーを使用します。
ms.date: 01/29/2021
ms.topic: tutorial
ms.openlocfilehash: a643e7ccede4966719972694ea29eeb77789595e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99221195"
---
# <a name="tutorial-create-and-manage-policies-to-enforce-compliance"></a>チュートリアル:コンプライアンスを強制するポリシーの作成と管理

Azure でポリシーを作成および管理する方法を理解することは、企業の標準とサービス レベル アグリーメントへの準拠を維持するために重要です。 このチュートリアルでは、Azure Policy を使用して、組織全体のポリシーの作成、割り当て、および管理に関連する次のような一般的なタスクをいくつか実行します。

> [!div class="checklist"]
> - 今後作成するリソースに条件を強制するポリシーを割り当てる
> - 複数のリソースについてコンプライアンスを追跡するイニシアチブ定義を作成して割り当てる
> - 準拠していないリソースまたは拒否されたリソースを解決する
> - 組織全体で新しいポリシーを実施する

既存のリソースの現在のコンプライアンス状態を識別するためのポリシーを割り当てる方法については、クイック スタートの記事で詳しく説明しています。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="assign-a-policy"></a>ポリシーを割り当てる

Azure Policy でコンプライアンスを強制する最初の手順は、ポリシー定義の割り当てです。 ポリシー定義は、ポリシーを適用する条件とその効果を定義します。 この例では、"_存在しない場合は、リソース グループからタグを継承する_" という組み込みのポリシー定義を割り当てて、タグが存在しない新しいリソースまたは更新されたリソースに対し、親リソース グループの値を引き継ぐ特定のタグを追加します。

1. ポリシーを割り当てるには、Azure portal に移動します。 **Policy** を検索して選択します。

   :::image type="content" source="../media/create-and-manage/search-policy.png" alt-text="検索バーで Policy を検索する操作のスクリーンショット。" border="false":::

1. Azure Policy ページの左側にある **[割り当て]** を選択します。 割り当ては、特定のスコープ内で実行するように割り当てられたポリシーです。

   :::image type="content" source="../media/create-and-manage/select-assignments.png" alt-text="[ポリシーの概要] ページで [割り当て] ノードを選択する操作のスクリーンショット。" border="false":::

1. **[ポリシー - 割り当て]** ページの上部で **[ポリシーの割り当て]** を選択します。

   :::image type="content" source="../media/create-and-manage/select-assign-policy.png" alt-text="[割り当て] ページで [ポリシーの割り当て] ボタンを選択する操作のスクリーンショット。" border="false":::

1. **[ポリシーの割り当て]** ページの **[基本]** タブで、省略記号を選択し、管理グループまたはサブスクリプションのどちらかを選択して、 **[スコープ]** を選択します。 任意でリソース グループを選択します。 スコープによって、ポリシー割り当てを強制するリソースまたはリソースのグループが決まります。
   次に、 **[スコープ]** ページの下部にある **[選択]** を選択します。

   この例では、**Contoso** サブスクリプションを使用しています。 お客様によってサブスクリプションは異なります。

1. リソースは **スコープ** に基づいて除外できます。 **除外** は **スコープ** のレベルよりも 1 つ下のレベルで開始されます。 **除外** は省略可能です。ここでは空のまま残してください。

1. **[ポリシー定義]** の省略記号を選択して、使用可能な定義の一覧を開きます。 ポリシー定義の **[種類]** を _[ビルトイン]_ でフィルター処理して、すべてを表示し、その説明を読むことができます。

1. **[存在しない場合は、リソース グループからタグを継承する]** を選択します。 すぐに見つからない場合は、検索ボックスに「**タグを継承**」と入力して Enter キーを押すか、検索ボックスの外側を選択します。
   ポリシー定義を見つけて選択したら、 **[使用可能な定義]** ページの下部にある **[選択]** を選択します。

   :::image type="content" source="../media/create-and-manage/select-available-definition.png" alt-text="ポリシー定義選択時の検索フィルターのスクリーンショット。":::

1. **[割り当て名]** には選択したポリシー名が自動的に入力されますが、この名前は変更できます。 この例では、 _[存在しない場合は、リソース グループからタグを継承する]_ のままにします。 必要に応じて、**説明** を追加することもできます。 説明では、このポリシーの割り当ての詳細を示します。

1. **[ポリシーの適用]** を "_有効_" のままにしておきます。 "_無効_" になっている場合、この設定によって、効果をトリガーせずにポリシーの結果をテストできます。 詳細については、「[適用モード](../concepts/assignment-structure.md#enforcement-mode)」を参照してください。

1. **[割り当て担当者]** は、ログイン ユーザーに基づいて自動的に入力されます。 このフィールドは任意です。カスタム値を入力できます。

1. ウィザードの上部にある **[パラメーター]** タブを選択します。

1. **[タグ名]** に「_Environment_」と入力します。

1. ウィザードの上部にある **[修復]** タブを選択します。

1. **[修復タスクを作成する]** のチェックをオフのままにします。 このボックスを利用すると、新規または更新されたリソースだけでなく、既存のリソースを変更するタスクを作成できます。 詳細については、[修復リソース](../how-to/remediate-resources.md)に関する記事をご覧ください。

1. このポリシー定義では [modify](../concepts/effects.md#modify) 効果を使用しているため、 **[マネージド ID を作成します]** のチェックが自動的にオンになります。 **[アクセス許可]** は、自動的にポリシー定義に基づく "_共同作成者_" に設定されます。 詳しくは、[マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) および[修復セキュリティのしくみ](../how-to/remediate-resources.md#how-remediation-security-works)に関するページをご覧ください。

1. ウィザードの上部にある **[Non-compliance messages]\(コンプライアンス違反メッセージ\)** タブを選択します。

1. **[Non-compliance message]\(コンプライアンス違反メッセージ\)** を _[This resource doesn't have the required tag]\(このリソースには必要なタグがありません\)_ に設定します。 このカスタム メッセージは、リソースが拒否されたときに表示されるほか、コンプライアンス違反のリソースについては、通常の評価時に表示されます。

1. ウィザードの上部にある **[Review + create]\(確認と作成\)** タブを選択します。

1. 選択内容を確認してから、ページ下部にある **[作成]** を選択します。

## <a name="implement-a-new-custom-policy"></a>新しいカスタム ポリシーを実施する

組み込みのポリシー定義を割り当てたので、Azure Policy でさらに多くのことを行うことができます。 次に、G シリーズの仮想マシンをお客様の環境に作成できないことを確認することでコストを節約する新しいカスタム ポリシーを作成します。 そのようにすると、組織内のユーザーが G シリーズで仮想マシンを作成しようとするたびに、要求が拒否されます。

1. Azure Policy ページの左側にある **[作成]** の下の **[定義]** を選択します。

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="[作成] グループの下にある [定義] ページのスクリーンショット。" border="false":::

1. ページの上部にある **[+ ポリシー定義]** を選択します。 このボタンをクリックすると **[ポリシー定義]** ページが開きます。

1. 次の情報を入力します。

   - ポリシー定義を保存する管理グループまたはサブスクリプション。 **[定義の場所]** の省略記号を使用して選択します。

     > [!NOTE]
     > このポリシー定義を複数のサブスクリプションに適用する場合、この場所は、ポリシーを割り当てるサブスクリプションを含む管理グループである必要があります。 これはイニシアティブ定義の場合も同様です。

   - ポリシー定義の名前 - "_G シリーズにない VM SKU を必須にする_"
   - そのポリシー定義の目的の説明 - "_このポリシー定義では、コストを削減するために、このスコープ内で作成するすべての仮想マシンが、G シリーズ以外の SKU を使用することを強制しています。_ "
   - このポリシー定義用のカテゴリを既存のオプション ("_コンピューティング_" など) から選択するか、新規に作成します。
   - 次の JSON コードをコピーし、必要に応じて以下のものを使用して更新します。
      - ポリシー パラメーター。
      - ポリシー ルール/条件。この例では、VM SKU サイズが G シリーズと同じサイズです。
      - ポリシーの効果。この例では、**Deny** です。

   JSON の作成例は次のとおりです。 変更後のコードを Azure Portal に貼り付けます。

   ```json
   {
       "policyRule": {
           "if": {
               "allOf": [{
                       "field": "type",
                       "equals": "Microsoft.Compute/virtualMachines"
                   },
                   {
                       "field": "Microsoft.Compute/virtualMachines/sku.name",
                       "like": "Standard_G*"
                   }
               ]
           },
           "then": {
               "effect": "deny"
           }
       }
   }
   ```

   ポリシー ルールの _field_ プロパティは、サポートされている値にする必要があります。 値の完全な一覧については、[ポリシー定義構造のフィールド](../concepts/definition-structure.md#fields)に関する記事を参照してください。 `"Microsoft.Compute/VirtualMachines/Size"` はエイリアスの 1 つの例です。

   Azure Policy の他のサンプルについては、「[Azure Policy のサンプル](../samples/index.md)」を参照してください。

1. **[保存]** を選択します。

## <a name="create-a-policy-definition-with-rest-api"></a>REST API を使用したポリシー定義の作成

Azure Policy 定義用の REST API を使用して、ポリシーを作成することができます。 REST API を使用すると、ポリシー定義の作成と削除、既存の定義に関する情報の取得を実行できます。 ポリシー定義を作成するには、次の例を使用します。

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}
```

要求の本文は次の例のようになります。

```json
{
    "properties": {
        "parameters": {
            "allowedLocations": {
                "type": "array",
                "metadata": {
                    "description": "The list of locations that can be specified when deploying resources",
                    "strongType": "location",
                    "displayName": "Allowed locations"
                }
            }
        },
        "displayName": "Allowed locations",
        "description": "This policy enables you to restrict the locations your organization can specify when deploying resources.",
        "policyRule": {
            "if": {
                "not": {
                    "field": "location",
                    "in": "[parameters('allowedLocations')]"
                }
            },
            "then": {
                "effect": "deny"
            }
        }
    }
}
```

## <a name="create-a-policy-definition-with-powershell"></a>PowerShell を使用したポリシー定義の作成

PowerShell の例に進む前に、Azure PowerShell Az モジュールの最新バージョンがインストール済みであることを確認してください。

`New-AzPolicyDefinition` コマンドレットを使用してポリシー定義を作成することができます。

ファイルからポリシー定義を作成するには、ファイルへのパスを渡します。 外部ファイルの場合は、次の例を使用します。

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -DisplayName 'Deny cool access tiering for storage' `
    -Policy 'https://raw.githubusercontent.com/Azure/azure-policy-samples/master/samples/Storage/storage-account-access-tier/azurepolicy.rules.json'
```

ローカル ファイルの場合は、次の例を使用します。

```azurepowershell-interactive
$definition = New-AzPolicyDefinition `
    -Name 'denyCoolTiering' `
    -Description 'Deny cool access tiering for storage' `
    -Policy 'c:\policies\coolAccessTier.json'
```

インライン ルールでポリシー定義を作成するには、次の例を使用します。

```azurepowershell-interactive
$definition = New-AzPolicyDefinition -Name 'denyCoolTiering' -Description 'Deny cool access tiering for storage' -Policy '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

出力は、ポリシー割り当ての際に使用される `$definition` オブジェクトに格納されます。 次の例では、パラメーターを含むポリシー定義を作成しています。

```azurepowershell-interactive
$policy = '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "not": {
                    "field": "location",
                    "in": "[parameters(''allowedLocations'')]"
                }
            }
        ]
    },
    "then": {
        "effect": "Deny"
    }
}'

$parameters = '{
    "allowedLocations": {
        "type": "array",
        "metadata": {
            "description": "The list of locations that can be specified when deploying storage accounts.",
            "strongType": "location",
            "displayName": "Allowed locations"
        }
    }
}'

$definition = New-AzPolicyDefinition -Name 'storageLocations' -Description 'Policy to specify locations for storage accounts.' -Policy $policy -Parameter $parameters
```

### <a name="view-policy-definitions-with-powershell"></a>PowerShell を使用したポリシー定義の表示

サブスクリプション内のすべてのポリシー定義を表示するには、次のコマンドを使用します。

```azurepowershell-interactive
Get-AzPolicyDefinition
```

組み込みのポリシーを含め、すべての使用可能なポリシー定義が返されます。 各ポリシーは、次の形式で返されます。

```output
Name               : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceId         : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceName       : e56962a6-4747-49cd-b67b-bf8b01975c4c
ResourceType       : Microsoft.Authorization/policyDefinitions
Properties         : @{displayName=Allowed locations; policyType=BuiltIn; description=This policy enables you to
                     restrict the locations your organization can specify when deploying resources. Use to enforce
                     your geo-compliance requirements.; parameters=; policyRule=}
PolicyDefinitionId : /providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c
```

## <a name="create-a-policy-definition-with-azure-cli"></a>Azure CLI を使用したポリシー定義の作成

`az policy definition` コマンドによって、Azure CLI を使用してポリシー定義を作成します。 インライン ルールでポリシー定義を作成するには、次の例を使用します。

```azurecli-interactive
az policy definition create --name 'denyCoolTiering' --description 'Deny cool access tiering for storage' --rules '{
    "if": {
        "allOf": [{
                "field": "type",
                "equals": "Microsoft.Storage/storageAccounts"
            },
            {
                "field": "kind",
                "equals": "BlobStorage"
            },
            {
                "field": "Microsoft.Storage/storageAccounts/accessTier",
                "equals": "cool"
            }
        ]
    },
    "then": {
        "effect": "deny"
    }
}'
```

### <a name="view-policy-definitions-with-azure-cli"></a>Azure CLI を使用したポリシー定義の表示

サブスクリプション内のすべてのポリシー定義を表示するには、次のコマンドを使用します。

```azurecli-interactive
az policy definition list
```

組み込みのポリシーを含め、すべての使用可能なポリシー定義が返されます。 各ポリシーは、次の形式で返されます。

```json
{
    "description": "This policy enables you to restrict the locations your organization can specify when deploying resources. Use to enforce your geo-compliance requirements.",
    "displayName": "Allowed locations",
    "id": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "name": "e56962a6-4747-49cd-b67b-bf8b01975c4c",
    "policyRule": {
        "if": {
            "not": {
                "field": "location",
                "in": "[parameters('listOfAllowedLocations')]"
            }
        },
        "then": {
            "effect": "Deny"
        }
    },
    "policyType": "BuiltIn"
}
```

## <a name="create-and-assign-an-initiative-definition"></a>イニシアチブ定義の作成と割り当て

イニシアチブ定義では、複数のポリシー定義をグループ化して、1 つの包括的な目標を実現することができます。 イニシアチブは、割り当てのスコープ内のリソースについて、含まれているポリシーに準拠しているかどうかを評価します。 イニシアティブ定義の詳細については、[Azure Policy の概要](../overview.md)に関するページを参照してください。

### <a name="create-an-initiative-definition"></a>イニシアチブ定義を作成する

1. Azure Policy ページの左側にある **[作成]** の下の **[定義]** を選択します。

   :::image type="content" source="../media/create-and-manage/definition-under-authoring.png" alt-text="[作成] グループの下にある [定義] ページのスクリーンショット。":::

1. ページの上部にある **[+ イニシアティブ定義]** を選択して **イニシアティブ定義** ウィザードを開きます。

   :::image type="content" source="../media/create-and-manage/initiative-definition.png" alt-text="[イニシアティブ定義] ページと設定するプロパティのスクリーンショット。":::

1. **[イニシアティブの場所]** の省略記号を使用して、管理グループと、定義を格納するサブスクリプションを選択します。 前のページのスコープが 1 つの管理グループまたはサブスクリプションであった場合、 **[イニシアティブの場所]** は自動的に設定されます。

1. イニシアティブの **名前** と **説明** を入力します。

   この例では、セキュリティ保護に関するポリシー定義にリソースが準拠していることを確認します。 イニシアティブに "**セキュリティ保護**" という名前を付け、"**このイニシアティブは、リソースのセキュリティ保護に関連するすべてのポリシー定義を処理するために作成されました**" という説明を入力します。

1. **[カテゴリ]** で、カテゴリを既存のオプションから選択するか、新規に作成します。

1. イニシアティブの **[バージョン]** を設定します (_1.0_ など)。

   > [!NOTE]
   > バージョン値は厳密にメタデータであり、Azure Policy サービスによる更新やプロセスには使用されません。

1. ページの下部にある **[次へ]** を選択するか、ウィザードの上部にある **[ポリシー]** タブを選択します。

1. **[ポリシー定義の追加]** ボタンを選択し、一覧を参照します。 このイニシアティブに追加するポリシー定義を選択します。 **セキュリティ保護** イニシアティブの場合、ポリシー定義の横にあるチェックボックスをオンにして、次の組み込みポリシー定義を追加します。

   - 許可される場所
   - Endpoint Protection の欠落の Azure Security Center での監視
   -  インターネットに接続されていない仮想マシンをネットワーク セキュリティ グループで保護する必要があります
   - Virtual Machines に対して Azure Backup を有効にする必要がある
   - 仮想マシンでディスク暗号化を適用する必要がある
   - リソースでタグを追加または置換する (このポリシー定義を 2 回追加する)

   各ポリシー定義をリストから選択したら、リストの下部にある **[追加]** を選択します。
   これは 2 回追加されるため、"_リソースのタグを追加または置換する_" ポリシー定義がそれぞれ異なる "_参照 ID_" を受け取ります。

   :::image type="content" source="../media/create-and-manage/initiative-definition-2.png" alt-text="[イニシアティブ定義] ページで、ポリシー定義がその参照 ID とグループと共に選択されているスクリーンショット。":::

   > [!NOTE]
   > 選択したポリシー定義をグループに追加するには、追加された定義を 1 つ以上選択し、 **[選択したポリシーをグループに追加します]** を選択します。 まずグループが存在している必要があります。ウィザードの **[グループ]** タブで作成できます。

1. ページの下部にある **[次へ]** を選択するか、ウィザードの上部にある **[グループ]** タブを選択します。 このタブから新しいグループを追加できます。このチュートリアルでは、グループは追加しません。

1. ページの下部にある **[次へ]** を選択するか、ウィザードの上部にある **[イニシアティブ パラメーター]** タブを選択します。 1 つ以上の含まれているポリシー定義に渡すために、イニシアティブにパラメーターが存在するようにする場合は、パラメーターがここで定義され、 **[ポリシー パラメーター]** タブで使用されます。このチュートリアルでは、イニシアティブ パラメーターは追加しません。

   > [!NOTE]
   > イニシアティブ定義に保存したイニシアティブ パラメーターは、イニシアティブから削除することはできません。 イニシアティブ パラメーターが不要になった場合は、ポリシー定義パラメーターによって使用されないように削除します。

1. ページの下部にある **[次へ]** を選択するか、ウィザードの上部にある **[ポリシー パラメーター]** タブを選択します。

1. パラメーターを持つイニシアティブに追加されたポリシー定義は、グリッドに表示されます。 _[値の型]_ には、[既定値]、[値の設定]、または [イニシアティブ パラメーターを使用する] を指定できます。 [値の設定] が選択されている場合、関連する値が _[値]_ の下に入力されます。 ポリシー定義のパラメーターに許可された値のリストが含まれている場合、入力ボックスはドロップダウン セレクターになります。 [イニシアティブ パラメーターを使用する] が選択されている場合は、 **[イニシアティブ パラメーター]** タブで作成されたイニシアティブ パラメーターの名前がドロップダウン リストに表示されます。

   :::image type="content" source="../media/create-and-manage/initiative-definition-3.png" alt-text="[イニシアティブ定義] ページの [ポリシー パラメーター] タブで &quot;許可されている場所&quot; 定義パラメーターに対する許可されている値のオプションのスクリーンショット。":::

   > [!NOTE]
   > 一部の `strongType` パラメーターでは、値の一覧を自動的に決定することができません。 このような場合、パラメーター行の右側に省略記号が表示されます。 それを選択すると、[パラメーターのスコープ (&lt;パラメーター名&gt;)] ページが開きます。 このページで、値のオプションの指定に使用するサブスクリプションを選択します。 このパラメーターの範囲は、イニシアチブ定義の作成時にのみ使用され、ポリシーの評価またはイニシアチブ (割り当てられる場合) の範囲には影響しません。

   "許可されている場所" の _[値の型]_ を [値の設定] に設定し、ドロップダウンから '米国東部 2' を選択します。 "_リソースのタグを追加または置換する_" ポリシー定義の 2 つのインスタンスについては、次に示すように、**Tag Name** パラメーターを ' Env ' と 'CostCenter' に、**Tag Value** パラメーターを 'Test' および 'Lab' に設定します。 他は [既定値] のままにします。 イニシアティブで同じ定義を 2 回使用 (ただしパラメーターが異なる) すると、この構成によって、割り当てのスコープ内のリソースで、'Env' タグの場合は値 'Test'、'CostCenter' タグの場合は値 'Lab' が追加されるかこれらの値に置換されます。

   :::image type="content" source="../media/create-and-manage/initiative-definition-4.png" alt-text="[イニシアティブ定義] ページの [ポリシー パラメーター] タブで &quot;許可されている場所&quot; 定義パラメーターに対する許可されている値に入力されたオプションと、両方のタグ パラメーターに設定されている値のスクリーンショット。":::

1. ページの下部またはウィザードの上部にある **[確認と作成]** を選択します。

1. 設定を確認し、 **[作成]** を選択します。

#### <a name="create-a-policy-initiative-definition-with-azure-cli"></a>Azure CLI を使用したポリシー イニシアチブ定義の作成

`az policy set-definition` コマンドによって、Azure CLI を使用してポリシー イニシアチブ定義を作成できます。 既存のポリシー定義によってポリシー イニシアチブ定義を作成するには、次の例を使用します。

```azurecli-interactive
az policy set-definition create -n readOnlyStorage --definitions '[
        {
            "policyDefinitionId": "/subscriptions/mySubId/providers/Microsoft.Authorization/policyDefinitions/storagePolicy",
            "parameters": { "storageSku": { "value": "[parameters(\"requiredSku\")]" } }
        }
    ]' \
    --params '{ "requiredSku": { "type": "String" } }'
```

#### <a name="create-a-policy-initiative-definition-with-azure-powershell"></a>Azure PowerShell によるポリシー イニシアチブ定義の作成

`New-AzPolicySetDefinition` コマンドレットによって、Azure PowerShell を使用してポリシー イニシアチブ定義を作成できます。 既存のポリシー定義によってポリシー イニシアチブ定義を作成するには、次のポリシー イニシアチブ定義ファイルを `VMPolicySet.json` として使用します。

```json
[
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/2a0e14a6-b0a6-4fab-991a-187a4f81c498",
        "parameters": {
            "tagName": {
                "value": "Business Unit"
            },
            "tagValue": {
                "value": "Finance"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/464dbb85-3d5f-4a1d-bb09-95a9b5dd19cf"
    }
]
```

```azurepowershell-interactive
New-AzPolicySetDefinition -Name 'VMPolicySetDefinition' -Metadata '{"category":"Virtual Machine"}' -PolicyDefinition C:\VMPolicySet.json
```

### <a name="assign-an-initiative-definition"></a>イニシアチブ定義を割り当てる

1. Azure Policy ページの左側にある **[作成]** の下の **[定義]** を選択します。

1. 前に作成した **セキュリティ保護** イニシアティブ定義を見つけて選択します。 ページの上部にある **[割り当て]** を選択して **[セキュリティ保護: イニシアティブの割り当て]** ページを開きます。

   :::image type="content" source="../media/create-and-manage/assign-definition.png" alt-text="[イニシアティブ定義] ページの [割り当て] ボタンのスクリーンショット。" border="false":::

   また、選択した行を右クリックするか、行の末尾にある省略記号を選択して、コンテキスト メニューを表示することもできます。 次に、 **[割り当て]** を選択します。

   :::image type="content" source="../media/create-and-manage/select-right-click.png" alt-text="割り当て機能を選択するためのイニシアティブのコンテキスト メニューのスクリーンショット。" border="false":::

1. **[セキュリティ保護: イニシアティブの割り当て]** ページに、次のサンプル情報を入力します。 独自の情報を使用することもできます。

   - スコープ:イニシアティブを保存した管理グループまたはサブスクリプションが既定値になります。
     スコープを変更して、保存場所内のサブスクリプションまたはリソース グループにイニシアティブを割り当てることができます。
   - 除外: イニシアティブ割り当てが適用されないように、スコープ内の任意のリソースを構成します。
   - イニシアティブ定義と割り当て名: セキュリティ保護 (割り当てられるイニシアティブの名前として事前に入力されます)。
   - 説明:このイニシアティブ割り当ては、このポリシー定義グループを強制するために調整されています。
   - ポリシーの適用: 既定の " _[有効]_ " のままにします。
   - 割り当て担当者: ログイン ユーザーに基づいて自動的に入力されます。 このフィールドは任意です。カスタム値を入力できます。

1. ウィザードの上部にある **[パラメーター]** タブを選択します。 前の手順でイニシアチブ パラメーターを構成した場合は、ここで値を設定します。

1. ウィザードの上部にある **[修復]** タブを選択します。 **[マネージド ID を作成します]** のチェックは外しておいてください。 このボックスは、割り当てられるポリシーまたはイニシアティブに [deployIfNotExists](../concepts/effects.md#deployifnotexists) または [modify](../concepts/effects.md#modify) 効果を利用したポリシーが含まれる場合に、チェックをオンにする "_必要があります_"。 このチュートリアルで使用するポリシーにはそれが含まれないため、空のままにします。 詳しくは、[マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) および[修復セキュリティのしくみ](../how-to/remediate-resources.md#how-remediation-security-works)に関するページをご覧ください。

1. ウィザードの上部にある **[Review + create]\(確認と作成\)** タブを選択します。

1. 選択内容を確認してから、ページ下部にある **[作成]** を選択します。

## <a name="check-initial-compliance"></a>初期のコンプライアンスを確認する

1. Azure Policy ページの左側にある **[コンプライアンス]** を選択します。

1. **[Get Secure]\(セキュリティ保護\)** イニシアチブを探します。 おそらくまだ _[コンプライアンスの状態]_ は **[未開始]** です。
   割り当ての詳細情報を取得するには、イニシアティブを選択します。

   :::image type="content" source="../media/create-and-manage/compliance-status-not-started.png" alt-text="イニシアティブ コンプライアンス ページのスクリーンショット。割り当て評価が [未開始] 状態です。" border="false":::

1. イニシアティブの割り当てが完了すると、コンプライアンス ページが更新されて、 _[コンプライアンスの状態]_ は **[準拠している]** になります。

   :::image type="content" source="../media/create-and-manage/compliance-status-compliant.png" alt-text="イニシアティブ コンプライアンス ページのスクリーンショット。割り当て評価が完了しており、[準拠している] 状態です。" border="false":::

1. イニシアティブ コンプライアンス ページ上の任意のポリシーを選択すると、そのポリシーのコンプライアンス詳細ページが開きます。 このページでは、コンプライアンスのリソース レベルの詳細が提供されます。

## <a name="remove-a-non-compliant-or-denied-resource-from-the-scope-with-an-exclusion"></a>準拠していないまたは拒否されたリソースを除外してスコープから削除する

特定の場所を要求するようにポリシー イニシアチブを割り当てた後は、別の場所に作成されたリソースはすべて拒否されます。 このセクションでは、単一のリソース グループに対する除外を作成することで、リソースを作成する要求の拒否を解決する手順について説明します。 除外することで、そのリソース グループに対してポリシー (またはイニシアティブ) が適用されなくなります。 次の例では、除外されたリソース グループ内で任意の場所が許可されています。 除外は、サブスクリプション、リソース グループ、または個々のリソースに適用できます。

> [!NOTE]
> [ポリシーの適用除外](../concepts/exemption-structure.md)を使用して、リソースの評価をスキップすることもできます。 詳細については、[Azure Policy のスコープ](../concepts/scope.md)に関するページを参照してください。

割り当てられたポリシーまたはイニシアチブによって阻止されたデプロイは、デプロイの対象となるリソース グループ上で閲覧できます。ページの左側にある **[デプロイ]** を選択し、失敗したデプロイの **デプロイ名** を選択します。 拒否されたリソースが、"_禁止_" ステータスで表示されます。 リソースを拒否したポリシーまたはイニシアティブと割り当てを確認するには、[デプロイの概要] ページの **[失敗しました。詳細については、ここをクリックしてください]** をクリックします。 ページの右側にウィンドウが開き、エラー情報が表示されます。 **[エラーの詳細]** には、関連するポリシー オブジェクトの GUID が表示されます。

:::image type="content" source="../media/create-and-manage/rg-deployment-denied.png" alt-text="ポリシー割り当てで拒否されたデプロイ失敗のスクリーンショット。" border="false":::

Azure Policy ページでの操作: ページの左側にある **[コンプライアンス]** を選択し、**セキュリティ保護** のポリシー イニシアチブを選択します。 このページでは、ブロックされたリソースに対する **[拒否]** カウントが増加しています。 **[イベント]** タブ下には、ポリシー定義によって拒否されたリソースの作成またはデプロイを試行したユーザーに関する詳細が示されます。

:::image type="content" source="../media/create-and-manage/compliance-overview.png" alt-text="[イニシアティブ コンプライアンス] ページの [イベント] タブとポリシー イベント詳細のスクリーンショット。" border="false":::

この例では、Contoso における仮想化のシニアスペシャリストの 1 人である Trent Baker が必要な作業を行っていました。 Trent に例外のスペースを付与する必要があります。 新しいリソース グループ **LocationsExcluded** を作成してから、このポリシー割り当てへの例外を付与します。

### <a name="update-assignment-with-exclusion"></a>除外による割り当ての更新

1. Azure Policy ページの左側にある **[作成]** の下の **[割り当て]** を選択します。

1. すべてのポリシー割り当て一覧から、_セキュリティ保護_ のポリシー割り当てを開きます。

1. 省略記号を選択し、除外するリソース グループ (この例では、_LocationsExcluded_) を選択して、 **[除外]** を設定します。 **[選択したスコープに追加]** を選択してから、 **[保存]** を選択します。

   :::image type="content" source="../media/create-and-manage/request-exclusion.png" alt-text="[イニシアティブ割り当て] ページにある、除外されたリソース グループをポリシー割り当てに追加するための [除外] オプションのスクリーンショット。" border="false":::

   > [!NOTE]
   > ポリシー定義とその効果に応じて、割り当てのスコープ内にあるリソース グループの中の特定のリソースに除外を付与することもできます。 このチュートリアルでは **Deny** 効果が使用されたため、既に存在する特定のリソースに除外を設定することには意味がありません。

1. **[Review + save]\(確認と保存\)** を選択して、 **[保存]** を選択します。

このセクションでは、単一のリソース グループに対する除外を作成することによって、拒否された要求を解決しました。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルからリソースに対する作業を完了したら、次の手順を使用して、ここで作成したポリシー割り当てまたは定義をすべて削除します。

1. Azure Policy ページの左側にある **[作成]** の下の **[定義]** (または割り当てを削除する場合は **[割り当て]** ) を選択します。

1. 削除する新しいイニシアティブまたはポリシー定義 (または割り当て) を見つけます。

1. 行を右クリックするか、定義 (または割り当て) の末尾にある省略記号を選択し、 **[定義の削除]** (または **[割り当ての削除]** ) を選択します。

## <a name="review"></a>確認

このチュートリアルでは、以下のタスクを完了しました。

> [!div class="checklist"]
> - 今後作成するリソースに条件を強制するポリシーを割り当てる
> - 複数のリソースについてコンプライアンスを追跡するイニシアチブ定義を作成して割り当てる
> - 準拠していないリソースまたは拒否されたリソースを解決する
> - 組織全体で新しいポリシーを実施する

## <a name="next-steps"></a>次のステップ

ポリシー定義の構造の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Azure Policy の定義の構造](../concepts/definition-structure.md)