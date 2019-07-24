---
title: コンプライアンスを強制するポリシーの作成と管理
description: Azure Policy を使用して、標準を強制し、規制遵守および監査の要件を満たし、コストを制御し、セキュリティとパフォーマンスの一貫性を維持し、企業全体の設計原則を適用します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/04/2019
ms.topic: tutorial
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 1641a88a95d4c056cdd1be8d855482c80b1430cc
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59283615"
---
# <a name="create-and-manage-policies-to-enforce-compliance"></a>コンプライアンスを強制するポリシーの作成と管理

Azure でポリシーを作成および管理する方法を理解することは、企業の標準とサービス レベル アグリーメントへの準拠を維持するために重要です。 このチュートリアルでは、Azure Policy を使用して、組織全体のポリシーの作成、割り当て、および管理に関連する次のような一般的なタスクをいくつか実行します。

> [!div class="checklist"]
> - 今後作成するリソースに条件を強制するポリシーを割り当てる
> - 複数のリソースについてコンプライアンスを追跡するイニシアチブ定義を作成して割り当てる
> - 準拠していないリソースまたは拒否されたリソースを解決する
> - 組織全体で新しいポリシーを実施する

既存のリソースの現在のコンプライアンス状態を識別するためのポリシーを割り当てる方法については、クイック スタートの記事で詳しく説明しています。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/) を作成してください。

## <a name="assign-a-policy"></a>ポリシーを割り当てる

Azure Policy でコンプライアンスを強制する最初の手順は、ポリシー定義の割り当てです。 ポリシー定義は、ポリシーを適用する条件とその効果を定義します。 この例では、*[SQL Server バージョン 12.0 を必須とする]* という組み込みのポリシー定義を割り当てて、すべての SQL Server データベースが v12.0 である必要があるという条件に準拠することを強制します。

1. Azure portal 上で **[すべてのサービス]** をクリックし、**[ポリシー]** を検索して選択し、Azure Policy サービスを起動します。

   ![[すべてのサービス] で [ポリシー] を検索する](../media/create-and-manage/search-policy.png)

1. Azure Policy ページの左側にある **[割り当て]** を選択します。 割り当ては、特定のスコープ内で実行するように割り当てられたポリシーです。

   ![ポリシーの概要ページで [割り当て] を選択する](../media/create-and-manage/select-assignments.png)

1. **[ポリシー - 割り当て]** ページの上部で **[ポリシーの割り当て]** を選択します。

   ![[割り当て] ページでポリシー定義を割り当てる](../media/create-and-manage/select-assign-policy.png)

1. **[ポリシーの割り当て]** ページで、**[スコープ]** を選択します。その際、省略記号をクリックし、管理グループまたはサブスクリプションを選択します。 任意でリソース グループを選択します。 スコープによって、ポリシー割り当てを強制するリソースまたはリソースのグループが決まります。  次に、**[スコープ]** ページの下部にある **[選択]** をクリックします。

   この例では、**Contoso** サブスクリプションを使用しています。 お客様によってサブスクリプションは異なります。

1. リソースは**スコープ**に基づいて除外できます。  **除外**は**スコープ**のレベルよりも 1 つ下のレベルで開始されます。 **除外**は省略可能です。ここでは空のまま残してください。

1. **[ポリシー定義]** の省略記号を選択して、使用可能な定義の一覧を開きます。 ポリシー定義の **[種類]** を *[ビルトイン]* でフィルター処理して、すべてを表示し、その説明を読むことができます。

1. **[SQL Server バージョン 12.0 を必須とする]** を選択します。 すぐに見つからない場合は、検索ボックスに「**SQL Server を必須とする**」と入力し、Enter キーを押すか、検索ボックスの外側をクリックします。 ポリシー定義を見つけて選択したら、**[使用可能な定義]** ページの下部にある **[選択]** をクリックします。

   ![検索フィルターを使用してポリシーを見つける](../media/create-and-manage/select-available-definition.png)

1. **[割り当て名]** には選択したポリシー名が自動的に入力されますが、この名前は変更できます。 この例では、"*SQL Server バージョン 12.0 を必須とする*" のままにしておきます。 必要に応じて、**説明**を追加することもできます。 説明では、このポリシーの割り当ての詳細を示します。  **[割り当て担当者]** は、ログイン ユーザーに基づいて自動的に入力されます。 このフィールドは任意です。カスタム値を入力できます。

