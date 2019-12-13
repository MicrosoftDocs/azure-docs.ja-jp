---
title: ポリシーのコンプライアンス データを取得する
description: Azure Policy の評価と効果によって、コンプライアンスが決まります。 Azure リソースのコンプライアンスの詳細を取得する方法を説明します。
ms.date: 02/01/2019
ms.topic: how-to
ms.openlocfilehash: 891c9c72d8e83dc8f9adb930e8ebd11b70f6aad8
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/05/2019
ms.locfileid: "74873150"
---
# <a name="get-compliance-data-of-azure-resources"></a>Azure リソースのコンプライアンス データを取得する

Azure Policy の最大の利点の 1 つは、サブスクリプション内のリソースまたはサブスクリプションの[管理グループ](../../management-groups/overview.md)を介して提供される分析情報と制御です。 この制御は、さまざまな方法で実行できます。たとえば、リソースが誤った場所に作成されるのを防いだり、一般的で一貫性のあるタグの使用を強制したり、既存のリソースの適切な構成と設定を監査したりすることができます。 どの場合でも Azure Policy によってデータが生成され、環境のコンプライアンス状態の理解に役立てることができます。

ポリシーとイニシアティブ割り当てによって生成されたコンプライアンス情報には、次のようないくつかの方法でアクセスすることができます。

