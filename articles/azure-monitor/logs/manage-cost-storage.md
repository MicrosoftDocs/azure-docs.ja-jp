---
title: Azure Monitor ログの使用量とコストを管理する
description: Azure Monitor で Log Analytics ワークスペースの料金プランを変更し、データ ボリュームとアイテム保持ポリシーを管理する方法について説明します。
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: azure-monitor
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: bwren
ms.openlocfilehash: 89264bc17180aaf47611aef73c9fd20427bce104
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104772282"
---
# <a name="manage-usage-and-costs-with-azure-monitor-logs"></a>Azure Monitor ログで使用量とコストを管理する    

> [!NOTE]
> この記事では、Azure Monitor ログにかかるコストを理解し、それを制御する方法について説明します。 関連記事の「[使用量と推定コストの監視](../usage-estimated-costs.md)」では、さまざまな価格モデルに対する複数の Azure 監視機能全体の使用量と推定コストを表示する方法について説明します。 この記事に記載されているすべての価格とコストは、単に例示を目的としています。 

Azure Monitor ログは、企業内のソースまたは Azure に展開されたソースから毎日大量のデータを収集し、インデックスを付けて、保存する処理をスケーリングおよびサポートするように設計されています。  これは組織の主要な原動力になる場合がありますが、最終的に基になる原動力はコスト効率です。 そのためには、Log Analytics ワークスペースのコストは、収集されるデータのボリュームだけでなく、選択されているプラン、および接続されたソースから生成されたデータの保持期間にも依存することを、理解しておくことが重要です。  

この記事では、取り込まれたデータ ボリュームとストレージの拡大を事前に監視し、制限を定義して関連コストを制御する方法を確認します。 

## <a name="pricing-model"></a>価格モデル

Log Analytics の既定の料金は、取り込まれたデータの量に基づく **従量課金制** であり、必要に応じてデータの保持期間を長くすることができます。 データ量は、GB (10^9 バイト) 単位で格納されるデータのサイズとして測定されます。 各 Log Analytics ワークスペースは個々のサービスとして課金され、Azure サブスクリプションの課金内容に加えられます。 データ インジェストの量は、次の要因に大きく依存する可能性があります。 

  - 有効にされている管理ソリューションの数とその構成
  - 監視対象 VM の数
  - 各監視対象 VM から収集されるデータの種類 
  
