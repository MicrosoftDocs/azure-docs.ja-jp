---
title: Azure のコストの割り当て
description: この記事では、サブスクリプション、リソース グループ、タグのコストを配分するためのコストの割り当てルールを作成する方法について説明します。
author: bandersmsft
ms.author: banders
ms.date: 08/11/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.reviewer: benshy
ms.openlocfilehash: dbdd93d6c82f7eefd85d2142a3ddee246739fa34
ms.sourcegitcommit: ef055468d1cb0de4433e1403d6617fede7f5d00e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2020
ms.locfileid: "88258817"
---
# <a name="create-and-manage-azure-cost-allocation-rules-preview"></a>Azure のコストの割り当てルールの作成と管理 (プレビュー)

大企業が利用する Azure のサービスやリソースは、一元管理されているものの、社内のさまざまな部署や事業部門によって利用されることが少なくありません。 通常、中央の管理チームは、共有サービスを実際に利用している社内の部署や事業部門に対し、そのサービスのコストを再度割り当てる必要があります。 この記事は、Azure Cost Management におけるコストの割り当てを理解して使用する際に役立ちます。

コストの割り当てを使用すると、組織内の複数のサブスクリプション、リソース グループ、タグ間で共有サービスのコストを再割り当て (配分) することができます。 コストの割り当ては、共有サービスの利用者である社内の部署や事業部門によって所有されている別のサブスクリプション、リソース グループ、タグにそのコストを移すものです。 つまり、コストの割り当ては、事業単位間の "_コストのアカウンタビリティ_" を管理し、明らかにするのに役立ちます。

請求書にはコストの割り当ての影響はありません。 請求先は変わらないということです。 コストの割り当ての主な目的は、他者へのコスト配賦を円滑化することです。 すべての配賦プロセスは、Azure とは切り離された組織内で発生します。 再割当 (配分) されたコストを明らかにすることによってコストを配分しやすくするのが、コストの割り当ての効果といえます。

割り当てられたコストは、コスト分析で示されます。 これらは、コストの割り当てルールの作成時に指定した配分先のサブスクリプション、リソース グループ、タグに関連した追加項目として表示されます。

> [!NOTE]
> Azure Cost Management のコストの割り当て機能は現在パブリック プレビュー段階です。 Cost Management の一部の機能はサポートされていないか、機能が制限されている可能性があります。

## <a name="prerequisites"></a>前提条件

