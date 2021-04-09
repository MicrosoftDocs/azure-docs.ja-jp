---
title: Azure Site Recovery を使用した VMware ディザスター リカバリーについての Deployment Planner レポートを分析する
description: この記事では、Azure Site Recovery を使用した Azure への VMware ディザスター リカバリーについて Recovery Deployment Planner によって生成されるレポートを分析する方法を説明します。
author: rajeswari-mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/4/2019
ms.author: ramamill
ms.openlocfilehash: 7e2db720bb37a25b8511bd1c42c0c18e139aa216
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93186605"
---
# <a name="analyze-the-deployment-planner-report-for-vmware-disaster-recovery-to-azure"></a>Azure への VMware ディザスター リカバリーについての Deployment Planner レポートを分析する

生成された Microsoft Excel レポートには、次のシートが含まれています。
## <a name="on-premises-summary"></a>On-Premises summary (オンプレミス サマリー)
[On-premises summary]\(オンプレミス サマリー\) ワークシートには、プロファイリングの対象となった VMware 環境の概要が表示されます。

![VMware 環境のオンプレミス サマリー](media/site-recovery-vmware-deployment-planner-analyze-report/on-premises-summary-v2a.png)

**[開始日]** / **[終了日]** : レポートの生成時に考慮されたプロファイリング データの開始日と終了日です。 既定では、プロファイリングを開始した日付が開始日に、プロファイリングを終了した日付が終了日になります。 この値は、レポートの生成時に StartDate パラメーターと EndDate パラメーターで指定できます。

**[Total number of profiling days]\(プロファイリングの合計日数\)** :レポートの生成対象期間 (開始日から終了日まで) におけるプロファイリングの合計日数です。

**[Number of compatible virtual machines]\(適合する仮想マシンの数\)** : 適合する (必要なネットワーク帯域幅、ストレージ アカウント数、Microsoft Azure コア数、構成サーバー数、追加プロセス サーバー数が計算される) VM の総数です。

**[Total number of disks across all compatible virtual machines]\(すべての適合仮想マシンにおけるディスクの総数\)** : この数は、デプロイ後の環境で使用する構成サーバー数と追加プロセス サーバー数を決定するための入力値の 1 つとして使用されます。

**[Average number of disks per compatible virtual machine]\(適合する仮想マシン 1 台あたりの平均ディスク数\)** : 適合するすべての VM から割り出される平均ディスク数です。

**[Average disk size (GB)]\(平均ディスク サイズ (GB)\)** : 適合するすべての VM から割り出される平均ディスク サイズです。

**[Desired RPO (minutes)]\(必要な RPO (分)\)** : 必要な帯域幅の見積もりに使用された回復ポイントの目標です。既定値またはレポートの生成時に "DesiredRPO" パラメーターに渡された値が該当します。

**[Desired bandwidth (Mbps)]\(必要な帯域幅 (Mbps)\)** : レポートの生成時に、達成可能な RPO を見積もる目的で "Bandwidth" パラメーターに指定した値です。

**[Observed typical data churn per day (GB)]\(観察された 1 日あたりの標準的なデータの変更頻度 (GB\))** : プロファイリングの全日数にわたって観察されたデータ変更頻度の平均値です。 この数は、デプロイ後の環境で使用する構成サーバー数と追加プロセス サーバー数を決定するための入力値の 1 つとして使用されます。

## <a name="recommendations"></a>Recommendations

VMware to Azure レポートの [Recommendations]\(推奨事項\) シートには、[Desired RPO]\(必要な RPO\) の選択内容に応じて、次の情報が表示されます。

![Recommendations for VMware to Azure レポート](media/site-recovery-vmware-deployment-planner-analyze-report/Recommendations-v2a.png)

### <a name="profiled-data"></a>プロファイリング データ
![Deployment Planner のプロファイリング データ ビュー](media/site-recovery-vmware-deployment-planner-analyze-report/profiled-data-v2a.png)

**[Profiled data period]\(プロファイリング データ期間\)** : プロファイリングが実行された期間です。 レポートの生成時に StartDate オプションと EndDate オプションを使用して生成対象期間を指定しない限り、既定では、すべてのプロファイリング データが計算に使用されます。