従量課金制モデルに加えて、Log Analytics には **容量予約** レベルがあります。これにより、従量課金制の料金と比較して 25 % も節約できます。 容量予約の価格を選択すると、1 日あたり 100 GB から予約を購入できます。 予約レベルを超える使用量は、従量課金制で請求されます。 容量予約レベルには、31 日間のコミットメント期間があります。 コミットメント期間中は、より高いレベルの容量予約レベルに変更できます (これにより 31 日間のコミットメント期間が再スタートされます) が、コミットメント期間が終了するまでは、従量課金制または低い容量予約レベルに戻ることはできません。 容量予約レベルの課金は 1 日単位で行われます。 Log Analytics の従量課金制および容量予約の価格の詳細については、[こちら](https://azure.microsoft.com/pricing/details/monitor/)をご覧ください。 

イベントのデータ サイズは、すべての価格レベルで、このイベントの Log Analytics に格納されているプロパティの文字列形式から計算されます。データがエージェントから送信されたか、インジェスト プロセス中に追加されたかは関係ありません。 これには、データが収集されて Log Analytics に格納されるときに追加されるすべての[カスタム フィールド](custom-fields.md)が含まれます。 一部の [Log Analytics 標準プロパティ](./log-standard-columns.md)を含む、すべてのデータ型に共通するいくつかのプロパティは、イベント サイズの計算から除外されます。 これには、`_ResourceId`、`_SubscriptionId`、`_ItemId`、`_IsBillable`、`_BilledSize`、および `Type` が含まれます。 Log Analytics に格納されている他のすべてのプロパティは、イベント サイズの計算に含まれます。 AzureActivity、Heartbeat、Usage 型などの一部のデータ型に対しては、データ インジェスト料金は一切かかりません。 イベントがデータ インジェストの課金から除外されたかどうかを確認するには、[以下](#data-volume-for-specific-events)に示すように `_IsBillable` プロパティを使用できます。 使用量は GB (1.0E9 バイト) 単位でレポートされます。 

また、[Azure Security Center](https://azure.microsoft.com/pricing/details/security-center/)、[Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/)、 [構成管理](https://azure.microsoft.com/pricing/details/automation/) などの一部のソリューションには、独自の価格モデルがあります。 

### <a name="log-analytics-dedicated-clusters"></a>Log Analytics 専用クラスター

Log Analytics 専用クラスターは、ワークスペースのコレクションを単一の管理された Azure Data Explorer クラスターに集約し、[カスタマー マネージド キー](customer-managed-keys.md)などの高度なシナリオをサポートします。  Log Analytics 専用クラスターには、1000 GB/日以上に構成されている必要がある容量予約価格モデルを使用します。 この容量レベルでは、従量課金制の価格と比較して、25% の割引があります。 予約レベルを超える使用量は、従量課金制で請求されます。 クラスターの容量予約には、予約レベルが増加した後の 31 日間のコミットメント期間があります。 コミットメント期間中は、容量予約レベルを下げることはできませんが、いつでも増やすことができます。 ワークスペースがクラスターに関連付けられている場合、それらのワークスペースのデータ インジェストの課金は、構成されている容量予約レベルを使用したクラスター レベルで行われます。 詳細については、[Log Analytics クラスターの作成](customer-managed-keys.md#create-cluster)と、[それへのワークスペースの関連付け](customer-managed-keys.md#link-workspace-to-cluster)に関するページを参照してください。 容量予約の価格情報については、[Azure Monitor の価格に関するページ]( https://azure.microsoft.com/pricing/details/monitor/)を参照してください。  

クラスター容量予約レベルは、`Sku` の下にある `Capacity` パラメーターを使用して、Azure Resource Manager でプログラムによって構成します。 `Capacity` は GB 単位で指定し、1000 GB/日以上の値を 100 GB/日の単位で設定できます。 詳細は「[Azure Monitor のカスタマー マネージド キー](customer-managed-keys.md#create-cluster)」にあります。 クラスターで 2000 GB/日を超える予約が必要な場合は、[LAIngestionRate@microsoft.com](mailto:LAIngestionRate@microsoft.com) にお問い合わせください。

クラスターでの使用については、2 つの課金モードがあります。 これらは、[ご自分のクラスターを構成する](customer-managed-keys.md#customer-managed-key-operations)ときに、`billingType` パラメーターで指定できます。 次の 2 つのモードがあります。 

1. **クラスター**: この場合 (既定)、取り込まれたデータに対してクラスター レベルで課金されます。 クラスターに関連付けられている各ワークスペースが取り込んだデータ量が集計され、クラスターの日次請求が計算されます。 [Azure Security Center](../../security-center/index.yml) からのノードごとの割り当ては、クラスター内のすべてのワークスペースで集計されるデータのこの集計の前にワークスペース レベルで適用されることに注意してください。 

2. **ワークスペース**: ご使用のクラスターの容量予約コストは、クラスター内のワークスペースに比例します (各ワークスペースに対する [Azure Security Center](../../security-center/index.yml) からのノードごとの割り当てを考慮した後)。1 日の間にワークスペースに取り込まれたデータ ボリュームの合計が容量予約よりも少ない場合、ワークスペースごとに容量予約分として取り込まれたデータに対して有効な 1 GB あたりの容量予約料金で課金され、容量予約の未使用分についてはクラスター リソースに課金されます。 1 日の間にワークスペースに取り込まれたデータ ボリュームの合計が容量予約よりも多い場合、ワークスペースごとにその日に取り込まれたデータの容量予約分について課金され、ワークスペースごとに容量予約を超えて取り込まれたデータ分について課金されます。 1 日の間にワークスペースに取り込まれたデータ ボリュームの合計が容量予約を超えている場合、クラスター リソースに課金されることはありません。

クラスター課金オプションでは、データ保有はワークスペースごとに課金されます。 クラスターが作成されると、ワークスペースがそのクラスターに関連付けられているかどうかに関係なく、クラスターの課金が開始されることに注意してください。 また、クラスターに関連付けられているワークスペースの価格レベルはなくなりました。

## <a name="estimating-the-costs-to-manage-your-environment"></a>ご利用の環境を管理するためのコストの見積もり 

Azure Monitor ログをまだ使用していない場合は、[Azure Monitor 料金計算ツール](https://azure.microsoft.com/pricing/calculator/?service=monitor)を使用して、Log Analytics を使用する場合のコストを見積もることができます。 まず、[検索] ボックスに「Azure Monitor」と入力し、結果として表示される Azure Monitor タイルをクリックします。 ページを下にスクロールして Azure Monitor に移動し、[種類] ドロップダウンから [Log Analytics] を選択します。  ここで、VM の数と、各 VM から収集すると予想されるデータ量 (GB) を入力できます。 通常、1 GB から 3 GB のデータ月が、一般的な Azure VM から取り込まれています。 既に Azure Monitor ログを評価している場合は、独自の環境からのデータ統計を使用することができます。 [監視対象の VM の数](#understanding-nodes-sending-data)と[ご利用のワークスペースによって取り込まれているデータ量](#understanding-ingested-data-volume)を特定する方法については、以下を参照してください。 

## <a name="understand-your-usage-and-estimate-costs"></a>ご自分の使用量を理解してコストを見積もる

現在 Azure Monitor ログを使用している場合は、最近の使用パターンに基づいてコストがどのくらいになるかを容易に理解できます。 これには、データ使用状況を確認して分析するために **Log Analytics の [使用量と推定コスト]** を使用します。 これにより、各ソリューションによって収集されるデータの量、保持されるデータの量、および取り込まれたデータ量と無料使用量を超える追加リテンション期間に基づいたコストの推定値が示されます。

:::image type="content" source="media/manage-cost-storage/usage-estimated-cost-dashboard-01.png" alt-text="使用量と推定コスト":::

データを詳細に調査するには、 **[使用量と推定コスト]** ページでグラフの右上にあるアイコンをクリックします。 これで、このクエリを操作して使用状況の詳細を調査できます。  

:::image type="content" source="media/manage-cost-storage/logs.png" alt-text="ログ ビュー":::

**[使用量と推定コスト]** ページでは、該当の月のデータ ボリュームを確認できます。 これには、受信して Log Analytics ワークスペースに格納されたすべての課金対象データが含まれます。  
 
Log Analytics の課金は Azure の課金内容に加えられます。 Azure Portal の [課金] または [Azure Billing Portal](https://account.windowsazure.com/Subscriptions) で、Azure の課金内容の詳細を確認できます。  

## <a name="viewing-log-analytics-usage-on-your-azure-bill"></a>ご自分の Azure 請求書での Log Analytics の使用状況の表示 

Azure では、[Azure Cost Management と課金](../../cost-management-billing/costs/quick-acm-cost-analysis.md?toc=%2fazure%2fbilling%2fTOC.json)ハブに便利な機能が多数用意されています。 たとえば、"コスト分析" 機能を使用すると、Azure リソースに対するご自分の支出を表示できます。 まず、"リソースの種類" によるフィルターを追加します (Log Analytics の場合は microsoft.operationalinsights/workspace、Log Analytics クラスターの場合は microsoft.operationalinsights/cluster)。これにより、Log Analytics の支出を追跡できます。 次に、[グループ化] で [測定カテゴリ] または [測定] を選択します。  Azure Security Center や Azure Sentinel などの他のサービスも、Log Analytics ワークスペース リソースに対して使用量の請求を行うことに注意してください。 サービス名へのマッピングを表示するには、グラフの代わりにテーブル ビューを選択できます。 

[Azure portal から使用状況をダウンロード](../../cost-management-billing/manage/download-azure-invoice-daily-usage-date.md#download-usage-in-azure-portal)することで、使用状況をさらに詳しく理解できます。 ダウンロードしたスプレッドシートでは、Azure リソースごとに (たとえば、Log Analytics ワークスペース)、1 日あたりの使用量を確認することができます。 この Excel スプレッドシートでは、ご利用の Log Analytics ワークスペースからの使用状況を検索できます。それには、まず、"測定カテゴリ" 列でフィルター処理を行って "Log Analytics"、"洞察と分析" (従来の価格レベルの一部で使用)、"Azure Monitor" (容量予約の価格レベルで使用) を表示し、次に "インスタンス ID" 列に対するフィルター "contains workspace" または "contains cluster" (後者の場合は、Log Analytics クラスターの使用量を含めます) を追加します。 使用量は "消費量" 列に表示され、各エントリの単位は "測定単位" 列に表示されます。  詳細については、「[Microsoft Azure の課金内容を確認する](../../cost-management-billing/understand/review-individual-bill.md)」を参照してください。 

## <a name="changing-pricing-tier"></a>価格レベルの変更

ワークスペースの Log Analytics 価格レベルを変更するには、 

1. Azure portal で、ワークスペースから **[使用量と推定コスト]** を開きます。このワークスペースでは、使用可能な各価格レベルの一覧が表示されます。

2. それぞれの価格レベルについての推定コストを確認します。 この見積もりは、過去 31 日間の使用量に基づいているため、このコストの見積もりは、通常の使用状況を表す直近の 31 日間に依存します。 次の例では、過去 31 日間のデータ パターンに基づいて、このワークスペースのコストが 100 GB/日の容量予約レベル (#2) と比較して従量課金制レベル (#1) ではどれほど低くなるかを確認することができます。  

:::image type="content" source="media/manage-cost-storage/pricing-tier-estimated-costs.png" alt-text="価格レベル":::
    
3. 過去 31 日間の使用量に基づいて推定コストを確認した後、価格レベルを変更する場合は、 **[選択 ]** をクリックします。  

`sku` パラメーター (Azure Resource Manager テンプレートの `pricingTier`) を使用して [Azure Resource Manager 経由で価格レベルを設定](../samples/resource-manager-workspace.md)することもできます。 

## <a name="legacy-pricing-tiers"></a>レガシ価格レベル

2018 年 4 月 2 日より前に Log Analytics ワークスペースまたはその中の Application Insights リソースがあったサブスクリプション、あるいは 2019 年 2 月 1 日より前に開始したマイクロソフト エンタープライズ契約にリンクされているサブスクリプションは、引き続きレガシ価格レベルにアクセスして利用できます: **Free**、**スタンドアロン (GB あたり)** 、**ノードごと (OMS)** 。  Free 価格レベルのワークスペースでは、毎日のデータ インジェストの上限が 500 MB になります ([Azure Security Center](../../security-center/index.yml) によって収集されるセキュリティ データの種類を除く)。また、データの保持は 7 日に制限されます。 Free 価格レベルでは、評価のみが目的として意図されています。 スタンドアロンまたはノードごとの価格レベルのワークスペースには、30 日から 730 日までのユーザーが構成可能な保持が含まれています。

スタンドアロン価格レベルでの使用量は、取り込まれたデータ ボリューム単位で請求されます。 これは **Log Analytics** サービス上で報告され、メーターには "分析対象データ" という名前が付けられています。 

ノードごとの価格レベルは、1 時間単位の細分性で監視対象の VM (ノード) ごとに課金されます。 監視対象のノードごとに、ワークスペースには 1 日あたり 500 MB の課金されないデータが割り当てられます。 この割り当ては、1 時間ごとの細分性で計算され、毎日ワークスペース レベルで集計されます。 集計される 1 日のデータ割り当てを超えて取り込まれたデータは、データ超過分として GB 単位で課金されます。 請求書では、ワークスペースがノードごとの価格レベルの場合、サービスはログ分析の利用状況の **洞察と分析** になることに注意してください。 使用量は、次の 3 つのメーターで報告されます。

1. ノード: これは、ノード * 月の単位での監視対象ノード (VM) 数に対応した使用量です。
2. ノードごとの超過データ: これは、集計されたデータの割り当てを超える取り込まれたデータの GB 数です。
3. ノードごとに含まれるデータ: これは、集計されたデータの割り当ての範囲に含まれる取り込まれたデータ量です。 このメーターは、すべての価格レベルにワークスペースが存在する場合に、Azure Security Center によって提供される範囲のデータ量を示するためにも使用されます。

> [!TIP]
> ワークスペースで **ノードごとの** 価格レベルにアクセスできるが、従量課金制レベルよりもコストが低いかどうか知りたい場合は、[次のクエリを使用](#evaluating-the-legacy-per-node-pricing-tier)して、推奨事項を簡単に取得できます。 

2016 年 4 月より前に作成されたワークスペースの場合、元の **Standard** と **Premium** の価格レベルにもアクセスできます。これらのデータ保持は、それぞれ 30 日と 365 日に固定されています。 **Standard** または **Premium** の価格レベルに新しいワークスペースを作成することはできません。ワークスペースをこれらのレベルから移動した場合、元に戻すことはできません。 これらの従来のレベルでのデータ インジェストのメーターは、"分析対象データ" と呼ばれています。

また、従来の Log Analytics レベルの使用と [Azure Security Center](../../security-center/index.yml) に対する使用量の請求方法の間にもいくつかの動作があります。 

1. ワークスペースが従来の Standard レベルまたは Premium レベルの場合、Azure Security Center はノードごとではなく Log Analytics データ インジェストに対してのみ課金されます。
2. ワークスペースが従来の Per Node レベルの場合、Azure Security Center は、現在の [Azure Security Center ノード ベースの価格モデル](https://azure.microsoft.com/pricing/details/security-center/)を使用して課金されます。 
3. 他の価格レベル (容量予約を含む) では、2017 年 6 月 19 日より前に Azure Security Center が有効になっている場合、Azure Security Center は Log Analytics データ インジェストに対してのみ課金されます。 それ以外の場合、Azure Security Center は、現在の Azure Security Center ノード ベースの価格モデルを使用して課金されます。

価格レベルの制限に関する詳細は、「[Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)」にあります。

レガシ価格レベルには、リージョンベースの価格がありません。  

> [!NOTE]
> OMS E1 Suite、OMS E2 Suite、または OMS Add-On for System Center のいずれかを購入することによって得られる資格を使用するには、OMS Log Analytics の *ノード単位* の価格レベルを選択します。

## <a name="log-analytics-and-security-center"></a>Log Analytics と Security Center

[Azure Security Center](../../security-center/index.yml) の課金は Log Analytics の課金と密接に結び付けられています。 Security Center では、[セキュリティ データの種類](/azure/azure-monitor/reference/tables/tables-category#security)のサブセット (WindowsEvent、SecurityAlert、SecurityBaseline、SecurityBaselineSummary、SecurityDetection、SecurityEvent、WindowsFirewall、MaliciousIPCommunication、LinuxAuditLog、SysmonEvent、ProtectionStatus) と、Update Management ソリューションがワークスペースで実行されていないか、またはソリューションのターゲット設定が有効にされている場合に、Update および UpdateSummary データの種類に対して 500 MB/ノード/日の割り当てが行われます。 ワークスペースがレガシのノードごとの価格レベルにある場合、Security Center と Log Analytics の割り当てが結合されて、すべての課金対象の取り込まれたデータにまとめて適用されます。  

## <a name="change-the-data-retention-period"></a>データ保持期間の変更

次の手順では、ワークスペースにログ データを保持する期間を構成する方法について説明します。 ワークスペース レベルでのデータ保持期間は、従来の Free 価格レベルを使用している場合を除き、すべてのワークスペースで 30 ～ 730 日 (2 年) に構成できます。 個々のデータ型の保有期間は、最短 4 日に設定できます。 データ保持期間の延長の価格の[詳細](https://azure.microsoft.com/pricing/details/monitor/)を参照してください。  730 日を超えてデータを保持するには、[Log Analytics ワークスペースのデータのエクスポート](logs-data-export.md)を使用することを検討してください。

### <a name="workspace-level-default-retention"></a>ワークスペース レベルの既定の保有期間

ご利用のワークスペースに既定のリテンション期間を設定するには 
 
1. Azure portal で、ご利用のワークスペースの左ウィンドウから **[使用量と推定コスト]** を選択します。
2. **[使用とコストの見積もり]** ページの上部にある **[データ保有期間]** をクリックします。
3. ウィンドウで、スライダーを移動して日数を増減し、 **[OK]** をクリックします。  *無料* プランをご利用の場合は、データ保持期間を変更できません。この設定を制御するには、有料プランにアップグレードする必要があります。

:::image type="content" source="media/manage-cost-storage/manage-cost-change-retention-01.png" alt-text="ワークスペースのデータ保持設定の変更":::

保持期間が短縮された場合、新しい保持設定よりも古いデータが削除されるまでには、数日の猶予期間があります。 

**[データ保有期間]** ページでは、30、31、60、90、120、180、270、365、550、および 730 日の保有期間設定を使用できます。 別の設定が必要な場合は、[Azure Resource Manager](../samples/resource-manager-workspace.md) で `retentionInDays` パラメーターを使用して構成できます。 データの保持期間を 30 日に設定すると、`immediatePurgeDataOn30Days` パラメーターを使用して、(数日の猶予期間を除去し) より古いデータの即時消去をトリガーできます。 これは、即時のデータ削除が不可欠なコンプライアンス関連のシナリオに役立ちます。 この即時消去機能は、Azure Resource Manager 経由でのみ公開されています。 

30 日の保持期間があるワークスペースでは、実際には 31 日間分のデータを保持することができます。 データを 30 日間しか保持しないことが不可欠な場合は、Azure Resource Manager を使用して保持期間を 30 日に設定したうえで、`immediatePurgeDataOn30Days` パラメーターを指定します。  

既定では、2 種類のデータ `Usage` と `AzureActivity` が少なくとも 90 日間保持され、この 90 日間の保持に対しては課金されません。 ワークスペースの保持期間が 90 日を超えた場合、これらのデータの種類の保持期間も長くなります。  これらのデータの種類は、データ インジェスト料金の対象にもなりません。 

ワークスペース ベースの Application Insights リソース (`AppAvailabilityResults`、`AppBrowserTimings`、`AppDependencies`、`AppExceptions`、`AppEvents`、`AppMetrics`、`AppPageViews`、`AppPerformanceCounters`、`AppRequests`、`AppSystemEvents`、`AppTraces`) のデータの種類も既定で 90 日間保持され、この 90 日間の保持に対しては課金されません。 保持期間は、データの種類別のリテンション期間によって調整できます。 

Log Analytics [消去 API](/rest/api/loganalytics/workspacepurge/purge) はデータ保有の課金に影響せず、非常に限られたケースでの使用が意図されています。 データ保有の課金を減らすには、ワークスペースか特定のデータ型に対してリテンション期間を短縮する必要があります。 

### <a name="retention-by-data-type"></a>データの種類別のリテンション期間

また、個々のデータの種類に対して、4 から 730 日でそれぞれ異なるリテンション期間を指定することもできます (従来の Free 価格レベルのワークスペースを除きます)。これは、ワークスペース レベルの既定の保有期間をオーバーライドします。 それぞれのデータの種類は、ワークスペースのサブリソースです。 たとえば、SecurityEvent テーブルは [Azure Resource Manager](../../azure-resource-manager/management/overview.md) で次のようにアドレス指定できます。

```
/subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent
```

データの種類 (テーブル) では大文字と小文字が区別されることに注意してください。  特定のデータ型 (この例では SecurityEvent) について、データ型別のリテンション期間の現在の設定を取得するには、次を使用します。

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
```

> [!NOTE]
> データ型のリテンション期間は、そのデータ型に対して明示的に設定されている場合にのみ返されます。  リテンション期間が明示的に設定されていない (そのため、ワークスペースのリテンション期間を継承する) データ型は、この呼び出しからは何も返しません。 

ご使用のワークスペース内でデータ型別のリテンション期間が設定されているすべてのデータ型について、データ型別のリテンション期間の現在の設定を取得するには、次のように特定のデータ型を単に省略します。

```JSON
    GET /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables?api-version=2017-04-26-preview
```

特定のデータの種類 (この例では SecurityEvent) のリテンション期間を 730 日に設定するには、次のようにします。

```JSON
    PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview
    {
        "properties": 
        {
            "retentionInDays": 730
        }
    }
```

`retentionInDays` の有効な値は、30 ～ 730 です。

データの種類 `Usage` および `AzureActivity` は、カスタム リテンション期間を使用して設定することはできません。 これらは既定のワークスペース リテンション期間の最大値 (90 日) を取ります。 

Azure Resource Manager に直接接続してデータの種類別にリテンション期間を設定する優れたツールとして、OSS ツール [ARMclient](https://github.com/projectkudu/ARMClient) があります。  ARMclient の詳細については、[David Ebbo](http://blog.davidebbo.com/2015/01/azure-resource-manager-client.html) および [Daniel Bowbyes](https://blog.bowbyes.co.nz/2016/11/02/using-armclient-to-directly-access-azure-arm-rest-apis-and-list-arm-policy-details/) による記事を参照してください。  ARMClient を使用して、SecurityEvent データを 730 日のリテンション期間に設定する例を次に示します。

```
armclient PUT /subscriptions/00000000-0000-0000-0000-00000000000/resourceGroups/MyResourceGroupName/providers/Microsoft.OperationalInsights/workspaces/MyWorkspaceName/Tables/SecurityEvent?api-version=2017-04-26-preview "{properties: {retentionInDays: 730}}"
```

> [!TIP]
> 各データの種類別にリテンション期間を設定すると、データ保有のコストを減らすことができます。  2019 年 10 月以降 (この機能がリリースされたタイミング) に収集されたデータで、リテンション期間を減らしたデータの種類では、時間が経つにつれデータ保有コストを減らすことができます。  それ以前に収集されたデータについては、種類ごとにリテンション期間の設定を減らしても、保有コストには影響しません。  

## <a name="manage-your-maximum-daily-data-volume"></a>ご自分のデータの 1 日の最大ボリュームを管理する

日次上限を構成して、ワークスペースの毎日のインジェストを制限できますが、1 日の上限に達することが目標ではないので注意する必要があります。  そうしないと、その日の残りの時間についてデータが失われ、ワークスペースで最新のデータが利用できることに依存する機能を持つ他の Azure サービスやソリューションに影響を与える可能性があります。  その結果、IT サービスをサポートするリソースの正常性状態を監視してアラートを受け取る機能が影響を受けます。  日次上限は、マネージド リソースからのデータ ボリュームの **予期しない増加** を管理して制限内に留めるため、またはワークスペースの計画外の料金を制限する手段として使うためにあります。 ワークスペース上での 1 日あたりに見合うように日次上限を設定するのは、適切ではありません。

各ワークスペースには、1 日の異なる時間に適用される 1 日の上限があります。 リセット時間は、 **[日次上限]** ページに表示されます (下記参照)。 このリセット時間は構成できません。 

日次上限に達するとすぐに、課金対象のデータの種類の収集は、その日はそれ以上行われません。 (日次上限の適用には待ち時間が内在するため、指定された日次上限のレベルは正確には適用されません)。 選択した Log Analytics ワークスペースのページの上部に警告バナーが表示され、**LogManagement** カテゴリの *Operation* テーブルに操作イベントが送信されます。 [*1 日の上限を次に設定する*] で定義されているリセット時刻が過ぎると、データ収集が再開されます。 この操作イベントに基づいてアラート ルールを定義し、データの日次制限に達したら通知するよう構成することをお勧めします ([下記](#alert-when-daily-cap-reached)を参照)。 

> [!NOTE]
> 特にワークスペース上で大量のデータを受信している場合には、日次上限によって、指定された上限レベルで厳密にデータ収集を停止することはできず、一部のデータが超過することが予想されます。 日次上限の動作を確認する際に役立つクエリについては、[下記](#view-the-effect-of-the-daily-cap)を参照してください。 

> [!WARNING]
> 日次上限によって、データの種類 (WindowsEvent、SecurityAlert、SecurityBaseline、SecurityBaselineSummary、SecurityDetection、SecurityEvent、WindowsFirewall、MaliciousIPCommunication、LinuxAuditLog、SysmonEvent、ProtectionStatus、Update および UpdateSummary) の収集が停止することはありません。ただし、2017 年 6 月 19 日より前に Azure Security Center がインストールされたワークスペースは除きます。 

### <a name="identify-what-daily-data-limit-to-define"></a>定義する日次データ制限を明らかにする

[Log Analytics の使用量と推定コスト](../usage-estimated-costs.md)に関する記事を参照し、データ インジェストの傾向および定義する日次データ ボリューム上限について理解してください。 上限に達した後はリソースを監視できなくなるので、慎重に検討してください。 

### <a name="set-the-daily-cap"></a>1 日の上限を設定する

次の手順では、Log Analytics ワークスペースが 1 日に取り込むデータのボリュームを管理する制限を構成する方法について説明します。  

1. ワークスペースの左ウィンドウから **[使用量と推定コスト]** を選びます。
2. 選択したワークスペースの **[使用とコストの見積もり]** ページの上部にある **[Data Cap]\(データの上限\)** をクリックします。 
3. 日次上限が既定で **[オフ]** になっていますか。 有効にするには、 **[オン]** をクリックし、GB/日でデータ ボリュームの制限を設定します。

:::image type="content" source="media/manage-cost-storage/set-daily-volume-cap-01.png" alt-text="Log Analytics のデータ制限の構成":::
    
1 日の上限は、「[ワークスペース - 作成または更新](/rest/api/loganalytics/workspaces/createorupdate#workspacecapping)」で説明されているように、`WorkspaceCapping` の下の `dailyQuotaGb` パラメーターを設定することにより、ARM を介して構成できます。 

### <a name="view-the-effect-of-the-daily-cap"></a>日次上限の効果を表示する

日次上限の効果を表示する際に重要な点は、日次上限に含まれていないセキュリティ データの種類と、ワークスペースのリセット時間を考慮することです。 日次上限のリセット時間は、 **[日次上限]** ページに表示されます。  次のクエリを使用すると、日次上限のリセット間で日次上限の対象となるデータ ボリュームを追跡できます。 この例では、ワークスペースのリセット時間は 14:00 です。  この時間は、ワークスペース用に更新する必要があります。

```kusto
let DailyCapResetHour=14;
Usage
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend TimeGenerated=datetime_add("hour",-1*DailyCapResetHour,TimeGenerated)
| where TimeGenerated > startofday(ago(31d))
| where IsBillable
| summarize IngestedGbBetweenDailyCapResets=sum(Quantity)/1000. by day=bin(TimeGenerated, 1d) | render areachart  
```

(Usage データ型では、`Quantity` の単位は MB です。)

### <a name="alert-when-daily-cap-reached"></a>1 日の上限に達したら警告する

データ制限のしきい値に達したら Azure Portal に視覚的な合図が表示されますが、この動作は、早急な措置を必要とする運用上の問題を管理する方法と、必ずしも一致していない場合があります。  アラート通知を受け取るには、Azure Monitor で新しいアラート ルールを作成します。  詳細については、[アラートを作成、表示、管理する方法](../alerts/alerts-metric.md)に関するページをご覧ください。

はじめに、`_LogOperation` 関数を使用して、`Operation` テーブルのクエリを実行するアラートの推奨される設定を次に示します。 

- ターゲット:お客様の Log Analytics リソースを選択します
- 条件: 
   - シグナル名: カスタム ログ検索
   - 検索クエリ: `_LogOperation | where Operation == "Data Collection Status" | where Detail contains "OverQuota"`
   - ベース: 結果の数
   - 条件: より大きい
   - しきい値: 0
   - 期間: 5 (分)
   - 頻度: 5 (分)
- アラート ルール名: 1 日のデータ制限に達しました
- 重大度: 警告 (重大度 1)

アラートを定義した後で制限に達すると、アラートがトリガーされ、アクション グループで定義されている応答が実行されます。 メールおよびテキスト メッセージでチームに通知したり、webhook、Automation Runbook、または[外部の ITSM ソリューションとの統合](../alerts/itsmc-definition.md#create-itsm-work-items-from-azure-alerts)を使ってアクションを自動化したりできます。 

## <a name="troubleshooting-why-usage-is-higher-than-expected"></a>使用量が予想よりも多い理由のトラブルシューティング

使用量が多くなる原因は、次のいずれかまたは両方です。
- 予想よりも多くのノードが Log Analytics ワークスペースにデータを送信している
- 予想よりも多くのデータが Log Analytics ワークスペースに送信されている (新しいソリューションの使用を開始したか、既存のソリューションの構成を変更したことが原因である可能性があります)。

## <a name="understanding-nodes-sending-data"></a>データを送信するノードについて理解する

過去 1 か月間の日単位のエージェントからのハートビートを報告するノード数を把握するには、次を使用します

```kusto
Heartbeat 
| where TimeGenerated > startofday(ago(31d))
| summarize nodes = dcount(Computer) by bin(TimeGenerated, 1d)    
| render timechart
```
過去 24 時間でデータを送信したノードの数を取得するには、次のクエリを使用します。 

```kusto
find where TimeGenerated > ago(24h) project Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodes = dcount(computerName)
```

データを送信するノードの一覧 (およびそれぞれによって送信されたデータの量) を取得するには、次のクエリを使用できます。

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, Computer
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize TotalVolumeBytes=sum(_BilledSize) by computerName
```

### <a name="nodes-billed-by-the-legacy-per-node-pricing-tier"></a>レガシのノードごとの価格レベルによって課金されるノード

[レガシのノードごとの価格レベル](#legacy-pricing-tiers)では、1 時間ごとの細分性でノードに課金され、一連のセキュリティ データの種類のみを送信するノードはカウントされません。 その毎日のノードのカウントは、次のクエリに近くなります。

```kusto
find where TimeGenerated >= startofday(ago(7d)) and TimeGenerated < startofday(now()) project Computer, _IsBillable, Type, TimeGenerated
| where Type !in ("SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent")
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| where _IsBillable == true
| summarize billableNodesPerHour=dcount(computerName) by bin(TimeGenerated, 1h)
| summarize billableNodesPerDay = sum(billableNodesPerHour)/24., billableNodeMonthsPerDay = sum(billableNodesPerHour)/24./31.  by day=bin(TimeGenerated, 1d)
| sort by day asc
```

請求書の単位数は、クエリで `billableNodeMonthsPerDay` によって表されるノード * 月の単位になります。 ワークスペースに Update Management ソリューションがインストールされている場合は、上記のクエリの where 句の一覧に Update および UpdateSummary データの種類を追加します。 最後に、上記のクエリで表されていないソリューションのターゲット設定を使用した場合に、実際の課金アルゴリズムにいくらかの複雑さが加わります。 


> [!TIP]
> 複数の種類のデータにわたるスキャンは、実行に[多量のリソースを使うため](../log-query/query-optimization.md#query-performance-pane)、これらの `find` クエリは多用しないようにします。 **コンピューターごと** の結果が不要な場合は、Usage データ型に関するクエリを実行します (下記参照)。

## <a name="understanding-ingested-data-volume"></a>取り込まれたデータ ボリュームについて理解する

**[使用量と推定コスト]** ページの *[ソリューションごとのデータの取り込み]* グラフに、送信されたデータの総量と各ソリューションによって送信されている量が表示されます。 これにより、全体的なデータ使用量 (または、特定のソリューションによる使用量) が、増加しているか、一定しているか、減少しているかといった傾向を確認できます。 

### <a name="data-volume-for-specific-events"></a>特定のイベントのデータ量

特定のイベント セット用に取り込まれたデータのサイズを確認するには、特定のテーブル (この例では `Event`) に対してクエリを実行してから、目的のイベントにクエリを制限します (この例では、イベント ID 5145 または 5156)。

```kusto
Event
| where TimeGenerated > startofday(ago(31d)) and TimeGenerated < startofday(now()) 
| where EventID == 5145 or EventID == 5156
| where _IsBillable == true
| summarize count(), Bytes=sum(_BilledSize) by EventID, bin(TimeGenerated, 1d)
``` 

句 `where _IsBillable = true` は、取り込み料金がかからない特定のソリューションからのデータの種類を除外することに注意してください。 `_IsBillable` に関する[詳細情報](./log-standard-columns.md#_isbillable)を参照してください。

### <a name="data-volume-by-solution"></a>ソリューション別のデータ ボリューム

過去 1 か月間 (まだ終わっていない最終日を除く) の課金対象データ ボリュームをソリューション別に表示するために使用するクエリは、次のとおりです。

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), Solution 
| render columnchart
```

`TimeGenerated` を含む句は、Azure portal のクエリ機能が既定の 24 時間を超えてさかのぼって参照するようにするためだけのものです。 Usage データ型を使用する場合、`StartTime` と `EndTime` は、結果が表示される時間バケットを表します。 

### <a name="data-volume-by-type"></a>種類別のデータ ボリューム

さらにドリルダウンして、データの種類別にデータの傾向を確認できます。

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000. by bin(StartTime, 1d), DataType 
| render columnchart
```

または、過去 1 か月について、ソリューション別および種類別にテーブルを表示するには、次のようにします

```kusto
Usage 
| where TimeGenerated > ago(32d)
| where StartTime >= startofday(ago(31d)) and EndTime < startofday(now())
| where IsBillable == true
| summarize BillableDataGB = sum(Quantity) / 1000 by Solution, DataType
| sort by Solution asc, DataType asc
```

### <a name="data-volume-by-computer"></a>コンピューターごとのデータ ボリューム

`Usage` データ型には、コンピューター レベルの情報は含まれていません。 コンピューターごとに、取り込まれたデータの **サイズ** を表示するには `_BilledSize` [プロパティ](./log-standard-columns.md#_billedsize)を使用します。サイズはバイト単位で示されます。

```kusto
find where TimeGenerated > ago(24h) project _BilledSize, _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize BillableDataBytes = sum(_BilledSize) by  computerName 
| sort by BillableDataBytes nulls last
```

`_IsBillable` [プロパティ](./log-standard-columns.md#_isbillable)では、取り込まれたデータで課金が発生するかどうかを指定します。 

コンピューターごとに取り込まれた請求対象のイベントの **数** を表示するには、次を使用します 

```kusto
find where TimeGenerated > ago(24h) project _IsBillable, Computer
| where _IsBillable == true 
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| summarize eventCount = count() by computerName  
| sort by eventCount nulls last
```

> [!TIP]
> 複数の種類のデータにわたるスキャンは、実行に[多量のリソースを使うため](../log-query/query-optimization.md#query-performance-pane)、これらの `find` クエリは多用しないようにします。 **コンピューターごと** の結果が不要な場合は、Usage データ型に関するクエリを実行します。

### <a name="data-volume-by-azure-resource-resource-group-or-subscription"></a>Azure リソース、リソース グループ、またはサブスクリプションごとのデータ ボリューム

__コンピューター__ ごとに取り込まれたデータの **サイズ** を取得できる Azure でホストされているノードからのデータについては、次のようにリソースへの完全パスを提供する _ResourceId [プロパティ](./log-standard-columns.md#_resourceid)を使用します。

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId | sort by BillableDataBytes nulls last
```

__Azure サブスクリプションごとに__ 取り込まれたデータの **サイズ** を取得できる Azure でホストされているノードからのデータについては、`_SubscriptionId` プロパティを次のように使用します。

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| summarize BillableDataBytes = sum(BillableDataBytes) by _SubscriptionId | sort by BillableDataBytes nulls last
```

リソース グループ別にデータ量を取得するには、`_ResourceId` を解析できます。

```kusto
find where TimeGenerated > ago(24h) project _ResourceId, _BilledSize, _IsBillable
| where _IsBillable == true 
| summarize BillableDataBytes = sum(_BilledSize) by _ResourceId
| extend resourceGroup = tostring(split(_ResourceId, "/")[4] )
| summarize BillableDataBytes = sum(BillableDataBytes) by resourceGroup | sort by BillableDataBytes nulls last
```

また、必要に応じて、次のように `_ResourceId` をより完全に解析することもできます

```Kusto
| parse tolower(_ResourceId) with "/subscriptions/" subscriptionId "/resourcegroups/" 
    resourceGroup "/providers/" provider "/" resourceType "/" resourceName   
```

> [!TIP]
> 複数の種類のデータにわたるスキャンは、実行に[多量のリソースを使うため](../log-query/query-optimization.md#query-performance-pane)、これらの `find` クエリは多用しないようにします。 サブスクリプション、リソース グループ、またはリソース名ごとの結果が不要な場合は、Usage データ型に関するクエリを実行します。

> [!WARNING]
> 使用状況データ型の一部のフィールドは、スキーマにはまだありますが非推奨とされていて、その値が設定されなくなります。 これらは、**Computer** と、取り込みに関連するフィールド (**TotalBatches**、**BatchesWithinSla**、**BatchesOutsideSla**、**BatchesCapped**、および **AverageProcessingTimeMs**) です。


### <a name="querying-for-common-data-types"></a>一般的なデータ型のクエリを実行する

特定のデータ型のデータのソースについてさらに詳しく調べるための、いくつかの便利なクエリの例を次に示します。

+ **ワークスペース ベースの Application Insights** リソース
  - 詳細は「[Application Insights の使用量とコストを管理する](../app/pricing.md#data-volume-for-workspace-based-application-insights-resources)」にあります
+ **Security** ソリューション
  - `SecurityEvent | summarize AggregatedValue = count() by EventID`
+ **Log Management** ソリューション
  - `Usage | where Solution == "LogManagement" and iff(isnotnull(toint(IsBillable)), IsBillable == true, IsBillable == "true") == true | summarize AggregatedValue = count() by DataType`
+ **Perf** データの種類
  - `Perf | summarize AggregatedValue = count() by CounterPath`
  - `Perf | summarize AggregatedValue = count() by CounterName`
+ **Event** データの種類
  - `Event | summarize AggregatedValue = count() by EventID`
  - `Event | summarize AggregatedValue = count() by EventLog, EventLevelName`
+ **Syslog** データの種類
  - `Syslog | summarize AggregatedValue = count() by Facility, SeverityLevel`
  - `Syslog | summarize AggregatedValue = count() by ProcessName`
+ **AzureDiagnostics** データ型
  - `AzureDiagnostics | summarize AggregatedValue = count() by ResourceProvider, ResourceId`

## <a name="tips-for-reducing-data-volume"></a>データ量の削減のためのヒント

収集されるログの量を削減するためのいくつかの提案は、次のとおりです。

| データ量の多いソース | データ量を削減する方法 |
| -------------------------- | ------------------------- |
| Container Insights         | 必要なデータのみを収集するように [Container Insights を構成](../insights/container-insights-cost.md#controlling-ingestion-to-reduce-cost)します。 |
| セキュリティ イベント            | [一般的または最小限のセキュリティ イベント](../../security-center/security-center-enable-data-collection.md#data-collection-tier)を選択します。 <br> 必要なイベントのみを収集するようにセキュリティ監査ポリシーを変更します。 特に、次のイベントを収集する必要性を検討します。 <br> - [フィルタリング プラットフォームの監査](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772749(v=ws.10)) <br> - [レジストリの監査](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941614(v%3dws.10))<br> - [ファイル システムの監査](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772661(v%3dws.10))<br> - [カーネル オブジェクトの監査](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd941615(v%3dws.10))<br> - [ハンドル操作の監査](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd772626(v%3dws.10))<br> - リムーバブル記憶域の監査 |
| パフォーマンス カウンター       | [パフォーマンス カウンターの構成](../agents/data-sources-performance-counters.md)を次のように変更します。 <br> - 収集の頻度を減らす <br> - パフォーマンス カウンターの数を減らす |
| イベント ログ                 | [イベント ログの構成](../agents/data-sources-windows-events.md)を次のように変更します。 <br> - 収集対象のイベント ログの数を減らす <br> - 必要なイベント レベルのみを収集する たとえば、"*情報*" レベルのイベントを収集しないようにします。 |
| syslog                     | [syslog の構成](../agents/data-sources-syslog.md)を次のように変更します。 <br> - 収集対象の施設の数を減らす <br> - 必要なイベント レベルのみを収集する たとえば、"*情報*" と "*デバッグ*" レベルのイベントを収集しないようにします。 |
| AzureDiagnostics           | [リソース ログの収集](../essentials/diagnostic-settings.md#create-in-azure-portal)を次のように変更します。 <br> - Log Analytics へのリソース送信ログの数を減らす <br> - 必要なログのみを収集する |
| ソリューションを必要としないコンピューターからのソリューション データ | [ソリューションのターゲット設定](../insights/solution-targeting.md)を使用して、必要なコンピューター グループからのみデータを収集するようにします。 |
| Application Insights | [https://docs.microsoft.com/azure/azure-monitor/app/pricing#managing-your-data-volume](managing Application Insights data volume) のオプションを確認します。 |
| [SQL Analytics](https://docs.microsoft.com/azure/azure-monitor/insights/azure-sql) | [AzSqlServerAudit](https://docs.microsoft.com/powershell/module/az.sql/set-azsqlserveraudit) を使用して、監査設定を調整します。 |
| Azure Sentinel | 追加のデータ ボリュームのソースとして最近有効にした [Sentinel データ ソース](https://docs.microsoft.com/azure/sentinel/connect-data-sources)を確認します。 |

### <a name="getting-nodes-as-billed-in-the-per-node-pricing-tier"></a>ノードごとの価格レベルでの課金対象のノードの取得

ワークスペースが "ノードごと" のレガシ価格レベルである場合に、ノードとして課金対象になるコンピューターの一覧を取得するには、**課金されるデータの種類** を送信しているノードを探します (一部のデータの種類は無料です)。 これを行うには、`_IsBillable` [プロパティ](./log-standard-columns.md#_isbillable)を使用し、完全修飾ドメイン名の左端のフィールドを使用します。 これにより、1 時間あたりの課金対象のデータがあるコンピューターの数が返されます (ノードがカウントされて課金される粒度)。

```kusto
find where TimeGenerated > ago(24h) project Computer, TimeGenerated
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize billableNodes=dcount(computerName) by bin(TimeGenerated, 1h) | sort by TimeGenerated asc
```

### <a name="getting-security-and-automation-node-counts"></a>セキュリティ ノード数と Automation ノード数を取得する

個別のセキュリティ ノードの数を表示するには、次のクエリを使用できます。

```kusto
union
(
    Heartbeat
    | where (Solutions has 'security' or Solutions has 'antimalware' or Solutions has 'securitycenter')
    | project Computer
),
(
    ProtectionStatus
    | where Computer !in~
    (
        (
            Heartbeat
            | project Computer
        )
    )
    | project Computer
)
| distinct Computer
| project lowComputer = tolower(Computer)
| distinct lowComputer
| count
```

個別の Automation ノードの数を表示するには、次のクエリを使用します。

```kusto
 ConfigurationData 
 | where (ConfigDataType == "WindowsServices" or ConfigDataType == "Software" or ConfigDataType =="Daemons") 
 | extend lowComputer = tolower(Computer) | summarize by lowComputer 
 | join (
     Heartbeat 
       | where SCAgentChannel == "Direct"
       | extend lowComputer = tolower(Computer) | summarize by lowComputer, ComputerEnvironment
 ) on lowComputer
 | summarize count() by ComputerEnvironment | sort by ComputerEnvironment asc
```

## <a name="evaluating-the-legacy-per-node-pricing-tier"></a>従来のノードごとの価格レベルの評価

従来の **ノードごとの** 価格レベルにアクセスできるワークスペースで、そのレベルのほうが適しているのか、または現在の **従量課金制** または **容量予約** レベルのほうが適しているのかを判断することは、多くの場合、お客様が評価するのは容易ではありません。  これには、ノードごとの価格レベルでの監視対象ノードあたりの固定費とそれに含まれる 500 MB/ノード/日のデータ割り当てと、従量課金制 (GB 単位) レベルでの取り込まれたデータのみに対して支払うコストとの間のトレードオフを理解することが伴います。 

この評価を容易にするために、次のクエリを使用して、ワークスペースの使用パターンに基づいて最適な価格レベルの推奨設定を作成できます。  このクエリでは、監視対象ノードとワークスペースに取り込まれた過去 7 日間のデータに注目し、各日でどの価格レベルが最適であるかが評価されます。 クエリを使用するには、以下を指定する必要があります。

1. ワークスペースが Azure Security Center を使用しているかどうかを指定するには、`workspaceHasSecurityCenter` を `true` または `false` に設定します。 
2. 特定の割引がある場合は価格を更新します。
3. さかのぼって分析する日数を指定するには、`daysToEvaluate` を設定します。 これは、クエリで 7 日間のデータの検索に時間がかかりすぎている場合に便利です。 

価格レベルの推奨クエリは次のとおりです。

```kusto
// Set these parameters before running query
// Pricing details available at https://azure.microsoft.com/en-us/pricing/details/monitor/
let daysToEvaluate = 7; // Enter number of previous days to analyze (reduce if the query is taking too long)
let workspaceHasSecurityCenter = false;  // Specify if the workspace has Azure Security Center
let PerNodePrice = 15.; // Enter your montly price per monitored nodes
let PerNodeOveragePrice = 2.30; // Enter your price per GB for data overage in the Per Node pricing tier
let PerGBPrice = 2.30; // Enter your price per GB in the Pay-as-you-go pricing tier
let CarRes100Price = 196.; // Enter your price for the 100 GB/day Capacity Reservation
let CarRes200Price = 368.; // Enter your price for the 200 GB/day Capacity Reservation
let CarRes300Price = 540.; // Enter your price for the 300 GB/day Capacity Reservation
let CarRes400Price = 704.; // Enter your price for the 400 GB/day Capacity Reservation
let CarRes500Price = 865.; // Enter your price for the 500 GB/day Capacity Reservation
// ---------------------------------------
let SecurityDataTypes=dynamic(["SecurityAlert", "SecurityBaseline", "SecurityBaselineSummary", "SecurityDetection", "SecurityEvent", "WindowsFirewall", "MaliciousIPCommunication", "LinuxAuditLog", "SysmonEvent", "ProtectionStatus", "WindowsEvent", "Update", "UpdateSummary"]);
let StartDate = startofday(datetime_add("Day",-1*daysToEvaluate,now()));
let EndDate = startofday(now());
union * 
| where TimeGenerated >= StartDate and TimeGenerated < EndDate
| extend computerName = tolower(tostring(split(Computer, '.')[0]))
| where computerName != ""
| summarize nodesPerHour = dcount(computerName) by bin(TimeGenerated, 1h)  
| summarize nodesPerDay = sum(nodesPerHour)/24.  by day=bin(TimeGenerated, 1d)  
| join kind=leftouter (
    Heartbeat 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where Computer != ""
    | summarize ASCnodesPerHour = dcount(Computer) by bin(TimeGenerated, 1h) 
    | extend ASCnodesPerHour = iff(workspaceHasSecurityCenter, ASCnodesPerHour, 0)
    | summarize ASCnodesPerDay = sum(ASCnodesPerHour)/24.  by day=bin(TimeGenerated, 1d)   
) on day
| join (
    Usage 
    | where TimeGenerated >= StartDate and TimeGenerated < EndDate
    | where IsBillable == true
    | extend NonSecurityData = iff(DataType !in (SecurityDataTypes), Quantity, 0.)
    | extend SecurityData = iff(DataType in (SecurityDataTypes), Quantity, 0.)
    | summarize DataGB=sum(Quantity)/1000., NonSecurityDataGB=sum(NonSecurityData)/1000., SecurityDataGB=sum(SecurityData)/1000. by day=bin(StartTime, 1d)  
) on day
| extend AvgGbPerNode =  NonSecurityDataGB / nodesPerDay
| extend OverageGB = iff(workspaceHasSecurityCenter, 
             max_of(DataGB - 0.5*nodesPerDay - 0.5*ASCnodesPerDay, 0.), 
             max_of(DataGB - 0.5*nodesPerDay, 0.))
| extend PerNodeDailyCost = nodesPerDay * PerNodePrice / 31. + OverageGB * PerNodeOveragePrice
| extend billableGB = iff(workspaceHasSecurityCenter,
             (NonSecurityDataGB + max_of(SecurityDataGB - 0.5*ASCnodesPerDay, 0.)), DataGB )
| extend PerGBDailyCost = billableGB * PerGBPrice
| extend CapRes100DailyCost = CarRes100Price + max_of(billableGB - 100, 0.)* PerGBPrice
| extend CapRes200DailyCost = CarRes200Price + max_of(billableGB - 200, 0.)* PerGBPrice
| extend CapRes300DailyCost = CarRes300Price + max_of(billableGB - 300, 0.)* PerGBPrice
| extend CapRes400DailyCost = CarRes400Price + max_of(billableGB - 400, 0.)* PerGBPrice
| extend CapResLevel500AndAbove = max_of(floor(billableGB, 100),500)
| extend CapRes500AndAboveDailyCost = CarRes500Price*CapResLevel500AndAbove/500 + max_of(billableGB - CapResLevel500AndAbove, 0.)* PerGBPrice
| extend MinCost = min_of(
    PerNodeDailyCost,PerGBDailyCost,CapRes100DailyCost,CapRes200DailyCost,
    CapRes300DailyCost, CapRes400DailyCost, CapRes500AndAboveDailyCost)
| extend Recommendation = case(
    MinCost == PerNodeDailyCost, "Per node tier",
    MinCost == PerGBDailyCost, "Pay-as-you-go tier",
    MinCost == CapRes100DailyCost, "Capacity Reservation (100 GB/day)",
    MinCost == CapRes200DailyCost, "Capacity Reservation (200 GB/day)",
    MinCost == CapRes300DailyCost, "Capacity Reservation (300 GB/day)",
    MinCost == CapRes400DailyCost, "Capacity Reservation (400 GB/day)",
    MinCost == CapRes500AndAboveDailyCost, strcat("Capacity Reservation (",CapResLevel500AndAbove," GB/day)"),
    "Error"
)
| project day, nodesPerDay, ASCnodesPerDay, NonSecurityDataGB, SecurityDataGB, OverageGB, AvgGbPerNode, PerGBDailyCost, PerNodeDailyCost, 
    CapRes100DailyCost, CapRes200DailyCost, CapRes300DailyCost, CapRes400DailyCost, CapRes500AndAboveDailyCost, Recommendation 
| sort by day asc
//| project day, Recommendation // Comment this line to see details
| sort by day asc
```

このクエリは、使用量の計算方法を正確に複製するものではなく、ほとんどの場合、価格レベルの推奨を提供するために使用されます。  

> [!NOTE]
> OMS E1 Suite、OMS E2 Suite、または OMS Add-On for System Center のいずれかを購入することによって得られる資格を使用するには、OMS Log Analytics の *ノード単位* の価格レベルを選択します。

## <a name="create-an-alert-when-data-collection-is-high"></a>収集したデータの量が多い場合のアラートを作成する

このセクションでは、Azure Monitor の[ログ アラート](../alerts/alerts-unified-log.md)を使用して、過去 24 時間のデータ量が指定された量を超えたことを示すアラートを作成する方法について説明します。 

過去 24 時間に取り込まれた課金対象データの量が 50 GB を超えた場合に警告するには、次の手順に従います。 

- **[アラートの条件を定義します]** では、リソース ターゲットとして Log Analytics ワークスペースを指定します。
- **[アラートの条件]** では、以下を指定します。
   - **[シグナル名]** では、 **[カスタム ログ検索]** を選択します。
   - **[検索クエリ]** : `Usage | where IsBillable | summarize DataGB = sum(Quantity / 1000.) | where DataGB > 50`。 (別のものが必要な場合) 
   - **[アラート ロジック]** は "*結果の数*" **に基づき、** **[条件]** は **しきい値** の *0* "*より大きい*" です
   - 1 日に 1 回実行するため、 **[期間]** を *1,440* 分に設定し、 **[アラートの頻度]** を *1,440* 分間隔に設定します。
- **[アラートの詳細を定義します]** では、以下を指定します。
   - **[名前]** : "*24 時間でデータ量が 50 GB を超える課金対象データ*"
   - **[重大度]** : *[警告]*

ログ アラートが条件に一致するときに通知されるように、既存の[アクション グループ](../alerts/action-groups.md)を指定するか、アクション グループを新たに作成します。

アラートを受け取ったら、上記の、使用量が予想よりも多い理由についてのトラブルシューティングを行う方法に関するセクションの手順を使用します。

## <a name="data-transfer-charges-using-log-analytics"></a>Log Analytics を使用したデータ転送の料金

Log Analytics にデータを送信すると、データ帯域幅の料金が発生する可能性がありますが、これは Log Analytics エージェントがインストールされている仮想マシンに限定され、診断設定を使用している場合や、Azure Sentinel に組み込まれている他のコネクタでは適用されません。 [Azure 帯域幅の価格ページ](https://azure.microsoft.com/pricing/details/bandwidth/)で説明されているように、2 つのリージョンに存在する Azure サービス間のデータ転送は、通常の料金で送信データ転送として課金されます。 受信データ転送は無料です。 ただし、この料金は、Log Analytics データ インジェストのコストと比へると非常に小さい (数パーセント) ものです。 そのため、Log Analytics のコスト管理では、ご自分で[取り込まれたデータ ボリューム](#understanding-ingested-data-volume)に注目する必要があります。 


## <a name="troubleshooting-why-log-analytics-is-no-longer-collecting-data"></a>Log Analytics がデータを収集しなくなった場合のトラブルシューティング

レガシの無料の価格レベルを使用しており、1 日に 500 MB を超えるデータを送信した場合、その日の残りはデータ収集が停止します。 1 日の制限に達したことが、Log Analytics がデータの収集を停止したり、データがないように見えたりする一般的な原因です。  データ収集が開始および停止すると、Log Analytics は Operation 型のイベントを作成します。 1 日の制限に達し、データがなくなっているかどうかを確認するには、検索で次のクエリを実行します。 

```kusto
Operation | where OperationCategory == 'Data Collection Status'
```

データ収集が停止するとき、OperationStatus は **[警告]** です。 データ収集が開始するとき、OperationStatus は **[成功]** です。 次の表は、データ収集が停止する原因と、データ収集を再開するための推奨されるアクションを示しています。  

|収集が停止する原因| 解決策| 
|-----------------------|---------|
|ワークスペースの 1 日あたりの上限に達した|収集が自動的に再開されるまで待つか、「最大日次データ ボリュームを管理する」で説明されているようにして 1 日のデータ ボリューム制限を増やします。 日次上限のリセット時間が **[日次上限]** ページに表示されます。 |
| お使いのワークスペースが[データ インジェストのボリューム レート](../service-limits.md#log-analytics-workspaces)に達しました | 診断設定を利用して Azure リソースから送信されるデータの既定のインジェスト ボリューム レート上限はワークスペースあたり約 6 GB/分です。 これは概算値であり、実際のサイズはログの長さとその圧縮率に左右され、データの種類によって異なることがあります。 この上限は、エージェントまたはデータ コレクター API から送信されるデータには適用されません。 1 つのワークスペースに高い比率でデータを送信すると、一部のデータが削除され、しきい値を超え続けている間、6 時間ごとにワークスペースの操作テーブルにイベントが送信されます。 インジェスト ボリュームがインジェスト率の制限を超えている場合、または間もなくそれに達すると予測される場合は、LAIngestionRate@microsoft.com にメールを送信するかサポート リクエストを開いて、ワークスペースの増加を要求できます。 データ インジェスト レート制限を示すイベントは、クエリ `Operation | where OperationCategory == "Ingestion" | where Detail startswith "The rate of data crossed the threshold"` によって見つけることができます。 |
|レガシの無料価格レベルの 1 日の制限に到達している |収集が自動的に再開される次の日まで待つか、または有料の価格レベルに変更します。|
|次のために、Azure サブスクリプションが中断された状態にある<br> 無料試用版が終了した<br> Azure パスの期限が切れた<br> 1 月の使用制限に達した (たとえば、MSDN または Visual Studio サブスクリプションで)|有料のサブスクリプションに変換する<br> 制限を削除するか、または制限がリセットされるまで待つ|

データ収集が停止したときに通知が届くようにするには、"*日次データ上限の作成*" アラートで説明されている手順を使用して、データ収集が停止したときに通知されるようにします。 [アクション グループの追加](../alerts/action-groups.md)に関する記事に記載されている手順を使用して、アラート ルールに電子メール、webhook、または Runbook アクションを構成します。 

## <a name="limits-summary"></a>制限の概要

追加の Log Analytics の制限がいくつかあり、その一部は Log Analytics 価格レベルに依存します。 詳細は「[Azure サブスクリプションとサービスの制限、クォータ、制約](../../azure-resource-manager/management/azure-subscription-service-limits.md#log-analytics-workspaces)」にあります。


## <a name="next-steps"></a>次のステップ

- 検索言語の使用方法については、[Azure Monitor ログでのログ検索](../logs/log-query-overview.md)に関する記事を参照してください。 検索クエリを使用して、使用量データをさらに分析できます。
- [新しいログ アラートの作成](../alerts/alerts-metric.md)に関するページで説明されている手順を使用して、検索条件が満たされた場合に通知されるようにします。
- [ソリューションのターゲット設定](../insights/solution-targeting.md)を使用して、必要なコンピューター グループからのみデータを収集するようにします。
- 効果的なイベント収集ポリシーを構成するには、[Azure Security Center のフィルタリング ポリシー](../../security-center/security-center-enable-data-collection.md)に関するページを参照してください。
- [パフォーマンス カウンターの構成](../agents/data-sources-performance-counters.md)を変更します。
- イベントの収集設定を変更する方法については、[イベント ログの構成](../agents/data-sources-windows-events.md)に関するページを参照してください。
- Syslog の収集設定を変更する方法については、[Syslog の構成](../agents/data-sources-syslog.md)に関するページを参照してください。
- Syslog の収集設定を変更する方法については、[Syslog の構成](../agents/data-sources-syslog.md)に関するページを参照してください。
