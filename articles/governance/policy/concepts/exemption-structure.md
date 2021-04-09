---
title: ポリシー適用除外の構造の詳細
description: Azure Policy でイニシアティブまたは定義の評価からリソースを除外するために使用されるポリシー適用除外の定義について説明します。
ms.date: 09/22/2020
ms.topic: conceptual
ms.openlocfilehash: e6ced56c1dc65ca68998c5c58d3e985b63873e0b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950178"
---
# <a name="azure-policy-exemption-structure"></a>Azure Policy 適用除外の構造

Azure Policy 適用除外 (プレビュー) 機能は、イニシアティブや定義の評価からリソースの階層または個々のリソースを "_除外_" する目的で使用します。 "_除外_" されたリソースは、全体的なコンプライアンスには考慮されますが、評価の対象にはできず、また一時的な特別承認の対象にすることもできません。 詳細については、「[Azure Policy でのスコープについて](./scope.md)」を参照してください。 Azure Policy の適用除外は、[Resource Manager モード](./definition-structure.md#resource-manager-modes)でのみ機能し、[リソース プロバイダー モード](./definition-structure.md#resource-provider-modes)では機能しません。

> [!IMPORTANT]
> この機能は、**プレビュー** 期間中は無料です。 価格の詳細については、「[Azure Policy の価格](https://azure.microsoft.com/pricing/details/azure-policy/)」を参照してください。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

ポリシー適用除外を作成するには、JSON を使用します。 ポリシー適用除外には、以下のものに対する要素が含まれています。

- 表示名
- description
- metadata
- ポリシー割り当て
- イニシアティブ内のポリシー定義
- 適用除外カテゴリ
- expiration

> [!NOTE]
> ポリシー適用除外は、その対象となる個々のリソースまたはリソース階層上の子オブジェクトとして作成されるため、ターゲットは適用除外の定義には含まれません。

たとえば、次の JSON は、`resourceShouldBeCompliantInit` という名前のイニシアティブ割り当てに対するリソースの **特別承認** カテゴリのポリシー適用除外を示しています。 リソースは、イニシアティブ内の 2 つのポリシー定義からのみ "_適用除外_" されます。`customOrgPolicy` カスタム ポリシー定義 (reference `requiredTags`) と "Allowed locations" 組み込みポリシー定義 (ID: `e56962a6-4747-49cd-b67b-bf8b01975c4c`、reference `allowedLocations`) です。

```json
{
    "id": "/subscriptions/{subId}/resourceGroups/ExemptRG/providers/Microsoft.Authorization/policyExemptions/resourceIsNotApplicable",
    "name": "resourceIsNotApplicable",
    "type": "Microsoft.Authorization/policyExemptions",
    "properties": {
        "displayName": "This resource is scheduled for deletion",
        "description": "This resources is planned to be deleted by end of quarter and has been granted a waiver to the policy.",
        "metadata": {
            "requestedBy": "Storage team",
            "approvedBy": "IA",
            "approvedOn": "2020-07-26T08:02:32.0000000Z",
            "ticketRef": "4baf214c-8d54-4646-be3f-eb6ec7b9bc4f"
        },
        "policyAssignmentId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyAssignments/resourceShouldBeCompliantInit",
        "policyDefinitionReferenceIds": [
            "requiredTags",
            "allowedLocations"
        ],
        "exemptionCategory": "waiver",
        "expiresOn": "2020-12-31T23:59:00.0000000Z"
    }
}
```

ポリシー適用除外によって使用される、対応する `policyDefinitionReferenceIds` を含んだ関連するイニシアティブのスニペットは次のとおりです。

```json
"policyDefinitions": [
    {
        "policyDefinitionId": "/subscriptions/{mySubscriptionID}/providers/Microsoft.Authorization/policyDefinitions/customOrgPolicy",
        "policyDefinitionReferenceId": "requiredTags",
        "parameters": {
            "reqTags": {
                "value": "[parameters('init_reqTags')]"
            }
        }
    },
    {
        "policyDefinitionId": "/providers/Microsoft.Authorization/policyDefinitions/e56962a6-4747-49cd-b67b-bf8b01975c4c",
        "policyDefinitionReferenceId": "allowedLocations",
        "parameters": {
            "listOfAllowedLocations": {
                "value": "[parameters('init_listOfAllowedLocations')]"
            }
        }
    }
]
```

## <a name="display-name-and-description"></a>表示名と説明

**displayName** と **description** は、ポリシー適用除外を識別し、特定のリソースで使用するためのコンテキストを提供するために使用します。 **displayName** の最大長は _128_ 文字で、**description** の最大長は _512_ 文字です。

## <a name="metadata"></a>Metadata

関連した情報を格納するために必要な子プロパティは、**メタデータ** プロパティを使用して作成できます。 上の例の **requestedBy**、**approvedBy**、**approvedOn**、**ticketRef** の各プロパティには、適用除外の要求者、承認者、承認日、要求の内部的な追跡チケットに関する情報を提供するユーザー値が格納されています。 これらの **メタデータ** プロパティを例に挙げましたが、必須ではなく、また **メタデータ** はこれらの子プロパティに限定されません。

## <a name="policy-assignment-id"></a>ポリシーの割り当て ID

このフィールドは、ポリシーの割り当てまたはイニシアティブの割り当ての完全なパス名にする必要があります。
`policyAssignmentId` は文字列であり、配列ではありません。 このプロパティは、親リソース階層または個々のリソースがどの割り当てから "_適用除外_" されるのかを定義します。

## <a name="policy-definition-ids"></a>ポリシー定義 ID

`policyAssignmentId` がイニシアティブの割り当て用である場合、サブジェクト リソースが適用除外の対象とするイニシアティブ内のポリシー定義は、`policyDefinitionReferenceIds` プロパティを使用して指定できます。 このリソースは、含まれる 1 つまたは複数のポリシー定義から適用除外される可能性があるため、このプロパティは "_配列_" です。 値は、`policyDefinitions.policyDefinitionReferenceId` フィールドのイニシアティブ定義内の値と一致する必要があります。

## <a name="exemption-category"></a>適用除外カテゴリ

次の 2 つの適用除外カテゴリがあり、適用除外をグループ化するために使用されます。

- **軽減済み**: ポリシーの目的が別の方法で満たされているため、除外対象が許可されます。
- **免除**: リソースの非コンプライアンスの状態が一時的に受け入れられるため、除外対象が許可されます。 このカテゴリを使用するもう 1 つの理由は、リソースまたはリソース階層をイニシアティブ内の 1 つ以上の定義から除外する必要はあるが、イニシアティブ全体から除外してはならない場合です。

## <a name="expiration"></a>有効期限

リソース階層または個々のリソースが割り当てから "_除外_" されなくなる期限を設定するには、`expiresOn` プロパティを設定します。 このオプション プロパティは、ユニバーサル ISO 8601 日時形式 `yyyy-MM-ddTHH:mm:ss.fffffffZ` であることが必要です。

> [!NOTE]
> `expiresOn` の日付になってもポリシー適用除外は削除されません。 記録保持のためにオブジェクトは維持されますが、適用除外は無効となります。

## <a name="required-permissions"></a>必要なアクセス許可

ポリシー適用除外オブジェクトを管理するために必要な Azure RBAC のアクセス許可は、`Microsoft.Authorization/policyExemptions` 操作グループに存在します。 組み込みロールである[リソース ポリシーの共同作成者](../../../role-based-access-control/built-in-roles.md#resource-policy-contributor)と[セキュリティ管理者](../../../role-based-access-control/built-in-roles.md#security-admin)は、どちらも `read` アクセス許可と `write` アクセス許可を持ち、[Policy Insights データ ライター (プレビュー)](../../../role-based-access-control/built-in-roles.md#policy-insights-data-writer-preview) は `read` アクセス許可を持ちます。

適用除外には、それを許可することの影響から、特別なセキュリティ対策が講じられています。 リソース階層または個々のリソースに対して `Microsoft.Authorization/policyExemptions/write` 操作を要求するだけでなく、適用除外の作成者は、ターゲットの割り当てに対する `exempt/Action` 動詞を持つ必要があります。

## <a name="next-steps"></a>次のステップ

- [ポリシー定義の構造](./definition-structure.md)についてさらに学習します。
- [プログラムによってポリシーを作成する](../how-to/programmatically-create.md)方法を理解します。
- [コンプライアンス データを取得する](../how-to/get-compliance-data.md)方法を学習します。
- [準拠していないリソースを修復する](../how-to/remediate-resources.md)方法を学習します。
- 「[Azure 管理グループのリソースを整理する](../../management-groups/overview.md)」で、管理グループとは何かを確認します。