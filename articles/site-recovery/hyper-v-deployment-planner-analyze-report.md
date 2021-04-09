---
title: Azure Site Recovery の Hyper-V Deployment Planner レポートの分析
description: この記事では、Hyper-V VM から Azure へのディザスター リカバリーについて、Azure Site Recovery Deployment Planner によって生成されるレポートを分析する方法を説明します。
services: site-recovery
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/21/2019
ms.author: mayg
ms.openlocfilehash: f230445ecdb046c2b631e89567df71e1d09c3234
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95999231"
---
# <a name="analyze-the-azure-site-recovery-deployment-planner-report"></a>Azure Site Recovery Deployment Planner レポートの分析
この記事では、Azure Site Recovery Deployment Planner で、Hyper-V から Azure へのシナリオに関して生成される Excel レポート内のシートについて取り上げます。

## <a name="on-premises-summary"></a>On-Premises summary (オンプレミス サマリー)
[On-premises summary]\(オンプレミス サマリー\) ワークシートには、プロファイリングの対象となった Hyper-V 環境の概要が表示されます。

![On-Premises summary (オンプレミス サマリー)](media/hyper-v-deployment-planner-analyze-report/on-premises-summary-h2a.png)

**[開始日]** / **[終了日]** : レポートの生成時に考慮されたプロファイリング データの開始日と終了日です。 既定では、プロファイリングを開始した日付が開始日に、プロファイリングを終了した日付が終了日になります。 この情報は、レポートの生成時に "StartDate" パラメーターと "EndDate" パラメーターで指定できます。

**[Total number of profiling days]\(プロファイリングの合計日数\)** :レポートの生成対象期間 (開始日から終了日まで) におけるプロファイリングの合計日数です。

**[Number of compatible virtual machines]\(適合する仮想マシンの数\)**: 適合する VM の総数です。この VM については、必要なネットワーク帯域幅、必要なストレージ アカウントの数、Azure コア数が計算されます。

**[Total number of disks across all compatible virtual machines]\(すべての適合仮想マシンにおけるディスクの総数\)**: 適合するすべての VM におけるディスクの総数です。

**[Average number of disks per compatible virtual machine]\(適合する仮想マシン 1 台あたりの平均ディスク数\)** : 適合するすべての VM から割り出される平均ディスク数です。

**[Average disk size (GB)]\(平均ディスク サイズ (GB)\)** : 適合するすべての VM から割り出される平均ディスク サイズです。

**[Desired RPO (minutes) (必要な RPO (分))]** : 必要な帯域幅の見積もりに使用された回復ポイントの目標です。既定の RPO またはレポートの生成時に "DesiredRPO" パラメーターに指定された値が該当します。

**[Desired bandwidth (Mbps) (必要な帯域幅 (Mbps))]**: レポートの生成時に、達成可能な RPO (回復ポイントの目標) を見積もる目的で "Bandwidth" パラメーターに指定した値です。

**[Observed typical data churn per day (GB)]\(観察された 1 日あたりの標準的なデータの変更頻度 (GB\))** : プロファイリングの全日数にわたって観察されたデータ変更頻度の平均値です。

## <a name="recommendations"></a>推奨事項 
Hyper-V to Azure レポートの [Recommendations]\(推奨事項\) シートには、[Desired RPO]\(必要な RPO\) の選択内容に応じて、次の情報が表示されます。

![Recommendations for Hyper-V to Azure レポート](media/hyper-v-deployment-planner-analyze-report/Recommendations-h2a.png)

### <a name="profile-data"></a>Profile data (プロファイル データ)
![Profile data (プロファイル データ)](media/hyper-v-deployment-planner-analyze-report/profile-data-h2a.png)

**[Profiled data period]\(プロファイリング データ期間\)** : プロファイリングが実行された期間です。 既定では、すべてのプロファイリング データが計算の対象となります。 レポートの生成に StartDate オプションと EndDate オプションを使用した場合、特定の期間のレポートが生成されます。 

**[Number of Hyper-V servers profiled]\(プロファイリング対象 Hyper-V サーバーの数\)**: VM のレポートが生成される Hyper-V サーバーの数。 この数値を選択すると、該当する Hyper-V サーバーの名前が表示されます。 [On-premises Storage Requirement]\(オンプレミス ストレージ要件\) シートが開き、すべてのサーバーがそのストレージ要件と共に表示されます。 

**[Desired RPO]\(必要な RPO\)** : ご利用のデプロイに対する回復ポイントの目標です。 既定では、15 分、30 分、60 分の 3 とおりの RPO 値について、必要なネットワーク帯域幅が計算されます。 選択した値に応じて、シート上で関連する値が更新されます。 レポートの生成中に DesiredRPOinMin パラメーターを使用した場合、その値がこの [Desired RPO]\(必要な RPO\) の結果に表示されます。

### <a name="profiling-overview"></a>プロファイリングの概要
![プロファイリングの概要](media/hyper-v-deployment-planner-analyze-report/profiling-overview-h2a.png)

**[Total Profiled Virtual Machines]\(プロファイリングされた仮想マシンの合計\)** : プロファイリング データを使用できる VM の総数です。 プロファイリングされなかった VM の名前が VMListFile に含まれている場合、それらの VM はレポートの生成時に考慮されず、プロファイリングされた VM の合計からは除外されます。