- [Azure ポータル](#portal)の使用
- [コマンド ライン](#command-line) スクリプトを通じて

コンプライアンスの報告方法を説明する前に、コンプライアンス情報がいつ更新されるかと、評価サイクルをトリガーする頻度とイベントについて説明します。

> [!WARNING]
> コンプライアンス状態が**未登録**と報告されている場合は、**Microsoft.PolicyInsights** リソース プロバイダーが登録されていること、ユーザーに適切なロールベースのアクセス制御 (RBAC) アクセス許可があることを確認します (詳しくは「[Azure Policy における RBAC アクセス許可](../overview.md#rbac-permissions-in-azure-policy)」をご覧ください)。

## <a name="evaluation-triggers"></a>評価のトリガー

完了した評価サイクルの結果は、`PolicyStates` および `PolicyEvents` 操作を通じて `Microsoft.PolicyInsights` リソース プロバイダーで使用可能です。 Azure Policy Insights REST API の操作の詳細については、「[Azure Policy Insights](/rest/api/policy-insights/)」を参照してください。

割り当てられたポリシーとイニシアティブの評価は、次のようなさまざまなイベントの結果として発生します。

- ポリシーまたはイニシアティブがスコープに新たに割り当てられる。 定義されたスコープに割り当てが適用されるまで、約 30 分かかります。 適用されると、新たに割り当てられたポリシーまたはイニシアティブに従って、そのスコープ内のリソースに対する評価サイクルが開始されます。リソースは、ポリシーまたはイニシアティブによって使用されている効果に応じて、準拠または非準拠としてマークされます。 大きなスコープのリソースに対する大きなポリシーまたはイニシアティブの評価には時間がかかることがあります。 そのため、評価サイクルがいつ完了するかを事前に予想することはできません。 完了すると、更新されたコンプライアンス結果をポータルと SDK で使用できるようになります。

- 既にスコープに割り当てられているポリシーまたはイニシアティブが更新される。 このシナリオでの評価サイクルとタイミングは、スコープへの新しい割り当ての場合と同じです。

- Resource Manager、REST、Azure CLI、または Azure PowerShell を介した割り当てで、リソースがスコープにデプロイされる。 このシナリオでは、個々のリソースに対する効果的なイベント (追加、監査、拒否、展開) とコンプライアンス ステータスの情報が、約 15 分後にポータルおよび SDKで利用可能です。 このイベントによって、他のリソースの評価が行われることはありません。

- 標準コンプライアンス評価サイクル。 24 時間に 1 回、割り当てが自動的に再評価されます。 多くのリソースの大きなポリシーまたはイニシアティブでは時間がかかることがあるため、評価サイクルがいつ完了するかを事前に予想することはできません。 完了すると、更新されたコンプライアンス結果をポータルと SDK で使用できるようになります。

- [ゲスト構成](../concepts/guest-configuration.md)リソース プロバイダーは、管理対象リソースによってコンプライアンスの詳細で更新されます。

- オンデマンド スキャン

### <a name="on-demand-evaluation-scan"></a>オンデマンドの評価スキャン

サブスクリプションまたはリソース グループの評価スキャンは、REST API の呼び出しで開始できます。 このスキャンは非同期プロセスです。 そのため、スキャンを開始する REST エンドポイントは、スキャンが応答を完了するまで待機しません。 代わりに、要求された評価の状態を照会する URI を提供します。

各 REST API URI には、独自の値で置き換える必要のある変数があります。

- `{YourRG}` - リソース グループの名前に置き換えます
- `{subscriptionId}` - サブスクリプション ID で置き換えます

スキャンは、サブスクリプションまたはリソース グループ内のリソースの評価をサポートしています。 次の URI の構造を使用して、REST API の **POST** コマンドでスコープによるスキャンを開始します。

- サブスクリプション

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

- Resource group

  ```http
  POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{YourRG}/providers/Microsoft.PolicyInsights/policyStates/latest/triggerEvaluation?api-version=2018-07-01-preview
  ```

この呼び出しは「**202 受理されました**」の状態を返します。 応答ヘッダーには、次の形式の **Location** プロパティが含まれています。

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/asyncOperationResults/{ResourceContainerGUID}?api-version=2018-07-01-preview
```

`{ResourceContainerGUID}` は、要求されたスコープに対して静的に生成されます。 スコープで既にオンデマンド スキャンが実行されている場合、新しいスキャンは開始されません。 代わりに、新しい要求で状態の同じ `{ResourceContainerGUID}` **場所**の URI が提供されます。 **場所**の URI に対する REST API の **GET** コマンドは、評価の進行中に「**202 受理されました**」を返します。 評価スキャンが完了すると、「**200 OK**」の状態を返します。 完了済みスキャンの本文は、状態を含む JSON 応答です。

```json
{
    "status": "Succeeded"
}
```

## <a name="how-compliance-works"></a>コンプライアンスのしくみ

割り当てのリソースがポリシーや統括ルールに従っていない場合、そのリソースは**非準拠**になります。
次の表は、さまざまなポリシーの効果での条件の評価と、その結果であるコンプライアンスの状態を示しています。

| リソースの状態 | 効果 | ポリシーの評価 | コンプライアンスの状態 |
| --- | --- | --- | --- |
| Exists | Deny、Audit、Append\*、DeployIfNotExist\*、AuditIfNotExist\* | True | 準拠していない |
| Exists | Deny、Audit、Append\*、DeployIfNotExist\*、AuditIfNotExist\* | False | 対応 |
| 新規 | Audit、AuditIfNotExist\* | True | 準拠していない |
| 新規 | Audit、AuditIfNotExist\* | False | 対応 |

\* Append、DeployIfNotExist、AuditIfNotExist の各効果では、IF ステートメントが TRUE である必要があります。
また、非準拠となるには、既存の条件が FALSE である必要があります。 TRUE のとき、IF 条件は関連するリソースの既存の条件の評価をトリガーします。

たとえば、ContosoRG というリソース グループがあり、このリソース グループの一部のストレージ アカウント (赤で強調表示されているアカウント) がパブリック ネットワークに公開されているとします。

![パブリック ネットワークに公開されているストレージ アカウント](../media/getting-compliance-data/resource-group01.png)

この例では、セキュリティ リスクに注意する必要があります。 これで、作成されたポリシー割り当てが、ContosoRG リソース グループ内のすべてのストレージ アカウントに対して評価されます。 3 つの非準拠のストレージ アカウントを監査し、結果としてそれらの状態を**非準拠**に変更します。

![監査された非準拠のストレージ アカウント](../media/getting-compliance-data/resource-group03.png)

**準拠**と**非準拠**の他に、ポリシーとリソースには次の 3 つの状態があります。

- **競合**:2 つ以上のポリシーで規則が競合しています。 たとえば、2 つのポリシーで同じタグに異なる値が追加されています。
- **未開始**:ポリシーまたはリソースの評価サイクルが開始されていません。
- **未登録**:Azure Policy リソース プロバイダーが登録されていないか、ログインしたアカウントにコンプライアンス データを読み取るアクセス許可がありません。

Azure Policy では、定義内の **type** と **name** フィールドを使用して、リソースの一致が判別されます。 リソースが一致している場合、適用可能と見なされ、**準拠**または**非準拠**のどちらかの状態になります。 **型**または**名前**のどちらかが定義の唯一のプロパティである場合は、すべてのリソースが適用可能と見なされ、評価されます。

コンプライアンス対応率は、**準拠している**リソースを _リソース合計_ で割って算出されます。
_リソース合計_ は、**準拠**、**非準拠**、**競合**の各リソースの合計と定義されています。 全体的なコンプライアンスの数値は、**準拠**している個別のリソースの合計を、すべての個別のリソースの合計で除算したものです。 次の図では、適用可能な個別のリソースが 20 個あり、そのうち 1 つだけが**非準拠**です。 全体的なリソース コンプライアンスは 95% (19/20) となります。

![[コンプライアンス] ページからのポリシー コンプライアンスの例](../media/getting-compliance-data/simple-compliance.png)

## <a name="portal"></a>ポータル

Azure portal には、環境のコンプライアンス状態を視覚化して理解するためのグラフィカルなエクスペリエンスが表示されます。 **[ポリシー]** ページの **[概要]** オプションでは、ポリシーとイニシアティブの両方のコンプライアンスに関して、利用可能なスコープの詳細が表示されます。 コンプライアンス状態および割り当てごとの数と共に、過去 7 日間のコンプライアンスを示すグラフが含まれます。 **[コンプライアンス]** ページには、この同じ情報の大部分 (グラフを除く) が含まれていますが、フィルター処理と並べ替えのオプションが追加されています。

![[Azure Policy コンプライアンス] ページの例](../media/getting-compliance-data/compliance-page.png)

ポリシーまたはイニシアティブは異なるスコープに割り当てることができるため、表には、各割り当てのスコープと割り当てられた定義の種類が含まれます。 各割り当ての準拠していないリソースおよび準拠していないポリシーの数も提供されます。 表のポリシーまたはイニシアティブをクリックすると、その特定の割り当てのコンプライアンスに関する詳しい情報が表示されます。

![[Azure Policy コンプライアンスの詳細] ページの例](../media/getting-compliance-data/compliance-details.png)

**[リソース コンプライアンス]** タブのリソース リストには、現在の割り当てに対する既存のリソースの評価状態が表示されます。 タブでは既定で **[非対応]** に設定されますが、これをフィルター処理することができます。
リソースの作成要求によってトリガーされるイベント (追加、監査、拒否、デプロイ) は、 **[イベント]** タブに表示されます。

> [!NOTE]
> AKS エンジン ポリシーの場合、表示されるリソースはリソース グループです。

![Azure Policy コンプライアンス イベントの例](../media/getting-compliance-data/compliance-events.png)

[リソース プロバイダー モード](../concepts/definition-structure.md#resource-provider-modes) リソースについては、 **[リソースのコンプライアンス]** タブで、リソースを選択するか、行を右クリックして **[ポリシー準拠状況の詳細]** を選択すると、コンポーネントのコンプライアンスの詳細が表示されます。 このページには、このリソースに割り当てられているポリシー、イベント、コンポーネント イベント、変更履歴を表示するためのタブも用意されています。

![Azure Policy コンポーネントのコンプライアンスの詳細の例](../media/getting-compliance-data/compliance-components.png)

リソースのコンプライアンス ページに戻って、詳細情報を収集するイベントの行を右クリックし、 **[アクティビティ ログの表示]** を選択します。 アクティビティ ログ ページが開き、事前にフィルター処理されて、割り当ておよびイベントの詳細が検索して表示されます。 アクティビティ ログは、これらのイベントに関する追加のコンテキストと情報を提供します。

![Azure Policy コンプライアンス アクティビティ ログの例](../media/getting-compliance-data/compliance-activitylog.png)

### <a name="understand-non-compliance"></a>コンプライアンス違反の把握

<a name="change-history-preview"></a>

リソースが**コンプライアンスに違反している**と判断される場合、多くの理由が考えられます。 リソースの**コンプライアンス違反**の理由や、原因となった変更を特定する方法については、「[コンプライアンス違反の原因の特定](./determine-non-compliance.md)」をご覧ください。

## <a name="command-line"></a>コマンド ライン

ポータルで利用できるのと同じ情報は、REST API ([ARMClient](https://github.com/projectkudu/ARMClient) を使用してなど)、Azure PowerShell、Azure CLI (プレビュー) を使用して取得することができます。
REST API の完全な詳細については、[Azure Policy Insights](/rest/api/policy-insights/) リファレンスを参照してください。 REST API のリファレンス ページには、各操作に緑色の [使ってみる] ボタンがあり、ブラウザーで直接試すことができます。

REST API の例については、ARMClient または同様のツールを使用して Azure に対する認証を処理します。

### <a name="summarize-results"></a>結果の要約

REST API を使用すると、コンテナー、定義、または割り当てごとに要約を実行できます。 Azure Policy Insights の [[Summarize For Subscription]\(サブスクリプションの要約\)](/rest/api/policy-insights/policystates/summarizeforsubscription) を使用した、サブスクリプション レベルでの要約の例を次に示します。

```http
POST https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/summarize?api-version=2018-04-04
```

出力はサブスクリプションを要約しています。 以下の出力例では、要約されたコンプライアンスは **value.results.nonCompliantResources** と **value.results.nonCompliantPolicies** の下にあります。 この要求では、より詳細な情報が提供されます。たとえば、非準拠として数えられている各割り当てや、各割り当ての定義情報などです。 階層内の各ポリシー オブジェクトは、そのレベルで追加の詳細情報を取得するために使用することができる **queryResultsUri** を提供しています。

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#summary/$entity",
        "results": {
            "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false",
            "nonCompliantResources": 15,
            "nonCompliantPolicies": 1
        },
        "policyAssignments": [{
            "policyAssignmentId": "/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77",
            "policySetDefinitionId": "",
            "results": {
                "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77'",
                "nonCompliantResources": 15,
                "nonCompliantPolicies": 1
            },
            "policyDefinitions": [{
                "policyDefinitionReferenceId": "",
                "policyDefinitionId": "/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
                "effect": "deny",
                "results": {
                    "queryResultsUri": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'",
                    "nonCompliantResources": 15
                }
            }]
        }]
    }]
}
```

### <a name="query-for-resources"></a>リソースのクエリ

上の例では、**value.policyAssignments.policyDefinitions.results.queryResultsUri** によって、特定のポリシー定義に準拠していないすべてのリソースのためのサンプル URI が提供されています。 **$filter** の値を見ると、IsCompliant は false に等しく (eq)、PolicyAssignmentId がポリシー定義に対して指定され、その後 PolicyDefinitionId 自体が指定されています。 フィルターに PolicyAssignmentId を含める理由は、PolicyDefinitionId がさまざまなスコープの複数のポリシーまたはイニシアティブ割り当てに存在する可能性があるためです。 PolicyAssignmentId と PolicyDefinitionId の両方を指定することにより、必要とする結果を明示することができます。 以前は、PolicyStates に **latest** を使用しました。この場合、直前の 24 時間の **from** および **to** 時間枠が自動的に設定されます。

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2018-04-04&$from=2018-05-18 04:28:22Z&$to=2018-05-19 04:28:22Z&$filter=IsCompliant eq false and PolicyAssignmentId eq '/subscriptions/{subscriptionId}/resourcegroups/rg-tags/providers/microsoft.authorization/policyassignments/37ce239ae4304622914f0c77' and PolicyDefinitionId eq '/providers/microsoft.authorization/policydefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62'
```

以下の応答例は、簡潔にするため、1 つの準拠していないリソースを示すようにトリミングされています。 詳細な応答には、リソース、ポリシーまたはイニシアティブ、および割り当てに関するいくつかのデータがあります。 ポリシー定義にどの割り当てパラメーターが渡されたかを確認することもできることに注意してください。

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 15,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
        "timestamp": "2018-05-19T04:41:09Z",
        "resourceId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Compute/virtualMachines/linux",
        "policyAssignmentId": "/subscriptions/{subscriptionId}/resourceGroups/rg-tags/providers/Microsoft.Authorization/policyAssignments/37ce239ae4304622914f0c77",
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "effectiveParameters": "",
        "isCompliant": false,
        "subscriptionId": "{subscriptionId}",
        "resourceType": "/Microsoft.Compute/virtualMachines",
        "resourceLocation": "westus2",
        "resourceGroup": "RG-Tags",
        "resourceTags": "tbd",
        "policyAssignmentName": "37ce239ae4304622914f0c77",
        "policyAssignmentOwner": "tbd",
        "policyAssignmentParameters": "{\"tagName\":{\"value\":\"costCenter\"},\"tagValue\":{\"value\":\"Contoso-Test\"}}",
        "policyAssignmentScope": "/subscriptions/{subscriptionId}/resourceGroups/RG-Tags",
        "policyDefinitionName": "1e30110a-5ceb-460c-a204-c1c3969c6d62",
        "policyDefinitionAction": "deny",
        "policyDefinitionCategory": "tbd",
        "policySetDefinitionId": "",
        "policySetDefinitionName": "",
        "policySetDefinitionOwner": "",
        "policySetDefinitionCategory": "",
        "policySetDefinitionParameters": "",
        "managementGroupIds": "",
        "policyDefinitionReferenceId": ""
    }]
}
```

### <a name="view-events"></a>イベントの表示

リソースを作成または更新すると、ポリシーの評価結果が生成されます。 これらの結果は "_ポリシー イベント_" と呼ばれます。 サブスクリプションに関連する最近のポリシー イベントを表示するには、次の URI を使用します。

```http
https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/default/queryResults?api-version=2018-04-04
```

次のような結果が返されます。

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default",
    "@odata.count": 1,
    "value": [{
        "@odata.id": null,
        "@odata.context": "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyEvents/$metadata#default/$entity",
        "NumAuditEvents": 16
    }]
}
```

ポリシー イベントについてクエリを実行する方法の詳細については、[Azure Policy のイベント](/rest/api/policy-insights/policyevents)に関する API リファレンスの記事を参照してください。

### <a name="azure-powershell"></a>Azure PowerShell

Azure Policy 用の Azure PowerShell モジュールは、PowerShell ギャラリーで [Az.PolicyInsights](https://www.powershellgallery.com/packages/Az.PolicyInsights) として入手できます。
PowerShellGet では、`Install-Module -Name Az.PolicyInsights` を使用してモジュールをインストールできます (最新の [Azure PowerShell](/powershell/azure/install-az-ps) がインストールされていることを確認してください)。

```azurepowershell-interactive
# Install from PowerShell Gallery via PowerShellGet
Install-Module -Name Az.PolicyInsights

# Import the downloaded module
Import-Module Az.PolicyInsights

# Login with Connect-AzAccount if not using Cloud Shell
Connect-AzAccount
```

モジュールには次のコマンドレットがあります。

- `Get-AzPolicyStateSummary`
- `Get-AzPolicyState`
- `Get-AzPolicyEvent`
- `Get-AzPolicyRemediation`
- `Remove-AzPolicyRemediation`
- `Start-AzPolicyRemediation`
- `Stop-AzPolicyRemediation`

例:準拠していないリソースの数が最も多い、最上位の割り当てられているポリシーの状態の要約を取得する。

```azurepowershell-interactive
PS> Get-AzPolicyStateSummary -Top 1

NonCompliantResources : 15
NonCompliantPolicies  : 1
PolicyAssignments     : {/subscriptions/{subscriptionId}/resourcegroups/RG-Tags/providers/micros
                        oft.authorization/policyassignments/37ce239ae4304622914f0c77}
```

例:直前に評価されたリソースの状態レコードを取得する (既定では降順のタイムスタンプに基づく)。

```azurepowershell-interactive
PS> Get-AzPolicyState -Top 1

Timestamp                  : 5/22/2018 3:47:34 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/networkInterfaces/linux316
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/networkInterfaces
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

例:準拠していないすべての仮想ネットワーク リソースの詳細情報を取得する。

```azurepowershell-interactive
PS> Get-AzPolicyState -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'"

Timestamp                  : 5/22/2018 4:02:20 PM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : westus2
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
```

例:特定の日付以降に発生した、準拠していない仮想ネットワーク リソースに関連するイベントを取得する。

```azurepowershell-interactive
PS> Get-AzPolicyEvent -Filter "ResourceType eq '/Microsoft.Network/virtualNetworks'" -From '2018-05-19'

Timestamp                  : 5/19/2018 5:18:53 AM
ResourceId                 : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Network/virtualNetworks/RG-Tags-vnet
PolicyAssignmentId         : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags/providers/Mi
                             crosoft.Authorization/policyAssignments/37ce239ae4304622914f0c77
PolicyDefinitionId         : /providers/Microsoft.Authorization/policyDefinitions/1e30110a-5ceb-460c-a204-c1c3969c6d62
IsCompliant                : False
SubscriptionId             : {subscriptionId}
ResourceType               : /Microsoft.Network/virtualNetworks
ResourceLocation           : eastus
ResourceGroup              : RG-Tags
ResourceTags               : tbd
PolicyAssignmentName       : 37ce239ae4304622914f0c77
PolicyAssignmentOwner      : tbd
PolicyAssignmentParameters : {"tagName":{"value":"costCenter"},"tagValue":{"value":"Contoso-Test"}}
PolicyAssignmentScope      : /subscriptions/{subscriptionId}/resourceGroups/RG-Tags
PolicyDefinitionName       : 1e30110a-5ceb-460c-a204-c1c3969c6d62
PolicyDefinitionAction     : deny
PolicyDefinitionCategory   : tbd
TenantId                   : {tenantId}
PrincipalOid               : {principalOid}
```

**PrincipalOid** フィールドは、Azure PowerShell コマンドレット `Get-AzADUser` を使用して特定のユーザーを取得するために使用できます。 **{principalOid}** を、前の例で取得した応答に置き換えます。

```azurepowershell-interactive
PS> (Get-AzADUser -ObjectId {principalOid}).DisplayName
Trent Baker
```

## <a name="azure-monitor-logs"></a>Azure Monitor ログ

サブスクリプションに関連付けられた [Activity Log Analytics ソリューション](../../../azure-monitor/platform/activity-log-collect.md)からの `AzureActivity` を使用した [Log Analytics ワークスペース](../../../log-analytics/log-analytics-overview.md)がある場合は、単純な Kusto クエリと `AzureActivity` テーブルを使用して、評価サイクルでの非準拠の結果を表示することもできます。 Azure Monitor ログの詳細情報を使用して、非準拠を監視するようにアラートを構成できます。


![Azure Monitor ログを使用した Azure Policy のコンプライアンス](../media/getting-compliance-data/compliance-loganalytics.png)

## <a name="next-steps"></a>次の手順

- [Azure Policy のサンプル](../samples/index.md)を確認します。
- 「[Azure Policy の定義の構造](../concepts/definition-structure.md)」を確認します。
- 「[Policy の効果について](../concepts/effects.md)」を確認します。
- [プログラムによってポリシーを作成する](programmatically-create.md)方法を理解します。
- [準拠していないリソースを修復する](remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。