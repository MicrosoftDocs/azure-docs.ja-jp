---
title: 管理グループを Azure Security Center にオンボードする
description: 提供されている Azure Policy 定義を使用して、管理グループ内のすべてのサブスクリプションで Azure Security Center を有効にする方法について説明します。
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 03/11/2021
ms.author: memildin
ms.openlocfilehash: 01036343e2585adf7c09ad3f0d236948a537fc29
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103016561"
---
# <a name="enable-security-center-on-all-subscriptions-in-a-management-group"></a>管理グループ内のすべてのサブスクリプションで Security Center を有効にする

Azure Policy を使用すると、同じ管理グループ (MG) 内のすべての Azure サブスクリプションで Azure Security Center を有効にすることができます。 これは、ポータルから個別にアクセスするよりも便利であり、サブスクリプションが異なる所有者に属している場合でも機能します。 

管理グループとそのすべてのサブスクリプションをオンボードするには:

1. **セキュリティ管理者** のアクセス許可を持つユーザーとして Azure Policy を開き、 **[Enable Azure Security Center on your subscription]\(サブスクリプションで Azure Security Center を有効にする\)** 定義を検索します。

    :::image type="content" source="./media/security-center-get-started/enable-security-center-policy.png" alt-text="Azure Policy の [Enable Azure Security Center on your subscription]\(サブスクリプションで Azure Security Center を有効にする\) 定義":::

1. **[割り当て]** を選択し、スコープが MG レベルに設定されていることを確認します。

    :::image type="content" source="./media/security-center-get-started/assign-policy.png" alt-text="[Enable Azure Security Center on your subscription]\(サブスクリプションで Azure Security Center を有効にする\) 定義の割り当て":::

    > [!TIP]
    > スコープ以外に必要なパラメーターはありません。

1. **[Create a remediation task]\(修復タスクを作成する\)** を選択して、Security Center が有効になっていない既存のサブスクリプションすべてがオンボードされるようにします。

    :::image type="content" source="./media/security-center-get-started/remediation-task.png" alt-text="Azure Policy の [Enable Azure Security Center on your subscription]\(サブスクリプションで Azure Security Center を有効にする\) 定義に対する修復タスクの作成":::

1. 定義が割り当てられると、次のようになります。

    1. Security Center にまだ登録されていない、MG 内のすべてのサブスクリプションが検出されます。
    1. それらのサブスクリプションが "非準拠" としてマークされます。
    1. (Azure Defender がオンかオフかに関係なく) 登録済みのすべてのサブスクリプションが "準拠" としてマークされます。

    その後、修復タスクにより、非準拠のサブスクリプションでは Security Center が無料で有効になります。

> [!IMPORTANT]
> このポリシー定義で有効になるのは、**既存の** サブスクリプションの Security Center のみです。 新しく作成されたサブスクリプションを登録するには、[コンプライアンス] タブを開き、該当する非準拠サブスクリプションを選択して、修正タスクを作成します。Security Center で監視する新しいサブスクリプションが 1 つ以上ある場合は、この手順を繰り返してください。

## <a name="optional-modifications"></a>省略可能な変更

Azure Policy の定義を変更するには、さまざまな方法があります。 

- **コンプライアンスをさまざまな方法で定義する** - 提供されるポリシーでは、Security Center にまだ登録されていない、MG 内のすべてのサブスクリプションが "非準拠" として分類されます。 これを、Azure Defender のないすべてのサブスクリプションに設定することもできます。

    提供されている定義では、以下の "価格" 設定の "*いずれか*" が準拠として定義されます。 これは、"standard" または "free" に設定されているサブスクリプションが準拠していることを意味します。

    > [!TIP]
    > Azure Defender プランが有効な場合は、"Standard" 設定としてポリシー定義に記述されます。 無効な場合は、"Free" になります。 これらのプランの違いについては、[Security Center (無料) と有効化された Azure Defender](security-center-pricing.md) に関するセクションをご覧ください。 

    ```
    "existenceCondition": {
        "anyof": [
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "standard"
            },
            {
                "field": "microsoft.security/pricings/pricingTier",
                "equals": "free"
            }
        ]
    },
    ```

    これを次のように変更すると、"standard" に設定されたサブスクリプションのみが準拠として分類されます。

    ```
    "existenceCondition": {
          {
            "field": "microsoft.security/pricings/pricingTier",
            "equals": "standard"
          },
    },
    ```

- **Security Center を有効にしたときに適用するいくつかの Azure Defender プランを定義する** - 提供されているポリシーを使用すると、オプションの Azure Defender プランがなくても Security Center が有効になります。 それらの 1 つ以上を有効にすることを選択できます。

    提供されている定義の `deployment` セクションには、`pricingTier` パラメーターがあります。 これは既定で `free` に設定されていますが、変更できます。 


## <a name="next-steps"></a>次のステップ:

管理グループ全体をオンボードしたので、Azure Defender の高度な保護を有効にします。 

> [!div class="nextstepaction"]
> [Azure Defender を有効にする](enable-azure-defender.md)