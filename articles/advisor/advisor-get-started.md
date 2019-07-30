---
title: Azure Advisor の使用を開始する| Microsoft Docs
description: Azure Advisor の使用を開始します。
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: kasparks
ms.openlocfilehash: f91e48a532a278c95d50775e135ac6379e8d8070
ms.sourcegitcommit: 08138eab740c12bf68c787062b101a4333292075
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/22/2019
ms.locfileid: "67332067"
---
# <a name="get-started-with-azure-advisor"></a>Azure Advisor の使用を開始する

Azure Portal を使用して Advisor にアクセスし、推奨事項を取得して、推奨事項を実装する方法について説明します。

> [!NOTE]
> Azure Advisor は、新しく作成されたリソースを検出するために、自動的にバック グラウンドで実行されます。 それらのリソースの推奨事項を提供するには、最大 24 時間かかる場合があります。

## <a name="get-recommendations"></a>推奨の取得

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. 左側のウィンドウで、 **[Advisor]** をクリックします。  左側のウィンドウに Advisor が表示されていない場合は、 **[すべてのサービス]** をクリックします。  サービス メニュー ウィンドウで、 **[Monitoring and Management]\(監視と管理\)** の **[Advisor]** をクリックします。 Advisor ダッシュボードが表示されます。

   ![Azure Portal を使用した Azure Advisor へのアクセス](./media/advisor-get-started/advisor-portal-menu.png) 

1. Advisor ダッシュボードには、選択したすべてのサブスクリプションの推奨事項の概要が表示されます。  サブスクリプション フィルター ドロップダウンを使用して、推奨事項を表示するサブスクリプションを選択できます。

1. 特定のカテゴリの推奨事項を取得するには、次の各タブのいずれかをクリックします。 **[高可用性]** 、 **[セキュリティ]** 、 **[パフォーマンス]** 、 **[コスト]** 。 

   ![Azure Advisor ダッシュボード](./media/advisor-overview/advisor-dashboard.png)

## <a name="get-recommendation-details-and-implement-a-solution"></a>推奨事項の詳細を取得してソリューションを実装する

Advisor の推奨事項を選択して、詳細 (推奨されるアクションや影響を受けるリソースなど) を表示したり、推奨事項のソリューションを実装したりできます。  