**[サーバー名]** : レポートの生成対象の VM がある VMware vCenter または ESXi ホストの名前または IP アドレスです。

**[Desired RPO]\(必要な RPO\)** : ご利用のデプロイに対する回復ポイントの目標です。 既定では、15 分、30 分、60 分の 3 とおりの RPO 値について、必要なネットワーク帯域幅が計算されます。 選択した値に応じて、シート上で関連する値が更新されます。 レポートの生成中に *DesiredRPOinMin* パラメーターを使用した場合、その値がこの [Desired RPO (必要な RPO)] の結果に表示されます。

### <a name="profiling-overview"></a>プロファイリングの概要

![Deployment Planner のプロファイリング結果](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-v2a.png)

**[Total Profiled Virtual Machines]\(プロファイリングされた仮想マシンの合計\)** : プロファイリング データを使用できる VM の総数です。 プロファイリングされなかった VM の名前が VMListFile に含まれている場合、それらの VM はレポートの生成時に考慮されず、プロファイリングされた VM の合計からは除外されます。

**[Compatible Virtual Machines]\(適合仮想マシン\)** : Site Recovery を使用して Azure で保護できる VM の数です。 適合する (必要なネットワーク帯域幅、ストレージ アカウント数、Azure コア数、構成サーバー数、追加プロセス サーバー数が計算される) VM の総数になります。 適合する VM ごとの詳しい情報は、「Compatible VMs (適合 VM)」セクションで確認できます。

**[Incompatible Virtual Machines]\(不適合仮想マシン\)** : プロファイリングされた VM のうち、Site Recovery を使用した保護に適合しない VM の数です。 不適合の理由については、「Incompatible VMs (不適合 VM)」セクションで取り上げます。 プロファイリングされなかった VM の名前が VMListFile に含まれている場合、それらの VM は、不適合 VM の数から除外されます。 そのような VM は、[Incompatible VMs (不適合 VM)] セクションの最後に [Data not found (データが見つかりません)] として列挙されます。

**[Desired RPO]\(必要な RPO\)** : 必要な回復ポイントの目標 (分単位) です。 次の 3 つの RPO 値についてレポートが生成されます: 15 分 (既定値)、30 分、60 分。 シートの右上にある [Desired RPO (必要な RPO)] ボックスの一覧での選択に応じて、レポートされる推奨帯域幅が変化します。 *-DesiredRPO* パラメーターに独自の値を指定してレポートを生成した場合、[Desired RPO (必要な RPO)] ボックスの一覧には、その独自の値が既定値として表示されます。

### <a name="required-network-bandwidth-mbps"></a>必要なネットワーク帯域幅 (Mbps)

![必要なネットワーク帯域幅 (Deployment Planner)](media/site-recovery-vmware-deployment-planner-analyze-report/required-network-bandwidth-v2a.png)

**[To meet RPO 100% of the time]\(RPO を 100% の時間満たす場合\)** : 必要な RPO を 100% の時間満たす場合に割り当てる必要のある推奨帯域幅 (Mbps) です。 適合するすべての VM について、RPO からの逸脱 (RPO 違反) を防止し、安定した状態の差分レプリケーションを行うためには、この帯域幅を専用に確保する必要があります。

**[To meet RPO 90 percent of the time (RPO を 90% の時間満たす場合)]** : 必要な RPO を 100% の時間満たす帯域幅を、ブロードバンドのコストなど何らかの理由で設定できない場合は、必要な RPO を 90% の時間満たすことのできる水準まで帯域幅を下げて設定することが選択肢として考えられます。 設定する帯域幅を引き下げたことによって生じる影響を明らかにするために、レポートには RPO 違反の件数とその期間についての what-if 分析が示されます。

**[Achieved Throughput]\(達成スループット\)** : GetThroughput コマンドが実行されたサーバーから、ストレージ アカウントが存在する Microsoft Azure リージョンまでのスループットです。 このスループットは、構成サーバー/プロセス サーバーのストレージとネットワークの特性が、Deployment Planner ツールを実行したサーバーのそれと同じ状態に保たれていることを前提とし、Site Recovery を使用して適合 VM を保護した場合に得られる推定値を表しています。

