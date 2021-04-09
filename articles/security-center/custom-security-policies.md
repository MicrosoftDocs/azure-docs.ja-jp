---
title: Azure Security Center でのカスタム セキュリティ ポリシーの作成 | Microsoft Docs
description: Azure Security Center で監視される Azure カスタム ポリシー定義。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/25/2021
ms.author: memildin
zone_pivot_groups: manage-asc-initiatives
ms.openlocfilehash: a901e71da640f8413e5714ad59073324f582c1b9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102441059"
---
# <a name="create-custom-security-initiatives-and-policies"></a>カスタム セキュリティ イニシアチブとポリシーを作成する

システムと環境のセキュリティ保護を支援するために、Azure Security Center ではセキュリティに関する推奨事項が生成されます。 これらの推奨事項は業界のベスト プラクティスに基づいており、すべてのお客様に提供される既定の汎用セキュリティ ポリシーに組み込まれています。 また、業界標準と規制基準に関する Security Center のナレッジから提供される場合もあります。

この機能を使用して、独自の "*カスタム*" イニシアティブを追加できます。 作成したポリシーに環境が従っていない場合は、推奨事項が提供されます。 作成したカスタム イニシアチブは、「[規制に対するコンプライアンスの向上](security-center-compliance-dashboard.md)」チュートリアルで説明されている規制コンプライアンス ダッシュボードの組み込みイニシアチブと一緒に表示されます。

