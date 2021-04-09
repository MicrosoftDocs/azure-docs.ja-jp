---
title: Hyper-V VM の Azure へのディザスター リカバリーのために Azure Site Recovery Deployment Planner のコスト見積もりレポートを確認する |Microsoft Docs
description: この記事では、Hyper-V の Azure へのディザスター リカバリーのために、Azure Site Recovery Deployment Planner によって生成されるコスト見積もりレポートを確認する方法を説明します。
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: 29457f2f5021fed9d8785f5764c4119de4be1fa9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "95999214"
---
# <a name="cost-estimation-report-by-azure-site-recovery-deployment-planner"></a>Azure Site Recovery Deployment Planner のコスト見積もりレポート 

Azure Site Recovery Deployment Planner のレポートでは、コスト見積もりの概要が [[Recommendations]\(推奨事項\)](hyper-v-deployment-planner-analyze-report.md#recommendations) シートに、詳しいコスト分析が [Cost Estimation]\(コスト見積もり\) シートに表示されます。 コスト分析は、VM ごとに詳しく表示されます。 

### <a name="cost-estimation-summary"></a>コスト見積もりの概要 
このグラフは、選択したターゲット リージョンの Azure に対するディザスター リカバリー (DR) の総コスト見積もりの概要と、レポート生成に関して指定された通貨を示しています。

![コスト見積もりの概要](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation-summary-h2a.png)

Azure Site Recovery を使用して適合 VM を保護する場合に、ストレージ、コンピューティング、ネットワーク、ライセンスに関して支払う必要のあるコストを把握できます。 コスト計算で加味されるのは適合 VM です。プロファイリングの対象となったすべての VM が対象になるわけではありません。 
 
コストは月単位または年単位で表示することができます。 詳細については、「[サポートされるターゲット リージョン](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions)」と「[サポートされる通貨](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies)」を参照してください。

**[Cost by components]\(コンポーネントごとのコスト\)**: DR コストの合計が、コンピューティング、ストレージ、ネットワーク、Site Recovery ライセンス コストの 4 つのコンポーネントに分けて表示されます。 コストは、レプリケーション中および DR ドリル時に発生する消費量に基づいて計算されます。 計算には、コンピューティングとストレージ (Premium と Standard) のほか、オンプレミス サイトと Azure との間に構成されている ExpressRoute/VPN、Site Recovery ライセンスが考慮されます。

**[Cost by states]\(状態ごとのコスト\)** ディザスター リカバリー (DR) の合計コストが、レプリケーションと DR ドリルという 2 種類の状態に基づいて分類されます。 

**[Replication cost]\(レプリケーション コスト\)**: レプリケーション時に発生するコスト。 ストレージ、ネットワーク、Site Recovery ライセンスのコストが含まれます。 

**[DR-Drill cost]\(DR ドリル コスト\)**: テスト フェールオーバー時に発生するコスト。 テスト フェールオーバー中は、Site Recovery によって VM がスピンアップされます。 DR ドリル コストには、実行中の VM のコンピューティング コストとストレージ コストが含まれます。 

**[Azure storage cost per Month/Year]\(月/年単位の Azure Storage コスト\)**: Premium ストレージと Standard ストレージに関して、レプリケーションと DR ドリルで生じる合計ストレージ コストが表示されます。

## <a name="detailed-cost-analysis"></a>Detailed cost analysis (詳細コスト分析)
Azure のコンピューティング、ストレージ、ネットワークの料金は Azure リージョンによって異なります。 サブスクリプションやそれに関連付けられているオファーに基づき、ターゲット Azure リージョンと通貨を指定して、最新の Azure 料金に関するコスト見積もりレポートを生成することができます。 既定では、Azure リージョンに "米国西部 2" が、通貨には米ドル (USD) が使用されます。 その他のリージョンや通貨を使用した場合、次回サブスクリプション ID、オファー ID、ターゲット リージョン、通貨を指定せずにレポートを生成すると、前回使用したターゲット リージョンと通貨に基づく料金がコスト見積もりに使用されます。

このセクションには、レポート生成に使用されたサブスクリプション ID とオファー ID が表示されます。 使用しなかった場合は、何も表示されません。

レポート全体のうち、灰色でマークされたセルは読み取り専用です。 白色のセルは、必要に応じて変更できます。

![コスト見積もりの詳細](media/hyper-v-azure-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-costs-by-components"></a>Overall DR costs by components (コンポーネントごとの総 DR コスト)
最初のセクションには、コンポーネントごとの総 DR コストと状態ごとの DR コストが表示されます。 

**[Compute]\(コンピューティング\)**: 必要な DR に関して Azure で実行される IaaS VM のコスト。 DR ドリル (テスト フェールオーバー) 時に Site Recovery によって作成される VM のほか、 Azure 上で実行される VM が対象となります (SQL Server Always On 可用性グループやドメイン コントローラー/ドメイン ネーム サーバーなど)。

**[Storage]\(ストレージ\)**: 必要な DR に関して消費される Azure Storage のコスト。 レプリケーションや DR ドリル時に使用されるストレージの消費が対象となります。

**[Network]\(ネットワーク\)**: 必要な DR に関する ExpressRoute とサイト間 VPN のコスト。 

**Azure Site Recovery ライセンス**: すべての適合 VM の Site Recovery ライセンス コスト。 Detailed cost analysis (詳細コスト分析) テーブルに VM を手動で入力した場合、その VM に関して生じる Site Recovery のライセンス コストも対象となります。

### <a name="overall-dr-costs-by-states"></a>Overall DR costs by states (状態ごとの総 DR コスト)
DR コストの合計は、レプリケーションと DR ドリルという 2 種類の状態に基づいて分類されます。

**[Replication]\(レプリケーション\)**: レプリケーション時に発生するコスト。 ストレージ、ネットワーク、Site Recovery ライセンスのコストが含まれます。 

**[DR-Drill]\(DR ドリル\)**: DR ドリル時に発生するコスト。 DR ドリル中は、Site Recovery によって VM がスピンアップされます。 DR ドリル コストには、実行中の VM のコンピューティング コストとストレージ コストが含まれます。

* 1 年間の合計 DR ドリル期間 = DR ドリル数 x 各 DR ドリル期間 (日)
* 平均 DR ドリル コスト (月ごと) = 合計 DR ドリル コスト / 12

### <a name="storage-cost-table"></a>ストレージ コスト テーブル
このテーブルには、レプリケーションと DR ドリルに関して発生する Premium ストレージと Standard ストレージのコストが、割引を適用した場合と適用しない場合とに分けて表示されます。

### <a name="site-to-azure-network"></a>Site to Azure network (サイトと Azure 間のネットワーク)
実際の要件に応じた適切な設定を選択してください。 

**ExpressRoute**:差分レプリケーションに必要なネットワーク帯域幅に合った、最寄りの ExpressRoute プランが既定で選択されます。 プランは、実際の要件に応じて変更することができます。

**[VPN Gateway type]\(VPN Gateway の種類\)**: 実際の環境に存在する場合は Azure VPN Gateway を選択します。 既定では NA になります。

**[Target region]\(ターゲット リージョン\)** : DR の対象として指定した Azure リージョン。 コンピューティング、ストレージ、ネットワーク、ライセンスに関してレポートに使用される料金は、そのリージョンの Azure 料金に基づいて決まります。 

### <a name="vm-running-on-azure"></a>VM running on Azure (Azure 上で実行されている VM)
ドメイン コントローラー VM、DNS VM、または Always On 可用性グループを使用した SQL Server VM が DR の対象として Azure 上で実行されていることも考えられます。 そのような場合は、VM の数やサイズを指定して、そのコンピューティング コストを合計 DR コストに反映することができます。 

### <a name="apply-overall-discount-if-applicable"></a>Apply overall discount if applicable (該当する場合に全体的な割引を適用)
Azure の料金総額に対して何らかの割引を受ける資格のある Azure パートナーまたはお客様は、このフィールドを使用できます。 割引 (%) はすべてのコンポーネントに適用されます。

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Number of virtual machines type and compute cost (per year) (各種仮想マシンの数とコンピューティング コスト (年間))
このテーブルには、Windows VM の数と非 Windows VM の数、さらに、それぞれの DR ドリルのコンピューティング コストが表示されます。

### <a name="settings"></a>設定 
**[Using Managed disk]\(管理ディスクの使用\)**: DR ドリル時にマネージド ディスクが使用されているかどうかを指定する設定です。 既定値は **[はい]** です。 **-UseManagedDisks** を **[No]\(いいえ\)** に設定した場合、非管理対象ディスクの料金がコスト計算に使用されます。

**Currency**:レポートの生成に使用される通貨。

**[Cost duration]\(コスト期間\)** : すべてのコストは、月または年単位で表示できます。 

## <a name="detailed-cost-analysis-table"></a>[Detailed cost analysis]\(詳細コスト分析\) テーブル
![Detailed cost analysis (詳細コスト分析)](media/hyper-v-azure-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png)

このテーブルには、適合 VM ごとのコスト明細が一覧表示されます。 プロファイリングの対象外となった VM の Azure DR コストは、このテーブルに手動で VM を追加することで見積もることができます。 この情報は、新しい DR デプロイに必要な Azure のコストを、詳細なプロファイリングを実行せずに見積もる必要がある場合に役立ちます。

VM を手動で追加するには、次の手順に従います。

1. **[Insert row]\(行の挿入\)** を選択して **開始** 行と **終了** 行の間に新しい行を挿入します。

1. 適切な VM サイズとその構成に合った VM 数に基づいて、次の各列の情報を入力します。 

    a. **[Number of VMs]\(VM の数\)**

    b. **[IaaS size (Your selection)]\(IaaS サイズ (ユーザーが選択)\)**

    c. **[Storage type (Standard/Premium)]\(ストレージの種類 (Standard/Premium)\)**

    d. **[VM total storage size (GB)]\(VM 合計ストレージ サイズ (GB)\)**

    e. **[Number of DR-Drills in a year]\(年間 DR ドリル数\)**

    f. **[Each DR-Drill duration (Days)]\(各 DR ドリル期間 (日数)\)**

    g. **OS の種類**

    h. **データの冗長性**

    i. **Azure Hybrid 利用特典**

1. **[Number of DR-Drills in a year]\(年間 DR ドリル数\)**、**[Each DR-Drill duration (Days)]\(各 DR ドリル期間 (日数)\)**、**[Data redundancy]\(データの冗長性\)**、**[Azure Hybrid Use Benefit]\(Azure ハイブリッド使用特典\)** に関しては、**[Apply to all]\(すべてに適用\)** を選択すると、テーブル内のすべての VM に同じ値を適用することができます。

1. **[Re-calculate cost]\(コストを再計算\)** を選択してコストを更新します。

**[VM Name]\(VM 名\)** :VM の名前。

**[Number of VMs]\(VM の数\)** :該当する構成と一致する VM の数。 同様の構成の VM が、プロファイリングされてはいないものの保護されている場合は、既存の VM の数を更新できます。

**[IaaS size (Recommendation)]\(IaaS サイズ (推奨)\)**: ツールによって推奨された、適合 VM の VM ロール サイズです。 

**[IaaS size (Your selection)]\(IaaS サイズ (ユーザーが選択)\)**: 既定では、推奨 VM ロール サイズと同じサイズが使用されます。 実際の要件に応じてロールは変更することができます。 コンピューティング コストは、選択された VM ロール サイズに基づきます。

**[Storage type]\(ストレージの種類\)**: VM によって使用されるストレージの種類。 Standard と Premium のどちらかのストレージになります。

**[VM total storage size (GB)]\(VM 合計ストレージ サイズ (GB)\)**: VM の合計ストレージ。

**[Number of DR-Drills in a year]\(年間 DR ドリル数\)**: 1 年間に実行する DR ドリルの回数。 既定では、年間 4 回です。 特定の VM の期間を変更できるほか、すべての VM に新しい値を適用することができます。 一番上の行に新しい値を入力し、**[Apply to all]\(すべてに適用\)** を選択してください。 年間 DR ドリル数と各 DR ドリル期間に基づいて、合計 DR ドリル コストが計算されます。 

**[Each DR-Drill duration (Days)]\(各 DR ドリル期間 (日数)\)**: 各 DR ドリルの期間。 [ディザスター リカバリーのソフトウェア アシュアランス特典](https://azure.microsoft.com/pricing/details/site-recovery)により、90 日ごとに 7 日が既定値となります。 特定の VM の期間を変更できるほか、すべての VM に新しい値を適用することができます。 一番上の行に新しい値を入力し、**[Apply to all]\(すべてに適用\)** を選択してください。 年間 DR ドリル数と各 DR ドリル期間に基づいて、合計 DR ドリル コストが計算されます。
 
**[OS の種類]**: VM のオペレーティングシステム (OS) の種類。 Windows または Linux を指定できます。 OS の種類が Windows の場合は、その VM に Azure ハイブリッド使用特典を適用できます。 

**[Data redundancy]\(データの冗長性\)**: ローカル冗長ストレージ、geo 冗長ストレージ、読み取りアクセス geo 冗長ストレージのいずれかを指定できます。 既定値はローカル冗長ストレージです。 特定の VM のストレージ アカウントに基づいて種類を変更できるほか、変更後の種類をすべての VM に適用することができます。 一番上の行で種類を変更し、**[Apply to all]\(すべてに適用\)** を選択してください。 レプリケーションに使用されるストレージのコストは、選択したデータ冗長性の料金に基づいて計算されます。 

**[Azure Hybrid Use Benefit]\(Azure ハイブリッド使用特典\)**: Windows VM には Azure ハイブリッド使用特典を適用できます (該当する場合)。 既定値は **[はい]** です。 特定の VM の設定を変更できるほか、すべての VM の設定を更新することができます。 **[Apply to all]\(すべてに適用\)** を選択してください。

**[Total Azure consumption]\(Azure 消費合計\)**: 対象 DR のコンピューティング、ストレージ、Site Recovery のライセンス コスト。 選択内容に応じて、月単位または年単位でコストが表示されます。

**[Steady state replication cost]\(定常状態のレプリケーション コスト\)**: レプリケーションのストレージ コスト。

**[Total DR-Drill cost (average)]\(合計 DR ドリル コスト (平均)\)**: DR ドリルのコンピューティング コストとストレージ コスト。

**Azure Site Recovery ライセンス コスト**: Site Recovery ライセンス コスト。

## <a name="supported-target-regions"></a>サポートされるターゲット リージョン
Site Recovery Deployment Planner では、以下の Azure リージョンに関してコスト見積もりを実行できます。 該当するリージョンがここに記載されていない場合は、料金が最も近いリージョンをどれか選んで使用してください。

eastus、eastus2、westus、centralus、northcentralus、southcentralus、northeurope、westeurope、eastasia、southeastasia、japaneast、japanwest、australiaeast、australiasoutheast、brazilsouth、southindia、centralindia、westindia、canadacentral、canadaeast、westus2、westcentralus、uksouth、ukwest、koreacentral、koreasouth 

## <a name="supported-currencies"></a>サポートされる通貨
Site Recovery Deployment Planner は、次のいずれかの通貨でコスト レポートを生成できます。

|Currency|名前|Currency|名前|Currency|名前|
|---|---|---|---|---|---|---|---|
|ARS|アルゼンチン ペソ ($)|AUD|オーストラリア ドル ($)|BRL|ブラジル レアル (R$)|
|CAD|カナダ ドル ($)|CHF|スイス フラン (chf)|DKK|デンマーク クローネ (kr)|
|EUR|ユーロ (€)|GBP|イギリス ポンド (£)|HKD|香港ドル (HK$)|
|IDR|インドネシア ルピア (Rp)|INR|インド ルピー (₹)|JPY|日本円 (¥)|
|KRW|韓国ウォン (₩)|MXN|メキシコ ペソ (MX$)|MYR|マレーシア リンギ (RM$)|
|NOK|ノルウェー クローネ (kr)||NZD|ニュージーランド ドル ($)||RUB|ロシア ルーブル (руб)|
|SAR|サウジ リアル (SR)|SEK|スウェーデン クローナ (kr)|TWD|台湾ドル (NT$)|
|TRY|トルコ リラ (TL)|USD| 米ドル ($)|ZAR|南アフリカ ランド (R)|

## <a name="next-steps"></a>次のステップ
[Site Recovery を使用して Hyper-V から Azure に VM](hyper-v-azure-tutorial.md) をレプリケートして保護する方法について詳しく調べてみましょう。