1. **[マネージド ID を作成します]** のチェックは外しておいてください。 このボックスは、割り当てられるポリシーまたはイニシアティブに [deployIfNotExists](../concepts/effects.md#deployifnotexists) 効果を含むポリシーが含まれるときに選択する "_必要があります_"。 このチュートリアルで使用するポリシーにはそれが含まれないため、空のままにします。 詳しくは、[マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) および[修復セキュリティのしくみ](../how-to/remediate-resources.md#how-remediation-security-works)に関するページをご覧ください。

1. **[割り当て]** をクリックします。

## <a name="implement-a-new-custom-policy"></a>新しいカスタム ポリシーを実施する

組み込みのポリシー定義を割り当てたので、Azure Policy でさらに多くのことを行うことができます。 次に、G シリーズの VM をお客様の環境に作成できないことを確認することでコストを節約する新しいカスタム ポリシーを作成します。 そのようにすると、組織内のユーザーが G シリーズで VM を作成しようとするたびに、要求が拒否されます。

1. Azure Policy ページの左側にある **[作成]** の下の **[定義]** を選択します。

   ![[作成] グループの [定義] ページ](../media/create-and-manage/definition-under-authoring.png)

1. ページの上部にある **[+ ポリシー定義]** を選択します。 このボタンをクリックすると **[ポリシー定義]** ページが開きます。

1. 次の情報を入力します。

   - ポリシー定義を保存する管理グループまたはサブスクリプション。 **[定義の場所]** の省略記号を使用して選択します。

     > [!NOTE]
     > このポリシー定義を複数のサブスクリプションに適用する場合、この場所は、ポリシーを割り当てるサブスクリプションを含む管理グループである必要があります。 これはイニシアティブ定義の場合も同様です。

   - ポリシー定義の名前 - *G シリーズよりも小さい VM SKU を必須にする*
   - そのポリシー定義の目的の説明 - "*このポリシー定義では、コストを削減するために、このスコープ内で作成するすべての VM が、G シリーズよりも小さい SKU を使用することを強制しています。*"
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

   ポリシー ルールの *field* プロパティは、Name、Type、Location、Tags、エイリアスのいずれかの値であることが必要です。 `"Microsoft.Compute/VirtualMachines/Size"` はエイリアスの 1 つの例です。

   Azure ポリシーの他のサンプルについては、「[Azure Policy のサンプル](../samples/index.md)」をご覧ください。

1. **[保存]** を選択します。

## <a name="create-a-policy-definition-with-rest-api"></a>REST API を使用したポリシー定義の作成

ポリシー定義用の REST API を使用して、ポリシーを作成できます。 REST API を使用すると、ポリシー定義の作成と削除、既存の定義に関する情報の取得を実行できます。 ポリシー定義を作成するには、次の例を使用します。

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

Azure CLI でポリシー定義コマンドを使用して、ポリシー定義を作成できます。 インライン ルールでポリシー定義を作成するには、次の例を使用します。

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

   ![[定義] ページで定義を選択する](../media/create-and-manage/definition-under-authoring.png)

1. ページの上部にある **[+ イニシアティブ定義]** を選択して **[イニシアティブ定義]** ページを開きます。

   ![イニシアティブ定義のページを確認する](../media/create-and-manage/initiative-definition.png)

1. **[定義の場所]** の省略記号を使用して、管理グループと、定義を格納するサブスクリプションを選択します。 前のページのスコープが 1 つの管理グループまたはサブスクリプションであった場合、**[定義の場所]** は自動的に設定されます。

1. イニシアティブの**名前**と**説明**を入力します。

   この例では、セキュリティ保護に関するポリシー定義にリソースが準拠していることを確認します。 イニシアティブに "**セキュリティ保護**" という名前を付け、"**このイニシアティブは、リソースのセキュリティ保護に関連するすべてのポリシー定義を処理するために作成されました**" という説明を入力します。

1. **[カテゴリ]** で、カテゴリを既存のオプションから選択するか、新規に作成します。

1. **[使用可能な定義]** (**[イニシアティブ定義]** ページの右半分) の一覧から、このイニシアティブに追加するポリシー定義を選択します。 **セキュリティ保護**イニシアティブに対して、次の組み込みのポリシー定義を追加します。そのためには、ポリシー定義情報の横にある **[+]** をクリックするか、ポリシー定義行をクリックしてから詳細ページの **[+ 追加]** オプションをクリックします。

   - SQL Server バージョン 12.0 が必要
   - [Preview]: Monitor unprotected web applications in Security Center.
   - [Preview]: Monitor permissive network across in Security Center.
   - [Preview]: Monitor possible app Whitelisting in Security Center.
   - [Preview]: Monitor unencrypted VM Disks in Security Center.

   一覧からポリシー定義を選択すると、そのポリシー定義が **[ポリシーとパラメーター]** に追加されます。

   ![イニシアティブ定義のパラメーターを確認する](../media/create-and-manage/initiative-definition-2.png)

1. イニシアチブに追加されているポリシー定義にパラメーターがある場合、ポリシー名の **[ポリシーとパラメーター]** 領域に表示されます。 _値_ は、[値の設定] (このイニシアチブのすべての割り当てにハードコード) または [イニシアティブ パラメーターを使用する] (各イニシアチブの割り当て時に設定) のいずれかに設定できます。 [値の設定] を選択すると、_[値]_ の右に表示されるドロップダウンで値を入力するか選択することができます。 [イニシアティブ パラメーターを使用する] を選択すると、新しい **[イニシアチブ パラメーター]** セクションが表示され、イニシアチブ割り当て中に設定されるパラメーターを定義できます。 このイニシアチブ パラメーターに許可される値によって、イニシアチブ割り当て時に設定できる値をさらに制限できます。

   ![イニシアティブ定義のパラメーターを、許可されている値から変更する](../media/create-and-manage/initiative-definition-3.png)

   > [!NOTE]
   > 一部の `strongType` パラメーターでは、値の一覧を自動的に決定することができません。 このような場合、パラメーター行の右側に省略記号が表示されます。 それをクリックすると、[パラメーターのスコープ (&lt;パラメーター名&gt;)] ページが開きます。 このページで、値のオプションの指定に使用するサブスクリプションを選択します。 このパラメーターの範囲は、イニシアチブ定義の作成時にのみ使用され、ポリシーの評価またはイニシアチブ (割り当てられる場合) の範囲には影響しません。

1. **[Save]** をクリックします。

### <a name="assign-an-initiative-definition"></a>イニシアチブ定義を割り当てる

1. Azure Policy ページの左側にある **[作成]** の下の **[定義]** を選択します。

1. 前に作成した**セキュリティ保護**イニシアティブ定義を見つけてクリックします。 ページの上部にある **[割り当て]** を選択して **[セキュリティ保護: イニシアティブの割り当て]** ページを開きます。

   ![イニシアティブ定義のページで定義を割り当てる](../media/create-and-manage/assign-definition.png)

   また、選択した行を右クリックするか、行の末尾にある省略記号を左クリックして、コンテキスト メニューを表示することもできます。  次に、**[割り当て]** を選択します。

   ![イニシアティブの別のオプション](../media/create-and-manage/select-right-click.png)

1. **[セキュリティ保護: イニシアティブの割り当て]** ページに、次のサンプル情報を入力します。 独自の情報を使用することもできます。

   - スコープ: イニシアティブを保存した管理グループまたはサブスクリプションが既定値になります。  スコープを変更して、保存場所内のサブスクリプションまたはリソース グループにイニシアティブを割り当てることができます。
   - 除外: イニシアティブ割り当てが適用されないように、スコープ内の任意のリソースを構成します。
   - イニシアティブ定義と割り当て名: セキュリティ保護 (割り当てられるイニシアティブの名前として事前に入力されます)。
   - 説明:このイニシアティブ割り当ては、このポリシー定義グループを強制するために調整されています。
   - 割り当て担当者: ログイン ユーザーに基づいて自動的に入力されます。 このフィールドは任意です。カスタム値を入力できます。

1. **[マネージド ID を作成します]** のチェックは外しておいてください。 このボックスは、割り当てられるポリシーまたはイニシアティブに [deployIfNotExists](../concepts/effects.md#deployifnotexists) 効果を含むポリシーが含まれるときに選択する "_必要があります_"。 このチュートリアルで使用するポリシーにはそれが含まれないため、空のままにします。 詳しくは、[マネージド ID](../../../active-directory/managed-identities-azure-resources/overview.md) および[修復セキュリティのしくみ](../how-to/remediate-resources.md#how-remediation-security-works)に関するページをご覧ください。

1. **[割り当て]** をクリックします。

## <a name="check-initial-compliance"></a>初期のコンプライアンスを確認する

1. Azure Policy ページの左側にある **[コンプライアンス]** を選択します。

1. **セキュリティ保護**イニシアティブを探します。 おそらくまだ _[コンプライアンスの状態]_ は **[未開始]** です。 割り当ての進行状況の詳細情報を取得するには、イニシアティブをクリックします。

   ![イニシアティブのコンプライアンスのページ - 評価は未開始](../media/create-and-manage/compliance-status-not-started.png)

1. イニシアティブの割り当てが完了すると、コンプライアンス ページが更新されて、_[コンプライアンスの状態]_ は **[準拠している]** になります。

   ![イニシアティブのコンプライアンスのページ - リソースは準拠している](../media/create-and-manage/compliance-status-compliant.png)

1. イニシアティブ コンプライアンス ページの任意のポリシーをクリックすると、ポリシーのコンプライアンス詳細ページが開きます。 このページでは、コンプライアンスのリソース レベルの詳細が提供されます。

## <a name="exempt-a-non-compliant-or-denied-resource-using-exclusion"></a>準拠していないリソースまたは拒否されたリソースを免除する

上の例に従って、SQL Server バージョン 12.0 を必須にするポリシー定義を割り当てると、12.0 以外のバージョンで作成された SQL サーバーは拒否されます。 このセクションでは、単一のリソース グループに対する除外を作成して、SQL サーバーを作成する要求が拒否される件を解決する手順について説明します。 除外の対象として設定すると、そのリソースに対してポリシー (またはイニシアティブ) が適用されなくなります。
次の例では、単一のリソース グループ内で任意の SQL Server バージョンが許可されます。 除外をサブスクリプションやリソース グループに適用することも、個々のリソースだけに絞り込むこともできます。

割り当てられているポリシーまたはイニシアティブによって回避されたデプロイは、次の 2 つの場所で表示できます。

- デプロイの対象となるリソース グループでの操作: ページの左側にある **[デプロイ]** を選択し、失敗したデプロイの**デプロイ名**をクリックします。 拒否されたリソースが、"_禁止_" ステータスで表示されます。 リソースを拒否したポリシーまたはイニシアティブと割り当てを確認するには、[デプロイの概要] ページの **[失敗しました。詳細については、ここをクリックしてください]** をクリックします。 ページの右側にウィンドウが開き、エラー情報が表示されます。 **[エラーの詳細]** には、関連するポリシー オブジェクトの GUID が表示されます。

  ![ポリシーの割り当てによって拒否されたデプロイ](../media/create-and-manage/rg-deployment-denied.png)

- Azure Policy ページでの操作: ページの左側にある **[コンプライアンス]** を選択し、**[SQL Server バージョン 12.0 を必須とする]** ポリシーをクリックします。 表示されたページでは、**[拒否]** の値が増えています。 **[イベント]** タブでは、ポリシーで拒否されたデプロイを試みたユーザーを確認することもできます。

  ![割り当てられたポリシーのコンプライアンスの概要](../media/create-and-manage/compliance-overview.png)

この例では、Contoso における仮想化のシニアスペシャリストの 1 人である Trent Baker が必要な作業を行っていました。 Trent には例外を認める必要がありますが、バージョン 12.0 以外の SQL Server をどのリソース グループにも含めたくはありません。 そこで、新しいリソース グループ **SQLServers_Excluded** を作成しました。次に、このポリシー割り当ての例外を許可します。

### <a name="update-assignment-with-exclusion"></a>除外による割り当ての更新

1. Azure Policy ページの左側にある **[作成]** の下の **[割り当て]** を選択します。

1. すべてのポリシー割り当て一覧から、*[Require SQL Server version 12.0]\(SQL Server バージョン 12.0 が必要\)* 割り当てを開きます。

1. **[除外]** の省略記号をクリックし、除外するリソース グループ (この例では、*SQLServers_Excluded*) を選択して除外を設定します。

   ![ポリシー割り当てに除外するリソース グループを追加する](../media/create-and-manage/request-exclusion.png)

   > [!NOTE]
   > ポリシーとその効果に応じて、割り当てのスコープ内のリソース グループ内の特定のリソースを除外することもできます。 このチュートリアルでは **[拒否]** 効果が使用されていたため、既に存在する特定のリソースに除外を設定するのは意味がありません。

1. **[選択]** をクリックし、**[保存]** をクリックします。

このセクションでは、単一のリソース グループに対する除外を作成することによって、拒否された要求を解決しました。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルのリソースに対する作業が完了した場合は、次の手順を使用して、ここで作成した割り当てまたは定義をすべて削除してください。

1. Azure Policy ページの左側にある **[作成]** の下の **[定義]** (または割り当てを削除する場合は **[割り当て]**) を選択します。

1. 削除する新しいイニシアティブまたはポリシー定義 (または割り当て) を見つけます。

1. 行を右クリックするか、定義 (または割り当て) の末尾にある省略記号を選択し、**[定義の削除]** (または **[割り当ての削除]**) を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下のタスクを完了しました。

> [!div class="checklist"]
> - 今後作成するリソースに条件を強制するポリシーを割り当てる
> - 複数のリソースについてコンプライアンスを追跡するイニシアチブ定義を作成して割り当てる
> - 準拠していないリソースまたは拒否されたリソースを解決する
> - 組織全体で新しいポリシーを実施する

ポリシー定義の構造の詳細については、次の記事を参照してください。

> [!div class="nextstepaction"]
> [Azure Policy の定義の構造](../concepts/definition-structure.md)