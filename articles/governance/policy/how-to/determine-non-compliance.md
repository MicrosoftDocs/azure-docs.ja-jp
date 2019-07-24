---
title: コンプライアンス違反の原因の特定
description: リソースのコンプライアンス違反には多くの理由が考えられます。 コンプライアンス違反の原因を確認する方法について説明します。
author: DCtheGeek
ms.author: dacoulte
ms.date: 02/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 8a593e92d7f24885c35043b874528e881d2e021e
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2019
ms.locfileid: "59276101"
---
# <a name="determine-causes-of-non-compliance"></a>コンプライアンス違反の原因の特定

Azure リソースにポリシー ルールへのコンプライアンス違反が確認された場合は、リソースがルールのどの部分に準拠していないのかを理解することをお勧めします。 また、どのような変更によって、以前は準拠していたリソースがコンプライアンス違反となったのかを把握することも有用です。 この情報を検索するには 2 つの方法があります。

> [!div class="checklist"]
> - [コンプライアンスの詳細](#compliance-details)
> - [変更履歴 (プレビュー)](#change-history-preview)

## <a name="compliance-details"></a>コンプライアンスの詳細

リソースがコンプライアンス違反となった場合は、そのリソースのコンプライアンスの詳細を **[Policy compliance]** (ポリシー コンプライアンス) ページで確認できます。 [ポリシー準拠情報の詳細] ウィンドウには、次の情報が含まれています。

- 名前、種類、場所、リソース ID などのリソースの詳細
- コンプライアンス対応状態と、現在のポリシー割り当ての最後の評価のタイムスタンプ
- リソースのコンプライアンス違反の_理由_の一覧

> [!IMPORTANT]
> _コンプライアンス違反_のリソースのコンプライアンスの詳細に、該当のリソースでのプロパティの現在の値が表示されるときには、ユーザーにその**種類**のリソースに対する**読み取り**操作が必要です。 たとえば、_コンプライアンス違反_のリソースが **Microsoft.Compute/virtualMachines** の場合、ユーザーには、**Microsoft.Compute/virtualMachines/read** 操作が必要です。 ユーザーに必要な操作がない場合は、アクセス エラーが表示されます。

コンプライアンスの詳細を表示するには、次の手順に従います。

1. Azure portal 上で **[すべてのサービス]** をクリックし、**[ポリシー]** を検索して選択し、Azure Policy サービスを起動します。

1. **[概要]** ページまたは **[コンプライアンス]** ページで、**[コンプライアンスの状態]** が _[非対応]_ になっているポリシーを選択します。

1. **[Policy compliance]** (ポリシー コンプライアンス) ページの **[リソースのコンプライアンス]** タブで、**[コンプライアンスの状態]** が _[非対応]_ になっているリソースを右クリックするか、リソースの省略記号を選択します。 次に、**[ポリシー準拠状況の詳細]** を選択します。

   ![[ポリシー準拠状況の詳細] オプション](../media/determine-non-compliance/view-compliance-details.png)

1. **[ポリシー準拠状況の詳細]** ウィンドウに、現在のポリシー割り当てに対するリソースの最新の評価からの情報が表示されます。 この例では、フィールド **Microsoft.Sql/servers/version** が _12.0_ であることが検出されていますが、ポリシー定義では _14.0_ を必要としています。 リソースのコンプライアンス違反の理由が複数ある場合は、このウィンドウにそれぞれの理由が表示されます。

   ![[ポリシー準拠状況の詳細] ウィンドウおよびコンプライアンス違反の理由](../media/determine-non-compliance/compliance-details-pane.png)

   **AuditIfNotExists** または **deployIfNotExists** ポリシー定義について、詳細に **details.type** プロパティと省略可能なプロパティが含まれます。 一覧については、「[auditIfNotExists プロパティ](../concepts/effects.md#auditifnotexists-properties)」と「[deployIfNotExists プロパティ](../concepts/effects.md#deployifnotexists-properties)」を参照してください。 **最後に評価されたリソース**は、定義の **details** セクションからの関連リソースです。

   部分的な **deployIfNotExists** 定義の例:

   ```json
   {
       "if": {
           "field": "type",
           "equals": "[parameters('resourceType')]"
       },
       "then": {
           "effect": "DeployIfNotExists",
           "details": {
               "type": "Microsoft.Insights/metricAlerts",
               "existenceCondition": {
                   "field": "name",
                   "equals": "[concat(parameters('alertNamePrefix'), '-', resourcegroup().name, '-', field('name'))]"
               },
               "existenceScope": "subscription",
               "deployment": {
                   ...
               }
           }
       }
   }
   ```

   ![[ポリシー準拠状況の詳細] ウィンドウ - *ifNotExists](../media/determine-non-compliance/compliance-details-pane-existence.png)

> [!NOTE]
> データを保護するために、プロパティ値が _secret_ の場合は現在の値にアスタリスクが表示されます。

これらの詳細は、リソースが現在コンプライアンス違反であることの理由を説明するものですが、コンプライアンス違反の原因となった変更がリソースに対して行われたタイミングでは表示しません。 この情報を得るには、「[変更履歴 (プレビュー)](#change-history-preview)」を参照してください。

### <a name="compliance-reasons"></a>コンプライアンスの理由

次のマトリックスは、可能性のある各_理由_と、ポリシー定義内でのその[条件](../concepts/definition-structure.md#conditions)の対応を示しています。

|理由 | 条件 |
|-|-|
|現在の値には、キーとしてターゲット値を含める必要があります。 |containsKey、または notContainsKey **ではない** |
|現在の値には、ターゲット値を含める必要があります。 |contains、または notContains **ではない** |
|現在の値は、ターゲット値と等しくなければなりません。 |equals、または notEquals **ではない** |
|現在の値は存在している必要があります。 |exists |
|現在の値は、ターゲット値に含まれている必要があります。 |in、または notIn **ではない** |
|現在の値は、ターゲット値とパターン一致する必要があります。 |like、または notLike **ではない** |
|現在の値は、ターゲット値と一致する必要があります (大文字/小文字を区別する)。 |match、または notMatch **ではない** |
|現在の値は、ターゲット値と一致する必要があります (大文字/小文字を区別しない)。 |matchInsensitively、または notMatchInsensitively **ではない** |
|現在の値には、キーとしてターゲット値を含めることはできません。 |notContainsKey、または containsKey **ではない**|
|現在の値には、ターゲット値を含めることはできません。 |notContains、または contains **ではない** |
|現在の値を、ターゲット値と等しくすることはできません。 |notEquals、または equals **ではない** |
|現在の値は存在してはなりません。 |exists **ではない**  |
|現在の値は、ターゲット値に含まれていてはなりません。 |notIn、または in **ではない** |
|現在の値は、ターゲット値とパターン一致してはなりません。 |notLike、または like **ではない** |
|現在の値は、ターゲット値と一致してはなりません (大文字/小文字を区別する)。 |notMatch、または match **ではない** |
|現在の値は、ターゲット値と一致してはなりません (大文字/小文字を区別しない)。 |notMatchInsensitively、または matchInsensitively **ではない** |
|ポリシー定義の効果の詳細と一致する関連リソースがありません。 |**then.details.type** で定義されている種類のリソース、およびポリシー ルールの **if** 部分に定義されているリソースに関連したリソースが存在しません。 |

## <a name="change-history-preview"></a>変更履歴 (プレビュー)

新しい**パブリック プレビュー**の一環として、[完全モードの削除](../../../azure-resource-manager/complete-mode-deletion.md)をサポートするすべての Azure リソースについて、過去 14 日間の変更履歴が使用可能です。 変更履歴では、変更が検出された日時についての詳細と、各変更の "_差分表示_" が提供されます。 変更の検出は、Resource Manager のプロパティが追加、削除、変更されるとトリガーされます。

1. Azure portal 上で **[すべてのサービス]** をクリックし、**[ポリシー]** を検索して選択し、Azure Policy サービスを起動します。

1. **[概要]** ページまたは **[コンプライアンス]** ページで、任意の **[コンプライアンスの状態]** のポリシーを選択します。

1. **[Policy compliance]** (ポリシー コンプライアンス) ページの **[リソースのコンプライアンス]** タブで、リソースを選択します。

1. **[リソース コンプライアンス]** ページで **[Change History (preview)]\(変更履歴 (プレビュー)\)** タブを選択します。 検出された変更がある場合は、その一覧が表示されます。

   ![[リソース コンプライアンス] ページの [Policy Change History]\(ポリシーの変更履歴\) タブ](../media/determine-non-compliance/change-history-tab.png)

1. 検出された変更のいずれかを選択します。 **[変更履歴]** ページに、リソースの_差分表示_が示されます。

   ![[変更履歴] ページでのポリシーの変更履歴の差分表示](../media/determine-non-compliance/change-history-visual-diff.png)

"_差分表示_" は、リソースの変更を識別するのに役立ちます。 検出された変更が、リソースの現在のコンプライアンス対応状態に関連していない場合があります。

## <a name="next-steps"></a>次の手順

- [Azure Policy のサンプル](../samples/index.md)を確認する
- [ポリシー定義の構造](../concepts/definition-structure.md)を確認する
- [ポリシーの効果について](../concepts/effects.md)確認する
- [プログラムによってポリシーを作成する](programmatically-create.md)方法を理解する
- [コンプライアンス データを取得する](getting-compliance-data.md)ための方法を学びます。
- [準拠していないリソースを修復する](remediate-resources.md)方法を確認する
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。