- 現在、コストの割り当てでサポートされるのは、[Microsoft 顧客契約](https://azure.microsoft.com/pricing/purchase-options/microsoft-customer-agreement/)または[マイクロソフト エンタープライズ契約 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) をご利用のお客様のみです。
- コストの割り当てルールを作成または管理するには、[マイクロソフト エンタープライズ契約](../manage/understand-ea-roles.md)のエンタープライズ管理者アカウントを使用する必要があります。 または、Microsoft 顧客契約の[課金アカウント](../manage/understand-mca-roles.md)の所有者である必要があります。

## <a name="create-a-cost-allocation-rule"></a>コストの割り当てルールを作成する

1. Azure Portal [https://portal.azure.com](https://portal.azure.com/) にサインインします。
2. **[コストの管理と請求]**  >  **[コスト管理]** の順に移動します。
3. **[設定]**  >  **[構成]** で **[コスト割り当て (プレビュー)]** を選択します。
4. 正しい EA 加入契約または課金アカウントが選択されていることを確認します。
5. **[+追加]** を選択します。
6. コストの割り当てルールの名前にわかりやすいテキストを入力します。

:::image type="content" source="./media/allocate-costs/rule-name.png" alt-text="ルール名の作成を示す例" lightbox="./media/allocate-costs/rule-name.png" :::

ルールの評価開始日は、事前入力されるコスト割り当ての割合の生成に使用されます。

1. **[ソースの追加]** を選択し、サブスクリプション、リソース グループ、タグのいずれかを選択して、配分するコストを選択します。
2. **[ターゲットの追加]** を選択して、コストの割当先となるサブスクリプション、リソース グループ、またはタグを選択します。
3. さらにコストの割り当てルールを作成する必要がある場合は、この手順を繰り返します。

## <a name="configure-the-allocation-percentage"></a>割り当ての割合を構成する

指定したターゲット間でどのようにコストを比例配分するかは、割り当ての割合を構成することによって定義します。 整数の割合を手動で定義して割り当てルールを作成することができます。 または、コンピューティング、ストレージ、またはネットワークの現在の使用量に基づいて、指定したターゲット間でコストを比例配分することもできます。

コンピューティング コスト、ストレージ コスト、ネットワーク コストのいずれかを基準にコストを配分する場合、選択したターゲットのコストを評価することによって比例割合が求められます。 このコストは、現在の課金月について、該当するリソースの種類に関連付けられています。

総コストに比例するようにコストを配分する場合、現在の課金月について、選択したターゲットの合計 (総コスト) を基準に、それに比例した割合が割り当てられます。

:::image type="content" source="./media/allocate-costs/cost-distribution.png" alt-text="割り当ての割合を示す例" lightbox="./media/allocate-costs/cost-distribution.png" :::

設定後、定義された事前入力の割合が確定されます。 これらは、進行中のすべての割り当てに使用されます。 割合が変化するのは、ルールが手動で更新されたときだけです。

1. **[次の割合を事前入力]** ボックスの一覧で、次のいずれかのオプションを選択します。
    - **[均等に配分]** - 総コストが均等な割合で配分されて各ターゲットに適用されます。
    - **[総コスト]** - ターゲットに比例した割合が、その総コストに基づいて作成されます。 その割合を使用して、選択したソースからのコストが配分されます。
    - **[コンピューティング コスト]** - ターゲットに比例した割合が、Azure のコンピューティング コスト ([Microsoft.Compute](https://docs.microsoft.com/azure/templates/microsoft.compute/allversions) 名前空間のリソース タイプ) に基づいて作成されます。その割合を使用して、選択したソースからのコストが配分されます。
    - **[ストレージ コスト]** - ターゲットに比例した割合が、Azure のストレージ コスト ([Microsoft.Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) 名前空間のリソース タイプ) に基づいて作成されます。 その割合を使用して、選択したソースからのコストが配分されます。
    - **[ネットワーク コスト]** - ターゲットに比例した割合が、Azure のネットワーク コスト ([Microsoft.Network](https://docs.microsoft.com/azure/templates/microsoft.network/allversions) 名前空間のリソース タイプ) に基づいて作成されます。 その割合を使用して、選択したソースからのコストが配分されます。
    - **[Custom]\(カスタム\)** - 整数のパーセンテージを手動で指定できます。 指定した合計が 100% になるようにする必要があります。
1. ルールを構成したら、 **[作成]** を選択します。

割り当てルールの処理が開始されます。 ルールがアクティブの場合は、選択したすべてのソースのコストが、指定したターゲットに割り当てられます。

> [!NOTE] 
> 新しいルールの処理が完了してアクティブになるまでには、最大 2 時間かかる場合があります。

## <a name="verify-the-cost-allocation-rule"></a>コストの割り当てルールを検証する

コストの割り当てルールがアクティブの場合、選択したソースからのコストが、指定したターゲット (割り当て先) に配分されます。 以下の情報を参考に、ターゲットに対してコストが適切に割り当てられていることを確認してください。

### <a name="view-cost-allocation-for-a-subscription"></a>サブスクリプションのコスト割り当てを表示する

コスト分析で割り当てルールの影響を確認します。 Azure portal で [[サブスクリプション]](https://portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade) に移動します。 アクティブなコストの割り当てルールの対象となるサブスクリプションをリストから選択します。 次に、メニューから **[コスト分析]** を選択します。 [コスト分析] で **[グループ化]** を選択し、 **[コストの割り当て]** を選択します。 サブスクリプションによって生成された簡単なコスト内訳が結果のビューに表示されます。 また、サブスクリプションに割り当てられたコストは、次の画像のように表示されます。

:::image type="content" source="./media/allocate-costs/cost-breakdown.png" alt-text="コスト内訳を示す例" lightbox="./media/allocate-costs/cost-breakdown.png" :::

### <a name="view-cost-allocation-for-a-resource-group"></a>リソース グループのコスト割り当てを表示する

リソース グループの場合も同様の手順で、コストの割り当てルールの影響を確認します。 Azure portal で、[[リソース グループ]](https://portal.azure.com/#blade/HubsExtension/BrowseResourceGroups) に移動します。 アクティブなコストの割り当てルールの対象となるリソース グループをリストから選択します。 次に、メニューから **[コスト分析]** を選択します。 [コスト分析] で **[グループ化]** を選択し、 **[コストの割り当て]** を選択します。 リソース グループによって生成された簡単なコスト内訳がビューに表示されます。 また、リソース グループに割り当てられたコストも表示されます。

### <a name="view-cost-allocation-for-tags"></a>タグのコスト割り当てを表示する

Azure portal で **[コストの管理と請求]**  >  **[コスト管理]**  >  **[コスト分析]** に移動します。 [コスト分析] で **[フィルターの追加]** を選択します。 **[タグ]** を選択し、タグ キーと、コストが割り当てられているタグ値を選択します。

:::image type="content" source="./media/allocate-costs/tagged-costs.png" alt-text="タグ付けされた項目のコストを示す例" lightbox="./media/allocate-costs/tagged-costs.png" :::

## <a name="edit-an-existing-cost-allocation-rule"></a>既存のコストの割り当てルールを編集する

コストの割り当てルールは編集できます。ソースまたはターゲットを変更したり、必要に応じて、コンピューティング、ストレージ、ネットワークのいずれかのオプションについて、事前入力された割合を更新したりすることができます。 ルールの編集は、作成と同じ方法で行うことができます。 既存のルールを変更した場合、再処理されるまでに最大で 2 時間かかることがあります。

## <a name="frequently-asked-questions-faq"></a>よく寄せられる質問 (FAQ)

以降のセクションでは、コストの割り当てに関してよく寄せられる質問を紹介します。

### <a name="what-are-the-current-limitations-with-cost-allocation-in-public-preview"></a>パブリック プレビューのコストの割り当てに関して、現在、どのような制限がありますか?
<a name="limitations"></a>

現在、コストの割り当ては、Cost Management のコスト分析、予算、予測の各ビューでサポートされています。 また、サブスクリプション一覧とサブスクリプションの概要ページにも、割り当てられたコストが表示されます。

パブリック プレビューのコストの割り当てでは、現在次の項目がサポートされていません。

- [エクスポート](tutorial-export-acm-data.md)のスケジュール実行
- [Usage Details](https://docs.microsoft.com/rest/api/consumption/usagedetails/list) API によって公開されたデータ
- 課金サブスクリプション領域
- [Cost Management Power BI アプリ](https://appsource.microsoft.com/product/power-bi/costmanagement.azurecostmanagementapp)
- [Power BI Desktop コネクタ](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management)

### <a name="are-costs-factored-into-budgets-and-forecast-views"></a>予算および予測ビューにコストは考慮されていますか?
<a name="budgets-forecast"></a>

はい。 予算および予測には、割り当てられたコストが考慮されており、サポートされています。 予算および予測ビューには、コストの割り当てルールの構成に従って割り当てられたコストが表示されます。

### <a name="if-a-cost-allocation-rule-is-deleted-what-happens"></a>コストの割り当てルールを削除するとどうなりますか?
<a name="delete-rule"></a>

コストの割り当てルールが削除されると、ターゲットに割り当てられている、まだ処理されていない現在の課金月のコストがすべて削除されます。 コストの割り当てルールが複数月にわたって存在していた場合、その過去の月の割り当てデータが、割り当てルールによって最初に設定された状態のままになります。

### <a name="why-is-an-enrollment-admin-or-a-billing-account-admin-needed-to-create-cost-allocation-rules"></a>コストの割り当てルールを作成するのに登録管理者や課金アカウント管理者が必要なのはなぜですか?
<a name="why-admin"></a>

コストの割り当てルールは、加入契約スコープ (マイクロソフト エンタープライズ契約) または課金アカウント スコープ (Microsoft 顧客契約) で作成されます。 それらのスコープで変更を加えるためのアクセス許可には、課金管理者の権限が必要となります。

### <a name="why-are-sources-and-targets-limited-to-25-per-rule"></a>ソースとターゲットが 1 ルールにつき 25 個に制限されているのはなぜですか?
<a name="source-target-rule-limit"></a>

この制限は、コストの割り当てのパフォーマンスとスケーラビリティを十分に確保することを目的としたプレビューにおける制限です。 コストの割り当ての一般提供 (GA) が開始された時点で、これらの制限は引き上げられる可能性もあれば廃止される可能性もあります。

### <a name="what-can-happen-if-cost-allocation-rules-sourcestargets-overlap"></a>コストの割り当てルール (ソースまたはターゲット) が重複した場合はどうなりますか?
<a name="rule-overlap"></a>

重複するソースや重複するターゲットを含むルールは推奨されません。 コストの割り当てルールはその作成日順に適用されます。そのため、コストの割り当てルールが重複している場合、最も古い作成日の割り当てルールが優先されます。

## <a name="next-steps"></a>次のステップ

- [コスト割り当ての REST API](https://go.microsoft.com/fwlink/?linkid=2135004) を使用して割り当てルールを作成または更新する
- [Azure Cost Management を使用してクラウドへの投資を最適化する方法](cost-mgt-best-practices.md)の詳細を確認する