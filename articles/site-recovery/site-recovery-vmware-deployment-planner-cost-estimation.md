---
title: Azure Site Recovery Deployment Planner のコスト見積もりを確認する
description: この記事では、VMware のディザスター リカバリーのために、Azure Site Recovery Deployment Planner のコスト見積もりを確認する方法について説明します。
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 7/29/2019
ms.author: ramamill
ms.openlocfilehash: dba585fbadca479c146ad42ac1bc5aa9d5349f49
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93186707"
---
# <a name="review-cost-estimations-in-the-vmware-deployment-planner"></a>VMware Deployment Planner のコスト見積もりを確認する 

Deployment Planner のレポートでは、コスト見積もりの概要が [[Recommendations]\(推奨事項\)](site-recovery-vmware-deployment-planner-analyze-report.md#recommendations) シートに、詳しいコスト分析が [Cost Estimation]\(コスト見積もり\) シートに表示されます。 コスト分析は、VM ごとに詳しく表示されます。 

>[!Note]
>Deployment Planner ツールの現行バージョンの v2.5 では、Managed Disks にレプリケートする VM 用のコスト見積もりは提供されません。

### <a name="cost-estimation-summary"></a>コスト見積もりの概要 
このグラフは、選択したターゲット リージョンの Azure に対するディザスター リカバリー (DR) の総コスト見積もりの概要と、レポート生成に関して指定された通貨を示しています。
コスト見積もりの概要

![コスト見積もりの概要](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

Azure Site Recovery を使用してすべての適合 VM を Azure に保護する場合に、ストレージ、コンピューティング、ネットワーク、ライセンスに関して支払う必要のあるコストを把握できます。 コスト計算で加味されるのは適合 VM です。プロファイリングの対象となったすべての VM が対象になるわけではありません。  
 
コストは月単位または年単位で表示することができます。 詳細については、「[サポートされるターゲット リージョン](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions)」と「[サポートされる通貨](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies)」を参照してください。

**[Cost by components]\(コンポーネントごとのコスト\)** DR コストの合計は次の 4 つのコンポーネントに分けられます: コンピューティング、ストレージ、ネットワーク、Azure Site Recovery ライセンス コスト。 コストは、コンピューティング、ストレージ (Premium と Standard)、オンプレミス サイトと Azure の間に構成されている ExpressRoute/VPN、Azure Site Recovery ライセンスに関して、レプリケーション中および DR ドリル時に発生する消費量に基づいて計算されます。

**[Cost by states]\(状態ごとのコスト\)** ディザスター リカバリー (DR) の合計コストが、レプリケーションと DR ドリルという 2 種類の状態に基づいて分類されます。 

**[Replication cost]\(レプリケーション コスト\)** :レプリケーション時に発生するコストです。 ストレージ、ネットワーク、Azure Site Recovery ライセンスのコストが含まれます。 

**[DR-Drill cost]\(DR ドリル コスト\)** :テスト フェールオーバー時に発生するコストです。 テスト フェールオーバー中は、Azure Site Recovery によって VM がスピンアップされます。 DR ドリル コストには、実行中の VM のコンピューティング コストとストレージ コストが含まれます。 

**[Azure storage cost per Month/Year]\(月/年単位の Azure Storage コスト\)** Premium ストレージと Standard ストレージに関して、レプリケーションと DR ドリルで生じる合計ストレージ コストが表示されます。

## <a name="detailed-cost-analysis"></a>Detailed cost analysis (詳細コスト分析)
Azure のコンピューティング、ストレージ、ネットワークなどの料金は Azure リージョンによって異なります。 サブスクリプションやそれに関連付けられているオファーに基づき、ターゲット Azure リージョンと通貨を指定して、最新の Azure 料金に関するコスト見積もりレポートを生成することができます。 既定では、Azure リージョンに "米国西部 2" が、通貨には米ドル (USD) が使用されます。 その他のリージョンや通貨を使用した場合、次回サブスクリプション ID、オファー ID、ターゲット リージョン、通貨を指定せずにレポートを生成すると、前回使用したターゲット リージョンと通貨に基づく料金がコスト見積もりに使用されます。
このセクションには、レポート生成に使用されたサブスクリプション ID とオファー ID が表示されます。  使用しなかった場合は、何も表示されません。

レポート全体のうち、灰色でマークされたセルは読み取り専用です。 白色のセルは、必要に応じて変更できます。

![コスト見積もりの詳細 1](media/site-recovery-hyper-v-deployment-planner-cost-estimation/cost-estimation1-h2a.png)

### <a name="overall-dr-cost-by-components"></a>Overall DR cost by components (コンポーネントごとの総 DR コスト)
最初のセクションには、コンポーネントごとの総 DR コストと状態ごとの DR コストが表示されます。 

**コンピューティング**:DR のニーズに応じて Azure で実行される IaaS VM のコストです。 DR ドリル (テスト フェールオーバー) 時に Azure Site Recovery によって作成される VM のほか、Azure 上で実行される VM が対象となります (SQL Server Always On 可用性グループやドメイン コントローラー/ドメイン ネーム サーバーなど)。

**ストレージ**: DR のニーズに応じて消費される Azure Storage のコストです。 レプリケーションや DR ドリル時に使用されるストレージの消費が対象となります。
[ネットワーク]\: DR のニーズに対して発生する ExpressRoute とサイト間 VPN のコストです。 

**[ASR license]\(ASR ライセンス\)** :すべての適合 VM に関する Azure Site Recovery のライセンス コストです。 Detailed cost analysis (詳細コスト分析) テーブルに VM を手動で入力した場合、その VM に関して生じる Azure Site Recovery のライセンス コストも対象となります。

### <a name="overall-dr-cost-by-states"></a>Overall DR cost by states (状態ごとの総 DR コスト)
DR コストの合計は、レプリケーションと DR ドリルという 2 種類の状態に基づいて分類されます。

**[Replication cost]\(レプリケーション コスト\)** :レプリケーション時に発生するコストです。 ストレージ、ネットワーク、Azure Site Recovery ライセンスのコストが含まれます。 

**[DR-Drill cost]\(DR ドリル コスト\)** :DR ドリル時に発生するコストです。 DR ドリル中は、Azure Site Recovery によって VM がスピンアップされます。 DR ドリル コストには、実行中の VM のコンピューティング コストとストレージ コストが含まれます。
1 年間の合計 DR ドリル期間 = DR ドリル数 x 各 DR ドリル期間 (日) の平均 DR ドリル コスト (月ごと) = 合計 DR ドリル コスト / 12

### <a name="storage-cost-table"></a>ストレージ コスト テーブル:
このテーブルには、レプリケーションと DR ドリルに関して発生する Premium ストレージと Standard ストレージのコストが、割引を適用した場合と適用しない場合とに分けて表示されます。

### <a name="site-to-azure-network"></a>Site to Azure network (サイトと Azure 間のネットワーク)
実際の要件に応じた設定を選択してください。 

**ExpressRoute**:差分レプリケーションに必要なネットワーク帯域幅に合った、最寄りの ExpressRoute プランが既定で選択されます。 プランは、実際の要件に応じて変更することができます。

**[VPN Gateway]** : ご利用の環境に存在する場合、VPN Gateway を選択します。 既定では NA になります。

**[Target region]\(ターゲット リージョン\)** : DR の対象として指定した Azure リージョン。 コンピューティング、ストレージ、ネットワーク、ライセンスに関してレポートに使用される料金は、そのリージョンの Azure 料金に基づいて決まります。 

### <a name="vm-running-on-azure"></a>VM running on Azure (Azure 上で実行されている VM)
ドメイン コントローラー VM、DNS VM、または Always On 可用性グループを使用した SQL Server VM が DR の対象として Azure 上で実行されている場合、VM の数やサイズを指定して、そのコンピューティング コストを合計 DR コストに反映させることができます。 

### <a name="apply-overall-discount-if-applicable"></a>Apply overall discount if applicable (該当する場合に全体的な割引を適用)
Azure の料金総額に対して何らかの割引を受ける資格のある Azure パートナーまたはお客様は、このフィールドを使用できます。 割引 (%) はすべてのコンポーネントに適用されます。

### <a name="number-of-virtual-machines-type-and-compute-cost-per-year"></a>Number of virtual machines type and compute cost (per year) (各種仮想マシンの数とコンピューティング コスト (年間))
このテーブルには、Windows VM の数と非 Windows VM の数、さらに、それぞれの DR ドリルのコンピューティング コストが表示されます。

### <a name="settings"></a>設定 

**Currency**:レポートの生成に使用される通貨。 [Cost duration]\(コスト期間\): すべてのコストは、月または年単位で表示できます。 

## <a name="detailed-cost-analysis-table"></a>[Detailed cost analysis]\(詳細コスト分析\) テーブル
![[Detailed cost analysis]\(詳細コスト分析\)](media/site-recovery-hyper-v-deployment-planner-cost-estimation/detailed-cost-analysis-h2a.png) このテーブルには、適合 VM ごとのコスト明細が一覧表示されます。 プロファイリングの対象外となった VM の Azure DR コストは、このテーブルに手動で VM を追加することで見積もることができます。 新しいディザスター リカバリー デプロイに必要な Azure のコストを、詳細なプロファイリングを実行せずに見積もる必要がある場合に役立ちます。
VM を手動で追加するには、次の手順に従います。 
1.  [Insert row]\(行の挿入\) ボタンをクリックして開始行と終了行の間に新しい行を挿入します。

2.  適切な VM サイズとその構成に合った VM 数に基づいて、次の各列の情報を入力します。 

* [Number of VMs]\(VM の数\)、[IaaS size (Your selection)]\(IaaS サイズ (ユーザーが選択)\)
* [Storage Type (Standard/Premium)]\(ストレージの種類 (Standard/Premium)\)
* ソース マシンの VM 合計ストレージ サイズ (GB)
* [Number of DR drills in a year]\(年間 DR ドリル数\) 
* [Each DR drill duration (Days)]\(各 DR ドリル期間 (日数)\) 
* OS の種類
* データの冗長性 
* Azure ハイブリッド特典

1. [Number of DR-Drills in a year]\(年間 DR ドリル数\)、[Each DR-Drill duration (Days)]\(各 DR ドリル期間 (日数)\)、[Data redundancy]\(データの冗長性\)、[Azure Hybrid Use Benefit]\(Azure ハイブリッド使用特典\) に関しては、[Apply to all]\(すべてに適用\) ボタンをクリックすると、テーブル内のすべての VM に同じ値を適用することができます。

1. [Re-calculate cost]\(コストを再計算\) をクリックしてコストを更新します。

**[VM Name]\(VM 名\)** :VM の名前。

**[Number of VMs]\(VM の数\)** :該当する構成と一致する VM の数。 同様の構成の VM がプロファイリングされていなくても、今後保護する予定がある場合は、既存の VM の数を更新できます。

**[IaaS size (Recommendation)]\(IaaS サイズ (推奨)\)** :ツールによって推奨された、適合 VM の VM ロール サイズです。 

**[IaaS size (Your selection)]\(IaaS サイズ (ユーザーが選択)\)** :既定では、推奨される VM ロール サイズと同じ値になります。 実際の要件に応じてロールは変更することができます。 コンピューティング コストは、選択された VM ロール サイズに基づきます。

**[Storage type]\(ストレージの種類\)** :VM によって使用されるストレージの種類です。 Standard と Premium のどちらかのストレージになります。

**[VM total storage size (GB)]\(VM 合計ストレージ サイズ (GB)\)** :ソース VM の合計ストレージ。

**[Number of DR-Drills in a year]\(年間 DR ドリル数\)** :1 年間に実行する DR ドリルの回数です。 既定では、年間 4 回です。 特定の VM の期間を変更できるほか、一番上の行に新しい値を入力し、[Apply to all]\(すべてに適用\) ボタンをクリックすることで、すべての VM に新しい値を適用することができます。 年間 DR ドリル数と各 DR ドリル期間に基づいて、合計 DR ドリル コストが計算されます。  

**[Each DR-Drill duration (Days)]\(各 DR ドリル期間 (日数)\)** :各 DR ドリルの期間です。 [ディザスター リカバリーのソフトウェア アシュアランス特典](https://azure.microsoft.com/pricing/details/site-recovery)により、90 日ごとに 7 日が既定値となります。 特定の VM の期間を変更できるほか、一番上の行に新しい値を入力し、[Apply to all]\(すべてに適用\) ボタンをクリックすることで、すべての VM に新しい値を適用することができます。 年間 DR ドリル数と各 DR ドリル期間に基づいて、合計 DR ドリル コストが計算されます。
  
**[OS Type]\(OS の種類\)** :VM の OS の種類です。 Windows または Linux を指定できます。 OS の種類が Windows の場合は、その VM に Azure ハイブリッド使用特典を適用できます。 

**[Data redundancy]\(データの冗長性\)** :ローカル冗長ストレージ (LRS)、geo 冗長ストレージ (GRS)、読み取りアクセス geo 冗長ストレージ (RA-GRS) のいずれかにすることができます。 既定値は LRS です。 特定の VM のストレージ アカウントに基づいて種類を変更できるほか、一番上の行で種類を変更し、[Apply to all]\(すべてに適用\) ボタンをクリックすることで、変更後の種類をすべての VM に適用することができます。  レプリケーションに使用されるストレージのコストは、選択したデータ冗長性の料金に基づいて計算されます。 

**[Azure ハイブリッド特典]** : Windows VM には Azure ハイブリッド特典を適用できます (該当する場合)。  既定値は Yes です。 特定の VM の設定を変更できるほか、[Apply to all]\(すべてに適用\) ボタンをクリックすることですべての VM の設定を更新することができます。

**[Total Azure consumption]\(Azure 消費合計\)** :ご利用の DR におけるコンピューティング、ストレージ、Azure Site Recovery のライセンス コストが含まれます。 選択内容に応じて、月単位または年単位でコストが表示されます。

**[Steady state replication cost]\(定常状態のレプリケーション コスト\)** :レプリケーションのストレージ コストが含まれます。

**[Total DR-Drill cost (average)]\(合計 DR ドリル コスト (平均)\)** :DR ドリルのコンピューティング コストとストレージ コストが含まれます。

**[ASR license cost]\(ASR ライセンス コスト\)** :Azure Site Recovery のライセンス コストです。

## <a name="supported-target-regions"></a>サポートされるターゲット リージョン
Azure Site Recovery Deployment Planner では、次の Azure リージョンに関してコスト見積もりを実行できます。 該当するリージョンが以下の一覧に記載されていない場合は、料金が最も近いリージョンをどれか選んで使用してください。

eastus、eastus2、westus、centralus、northcentralus、southcentralus、northeurope、westeurope、eastasia、southeastasia、japaneast、japanwest、australiaeast、australiasoutheast、brazilsouth、southindia、centralindia、westindia、canadacentral、canadaeast、westus2、westcentralus、uksouth、ukwest、koreacentral、koreasouth 

## <a name="supported-currencies"></a>サポートされる通貨
Azure Site Recovery Deployment Planner は、次のいずれかの通貨でコスト レポートを生成できます。

|Currency|名前|Currency|名前|Currency|名前|
|---|---|---|---|---|---|---|---|
|ARS|アルゼンチン ペソ ($)|AUD|オーストラリア ドル ($)|BRL|ブラジル レアル (R$)|
|CAD|カナダ ドル ($)|CHF|スイス フラン (chf)|DKK|デンマーク クローネ (kr)|
|EUR|ユーロ (€)|GBP|イギリス ポンド (£)|HKD|香港ドル (HK$)|
|IDR|インドネシア ルピア (Rp)|INR|インド ルピー (₹)|JPY|日本円 (¥)|
|KRW|韓国ウォン (₩)|MXN|メキシコ ペソ (MX$)|MYR|マレーシア リンギ (RM$)|
|NOK|ノルウェー クローネ (kr)|NZD|ニュージーランド ドル ($)|RUB|ロシア ルーブル (руб)|
|SAR|サウジ リアル (SR)|SEK|スウェーデン クローナ (kr)|TWD|台湾ドル (NT$)|
|TRY|トルコ リラ (TL)|USD| 米ドル ($)|ZAR|南アフリカ ランド (R)|

## <a name="next-steps"></a>次のステップ
[Azure Site Recovery を使用して VMware から Azure に VM](./vmware-azure-tutorial.md) をレプリケートして保護する方法について詳しく調べてみましょう。