1. [Azure Portal](https://portal.azure.com) にサインインし、[Advisor](https://aka.ms/azureadvisordashboard) を開きます。

1. 推奨事項のカテゴリを選択して、そのカテゴリ内の推奨事項の一覧を表示するか、 **[すべて]** タブを選択して、すべての推奨事項を表示します。

1. 詳細を確認する推奨事項をクリックします。

1. 推奨事項とその推奨事項が適用されるリソースに関する情報を確認します。

1. **[Recommended Action]\(推奨されるアクション\)** をクリックして、推奨事項を実装します。

## <a name="filter-recommendations"></a>Filter recommendations

推奨事項をフィルター処理して、最も重要なものにドリルダウンできます。  サブスクリプション、リソースの種類、または推奨事項の状態でフィルター処理できます。  

1. [Azure Portal](https://portal.azure.com) にサインインし、[Advisor](https://aka.ms/azureadvisordashboard) を開きます。

1. Advisor ダッシュボードのドロップダウンを使用して、サブスクリプション、リソースの種類、または推奨事項の状態でフィルター処理します。

    ![Advisor 検索フィルターの条件](./media/advisor-get-started/advisor-filters.png)

## <a name="postpone-or-dismiss-recommendations"></a>推奨事項を延期または無視する

1. [Azure Portal](https://portal.azure.com) にサインインし、[Advisor](https://aka.ms/azureadvisordashboard) を開きます。

1. 延期または無視する推奨事項に移動します。

1. 推奨事項をクリックします。

1. **[延期]** をクリックします。 

1. 延期期間を指定するか、 **[Never]** を選択して推奨事項を無視します。

## <a name="exclude-subscriptions-or-resource-groups"></a>サブスクリプションまたはリソース グループを除外する

"テスト" リソースなど、Advisor の推奨事項を受け取る必要のないリソース グループまたはサブスクリプションがある場合があります。  特定のサブスクリプションやリソース グループの推奨事項だけを生成するように Advisor を構成できます。

> [!NOTE]
> サブスクリプションまたはリソース グループを Advisor に含めたり、Advisor から除外したりするには、サブスクリプションの所有者である必要があります。  サブスクリプションまたはリソース グループに対する必要なアクセス許可がない場合、含めるまたは除外するオプションがユーザー インターフェイスで無効になります。

1. [Azure Portal](https://portal.azure.com) にサインインし、[Advisor](https://aka.ms/azureadvisordashboard) を開きます。

1. 操作バーの **[構成]** をクリックします。

1. Advisor の推奨事項を受け取らないサブスクリプションまたはリソース グループのチェック ボックスをオフにします。

    ![Advisor でのリソースの構成の例](./media/advisor-get-started/advisor-configure-resources.png)

1. **[Apply]** ボタンをクリックします。

## <a name="configure-low-usage-vm-recommendation"></a>使用率が低い VM の推奨事項を構成する

この手順では、使用率が低い仮想マシンの推奨事項に対する平均 CPU 使用率ルールを構成します。

Advisor は、仮想マシンの使用状況を 7 日間にわたって監視して、使用率が低い仮想マシンを識別します。 仮想マシンは、CPU 使用率が 5% 以下およびネットワーク使用率が 2% 以下の場合、または現在のワークロードがより小さい仮想マシン サイズで対応できる場合に、使用率が低いと見なされます。

使用率が低い仮想マシンを積極的に特定する場合は、サブスクリプションごとに平均 CPU 使用率ルールを調整できます。  CPU 使用率ルールを、5%、10%、15%、または 20% に設定できます。

> [!NOTE]
> 使用率が低い仮想マシンを特定するための平均 CPU 使用率ルールを調整するには、サブスクリプションの "*所有者*" である必要があります。  サブスクリプションまたはリソース グループに対する必要なアクセス許可がない場合、含めるまたは除外するオプションがユーザー インターフェイスで無効になります。 

1. [Azure Portal](https://portal.azure.com) にサインインし、[Advisor](https://aka.ms/azureadvisordashboard) を開きます。

1. 操作バーの **[構成]** をクリックします。

1. **[ルール]** タブをクリックします。

1. 平均 CPU 使用率ルールを調整するサブスクリプションを選択し、 **[編集]** をクリックします。

1. 平均 CPU 使用率の目的の値を選択し、 **[適用]** をクリックします。

1. **[推奨事項を更新する]** をクリックして、新しい平均 CPU 使用率ルールを使用する既存の推奨事項を更新します。 

   ![Advisor の推奨事項ルールの構成の例](./media/advisor-get-started/advisor-configure-rules.png)

## <a name="download-recommendations"></a>推奨事項をダウンロードする

Advisor では、推奨事項の概要をダウンロードできます。  推奨事項は、PDF ファイルまたは CSV ファイルとしてダウンロードできます。  推奨事項をダウンロードすることで、同僚と簡単に共有したり、推奨事項のデータで独自の分析を実行したりできます。

1. [Azure Portal](https://portal.azure.com) にサインインし、[Advisor](https://aka.ms/azureadvisordashboard) を開きます。

1. 操作バーの **[CSV としてダウンロードする]** または **[PDF としてダウンロードする]** をクリックします。

ダウンロード オプションでは、Advisor ダッシュボードに適用したフィルターが考慮されます。  推奨事項の特定のカテゴリまたは特定の推奨事項を表示しているときにダウンロード オプションを選択すると、ダウンロードされた概要には、そのカテゴリまたは推奨事項の情報だけが含まれます。 

## <a name="next-steps"></a>次の手順

Advisor の詳細については、次を参照してください。

- [Azure Advisor の概要](advisor-overview.md)
- [Advisor の高可用性に関する推奨事項](advisor-high-availability-recommendations.md)
- [Advisor のセキュリティに関する推奨事項](advisor-security-recommendations.md)
- [Advisor のパフォーマンスに関する推奨事項](advisor-performance-recommendations.md)
- [Advisor のコストに関する推奨事項](advisor-performance-recommendations.md)