**[Compatible Virtual Machines]\(適合仮想マシン\)**: Azure Site Recovery を使用して Azure で保護できる VM の数です。 これは適合する VM の総数になります。この VM については、必要なネットワーク帯域幅、ストレージ アカウントの数、Azure コア数が計算されます。 適合する VM ごとの詳しい情報は、「Compatible VMs (適合 VM)」セクションで確認できます。

**[Incompatible Virtual Machines]\(不適合仮想マシン\)** : プロファイリングされた VM のうち、Site Recovery を使用した保護に適合しない VM の数です。 不適合の理由については、「Incompatible VMs (不適合 VM)」セクションで取り上げます。 プロファイリングされなかった VM の名前が VMListFile に含まれている場合、それらの VM は、不適合 VM の数から除外されます。 そのような VM は、[Incompatible VMs (不適合 VM)] セクションの最後に [Data not found (データが見つかりません)] として列挙されます。

**[Desired RPO]\(必要な RPO\)** : 必要な回復ポイントの目標 (分単位) です。 次の 3 つの RPO 値についてレポートが生成されます: 15 分 (既定値)、30 分、60 分。 シートの右上にある **[Desired RPO (必要な RPO)]** ボックスの一覧での選択に応じて、レポートされる推奨帯域幅が変化します。 -DesiredRPO パラメーターに独自の値を指定してレポートを生成した場合、**[Desired RPO]\(必要な RPO\)** ボックスの一覧には、その独自の値が既定値として表示されます。

### <a name="required-network-bandwidth-mbps"></a>必要なネットワーク帯域幅 (Mbps)
![必要なネットワーク帯域幅](media/hyper-v-deployment-planner-analyze-report/required-network-bandwidth-h2a.png)

**[To meet RPO 100% of the time]\(RPO を 100% の時間満たす場合\)**: 必要な RPO を 100% の時間満たす場合に割り当てる必要のある推奨帯域幅 (Mbps) です。 適合するすべての VM について、RPO からの逸脱 (RPO 違反) を防止し、安定した状態の差分レプリケーションを行うためには、この帯域幅を専用に確保する必要があります。

**[To meet RPO 90% of the time]\(RPO を 90% の時間満たす場合\)**: ブロードバンドの料金やその他の理由から、必要な RPO を 100% の時間満たすうえで必要な帯域幅を設定できないことも考えられます。 その場合は、必要な RPO を 90% の時間満たすことができるレベルにまで、使用する帯域幅の設定を下げることができます。 設定する帯域幅を引き下げたことによって生じる影響を明らかにするために、レポートには RPO 違反の件数とその期間についての what-if 分析が示されます。

**[Achieved Throughput]\(達成スループット\)**: GetThroughput コマンドが実行されたサーバーから、ストレージ アカウントが存在する Azure リージョンまでのスループットです。 このスループットは、Site Recovery を使用して適合 VM を保護した場合に得られる推定値を表しています。 Hyper-V サーバーのストレージとネットワークの特性が、ツールを実行したサーバーのそれと同じ状態に保たれていることが必要となります。