レプリケーションに関しては、RPO を 100% の時間満たすうえで推奨される帯域幅を設定してください。 必要な帯域幅を設定したにもかかわらず、ツールから報告される達成スループットに改善が見られない場合は、次の作業を行ってください。

1. Site Recovery のスループットを制限するネットワーク QoS (サービス品質) が存在しているかどうかを確認します。

2. 物理的にサポートされる最も近い Microsoft Azure リージョンに Site Recovery コンテナーが存在し、ネットワーク待ち時間が最小限で済んでいるかどうかを確認します。

3. ローカル ストレージの特性を確認し、ハードウェアの強化 (例: HDD から SSD など) が可能であるかどうかを調べます。

4. [レプリケーションに使用されるネットワーク帯域幅を増やす](./site-recovery-plan-capacity-vmware.md#control-network-bandwidth)ために、プロセス サーバーで Site Recovery の設定を変更します。

既に保護された VM がある構成サーバーまたはプロセス サーバーで Deployment Planner ツールを実行する場合は、複数回にわたって実行してください。 特定の時点で実行されている処理に伴う変更の頻度によって、達成スループットの値は変化します。

企業による Site Recovery のデプロイでは、常に [ExpressRoute](https://aka.ms/expressroute) を使用することをお勧めします。

### <a name="required-storage-accounts"></a>必要なストレージ アカウント
次のグラフは、すべての適合 VM を保護するうえで必要なストレージ アカウントの総数です (Standard と Premium)。 使用するストレージ アカウントを VM ごとに把握するには、「VM-Storage placement (VM<->ストレージの配置)」セクションを参照してください。 Deployment Planner の v2.5 を使用している場合、データが Managed Disks に直接書き込まれるため、このレコメンデーションには、レプリケーションに必要な標準キャッシュ ストレージ アカウントの数のみが表示されます。

![必要なストレージ アカウント (Deployment Planner)](media/site-recovery-vmware-deployment-planner-analyze-report/required-storage-accounts-v2a.png)

### <a name="required-number-of-azure-cores"></a>必要な Azure コア数
適合するすべての VM のフェールオーバー (またはテスト フェールオーバー) 前にセットアップされるコアの総数です。 使用できるコア数がサブスクリプションに不足している場合、フェールオーバー (またはテスト フェールオーバー) 時に Site Recovery が VM を作成できません。

![必要な Azure のコア数 (Deployment Planner)](media/site-recovery-vmware-deployment-planner-analyze-report/required-cores-v2a.png)

### <a name="required-on-premises-infrastructure"></a>必要なオンプレミス インフラストラクチャ
この図は、適合するすべての VM を保護するために必要な構成サーバーと追加のプロセス サーバーの総数を表しています。 サポートされる[構成サーバーの推奨サイズ](/en-in/azure/site-recovery/site-recovery-plan-capacity-vmware#size-recommendations-for-the-configuration-server)に応じて、追加のサーバーが提案されます。 この提案は、1 日あたりの変更頻度か、または保護対象 VM の最大数 (VM ごとに平均 3 台のディスクを想定) に基づいて行われ、構成サーバーまたは追加のプロセス サーバーで、最初に到達した方の制限が適用されます。 1 日あたりの総変更頻度と保護対象ディスクの総数の詳細については、[On-premises summary]\(オンプレミス サマリー\) セクションに記載されています。

![必要なオンプレミス インフラストラクチャ (Deployment Planner)](media/site-recovery-vmware-deployment-planner-analyze-report/required-on-premises-components-v2a.png)

### <a name="what-if-analysis"></a>What-if 分析
What-if 分析は、設定する帯域幅を引き下げて、必要な RPO を 90% の時間だけ満たすようにしたとき、プロファイリング期間中、おおよそ何件の違反が発生するかを可能性として提示するものです。 RPO 違反は日々生じることが考えられ、 グラフにはそれぞれの日のピーク RPO が表示されます。
この分析結果を見て、RPO 違反がすべての日にわたって存在しているかどうか、また、帯域幅を 100% から引き下げた場合の 1 日のピーク RPO が許容範囲内にあるかどうかを判断することができます。 許容範囲内であれば、レプリケーションに割り当てる帯域幅を引き下げることができます。そうでなければ、必要な RPO を 100% の時間満たすことのできる水準まで帯域幅を引き上げます。

![What-if 分析 (Deployment Planner)](media/site-recovery-vmware-deployment-planner-analyze-report/what-if-analysis-v2a.png)

### <a name="recommended-vm-batch-size-for-initial-replication"></a>Recommended VM batch size for initial replication (初回レプリケーションの推奨 VM バッチ サイズ)
このセクションには、必要な RPO を 100% の時間満たすことのできる推奨帯域幅を設定した状態で、初回レプリケーションを 72 時間以内に完了するという前提で保護することができる VM の推奨台数が表示されます。 この値は設定によって変更することができます。 レポートの生成時に変更するには、*GoalToCompleteIR* パラメーターを使用します。

ここに示されるグラフには、適合するすべての VM について検出された平均 VM サイズに基づき、初回レプリケーションを 72 時間以内に完了する場合の、帯域幅値の範囲と算出された VM のバッチ サイズ カウントが表示されます。

パブリック プレビューのレポートでは、どの VM をバッチに含めるべきかは明記されません。 [Compatible VMs (適合 VM)] セクションに示されたディスク サイズをもとに、それぞれの VM のサイズを調べ、1 つのバッチに含める VM を選択するか、既知のワークロード特性に基づいて VM を選択してください。 初回レプリケーションの完了時間は、VM の実際のディスク サイズや使用済みディスク領域、ネットワーク スループットに比例して変化します。

![推奨 VM バッチ サイズ](media/site-recovery-vmware-deployment-planner-analyze-report/ir-batching-v2a.png)

### <a name="cost-estimation"></a>コスト見積もり
このグラフは、選択したターゲット リージョンの Azure に対するディザスター リカバリー (DR) の総コスト見積もりの概要と、レポート生成に関して指定された通貨を示しています。

![コスト見積もりの概要](media/site-recovery-vmware-deployment-planner-analyze-report/cost-estimation-summary-v2a.png)

Azure Site Recovery を使用してすべての適合 VM を Azure に保護する場合に、ストレージ、コンピューティング、ネットワーク、ライセンスに関して支払う必要のあるコストを把握できます。 コスト計算で加味されるのは適合 VM です。プロファイリングの対象となったすべての VM が対象になるわけではありません。  

コストは月単位または年単位で表示することができます。 詳細については、「[サポートされるターゲット リージョン](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-target-regions)」と「[サポートされる通貨](./site-recovery-vmware-deployment-planner-cost-estimation.md#supported-currencies)」を参照してください。

**[Cost by components]\(コンポーネントごとのコスト\)** DR コストの合計は次の 4 つのコンポーネントに分けられます: コンピューティング、ストレージ、ネットワーク、Azure Site Recovery ライセンス コスト。 コストは、コンピューティング、ストレージ (Premium と Standard)、オンプレミス サイトと Azure の間に構成されている ExpressRoute/VPN、Azure Site Recovery ライセンスに関して、レプリケーション中および DR ドリル時に発生する消費量に基づいて計算されます。

**[Cost by states]\(状態ごとのコスト\)** ディザスター リカバリー (DR) の合計コストが、レプリケーションと DR ドリルという 2 種類の状態に基づいて分類されます。

**[Replication cost]\(レプリケーション コスト\)** :レプリケーション時に発生するコストです。 ストレージ、ネットワーク、Azure Site Recovery ライセンスのコストが含まれます。

**[DR-Drill cost]\(DR ドリル コスト\)** :テスト フェールオーバー時に発生するコストです。 テスト フェールオーバー中は、Azure Site Recovery によって VM がスピンアップされます。 DR ドリル コストには、実行中の VM のコンピューティング コストとストレージ コストが含まれます。

**[Azure storage cost per Month/Year]\(月/年単位の Azure Storage コスト\)** Premium ストレージと Standard ストレージに関して、レプリケーションと DR ドリルで生じる合計ストレージ コストが表示されます。
VM ごとの詳細なコスト分析は、[[Cost Estimation]\(コスト見積もり\)](site-recovery-vmware-deployment-planner-cost-estimation.md) シートで確認できます。

### <a name="growth-factor-and-percentile-values-used"></a>増加率と使用パーセンタイル値
このセクションのシートの一番下には、プロファイリング対象 VM に対するすべてのパフォーマンス カウンターのパーセンタイル値 (既定では 95 パーセンタイル) と、すべての計算に使用される増加率 (%) (既定では 30%) が表示されます。

![増加率と使用パーセンタイル値](media/site-recovery-vmware-deployment-planner-analyze-report/growth-factor-v2a.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>使用可能な帯域幅の入力に関する推奨事項

![使用可能な帯域幅の入力に関する推奨事項](media/site-recovery-vmware-deployment-planner-analyze-report/profiling-overview-bandwidth-input-v2a.png)

Site Recovery のレプリケーション用に設定できる帯域幅 (Mbps) の上限があらかじめわかっているケースもあるでしょう。 Deployment Planner ツールでは、使用可能な帯域幅を (レポートの生成中に -Bandwidth パラメーターで) 入力し、達成可能な RPO (分) を取得することができます。 こうして得た達成可能 RPO 値をもとに、帯域幅を追加でセットアップすべきか、またはディザスター リカバリー ソリューションとしてその RPO で問題がないかを判断することができます。

![500 Mbps の帯域幅で達成可能な RPO](media/site-recovery-vmware-deployment-planner-analyze-report/achievable-rpo-v2a.png)

## <a name="vm-storage-placement"></a>VM-Storage placement (VM<->ストレージの配置)

>[!Note]
>Deployment Planner v 2.5 以降では、マネージド ディスクに直接レプリケートするマシンへのストレージの配置を推奨しています。

![VM-Storage placement (VM<->ストレージの配置)](media/site-recovery-vmware-deployment-planner-analyze-report/vm-storage-placement-v2a.png)

**Replication Storage Type (レプリケーション ストレージの種類)** : **[VMs to Place]\(配置する VM\)** 列に示されている該当 VM をすべてレプリケートするために使用される Standard または Premium のマネージド ディスクです。

**Log Storage Account Type (ログ ストレージ アカウントの種類)** :レプリケーションのログはすべて Standard ストレージ アカウントに格納されます。

**Suggested Prefix for Storage Account (ストレージ アカウントに推奨されるプレフィックス)** :キャッシュ ストレージ アカウントに名前を付ける場合に使用できる 3 文字の推奨プレフィックスです。 独自のプレフィックスを使用することもできますが、[ストレージ アカウントのパーティションの名前付け規則](/en-in/azure/storage/blobs/storage-performance-checklist)に準拠したプレフィックスがツールから提案されます。

**[Suggested Log Account Name]\(推奨ログ アカウント名\)** : 推奨されるプレフィックスを付けた後のストレージ アカウント名です。 山かっこ (< と >) で囲まれた名前は、カスタムの入力値に置き換えてください。

**[Placement Summary]\(配置の概要\)** : ストレージの種類別に保護された VM に必要なディスクの概要。 これには、VM の合計数、全ディスクのプロビジョニング済み合計サイズ、ディスクの総数が含まれます。

**[VMs to Place (配置する VM)]** : パフォーマンスと稼働率を最大限に引き出すために、指定のストレージ アカウントに配置する必要がある全 VM の一覧です。

## <a name="compatible-vms"></a>Compatible VMs (適合 VM)
![適合 VM の Excel スプレッドシート](media/site-recovery-vmware-deployment-planner-analyze-report/compatible-vms-v2a.png)

**[VM Name]\(VM 名\)** :レポートの生成時に VMListFile で使った VM の名前または IP アドレスです。 また、VM にアタッチされたディスク (VMDK) もこの列に一覧表示されます。 vCenter において名前または IP アドレスが重複する VM を区別するために、VM 名には ESXi ホスト名が一緒に表示されます。 このときに表示される ESXi ホストは、プロファイリング期間中、Deployment Planner ツールによって VM が最初に検出されたときに配置されていたホストです。

**[VM Compatibility]\(VM 適合性\)** : 値は **Yes** および **Yes\* *_ です。_* Yes**\* は、[Premium SSD](../virtual-machines/disks-types.md) に適した VM があるインスタンスに付けられます。 ただしこの場合、プロファイリング結果によると変更頻度または IOPS の高いディスクが、P20 または P30 のカテゴリに適しているのに、ディスクのサイズが原因でそれよりも低い P10 または P20 にマッピングされています。 ストレージ アカウントでは、Premium Storage のディスク タイプが、そのサイズに基づいて決定されます。 次に例を示します。
* 128 GB 未満の場合は P10
* 128 ～ 256 GB の場合は P15
* 256 ～ 512 GB の場合は P20
* 512 ～ 1,024 GB の場合は P30
* 1,025 ～ 2,048 GB の場合は P40
* 2,049 ～ 4,095 GB の場合は P50

たとえば、ディスクのワークロード特性上は P20 または P30 に分類されるものの、サイズ上はそれよりも低い Premium Storage ディスク タイプに対応している VM は、Deployment Planner ツールによって **Yes**\* として表示されます。 そのうえで、推奨される適切な Premium Storage ディスク タイプに合わせてレプリケーション元のディスク サイズを変更するか、またはレプリケーション先のディスク タイプをフェールオーバー後に変更するように促されます。

**[ストレージの種類]**: Standard または Premium があります。

**Asrseeddisk (Managed Disk) created for replication (レプリケーション用に作成された Asrseeddisk (マネージド ディスク))** :レプリケーションを有効にしたときに作成されるディスクの名前。 データとそのスナップショットが Azure に格納されます。

**[Peak R/W IOPS (with Growth Factor) (読み取り/書き込み IOPS のピーク (増加率を考慮))]**: 将来的な増加率 (既定では 30 %) を加味した、ディスクに対するピーク ワークロードの読み取り/書き込み IOPS (既定では 95 パーセンタイル) です。 VM のトータルな読み取り/書き込み IOPS は、必ずしもその個々のディスクの読み取り/書き込み IOPS を足した値になるとは限りません。VM の読み取り/書き込みのピーク IOPS が、プロファイリングの全期間を通じての、その各ディスクにおける読み取り/書き込み IOPS の合計のピークであるためです。

**[Peak Data Churn in Mbps (with Growth Factor) ( データの変更頻度のピーク (Mbps) (増加率を考慮))]**: 将来的な増加率 (既定では 30%) を加味した、ディスクに対する変更頻度のピーク値 (既定では 95 パーセンタイル) です。 VM のトータルなデータ変更頻度は、必ずしもその個々のディスクのデータ変更頻度を足した値になるとは限りません。VM のデータ変更頻度のピークが、プロファイリングの全期間を通じての、その各ディスクにおける変更頻度の合計のピークであるためです。

**[Azure VM Size (Azure VM サイズ)]**: オンプレミス VM に対応する Azure Cloud Services の仮想マシンとして理想的なサイズです。 この判断は、オンプレミス VM のメモリ、ディスク数/コア数/NIC 数、読み取り/書き込みの IOPS に基づいて行われます。 オンプレミスの VM が備えているこうした特性をすべて満たした最小の Azure VM サイズが確実に提案されます。

**[ディスク数]**: VM の仮想マシン ディスク (VMDK) の総数です。

**[ディスク サイズ (GB)]**: VM の全ディスクの合計セットアップ サイズです。 VM に含まれる個々のディスクのサイズも表示されます。

**[コア]**: VM に搭載されている CPU コアの数です。

**[メモリ (MB)]**: VM に搭載されている RAM です。

**[NIC]**: VM に搭載されている NIC の数です。

**[Boot Type (ブートの種類)]**: VM のブートの種類です。 BIOS と EFI のどちらかになります。  現在の Azure Site Recovery では、ブート ディスク内のパーティションの数が 4 個未満で、ブート セクターのサイズが 512 バイトの場合に、Windows Server EFI VM (Windows Server 2012、2012 R2、2016) がサポートされます。 EFI VM を保護するには、Azure Site Recovery モビリティ サービスのバージョンが 9.13 以上である必要があります。 EFI VM でサポートされているのはフェールオーバーだけです。 フェールバックはサポートされていません。  

**[OS の種類]**: VM の OS の種類です。 これは、Windows または Linux、あるいは VM の作成時に VMware vSphere で選択されたテンプレートに基づくその他の OS の場合があります。  

## <a name="incompatible-vms"></a>Incompatible VMs (不適合 VM)

![不適合 VM の Excel スプレッドシート
](media/site-recovery-vmware-deployment-planner-analyze-report/incompatible-vms-v2a.png)

**[VM 名]**: レポートの生成時に VMListFile で使った VM の名前または IP アドレスです。 また、VM にアタッチされた VMDK もこの列に一覧表示されます。 vCenter において名前または IP アドレスが重複する VM を区別するために、VM 名には ESXi ホスト名が一緒に表示されます。 このときに表示される ESXi ホストは、プロファイリング期間中、Deployment Planner ツールによって VM が最初に検出されたときに配置されていたホストです。

**[VM Compatibility (VM 適合性)]**: 指定された VM が Site Recovery での使用に不適合である理由が表示されます。 理由は VM の不適合ディスクごとに記述され、公開されている[ストレージの制限](/en-in/azure/storage/common/scalability-targets-standard-account)に基づく次のいずれかの状況に該当します。

* データ ディスク サイズが間違っているか、OS ディスク サイズが間違っている。 サポートの制限を[確認してください](vmware-physical-azure-support-matrix.md#azure-vm-requirements)。 
* VM サイズの合計 (レプリケーション + テスト フェールオーバー) が、サポートされているストレージ アカウントの上限サイズ (35 TB) を超えている。 これは通常、VM にあるいずれか 1 台のディスクのパフォーマンス特性が、Azure または Site Recovery でサポートされる Standard ストレージの上限を超えている場合に発生します。 そのような VM は Premium Storage の領域に分類されます。 一方、Premium ストレージ アカウントでサポートされる最大サイズは 35 TB です。保護対象となる単一の VM を複数のストレージ アカウントにまたがって保護することはできません。 また、保護対象 VM で実行されるテスト フェールオーバーは、レプリケーション処理と同じストレージ アカウントで実行されることに注意してください。 この場合、レプリケーションの進行と同時にテスト フェールオーバーが正常完了するためには、対象となるディスク サイズの 2 倍の容量をセットアップする必要があります。

* レプリケーション元の IOPS が、ストレージでサポートされている IOPS の上限 (ディスクあたり 7,500 IOPS) を超えている。

* レプリケーション元の IOPS が、ストレージでサポートされている IOPS の上限 (VM あたり 80,000 IOPS) を超えている。

* 平均データ変更頻度が、Site Recovery でサポートされているデータ変更頻度の上限 (ディスクの平均 I/O サイズで 20 MB/秒) を超えている。

* VM 上の全ディスクにおけるデータ変更頻度のピークが、Site Recovery でサポートされるデータ変更頻度のピークの上限 (VM あたり 54 MB/秒) を超えている。

* 平均実効書き込み IOPS が、Site Recovery でサポートされるディスク IOPS の上限 (840) を超えている。

* 算出されたスナップショット ストレージが、スナップショット ストレージに関してサポートされている上限 (10 TB) を超えている。

* 1 日あたりの総データ変更頻度が、プロセス サーバーでサポートされる 1 日あたりの変更頻度の上限 (2 TB) を超えている。


**[Peak R/W IOPS (with Growth Factor)]\(ピーク読み取り/書き込み IOPS (増加率を考慮)\)**: 将来的な増加率 (既定では 30%) を加味した、ディスクに対するピーク ワークロードの IOPS (既定では 95 パーセンタイル) です。 VM のトータルな読み取り/書き込み IOPS は、必ずしもその個々のディスクの読み取り/書き込み IOPS を足した値になるとは限りません。VM の読み取り/書き込みのピーク IOPS が、プロファイリングの全期間を通じての、その各ディスクにおける読み取り/書き込み IOPS の合計のピークであるためです。

**[Peak Data Churn in Mbps (with Growth Factor)]\(データの変更頻度のピーク (Mbps) (増加率を考慮)\)**: 将来的な増加率 (既定では 30%) を加味した、ディスクに対する変更頻度のピーク値 (既定では 95 パーセンタイル) です。 VM のトータルなデータ変更頻度は、必ずしもその個々のディスクのデータ変更頻度を足した値になるとは限りません。VM のデータ変更頻度のピークが、プロファイリングの全期間を通じての、その各ディスクにおける変更頻度の合計のピークであるためです。

**[ディスク数]**: VM の VMDK の総数です。

**[ディスク サイズ (GB)]**: VM の全ディスクの合計セットアップ サイズです。 VM に含まれる個々のディスクのサイズも表示されます。

**[コア]**: VM に搭載されている CPU コアの数です。

**[メモリ (MB)]**: VM に搭載されている RAM のサイズです。

**[NIC]**: VM に搭載されている NIC の数です。

**[Boot Type (ブートの種類)]**: VM のブートの種類です。 BIOS と EFI のどちらかになります。  現在の Azure Site Recovery では、ブート ディスク内のパーティションの数が 4 個未満で、ブート セクターのサイズが 512 バイトの場合に、Windows Server EFI VM (Windows Server 2012、2012 R2、2016) がサポートされます。 EFI VM を保護するには、Azure Site Recovery モビリティ サービスのバージョンが 9.13 以上である必要があります。 EFI VM でサポートされているのはフェールオーバーだけです。 フェールバックはサポートされていません。

**[OS Type]\(OS の種類\)** :VM の OS の種類です。 これは、Windows または Linux、あるいは VM の作成時に VMware vSphere で選択されたテンプレートに基づくその他の OS の場合があります。

## <a name="azure-site-recovery-limits"></a>Azure Site Recovery の制限
以下の表は、Azure Site Recovery の制限を示したものです。 上記の制限は、弊社のテストに基づいて公開されていますが、アプリケーション I/O として想定されるすべての組み合わせを網羅したものではありません。 実際の結果は、ご使用のアプリケーションで発生するさまざまな I/O によって異なることが考えられます。 理想的な結果を得るために、デプロイ計画後も必ず、テスト フェールオーバーを実行してアプリケーションのテストを徹底し、そのパフォーマンスの真の姿を把握することをお勧めします。

**レプリケーション先のストレージ** | **レプリケーション元の平均ディスク I/O サイズ** |**レプリケーション元ディスクの平均データ変更頻度** | **レプリケーション元ディスクの 1 日あたりのデータ変更頻度合計**
---|---|---|---
Standard Storage | 8 KB | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 8 KB  | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 16 KB | 4 MB/秒 |  (ディスクあたり) 336 GB
Premium P10 または P15 ディスク | 32 KB 以上 | 8 MB/秒 | (ディスクあたり) 672 GB
Premium P20、P30、P40、または P50 ディスク | 8 KB    | 5 MB/s | (ディスクあたり) 421 GB
Premium P20、P30、P40、または P50 ディスク | 16 KB 以上 | 20 MB/秒 | (ディスクあたり) 1,684 GB

**ソース データ変更頻度** | **上限**
---|---
VM 上の全ディスクにおけるデータ変更頻度のピーク | 54 MB/秒
プロセス サーバーでサポートされる 1 日あたりのデータ変更頻度の上限 | 2 TB

前述の数値は、I/O のオーバーラップを 30% とした場合の平均値です。 Site Recovery は、オーバーラップ比に基づくより高いスループットと、より大きな書き込みサイズ、そして実ワークロード I/O 動作を扱うことができます。 上記の数値には、標準的なバックログとして約 5 分が想定されています。 つまりデータはアップロード後 5 分以内に処理されて復旧ポイントが作成されます。


## <a name="cost-estimation"></a>コスト見積もり
[コスト見積もり](site-recovery-vmware-deployment-planner-cost-estimation.md)について詳しい情報をご覧ください。


## <a name="next-steps"></a>次のステップ
[コスト見積もり](site-recovery-vmware-deployment-planner-cost-estimation.md)について詳しい情報をご覧ください。