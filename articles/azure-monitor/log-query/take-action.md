---
title: Log Analytics でのユーザーが開始した Azure Automation Runbook アクション | Microsoft Docs
description: この記事では、Log Analytics の検索結果から Automation Runbook をオンデマンドで実行する方法について説明します。
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2019
ms.author: magoedte
ms.openlocfilehash: 9194d5fe6553607ac5a0bb4e133da97f53790984
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005354"
---
# <a name="take-action-with-an-automation-runbook-from-a-log-analytics-log-search-result"></a>Log Analytics のログ検索結果から Automation Runbook を使用してアクションを実行する

> [!NOTE]
> 検索結果からの Runbook の開始は、2019 年 2 月 15 日に非推奨となる、古いログ検索ポータルの機能です。 Azure Monitor では、[アラート ルール](../platform/alerts-log.md)からその他のアクションだけでなく、Runbook を開始できるアクション グループを構成できます。

Azure Log Analytics のログ検索結果から、**[Take action] \(アクションの実行)** を選択して Automation Runbook を実行できるようになりました。  Runbook を使用すると、問題を修正したり、トラブルシューティング情報の収集、電子メールの送信、サービス要求の作成などのその他の何らかのアクションを実行したりできます。 


## <a name="components-and-features-used"></a>使用されるコンポーネントと機能
* [Azure Automation アカウント](../../automation/automation-quickstart-create-account.md)
* [Log Analytics ワークスペース](../../azure-monitor/log-query/log-query-overview.md)

## <a name="to-initiate-runbook-from-log-search"></a>ログ検索から Runbook を開始するには

イベントに対してアクションを実行し、ログ検索結果から Runbook を開始するには、ログ検索の作成から始め、その結果から Runbook をオンデマンドで呼び出すことができます。 これは、[Azure portal](../../azure-monitor/log-query/log-query-overview.md) のクラシック ログ検索機能から実現できます。 この例では、この機能の基本的なデモンストレーションを使用して Azure Portal からログ検索を実行します。

1. Azure Portal で、**[すべてのサービス]** をクリックし、**[Log Analytics]** を選択します。  
2. Log Analytics ワークスペースを選択します。
3. ワークスペースで、**[ログ (クラシック)]** を選択します。  
4. [ログ検索] ページで、ログ検索を実行します。  
5. ログ検索結果から、いずれかのフィールドの左にある省略記号ボタンをクリックし、ポップアップから **[Take action on] \(アクションの実行対象)** を選択します。<br><br> ![検索結果から [Take action] \(アクションの実行) を選択する](./media/take-action/log-search-takeaction-menuoption.png) 
6. **[Run a runbook] (Runbook の実行)** を選択し、実行する Runbook を選択します。  Log Analytics ワークスペースにリンクされている Automation アカウントで任意の Runbook を選択できます。  以下の点に注意してください。

    * Runbook はタグごとに整理されています
    * Runbook の入力パラメータ値は、検索結果のフィールドから直接選択できます。  ドロップダウン リストが表示され、選択対象となる、結果から使用可能なすべてのフィールドが表示されます。  
    * 問題が存在するマシンのみをメンバーとして含む対応する Hybrid Runbook Worker グループがある場合は、このマシンにインストールされている [Hybrid Runbook Worker](../../automation/automation-hybrid-runbook-worker.md) で Runbook を実行することを選択できます。  Hybrid Worker グループの名前がログ検索結果にあるコンピューターの名前に一致している場合は、そのグループが自動的に選択されます。    

6. **[実行]** をクリックすると、Runbook ジョブ ページが開き、ジョブの状態を確認できるようになります。   

[Log Analytics アラートから呼び出される](../../automation/automation-create-alert-triggered-runbook.md)ように構成された Runbook を選択した場合、その Runbook には、**[オブジェクト]** の種類である **WebhookData** という名前の入力パラメータがあります。  その入力パラメータが必須である場合は、Runbook が JSON 形式の文字列をオブジェクトの種類に変換することによってユーザーが Runbook アクティビティで参照する特定の項目に対してフィルター処理できるように、検索結果を Runbook に渡す必要があります。  これは、ドロップダウン リストから **[Search result (Object)] \(検索結果 (オブジェクト))** を選択することによって実行します。<br><br> ![Runbook パラメーターの Webhook データ オブジェクトを選択する](media/take-action/select-runbook-and-properties.png)   
    
## <a name="next-steps"></a>次の手順

* Log Analytics で使用できるすべての検索フィールドとファセットは、 [Log Analytics のログ検索のリファレンス](../../azure-monitor/log-query/log-query-overview.md) でご覧いただけます。
* Automation Runbook を自動的に呼び出す方法を学習するには、「[calling an Azure Automation runbook from a Log Analytics alert (Log Analytics アラートから Azure Automation Runbook を呼び出す)](../../automation/automation-create-alert-triggered-runbook.md)」をレビューしてください。  