企業による Site Recovery のデプロイでは、常に [ExpressRoute](https://aka.ms/expressroute) を使用することをお勧めします。

### <a name="required-storage-accounts"></a>必要なストレージ アカウント
次のグラフは、すべての適合 VM を保護するうえで必要なストレージ アカウントの総数です (Standard と Premium)。 使用するストレージ アカウントを VM ごとに把握するには、「VM-Storage placement (VM<->ストレージの配置)」セクションを参照してください。

![必要な Azure Storage アカウント](media/hyper-v-deployment-planner-analyze-report/required-storage-accounts-h2a.png)

### <a name="required-number-of-azure-cores"></a>必要な Azure コア数
適合するすべての VM のフェールオーバー (またはテスト フェールオーバー) 前にセットアップされるコアの総数です。 使用できるコア数がサブスクリプションに不足している場合、フェールオーバー (またはテスト フェールオーバー) 時に Site Recovery が VM を作成できません。

![必要な Azure コア数](media/hyper-v-deployment-planner-analyze-report/required-number-of-azure-cores-h2a.png)


### <a name="additional-on-premises-storage-requirement"></a>Additional On-premises Storage Requirement (その他のオンプレミス ストレージ要件)

VM レプリケーションによって好ましくないダウンタイムが運用環境のアプリケーションに生じることがないよう、初回レプリケーションと差分レプリケーションを正常に完了させるうえで Hyper-V サーバー上に必要な空き記憶域の合計です。 各ボリュームの要件について詳しくは、「[オンプレミス ストレージ要件](#on-premises-storage-requirement)」をご覧ください。 

レプリケーションに空き領域が必要である理由については、「[オンプレミス ストレージ要件](#why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication)」セクションを参照してください。

![オンプレミス ストレージ要件とコピー頻度](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-and-copy-frequency-h2a.png)

### <a name="maximum-copy-frequency"></a>Maximum Copy Frequency (最大コピー頻度)
必要な RPO をレプリケーションで確保するためには、推奨される最大コピー頻度を設定する必要があります。 既定では 5 分です。 30 秒のコピー頻度を設定すれば、さらに RPO を短くすることができます。

### <a name="what-if-analysis"></a>What-if 分析
![What-if 分析](media/hyper-v-deployment-planner-analyze-report/what-if-analysis-h2a.png) この分析は、設定する帯域幅を引き下げて、必要な RPO を 90% の時間だけ満たすようにしたとき、プロファイリング期間中、おおよそ何件の違反が発生するかを可能性として提示するものです。 RPO 違反は日々生じることが考えられ、 グラフにはそれぞれの日のピーク RPO が表示されます。 この分析結果を見て、RPO 違反がすべての日にわたって存在しているかどうか、また、帯域幅を 100% から引き下げた場合の 1 日のピーク RPO が許容範囲内にあるかどうかを判断することができます。 許容範囲内であれば、レプリケーションに割り当てる帯域幅を引き下げることができます。 そうでなければ、必要な RPO を 100% の時間満たすことのできる水準まで帯域幅を引き上げます。 

### <a name="recommendation-for-successful-initial-replication"></a>Recommendation for Successful Initial Replication (初回レプリケーションを正常に完了するための推奨事項)
このセクションには、バッチ数 (一連の VM を何回のバッチに分けて保護するか) と初回レプリケーション (IR) を正常に完了させるために最低限必要な帯域幅とが表示されます。 

![IR バッチ分割](media/hyper-v-deployment-planner-analyze-report/ir-batching-h2a.png)

VM は、決められたバッチ順で保護されなければなりません。 それぞれのバッチには、一連の VM が具体的に割り当てられます。 バッチ 1 の VM は、バッチ 2 の VM よりも前に保護する必要があります。 同様に、バッチ 2 の VM は、バッチ 3 の VM よりも前に保護する必要があります。 バッチ 2 の VM は、バッチ 1 の VM の初回レプリケーションが完了した後で、レプリケーションを有効にすることができます。 バッチ 3 の VM も、バッチ 2 の VM の初回レプリケーションが完了した後で、レプリケーションを有効にすることができます。 

バッチの順序に従わなかった場合、VM の初回レプリケーションで必要な帯域幅が確保できず、保護が後回しにされる可能性があります。 その場合、VM は初回レプリケーションを完了できなくなるか、保護されたわずかな VM が再同期モードに移行するかのどちらかになります。 各バッチにどの VM を含めるべきかについては、[IR batching for the selected RPO]\(選択した RPO に必要な IR バッチ分割\) シートに詳しい情報があります。

このグラフは、指定された順序に沿って各バッチの初回レプリケーションと差分レプリケーションを実行する際に使用される帯域幅の配分を示しています。 第 1 バッチの VM を保護するときは、初回レプリケーションに帯域幅全体が利用できます。 第 1 バッチの初回レプリケーションが完了した後は、帯域幅の一部を差分レプリケーション用に確保する必要が生じます。 第 2 バッチの VM の初回レプリケーションに利用できるのは、その残りの帯域幅です。 

バッチ 2 の棒グラフは、バッチ 1 の VM の差分レプリケーションに必要な帯域幅と、バッチ 2 の VM の初回レプリケーションに利用できる帯域幅を示しています。 同様に、バッチ 3 の棒グラフは、先行するバッチ (バッチ 1 とバッチ 2 の VM) の差分レプリケーションに必要な帯域幅と、バッチ 3 の初回レプリケーションに利用できる帯域幅を示しています。 全バッチの初回レプリケーションが完了した後に最後の棒グラフが示すのは、保護対象となる全 VM の差分レプリケーションに必要な帯域幅ということになります。

**初回レプリケーションのバッチ分割が必要である理由**
初回レプリケーションの所要時間は、VM のディスク サイズや使用済みディスク領域、ネットワーク スループットによって決まります。 詳しい情報は、[IR batching for a selected RPO]\(選択した RPO に必要な IR バッチ分割\) シートで確認できます。

### <a name="cost-estimation"></a>コスト見積もり
このグラフは、選択したターゲット リージョンの Azure に対するディザスター リカバリー (DR) の総コスト見積もりの概要と、レポート生成に関して指定された通貨を示しています。

![コスト見積もりの概要](media/hyper-v-deployment-planner-analyze-report/cost-estimation-summary-h2a.png)

Site Recovery を使用してすべての適合 VM を Azure に保護する場合に、ストレージ、コンピューティング、ネットワーク、ライセンスに関して支払う必要のあるコストを把握できます。 コスト計算で加味されるのは適合 VM です。プロファイリングの対象となったすべての VM が対象になるわけではありません。 
 
コストは月単位または年単位で表示することができます。 詳細については、「[サポートされるターゲット リージョン](./hyper-v-deployment-planner-cost-estimation.md#supported-target-regions)」と「[サポートされる通貨](./hyper-v-deployment-planner-cost-estimation.md#supported-currencies)」を参照してください。

**[Cost by components]\(コンポーネントごとのコスト\)**: DR コストの合計が、コンピューティング、ストレージ、ネットワーク、Site Recovery ライセンス コストの 4 つのコンポーネントに分けて表示されます。 コストは、レプリケーション中および DR ドリル時に発生する消費量に基づいて計算されます。 計算には、コンピューティングとストレージ (Premium と Standard) のほか、オンプレミス サイトと Azure との間に構成されている ExpressRoute/VPN、Site Recovery ライセンスが考慮されます。

**[Cost by states]\(状態ごとのコスト\)**: ディザスター リカバリーの合計コストが、レプリケーションと DR ドリルという 2 種類の状態に基づいて分類されます。 

**[Replication cost]\(レプリケーション コスト\)**: レプリケーション時に発生するコスト。 ストレージ、ネットワーク、Site Recovery ライセンスのコストが含まれます。 

**[DR-Drill cost]\(DR ドリル コスト\)**: テスト フェールオーバー時に発生するコスト。 テスト フェールオーバー中は、Site Recovery によって VM がスピンアップされます。 DR ドリル コストには、実行中の VM のコンピューティング コストとストレージ コストが含まれます。 

**[Azure storage cost per Month/Year]\(月/年単位の Azure Storage コスト\)**: Premium ストレージと Standard ストレージに関して、レプリケーションと DR ドリルで生じる合計ストレージ コストが棒グラフで表示されます。 VM ごとの詳細なコスト分析は、[[Cost Estimation]\(コスト見積もり\)](hyper-v-deployment-planner-cost-estimation.md) シートで確認できます。

### <a name="growth-factor-and-percentile-values-used"></a>増加率と使用パーセンタイル値
シートの一番下にあるこのセクションには、プロファイリング対象 VM の全パフォーマンス カウンターに使用されるパーセンタイル値 (既定では 95 パーセンタイル) が表示されます。 また、すべての計算に使用される増加率 (%) (既定では 30%) も表示されます。

![増加率と使用パーセンタイル値](media/hyper-v-deployment-planner-run/growth-factor-max-percentile-value.png)

## <a name="recommendations-with-available-bandwidth-as-input"></a>使用可能な帯域幅の入力に関する推奨事項
![入力した帯域幅に基づくプロファイリング概要](media/hyper-v-deployment-planner-analyze-report/profiling-overview-bandwidth-input-h2a.png)

Site Recovery のレプリケーション用に設定できる帯域幅 (Mbps) の上限があらかじめわかっているケースもあるでしょう。 このツールを使うと、使用可能な帯域幅を (レポートの生成中に -Bandwidth パラメーターで) 入力することにより、達成可能な RPO (分) を求めることができます。 こうして得た達成可能 RPO 値をもとに、帯域幅を追加でプロビジョニングすべきか、またはディザスター リカバリー ソリューションとしてその RPO で問題がないかを判断することができます。

![達成可能 RPO](media/hyper-v-deployment-planner-analyze-report/achivable-rpo-h2a.PNG)

## <a name="vm-storage-placement-recommendation"></a>VM<->ストレージの配置に関する推奨情報 
![VM-Storage placement (VM<->ストレージの配置)](media/hyper-v-deployment-planner-analyze-report/vm-storage-placement-h2a.png)

**[Disk Storage Type (ディスク ストレージの種類)]**: **[VMs to Place (配置する VM)]** 列に示されている該当 VM をすべてレプリケートするために使用される "Standard" または "Premium" のストレージ アカウントです。

**[Suggested Prefix (推奨プレフィックス)]**: ストレージ アカウントの名前に使用できる 3 文字のプレフィックスです。 独自のプレフィックスを使用することもできますが、[ストレージ アカウントのパーティションの名前付け規則](/en-in/azure/storage/blobs/storage-performance-checklist)に準拠したプレフィックスがツールから提案されます。

**[Suggested Account Name (推奨アカウント名)]**: 推奨されるプレフィックスを付けた場合のストレージ アカウントの名前が表示されます。 山かっこ (< と >) で囲まれた名前は、カスタムの入力値に置き換えてください。

**[Log Storage Account (ログ ストレージ アカウント)]**: レプリケーションのログはすべて Standard ストレージ アカウントに格納されます。 レプリケーション先が Premium ストレージ アカウントである VM については、ログの格納用として別途 Standard ストレージ アカウントをセットアップしてください。 1 つの Standard ログ ストレージ アカウントを複数の Premium レプリケーション ストレージ アカウントで使用できます。 レプリケーション先が Standard ストレージ アカウントである VM では、同じストレージ アカウントがログに使用されます。

**[Suggested Log Account Name (推奨ログ アカウント名)]**: 推奨されるプレフィックスを付けた場合のストレージ ログ アカウントの名前が表示されます。 山かっこ (< と >) で囲まれた名前は、カスタムの入力値に置き換えてください。

**[Placement Summary (配置の概要)]**: レプリケーションとフェールオーバー (テスト フェールオーバーを含む) 時にストレージ アカウントにかかる VM の総負荷の概要が表示されます。 表示される概要の例を次に示します。

* ストレージ アカウントに関連付けられた VM の総数。 
* そのストレージ アカウントに配置されている全 VM における読み取り/書き込み IOPS の合計。
* 書き込み (レプリケーション) IOPS の合計。
* 全ディスクの合計セットアップ サイズ。
* ディスクの総数。

**[VMs to Place (配置する VM)]**: パフォーマンスと稼働率を最大限に引き出すために、指定のストレージ アカウントに配置すべき全 VM が一覧表示されます。

## <a name="compatible-vms"></a>Compatible VMs (適合 VM)
Site Recovery Deployment Planner によって生成される Excel レポートでは、すべての適合 VM に関する詳しい情報が "Compatible VMs (適合 VM)" シートに表示されます。

![Compatible VMs (適合 VM)](media/hyper-v-deployment-planner-analyze-report/compatible-vms-h2a.png)

**[VM 名]**: レポートの生成時に VMListFile で使った VM の名前です。 また、VM にアタッチされたディスク (VHD) もこの列に一覧表示されます。 この名前には、VM がプロファイリング期間中に検出された時点で配置されていた Hyper-V ホストの名前が含まれます。

**[VM Compatibility (VM 適合性)]**: **Yes** と **Yes**\* の 2 つの値があります。 **Yes**\* は、[Premium SSD](../virtual-machines/disks-types.md) に適した VM があるインスタンスに付けられます。 この場合、ディスクの変更頻度または IOPS が高く、現在ディスクにマップされているサイズよりも上位の Premium ディスク サイズが適していることをプロファイリングの結果は示しています。 ストレージ アカウントでは、Premium Storage のディスク タイプが、そのサイズに基づいて決定されます。 
* 128 GB 未満の場合は P10
* 128 GB から 256 GB の場合は P15
* 256 ～ 512 GB の場合は P20
* 512 GB から 1,024 GB の場合は P30
* 1,025 GB から 2,048 GB の場合は P40
* 2,049 GB から 4,095 GB の場合は P50

たとえば、ディスクのワークロード特性上は P20 または P30 に分類されるものの、サイズ上はそれよりも低い Premium Storage ディスク タイプに対応している VM は、Deployment Planner ツールによって **Yes**\* として表示されます。 そのうえで、推奨される適切な Premium Storage ディスク タイプに合わせてレプリケーション元のディスク サイズを変更するか、またはレプリケーション先のディスク タイプをフェールオーバー後に変更するように促されます。

**[ストレージの種類]**: Standard または Premium があります。

**[Suggested Prefix (推奨プレフィックス)]**: ストレージ アカウントの 3 文字のプレフィックスです。

**[ストレージ アカウント]**: ストレージ アカウントの推奨プレフィックスを使用した名前です。

**[Peak R/W IOPS (with Growth Factor)]\(ピーク読み取り/書き込み IOPS (増加率を考慮)\)**: 将来的な増加率 (既定では 30%) を加味した、ディスクに対するピーク ワークロードの読み取り/書き込み IOPS (既定では 95 パーセンタイル) です。 VM のトータルな読み取り/書き込み IOPS は、その個々のディスクの読み取り/書き込み IOPS を足した値になるとは限りません。 VM の読み取り/書き込みのピーク IOPS は、プロファイリングの全期間を通じての、その各ディスクにおける読み取り/書き込み IOPS の合計のピークです。

**[Peak Data Churn in MB/s (with Growth Factor)]\(データの変更頻度のピーク (MB/秒) (増加率を考慮)\)**: 将来的な増加率 (既定では 30%) を加味した、ディスクに対する変更頻度のピーク値 (既定では 95 パーセンタイル) です。 VM のトータルなデータ変更頻度は、その個々のディスクのデータ変更頻度を足した値になるとは限りません。 VM のデータ変更頻度のピークは、プロファイリングの全期間を通じての、その各ディスクにおける変更頻度の合計のピークです。

**[Azure VM Size (Azure VM サイズ)]**: オンプレミス VM に対応する Azure Cloud Services の VM として理想的なサイズです。 この判断は、オンプレミス VM のメモリ、ディスク数/コア数/NIC 数、読み取り/書き込みの IOPS に基づいて行われます。 オンプレミスの VM が備えているこうした特性をすべて満たした最小の Azure VM サイズが確実に提案されます。

**[ディスク数]**: VM の仮想マシン ディスク (VHD) の総数です。

**[ディスク サイズ (GB)]** : VM の全ディスクの合計サイズです。 VM に含まれる個々のディスクのサイズも表示されます。

**[コア]**: VM に搭載されている CPU コアの数です。

**[メモリ (MB)]**: VM に搭載されている RAM です。

**[NIC]**: VM に搭載されている NIC の数です。

**[ブートの種類]**: VM のブートの種類です。 BIOS と EFI のどちらかになります。

## <a name="incompatible-vms"></a>Incompatible VMs (不適合 VM)
Site Recovery Deployment Planner によって生成される Excel レポートでは、すべての不適合 VM に関する詳しい情報が "Incompatible VMs (不適合 VM)" シートに表示されます。

![Incompatible VMs (不適合 VM)](media/hyper-v-deployment-planner-analyze-report/incompatible-vms-h2a.png)

**[VM 名]**: レポートの生成時に VMListFile で使った VM の名前です。 また、VM にアタッチされたディスク (VHD) もこの列に一覧表示されます。 この名前には、VM がプロファイリング期間中に検出された時点で配置されていた Hyper-V ホストの名前が含まれます。

**[VM Compatibility (VM 適合性)]**: 指定された VM が Site Recovery での使用に不適合である理由が表示されます。 理由は VM の不適合ディスクごとに記述され、公開されている[ストレージの制限](/en-in/azure/storage/common/scalability-targets-standard-account)に基づく次のいずれかの状況に該当します。

* ディスク サイズが 4,095 GB を超えている。 Azure Storage では現在、4,095 GB を超えるデータ ディスク サイズがサポートされません。

* 第 1 世代 (BIOS ブート タイプ) の VM の OS ディスクが 2,047 GB を超えている。 Site Recovery では、第 1 世代の VM については 2,047 GB を超える OS ディスク サイズがサポートされません。

* 第 2 世代 (EFI ブート タイプ) の VM の OS ディスクが 300 GB を超えている。 Site Recovery では、第 2 世代の VM については、300 GB を超える OS ディスク サイズがサポートされません。

* VM の名前にサポートされない文字 (“” [] `) が含まれている。 これらの文字を 1 つでも名前に含んでいる VM については、プロファイリング データを取得できません。 

* 複数の VM によって VHD が共有されている。 Azure では、共有 VHD を含んだ VM がサポートされません。

* 仮想ファイバー チャネルを使用した VM はサポートされない。 Site Recovery では、仮想ファイバー チャネルを使用した VM がサポートされません。

* Hyper-V クラスターにレプリケーション ブローカーが存在しない。 Hyper-V レプリカ ブローカーが構成されていない Hyper-V クラスターの VM は、Site Recovery ではサポートされません。

* VM に高可用性が確保されていない。 クラスター ディスクではなくローカル ディスクに VHD が格納されている Hyper-V クラスター ノードの VM は、Site Recovery ではサポートされません。 

* VM サイズの合計 (レプリケーション + テスト フェールオーバー) が、サポートされている Premium ストレージ アカウントの上限サイズ (35 TB) を超えている。 これは通常、VM にあるいずれか 1 台のディスクのパフォーマンス特性が、Azure または Site Recovery でサポートされる Standard ストレージの上限を超えている場合に発生します。 そのような VM は Premium Storage の領域に分類されます。 一方、Premium ストレージ アカウントでサポートされる最大サイズは 35 TB です。 保護対象となる単一の VM を複数のストレージ アカウントにまたがって保護することはできません。 

    テスト フェールオーバー用に構成されているのが非管理対象ディスクである場合、保護対象 VM で実行されるテスト フェールオーバーは、レプリケーション処理と同じストレージ アカウントで実行されます。 この場合、レプリケーションの記憶域と同じ容量の記憶域が別途必要となります。 レプリケーションの進行と同時にテスト フェールオーバーを正常に完了できるようにするためです。 テスト フェールオーバー用に構成されているのがマネージド ディスクである場合、テスト フェールオーバー VM のための追加の領域は不要です。

* レプリケーション元の IOPS が、ストレージでサポートされている IOPS の上限 (ディスクあたり 7,500 IOPS) を超えている。

* レプリケーション元の IOPS が、ストレージでサポートされている IOPS の上限 (VM あたり 80,000 IOPS) を超えている。

* ソース VM の平均データ変更頻度が、Site Recovery でサポートされているデータ変更頻度の上限 (平均 I/O サイズで 20 MB/秒) を超えている。

* ソース VM の平均実効書き込み IOPS が、Site Recovery でサポートされる IOPS の上限 (840) を超えている。

* 算出されたスナップショット ストレージが、スナップショット ストレージに関してサポートされている上限 (10 TB) を超えている。

**[Peak R/W IOPS (with Growth Factor)]\(ピーク読み取り/書き込み IOPS (増加率を考慮)\)**: 将来的な増加率 (既定では 30%) を加味した、ディスクに対するピーク ワークロードの IOPS (既定では 95 パーセンタイル) です。 VM のトータルな読み取り/書き込み IOPS は、その個々のディスクの読み取り/書き込み IOPS を足した値になるとは限りません。 VM の読み取り/書き込みのピーク IOPS は、プロファイリングの全期間を通じての、その各ディスクにおける読み取り/書き込み IOPS の合計のピークです。

**[Peak Data Churn (MB/s) (with Growth Factor)]\(データの変更頻度のピーク (MB/秒) (増加率を考慮)\)**: 将来的な増加率 (既定では 30%) を加味した、ディスクに対する変更頻度のピーク値 (既定では 95 パーセンタイル) です。 VM のトータルなデータ変更頻度は、その個々のディスクのデータ変更頻度を足した値になるとは限らないことに注意してください。 VM のデータ変更頻度のピークは、プロファイリングの全期間を通じての、その各ディスクにおける変更頻度の合計のピークです。

**[ディスク数]**: VM の VHD の総数です。

**[ディスク サイズ (GB)]** : VM の全ディスクの合計セットアップ サイズです。 VM に含まれる個々のディスクのサイズも表示されます。

**[コア]**: VM に搭載されている CPU コアの数です。

**[メモリ (MB)]**: VM に搭載されている RAM のサイズです。

**[NIC]**: VM に搭載されている NIC の数です。

**[ブートの種類]**: VM のブートの種類です。 BIOS と EFI のどちらかになります。

## <a name="azure-site-recovery-limits"></a>Azure Site Recovery の制限
以下の表は、Site Recovery の制限を示したものです。 上記の制限は、弊社のテストに基づいて公開されていますが、アプリケーション I/O として想定されるすべての組み合わせを網羅したものではありません。 実際の結果は、ご使用のアプリケーションで発生するさまざまな I/O によって異なることが考えられます。 理想的な結果を得るために、デプロイ計画後も必ず、テスト フェールオーバーを実行してアプリケーションのテストを徹底し、そのパフォーマンスの真の姿を把握してください。
 
**レプリケーション先のストレージ** | **ソース VM の平均 I/O サイズ** |**ソース VM の平均データ変更頻度** | **ソース VM の 1 日あたりのデータ変更頻度合計**
---|---|---|---
Standard Storage | 8 KB | VM あたり 2 MB/秒 | VM あたり 168 GB
Premium Storage | 8 KB  | VM あたり 5 MB/秒 | VM あたり 421 GB
Premium Storage | 16 KB 以上| VM あたり 20 MB/秒 | VM あたり 1,684 GB

これらの制限は、I/O のオーバーラップを 30% とした場合の平均値です。 Site Recovery は、オーバーラップ比に基づくより高いスループットと、より大きな書き込みサイズ、そして実ワークロード I/O 動作を扱うことができます。 上記の数値には、標準的なバックログとして約 5 分が想定されています。 つまりデータはアップロード後 5 分以内に処理されて復旧ポイントが作成されます。

## <a name="on-premises-storage-requirement"></a>オンプレミス ストレージ要件

このワークシートには、初回レプリケーションと差分レプリケーションを正常に実行するうえで必要な空き記憶域の合計が、Hyper-V サーバーの (VHD が存在する) ボリュームごとに表示されます。 レプリケーションが原因で運用環境のアプリケーションに不要なダウンタイムが生じないよう、レプリケーションを有効にする前に、必要な記憶域を各ボリュームに追加してください。 

  レプリケーションに使用されるネットワーク帯域幅と VHD サイズに基づき、最適な記憶域要件が Site Recovery Deployment Planner によって割り出されます。

![オンプレミス ストレージ要件](media/hyper-v-deployment-planner-analyze-report/on-premises-storage-requirement-h2a.png)

### <a name="why-do-i-need-free-space-on-the-hyper-v-server-for-the-replication"></a>Hyper-V サーバーにレプリケーション用の空き領域が必要である理由
* VM のレプリケーションを有効にすると、その VM の VHD ごとに、初回レプリケーションに必要なスナップショットが Site Recovery によって作成されます。 初回レプリケーションの進行中も、新しい変更がアプリケーションによってディスクに書き込まれます。 Site Recovery では、そうした差分変更がログ ファイルで追跡されるため、そのための記憶域が別途必要になります。 初回レプリケーションが完了するまで、ログ ファイルはローカルに保存されます。 

    ログ ファイルとスナップショット (AVHDX) に必要な空き領域がない場合、レプリケーションは再同期モードとなり、レプリケーションはいつまでも完了しない状態となります。 最悪のケースでは、初回レプリケーションに、VHD サイズの 100% の空き領域が追加で必要になります。
* 初回レプリケーションが完了した後に、差分レプリケーションが開始されます。 Site Recovery では、そうした差分変更がログ ファイルで追跡され、ログ ファイルは、VM の VHD と同じボリュームに格納されます。 これらのログ ファイルは、構成済みのコピー頻度で Azure にレプリケートされます。 使用できるネットワーク帯域幅にもよりますが、ログ ファイルが Azure にレプリケートされるまでには、ある程度時間がかかります。 

    ログ ファイルを格納するために必要な空き領域がない場合、レプリケーションは一時停止されます。 その後、VM のレプリケーションは "再同期が必要" な状態に移行します。
* Azure にログ ファイルをプッシュできるだけの十分なネットワーク帯域幅がない場合、ログ ファイルがボリューム上にたまっていきます。 最悪のケースでは、ログ ファイルが VHD の 50% の大きさにまで増えたとき、VM のレプリケーションは "再同期が必要" な状態に移行します。 最悪のケースでは、差分レプリケーションに、VHD サイズの 50% の空き領域が追加で必要になります。

**[Hyper-V host]\(Hyper-V ホスト\)**: プロファイリングされた Hyper-V サーバーの一覧です。 サーバーが Hyper-V クラスターに属している場合、すべてのクラスター ノードが 1 つにまとめられます。

**[Volume (VHD path)]\(ボリューム (VHD パス)\)**: VHD/VHDX が存在する Hyper-V ホストの各ボリュームです。 

**[Free space available (GB)]\(空き領域 (GB)\)**: ボリューム上の空き領域です。

**[Total storage space required on the volume (GB)]\(ボリュームに必要な合計記憶域 (GB)\)** : 初回レプリケーションと差分レプリケーションを正常に完了させるためにボリューム上に必要な合計空き記憶域です。 

**[Total additional storage to be provisioned on the volume for successful replication (GB)]\(レプリケーションを正常に完了するためにボリュームに別途プロビジョニングする必要のあるストレージの合計 (GB)\)**: 初回レプリケーションと差分レプリケーションを正常に完了させるために、ボリュームに別途プロビジョニングされる必要のある領域の合計です。

## <a name="initial-replication-batching"></a>初回レプリケーションのバッチ分割 

### <a name="why-do-i-need-initial-replication-batching"></a>初回レプリケーションのバッチ分割が必要である理由
すべての VM を同時に保護する場合、それだけ多くの空き記憶域が必要になります。 十分な空き記憶域がない場合、VM のレプリケーションは再同期モードに移行します。 同様に、一度にすべての VM の初回レプリケーションを正常完了させるためには、より多くのネットワーク帯域幅が必要になるでしょう。 

### <a name="initial-replication-batching-for-a-selected-rpo"></a>選択した RPO に必要な初回レプリケーションのバッチ分割
このワークシートには、初回レプリケーション (IR) の詳しい情報がバッチごとに表示されます。 IR バッチ分割シートは、RPO ごとに作成されます。 

ボリュームごとの推奨オンプレミス ストレージ要件に従った後に再現すべき主な情報は、同時に保護できる一連の VM です。 それらの VM はバッチとして 1 つにまとめられます。バッチは複数存在する場合もあります。 VM は、決められたバッチ順で保護してください。 たとえば、まずバッチ 1 の VM を保護し、 初回レプリケーションが完了したら、バッチ 2 の VM を保護する、という流れになります。 バッチのリストおよび対応する VM をこのシートで把握することができます。 

![IR バッチ分割の詳細](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo-h2a.png)

![IR バッチ分割の詳細 (追加情報)](media/hyper-v-deployment-planner-analyze-report/ir-batching-for-rpo2-h2a.png)

### <a name="each-batch-provides-the-following-information"></a>それぞれのバッチについて、次の情報が得られます。 
**[Hyper-V host]\(Hyper-V ホスト\)**: 保護の対象となる VM の Hyper-V ホスト。

**[Virtual Machine]\(仮想マシン\)** : 保護の対象となる VM。 

**[Comments]\(コメント\)**: VM の特定のボリュームに関して必要なアクションがあれば、ここにコメントとして記載されます。 たとえば、必要な空き領域がボリュームに存在しない場合、その VM を保護するためのストレージを追加するよう促すコメントが表示されます。

**[Volume (VHD path)]\(ボリューム (VHD パス)\)**: VM の VHD が存在するボリュームの名前。 

**[Free space available on the volume (GB)]\(ボリューム上の空き領域 (GB)\)** : VM のボリューム上の空きディスク領域。 ボリューム上で使用できる空き領域を計算するときは、同じボリュームに VHD が存在する先行バッチの各 VM による差分レプリケーション用のディスク領域が考慮されます。 

たとえば、VM1、VM2、VM3 が同じボリューム (E:\VHDpath など) に存在するとします。 レプリケーション前の時点で、このボリュームに存在する空き領域は 500 GB です。 VM1 はバッチ 1 に、VM2 はバッチ 2 に、VM3 はバッチ 3 に属しています。 VM1 については、利用できる空き領域は 500 GB です。 VM2 については、利用できる空き領域は、VM1 の差分レプリケーションに必要なディスク領域を 500 から差し引いた値となります。 VM1 の差分レプリケーションに 300 GB が必要である場合、VM2 で利用できる空き領域は 200 GB (500 GB - 300 GB) です。 同様に、VM2 の差分レプリケーションに 300 GB の領域が必要であるとしましょう。 VM3 で利用できる空き領域は -100 GB (200 GB - 300 GB) となります。

**[Storage required on the volume for initial replication (GB)]\(初回レプリケーションに関してボリュームに必要な空き記憶域 (GB)\)**: VM の初回レプリケーションに関してボリュームに必要な空き記憶域。

**[Storage required on the volume for delta replication (GB)]\(差分レプリケーションに関してボリュームに必要な空き記憶域 (GB)\)** : VM の差分レプリケーションに関してボリュームに必要な空き記憶域。

**[Additional storage required based on deficit to avoid replication failure (GB)]\(レプリケーション エラーを防ぐために領域不足に基づいて追加する必要のある記憶域 (GB)\)**: VM のボリュームに別途必要な記憶域。 初回レプリケーションと差分レプリケーションの記憶域要件のどちらか大きい方から、ボリューム上の空き領域を差し引いた値となります。

**[Minimum bandwidth required for initial replication (Mbps)]\(初回レプリケーションに必要な最小帯域幅 (Mbps)\)**: VM の初回レプリケーションに必要な最小帯域幅。

**[Minimum bandwidth required for delta replication (Mbps)]\(差分レプリケーションに必要な最小帯域幅 (Mbps)\)** : VM の差分レプリケーションに必要な最小帯域幅。

### <a name="network-utilization-details-for-each-batch"></a>各バッチのネットワーク使用率の詳細 
バッチごとのテーブルには、そのバッチのネットワーク使用率の概要が表示されます。

**[Bandwidth available for Batch]\(バッチで使用可能な帯域幅\)** : バッチに使用できる帯域幅。先行するバッチの差分レプリケーションの帯域幅を考慮したうえで求められます。

**[Approximate bandwidth available for initial replication of batch]\(バッチの初回レプリケーションで使用できるおおよその帯域幅\)**: バッチの VM の初回レプリケーションで使用できる帯域幅。 

**[Approximate bandwidth consumed for delta replication of batch]\(バッチの差分レプリケーションで消費されるおおよその帯域幅\)**: バッチの VM の差分レプリケーションに必要な帯域幅。 

**[Estimated initial replication time for batch (HH:MM)]\(バッチの初回レプリケーションの推定時間 (時:分)\)** : 初回レプリケーションの推定時間 (時:分)。



## <a name="next-steps"></a>次のステップ
[コスト見積もり](hyper-v-deployment-planner-cost-estimation.md)について詳しい情報をご覧ください。