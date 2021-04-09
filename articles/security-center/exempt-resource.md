---
title: リソース、サブスクリプション、管理グループ、セキュリティ スコアから Azure Security Center の推奨事項を除外する
description: サブスクリプションまたは管理グループからのセキュリティの推奨事項を除外する規則を作成し、セキュリティ スコアに対する影響を防ぐ方法について説明します。
author: memildin
ms.author: memildin
ms.date: 01/22/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 374ddaa088fba9ae7035f170562e06b7f07eae47
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101709378"
---
# <a name="exempting-resources-and-recommendations-from-your-secure-score"></a>セキュリティ スコアからのリソースと推奨事項の除外 

すべてのセキュリティ チームの最優先事項として重要なのは、組織にとって重要なタスクとインシデントにアナリストが集中できるようにすることです。 Security Center には、エクスペリエンスをカスタマイズし、セキュリティ スコアが組織のセキュリティの優先順位を反映していることを確認するための多くの機能があります。 **除外** オプションは、このような機能の 1 つです。

Azure Security Center でセキュリティに関する推奨事項を調査するとき、最初にレビューする情報の 1 つが、影響を受けたリソースの一覧です。

この一覧には、含まれるべきではないと思われるリソースが含まれていることがあります。 または、推奨事項が属していないと思われるスコープに表示されます。 リソースは、Security Center によって追跡されていないプロセスによって修復された可能性があります。 この推奨事項は、特定のサブスクリプションに適していない可能性があります。 または、組織が特定のリソースまたは推奨事項に関連するリスクに同意することを決定した場合もあります。

このような場合は、次のような推奨事項の除外を作成できます。

- **リソースを除外** して、将来、問題のあるリソースと共に一覧に表示されないようにします。セキュリティ スコアには影響しません。 リソースは適用外として表示され、その理由は "除外" として、選択した特定の正当な理由と共に示されます。

- **サブスクリプションまたは管理グループを除外** して、推奨事項がセキュリティ スコアに影響を与えないようにします。サブスクリプションまたは管理グループには今後表示されません。 これは、既存のリソースと、今後作成するすべてのリソースに関係してきます。 推奨事項は、選択したスコープに対して選択した特定の理由でマークされます。

## <a name="availability"></a>可用性