[Azure Policy のドキュメント](../governance/policy/concepts/definition-structure.md#definition-location)で説明されているように、カスタム イニシアチブの場所を指定するとき、その場所は管理グループまたはサブスクリプションである必要があります。 

> [!TIP]
> このページの主要な概念の概要については、「[セキュリティポリシー、イニシアチブ、推奨事項とは](security-policy-concept.md)」を参照してください。

::: zone pivot="azure-portal"

## <a name="to-add-a-custom-initiative-to-your-subscription"></a>イニシアティブをサブスクリプションに追加するには 

1. Security Center のサイドバーから、 **[セキュリティ ポリシー]** ページを開きます。

1. カスタム イニシアティブを追加するサブスクリプションまたは管理グループを選択します。

    [![カスタム ポリシーを作成するサブスクリプションの選択](media/custom-security-policies/custom-policy-selecting-a-subscription.png)](media/custom-security-policies/custom-policy-selecting-a-subscription.png#lightbox)

    > [!NOTE]
    > Security Center でカスタム標準を評価および表示するためには、サブスクリプション レベル (またはそれ以上) でカスタム標準を追加する必要があります。 
    >
    > カスタム標準を追加すると、そのスコープに "*イニシアティブ*" が割り当てられます。 そのため、その割り当てに必要な最も広いスコープを選択することをお勧めします。

1. [セキュリティ ポリシー] ページの [Your custom initiatives]\(カスタム イニシアティブ\) で、 **[カスタム イニシアティブの追加]** をクリックします。

    [![[カスタム イニシアティブの追加] をクリック](media/custom-security-policies/custom-policy-add-initiative.png)](media/custom-security-policies/custom-policy-add-initiative.png#lightbox)

    次のページが表示されます。

    ![ポリシーの作成または追加](media/custom-security-policies/create-or-add-custom-policy.png)

1. [カスタム イニシアティブの追加] ページで、組織で既に作成されているカスタム ポリシーの一覧を確認します。 サブスクリプションに割り当てるイニシアティブが表示されている場合は、 **[追加]** をクリックします。 ニーズに合ったイニシアティブが一覧にない場合は、この手順をスキップしてください。

1. 新しいカスタム イニシアティブを作成するには、次の手順に従います。

    1. **[新規作成]** をクリックします。
    1. 定義の場所と名前を入力します。
    1. 含めるポリシーを選択し、 **[追加]** をクリックします。
    1. 必要なパラメーターを入力します。
    1. **[保存]** をクリックします。
    1. [カスタム イニシアティブの追加] ページで、更新をクリックします。 新しいイニシアティブが使用可能として表示されます。
    1. **[追加]** をクリックして、サブスクリプションに割り当てます。

    > [!NOTE]
    > 新しいイニシアティブを作成するには、サブスクリプション所有者の資格情報が必要です。 Azure ロールの詳細については、「[Azure Security Center におけるアクセス許可](security-center-permissions.md)」をご覧ください。

    新しいイニシアチブが有効になり、次の 2 つの方法で影響を確認できます。

    * Security Center のサイドバーの [ポリシーとコンプライアンス] で、 **[規制コンプライアンス]** を選択します。 コンプライアンス ダッシュボードが開き、組み込みイニシアチブと一緒に新しいカスタム イニシアチブが表示されます。
    
    * 定義したポリシーに環境が従っていない場合は、推奨事項を受け取り始めます。

1. ポリシーの生成された推奨事項を表示するには、サイドバーの **[推奨事項]** をクリックして推奨事項ページを開きます。 推奨事項は [カスタム] ラベル付きで表示され、約 1 時間以内に提供されます。

    [![カスタム推奨事項](media/custom-security-policies/custom-policy-recommendations.png)](media/custom-security-policies/custom-policy-recommendations-in-context.png#lightbox)

::: zone-end

::: zone pivot="rest-api"

## <a name="configure-a-security-policy-in-azure-policy-using-the-rest-api"></a>REST API を使用して Azure Policy でセキュリティ ポリシーを構成する

Azure Policy とのネイティブ統合の一環として、Azure Security Center では、Azure Policy の REST API を利用してポリシー割り当てを作成できます。 次の手順では、ポリシー割り当ての作成と既存の割り当てのカスタマイズの手順について説明します。 

Azure Policy で重要な概念: 

- **ポリシー定義** はルールです 

- **イニシアティブ** はポリシー定義 (ルール) のコレクションです 

- **割り当て** は、特定のスコープ (管理グループ、サブスクリプションなど) にイニシアティブまたはポリシーを適用することです 

Security Center では、そのセキュリティ ポリシーをすべて含んだ組み込みイニシアチブ (Azure セキュリティ ベンチマーク) があります。 Azure リソースに対する Security Center のポリシーを評価するには、評価する管理グループやサブスクリプションで割り当てを作成する必要があります。

組み込みイニシアティブでは、既定で Security Center のポリシーがすべて有効になっています。 組み込みのイニシアチブから特定のポリシーを無効にすることもできます。 たとえば、**Web アプリケーション ファイアウォール** を除くすべての Security Center のポリシーを適用するには、ポリシーの effect パラメーターの値を **Disabled** に変更します。

## <a name="api-examples"></a>API の例

以下の例では、次のように変数を置き換えてください。

- **{scope}** には、ポリシーを適用する管理グループまたはサブスクリプションの名前を入力します
- **{poicyAssignmentName}** には、関連するポリシー割り当ての名前を入力します
- **{name}** には、名前、またはポリシーの変更を承認した管理者の名前を入力します

この例では、サブスクリプションまたは管理グループで組み込み Security Center イニシアティブを割り当てる方法を示します
 
 ```
    PUT  
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 

    Request Body (JSON) 

    { 

      "properties":{ 

    "displayName":"Enable Monitoring in Azure Security Center", 

    "metadata":{ 

    "assignedBy":"{Name}" 

    }, 

    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 

    "parameters":{}, 

    } 

    } 
 ```

この例では、次のポリシーを無効にして、サブスクリプションで組み込み Security Center イニシアティブを割り当てる方法を示します。 

- システムの更新プログラム ("systemUpdatesMonitoringEffect") 

- セキュリティ構成 ("systemConfigurationsMonitoringEffect") 

- エンドポイント保護 ("endpointProtectionMonitoringEffect") 

 ```
    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
    
    Request Body (JSON) 
    
    { 
    
      "properties":{ 
    
    "displayName":"Enable Monitoring in Azure Security Center", 
    
    "metadata":{ 
    
    "assignedBy":"{Name}" 
    
    }, 
    
    "policyDefinitionId":"/providers/Microsoft.Authorization/policySetDefinitions/1f3afdf9-d0c9-4c3d-847f-89da613e70a8", 
    
    "parameters":{ 
    
    "systemUpdatesMonitoringEffect":{"value":"Disabled"}, 
    
    "systemConfigurationsMonitoringEffect":{"value":"Disabled"}, 
    
    "endpointProtectionMonitoringEffect":{"value":"Disabled"}, 
    
    }, 
    
     } 
    
    } 
 ```
この例では、割り当てを削除する方法を示します。
 ```
    DELETE   
    https://management.azure.com/{scope}/providers/Microsoft.Authorization/policyAssignments/{policyAssignmentName}?api-version=2018-05-01 
 ```

::: zone-end


## <a name="enhance-your-custom-recommendations-with-detailed-information"></a>詳細情報でのカスタム推奨事項の拡張

Azure Security Center で提供される組み込みの推奨事項には、重大度レベルや修復手順などの詳細が含まれます。 この種の情報をカスタム推奨事項に追加して、Azure portal や、推奨事項にアクセスする場所で表示されるようにする場合は、REST API を使用する必要があります。 

追加できる情報は次の 2 種類です。

- **RemediationDescription** – 文字列
- **Severity** – 列挙 [Low、Medium、High]

カスタム イニシアティブの一部であるポリシーのポリシー定義に、メタデータを追加する必要があります。 次のように、"securityCenter" プロパティに含める必要があります。

```json
 "metadata": {
    "securityCenter": {
        "RemediationDescription": "Custom description goes here",
        "Severity": "High"
    },
```

次に示すのは、metadata と securityCenter プロパティが含まれカスタム ポリシーの例です。

  ```json
  {
"properties": {
    "displayName": "Security - ERvNet - AuditRGLock",
    "policyType": "Custom",
    "mode": "All",
    "description": "Audit required resource groups lock",
    "metadata": {
        "securityCenter": {
            "RemediationDescription": "Resource Group locks can be set via Azure Portal -> Resource Group -> Locks",
            "Severity": "High"
        }
    },
    "parameters": {
        "expressRouteLockLevel": {
            "type": "String",
            "metadata": {
                "displayName": "Lock level",
                "description": "Required lock level for ExpressRoute resource groups."
            },
            "allowedValues": [
                "CanNotDelete",
                "ReadOnly"
            ]
        }
    },
    "policyRule": {
        "if": {
            "field": "type",
            "equals": "Microsoft.Resources/subscriptions/resourceGroups"
        },
        "then": {
            "effect": "auditIfNotExists",
            "details": {
                "type": "Microsoft.Authorization/locks",
                "existenceCondition": {
                    "field": "Microsoft.Authorization/locks/level",
                    "equals": "[parameters('expressRouteLockLevel')]"
                }
            }
        }
    }
}
}
  ```

securityCenter プロパティを使用する別の例については、[REST API のドキュメントのこちらのセクション](/rest/api/securitycenter/assessmentsmetadata/createinsubscription#examples)をご覧ください。


## <a name="next-steps"></a>次のステップ

この記事では、カスタム セキュリティ ポリシーの作成方法について説明しました。 

その他の関連資料については、次の記事を参照してください。 

- [セキュリティ ポリシーの概要](tutorial-security-policy.md)
- [組み込みのセキュリティ ポリシーの一覧](./policy-reference.md)