|側面|詳細|
|----|:----|
|リリース状態:|プレビュー<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)] |
|価格:|これは、Azure Defender のお客様が追加コストなしで利用できる Azure Policy の Premium 機能です。 他のユーザーについては、将来、料金が適用されることがあります。|
|必要なロールとアクセス許可:|**サブスクリプションの所有者** または **ポリシーの共同作成者** (除外対象を作成する場合)<br>規則を作成するには、Azure Policy でポリシーを編集するためのアクセス許可が必要です。<br>詳細については、「[Azure Policy における RBAC アクセス許可](../governance/policy/overview.md#azure-rbac-permissions-in-azure-policy)」を参照してください。|
|クラウド:|![Yes](./media/icons/yes-icon.png) 商用クラウド<br>![No](./media/icons/no-icon.png) ナショナル/ソブリン (US Gov、China Gov、その他の Gov)|
|||

## <a name="define-an-exemption"></a>除外対象の定義

サブスクリプション、管理グループ、またはリソースに対して Security Center が行うセキュリティの推奨事項を微調整するには、次のような除外規則を作成します。

- 特定の **推奨事項** をマークするか、"軽減済み" または "リスク承認済み" としてマークします。 サブスクリプション、複数のサブスクリプション、または管理グループ全体に対して推奨設定の除外を作成することができます。
- 特定の推奨事項について、**1 つまたは複数のリソース** を "軽減済み" または "リスク承認済み" としてマークします。

> [!TIP]
> API を使用して除外を作成することもできます。 JSON の例と関連する構造の説明については、「[Azure Policy 適用除外の構造](../governance/policy/concepts/exemption-structure.md)」を参照してください。

除外規則を作成するには、次の手順を実行します。

1. 特定の推奨事項について、推奨事項の詳細ページを開きます。

1. ページの上部にあるツールバーで、 **[除外]** を選択します。

    :::image type="content" source="media/exempt-resource/exempting-recommendation.png" alt-text="サブスクリプションまたは管理グループから除外する推奨事項の除外規則を作成します。":::

1. **[除外]** ウィンドウで次のようにします。
    1. この除外規則のスコープを選択します:
        - 管理グループを選択すると、そのグループ内のすべてのサブスクリプションから推奨事項が除外されます。
        - この規則を作成して推奨設定から 1 つまたは複数のリソースを除外する場合は、[Selected resources]\(選択したリソース\) を選択し、一覧から関連するリソースを選択します。

    1. この除外規則の名前を入力します。
    1. 必要に応じて、有効期限を設定します。
    1. 除外対象のカテゴリを選択します。
        - **サードパーティによって解決済み (軽減済み)** – Security Center が特定していないサードパーティのサービスを使用している場合。 

            > [!NOTE]
            > 推奨事項を軽減済みとして除外すると、セキュリティ スコアに対するポイントは付与されません。 ただし、問題のあるリソースのポイントは *削除* されないため、スコアは増えることになります。

        - **リスク承認済み (免除)** - この推奨事項を軽減しないというリスクに同意する場合
    1. 必要に応じて、説明を入力します。
    1. **［作成］** を選択します

    :::image type="content" source="media/exempt-resource/defining-recommendation-exemption.png" alt-text="サブスクリプションまたは管理グループからの推奨事項を除外するための除外規則を作成する手順":::

    除外対象が有効になるタイミング (最大で 30 分かかる場合がある):
    - 推奨事項またはリソースは、セキュリティ スコアには影響しません。
    - 特定のリソースを除外した場合は、推奨事項の詳細ページの **[適用外]** タブに表示されます。
    - 推奨事項を除外した場合、Security Center の推奨事項ページでは既定で非表示になります。 これは、そのページの **推奨事項のステータス** フィルターの既定のオプションが、 **[適用外]** 推奨事項を除外するためのオプションではないからです。 セキュリティ コントロールのすべての推奨事項を除外する場合も同様です。

        :::image type="content" source="media/exempt-resource/recommendations-filters-hiding-not-applicable.png" alt-text="Azure Security Center の推奨事項ページの既定のフィルターで、適用外推奨事項とセキュリティ コントロールが非表示になります。":::

    - 推奨事項の詳細ページの上部にある情報ストリップには、除外されたリソースの数が更新されます。
        
        :::image type="content" source="./media/exempt-resource/info-banner.png" alt-text="除外されたリソースの数":::

1. 除外されたリソースを確認するには、 **[適用外]** タブを開きます。

    :::image type="content" source="./media/exempt-resource/modifying-exemption.png" alt-text="除外対象の変更":::

    各除外の理由が表 (1) に示されています。

    除外対象を変更または削除するには、(2) に示すように、省略記号メニュー ("...") を選択します。

1. サブスクリプションのすべての除外ルールを確認するには、情報ストリップから **[View exemptions]\(除外の表示\)** を選択します。

    > [!IMPORTANT]
    > 1 つの推奨事項に関連する特定の除外を確認するには、該当するスコープと推奨名に従って一覧をフィルター処理します。

    :::image type="content" source="./media/exempt-resource/policy-page-exemption.png" alt-text="Azure Policy の除外対象ページ":::

    > [!TIP]
    > または、[Azure Resource Graph を使用して、除外対象に関する推奨事項を検索](#find-recommendations-with-exemptions-using-azure-resource-graph)します。

## <a name="monitor-exemptions-created-in-your-subscriptions"></a>サブスクリプションで作成された除外を監視する

このページで既に説明したように、除外規則は、サブスクリプションと管理グループ内のリソースに影響する推奨事項をきめ細かく制御できる強力なツールです。 

ユーザーがこの機能をどのように使用しているかを追跡するために、Logic App Playbook をデプロイする Azure Resource Manager (ARM) テンプレートと、除外の作成時に通知するために必要なすべての API 接続を作成しました。

- プレイブックの詳細については、テクニカル コミュニティのブログ投稿「[How to keep track of Resource Exemptions in Azure Security Center (Azure Security Center でリソースの除外を追跡する方法)](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-keep-track-of-resource-exemptions-in-azure-security/ba-p/1770580)」を参照してください
- ARM テンプレートは、[Azure Security Center GitHub リポジトリ](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation/Notify-ResourceExemption)にあります。
- 必要なすべてのコンポーネントをデプロイするには、[こちらの自動化されたプロセス](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Security-Center%2Fmaster%2FWorkflow%2520automation%2FNotify-ResourceExemption%2Fazuredeploy.json)を使用してください


## <a name="find-recommendations-with-exemptions-using-azure-resource-graph"></a>Azure Resource Graph を使用して除外対象の推奨事項を検索する

Azure Resource Graph (ARG) を使用すると、堅牢なフィルター処理、グループ化、および並べ替え機能を使用して、クラウド環境全体のリソース情報にすばやくアクセスできます。 これは、Azure サブスクリプション全体の情報を、プログラムから、または Azure portal 内ですばやく効率的に照会する方法です。

除外規則があるすべての推奨事項を表示するには、次の手順を実行します。

1. **Azure Resource Graph エクスプローラー** を開きます。

    :::image type="content" source="./media/security-center-identity-access/opening-resource-graph-explorer.png" alt-text="Azure Resource Graph エクスプローラーの起動** 推奨ページ" :::

1. 次のクエリを入力し、 **[クエリの実行]** を選択します。

    ```kusto
    securityresources
    | where type == "microsoft.security/assessments"
    // Get recommendations in useful format
    | project
    ['TenantID'] = tenantId,
    ['SubscriptionID'] = subscriptionId,
    ['AssessmentID'] = name,
    ['DisplayName'] = properties.displayName,
    ['ResourceType'] = tolower(split(properties.resourceDetails.Id,"/").[7]),
    ['ResourceName'] = tolower(split(properties.resourceDetails.Id,"/").[8]),
    ['ResourceGroup'] = resourceGroup,
    ['ContainsNestedRecom'] = tostring(properties.additionalData.subAssessmentsLink),
    ['StatusCode'] = properties.status.code,
    ['StatusDescription'] = properties.status.description,
    ['PolicyDefID'] = properties.metadata.policyDefinitionId,
    ['Description'] = properties.metadata.description,
    ['RecomType'] = properties.metadata.assessmentType,
    ['Remediation'] = properties.metadata.remediationDescription,
    ['Severity'] = properties.metadata.severity,
    ['Link'] = properties.links.azurePortal
    | where StatusDescription contains "Exempt"    
    ```


詳細については、次のページを参照してください。
- [Azure Resource Graph の詳細についてさらに学習します](../governance/resource-graph/index.yml)。
- [Azure Resource Graph Explorer を使用してクエリを作成する方法](../governance/resource-graph/first-query-portal.md)
- [Kusto クエリ言語 (KQL)](/azure/data-explorer/kusto/query/)





## <a name="exemption-rule-faq"></a>除外規則に関する FAQ

### <a name="what-happens-when-one-recommendation-is-in-multiple-policy-initiatives"></a>複数のポリシー イニシアチブに 1 つの推奨事項がある場合はどうなりますか。

場合によっては、複数のポリシー イニシアチブにセキュリティの推奨事項が表示されることがあります。 同じ推奨事項の複数のインスタンスが同じサブスクリプションに割り当てられていて、その推奨設定の除外対象を作成した場合は、編集権限があるすべてのイニシアチブに影響します。 

たとえば、**** という推奨事項は、Azure Security Center によってすべての Azure サブスクリプションに割り当てられた既定のポリシー イニシアチブの一部だとします。 これが XXXXX にもあります。

この推奨設定の除外を作成しようとすると、次の 2 つのメッセージのいずれかが表示されます。

- 両方のイニシアチブを編集するために必要なアクセス許可がある場合は、次のように表示されます。

    *この推奨事項は、「コンマで区切られたイニシアチブ名」といういくつかのポリシー イニシアチブに含まれています。除外はすべてのユーザーに対して作成されます。*  

- 両方のイニシアチブに対する十分なアクセス許可がない場合は、代わりに次のメッセージが表示されます。

    *すべてのポリシー イニシアチブに適用の除外を適用するにはアクセス許可が制限されているために、除外は十分なアクセス許可を持つイニシアチブに対してのみ作成されます。*


## <a name="next-steps"></a>次の手順

この記事では、セキュリティ スコアに影響しないように、リソースを推奨事項から除外する方法を学習しました。 セキュリティ スコアの詳細については、以下を参照してください。

- [Azure Security Center 内のセキュリティ スコア](secure-score-security-controls.md)