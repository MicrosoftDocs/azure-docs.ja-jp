---
title: Azure Site Recovery を使用する Hyper-V ディザスター リカバリーの容量を計画する | Microsoft Docs
description: この記事を使用して、Azure Site Recovery サービスによるディザスター リカバリーを設定するときに容量を見積もります。
author: rayne-wiselman
manager: carmonm
services: site-recovery
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: eeadfd6a57ff8a26f3f124e2a807fcd66e77b85f
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/02/2019
ms.locfileid: "53976717"
---
# <a name="plan-capacity-for-hyper-v-vm-disaster-recovery"></a>Hyper-V VM のディザスター リカバリーの容量を計画する 

[Azure へのデプロイを行う Azure Site Recovery Deployment Planner for Hyper-V](site-recovery-hyper-v-deployment-planner.md) の新しい強化されたバージョンが使用可能になりました。 これは従来のツールに代わるものです。 デプロイの計画には新しいツールを使用します。
このツールでは次の指標が提供されます。

* ディスク数、ディスク サイズ、IOPS、変更頻度、いくつかの VM 特性に基づく VM の適格性評価
* ネットワーク帯域幅ニーズ対 RPO の評価
* Azure インフラストラクチャの要件
* オンプレミス インフラストラクチャの要件
* 初期レプリケーションのバッチ処理に関するガイダンス
* Azure へのディザスター リカバリーの推定合計コスト


Azure Site Recovery Capacity Planner を使用すると、Azure Site Recovery で Hyper-V VM をレプリケートするために必要な容量の見極めが楽になります。

Site Recovery Capacity Planner は、ソース環境とワークロードの分析に使用します。 帯域幅ニーズ、ソースの場所に必要なサーバー リソース、およびターゲットの場所に必要なリソース (VM やストレージなど) を見積もるのに役立ちます。

このツールは 2 つのモードで実行できます。

* **クイック プランニング**:VM、ディスク、ストレージ、変更率の平均値に基づいてネットワークとサーバーを予測します。
* **詳細なプランニング**:VM レベルで各ワークロードの詳細を提供します。 VM の互換性が分析され、ネットワークおよびサーバーが予測されます。

## <a name="before-you-start"></a>開始する前に

* VM、VM あたりのディスク数、ディスクあたりのストレージなど、環境の情報を収集します。
* レプリケートされたデータの 1 日の変更 (チャーン) 率を識別します。 [Hyper-V 容量計画ツール](https://www.microsoft.com/download/details.aspx?id=39057)をダウンロードして変更率を得ます。 [こちら](site-recovery-capacity-planning-for-hyper-v-replication.md) を参照してください。 このツールは 1 週間にわたって実行して平均値を取り込むことをお勧めします。


## <a name="run-the-quick-planner"></a>クイック プランナーの実行
1. [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel) をダウンロードして起動します。 マクロを実行する必要があります。 メッセージが表示されたら、編集とコンテンツを有効にするよう選択します。

2. **[Select a planner type]\(プランナーの種類を選択する\)** リスト ボックスから **[Quick Planner]\(クイック プランナー\)** を選択します。

   ![作業開始](./media/site-recovery-capacity-planner/getting-started.png)

3. **Capacity Planner** ワークシートに、必要な情報を入力します。 以下のスクリーンショットの赤の丸が付いているすべてのフィールドに入力します。

   a. **[シナリオの選択]** で **[Hyper-V から Azure]** または **[VMware/物理から Azure]** を選択します。

   b. **[Average daily data change rate (%)]\(1 日の平均データ変更率 (%)\)** に [Hyper-V 容量計画ツール](site-recovery-capacity-planning-for-hyper-v-replication.md)または [Site Recovery Deployment Planner](./site-recovery-deployment-planner.md) を使用して収集した情報を入力します。

   c. **[Compression]\(圧縮\)** 設定は、Hyper-V VM を Azure にレプリケートする場合には使用されません。 圧縮する場合は、Riverbed などのサードパーティのアプライアンスを使用します。

   d. **[Retention in days]\(リテンション期間 (日数)\)** に、レプリカを保持する期間を日数で指定します。

   e. **[Number of hours in which initial replication for the batch of virtual machines should complete]\(仮想マシンのバッチの初期レプリケーションを完了させる時間\)** と **[Number of virtual machines per initial replication batch]\(初期レプリケーションのバッチあたりの仮想マシンの数\)** には、初期レプリケーションの要件の計算に使用した設定を入力します。 Site Recovery をデプロイする際には、初期データ セット全体をアップロードします。

   ![入力](./media/site-recovery-capacity-planner/inputs.png)

4. ソース環境の値を入力した後には、以下などが出力されます。

   * **[Bandwidth required for delta replication (in Megabits/sec)]\(差分レプリケーションに必要な帯域幅 (MB/秒)\)**:1 日の平均データ変更率に基づいてデルタ レプリケーションのネットワーク帯域幅が計算されます。
   * **[Bandwidth required for initial replication (in Megabits/sec)]\(初期レプリケーションに必要な帯域幅 (MB/秒)\)**:入力した初回レプリケーションの値に基づいて初回レプリケーションのネットワーク帯域幅が計算されます。
   * **[Storage required (in GBs)]\(必要なストレージ (GB)\)**:Azure で必要なストレージの合計です。
   * **[Total IOPS on Standard Storage]\(Standard Storage の IOPS 合計\)**:Standard ストレージ アカウントの合計での IOPS が 8K の単位サイズで計算されます。 Quick Planner の場合は、この数値はすべてのソース VM ディスクおよび 1 日のデータ変更率に基づいて計算されます。 Detailed Planner の場合、この数値は、Standard の Azure VM にマップされている VM 数の合計およびそれらの VM でのデータ変更率に基づいて計算されます。
   * **[Number of Standard storage accounts required]\(必要な Standard Storage アカウントの数\)**:VM を保護するために必要な Standard Storage アカウントの合計数。 Standard Storage アカウントの場合は、Standard Storage のすべての VM で最大 20,000 IOPS まで対応できます。 また、ディスクごとでは最大 500 IOPS まで対応できます。
   * **[Number of Blob disks required]\(必要な BLOB ディスクの数\)**:Azure ストレージに作成されるディスクの数。
   * **[Number of premium accounts required]\(必要な Premium Storage アカウントの数\)**:VM を保護するために必要な Premium Storage アカウントの合計数。 IOPS が多い (20,000 を超える) ソース VM には、Premium Storage アカウントが必要です。 Premium Storage アカウントでは、最大 80,000 IOPS に対応できます。
   * **[Total IOPS on Premium Storage]\(Premium Storage の IOPS 合計\)**:Premium Storage アカウントの合計での IOPS が 256K の単位サイズで計算されます。 Quick Planner の場合は、この数値はすべてのソース VM ディスクおよび 1 日のデータ変更率に基づいて計算されます。 Detailed Planner の場合、この数値は、Premium の Azure VM (DS および GS シリーズ) にマップされている VM 数の合計およびそれらの VM でのデータ変更率に基づいて計算されます。
   * **[Number of Configuration Servers required]\(必要な構成サーバーの数\)**:デプロイに必要な構成サーバーの数を示します。
   * **[Number of additional Process Servers required]\(必要なその他のプロセス サーバーの数\)**:既定で構成サーバーで実行しているプロセス サーバーの他に、追加のプロセス サーバーが必要かどうかを示します。
   * **[100% additional storage on the Source]\(ソースでの 100% 追加ストレージ\)**:ソースの場所に追加ストレージが必要かどうかを示します。

      ![出力](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>詳細なプランナーの実行

1. [Site Recovery Capacity Planner](https://aka.ms/asr-capacity-planner-excel) をダウンロードして起動します。 マクロを実行する必要があります。 メッセージが表示されたら、編集とコンテンツを有効にするよう選択します。

2. **[Select a planner type (プランナーの種類を選択する]** リスト ボックスから **[Detailed Planner (詳細なプランナー)]** を選択します。

   ![ファースト ステップ ガイド](./media/site-recovery-capacity-planner/getting-started-2.png)

3. **Workload Qualification** ワークシートに、必要な情報を入力します。 マークが付いているフィールドはすべて入力する必要があります。

   a. **[Processor Cores]\(プロセッサ コア\)** には、ソース サーバー上のコアの総数を指定します。

   b. **[Memory allocation (in MBs)]\(メモリの割り当て (MB)\)** には、ソース サーバーの RAM サイズを指定します。

   c. **[Number of NICs]\(NIC 数\)** には、ソース サーバー上のネットワーク アダプターの数を指定します。

   d. **[Total Storage (in GB)]\(ストレージ合計 (GB)\)** には、VM ストレージの合計サイズを指定します。 たとえば、ソース サーバーにそれぞれ 500 GB のディスクが 3 つある場合、ストレージの合計サイズは 1,500 GB になります。

   e. **[Number of disks attached (接続されているディスクの数)]** には、ソース サーバーのディスクの合計数を指定します。

   f. **[Disk capacity utilization (%)]\(ディスク容量の使用率 (%)\)** には、平均使用率を指定します。

   g. **[Daily data change rate (%)]\(日次データ変化率 (%)\)** には、ソース サーバーの 1 日のデータ変更率を指定します。

   h. **[Mapping Azure VM size]\(マッピングする Azure VM サイズ\)** には、マップする Azure VM のサイズを入力します。 これを手動で行わない場合は、**[Compute IaaS VMs]\(IaaS VM を計算する\)** を選択します。 手動で設定を入力した後、**[Compute IaaS VMs]\(IaaS VM を計算する\)** を選択すると、手動で入力した設定が上書きされることがあります。 計算プロセスによって、最も一致する Azure VM のサイズが自動的に特定されます。

   ![Workload Qualification ワークシート](./media/site-recovery-capacity-planner/workload-qualification.png)

4. **[Compute IaaS VMs]\(IaaS VM を計算する\)** を選択すると、以下が実行されます。

   * 必須の入力が検証されます。
   * IOPS が計算され、Azure へのレプリケーション対象である各 VM に最も一致する Azure VM のサイズが提案されます。 Azure VM の適切なサイズを検出できない場合、エラーが表示されます。 たとえば、接続されているディスク数が 65 の場合、Azure VM の最大サイズが 64 であるため、エラーが表示されます。
   * Azure VM に使用できるストレージ アカウントが推奨されます。
   * ワークロードに必要な Standard Storage アカウントおよび Premium Storage アカウントの総数が算出されます。 下にスクロールして、ソース サーバーに使用できる Azure のストレージ タイプとストレージ アカウントを表示します。
   * VM に割り当てられた必要なストレージの種類 (Standard または Premium) に基づき、残りのテーブルが完了およびソートされます。 Azure のバックアップ要件を満たすすべての VM では、列 **[対象 VM]** に **[はい]** が表示されます。 VM を Azure にバックアップできない場合は、エラーが表示されます。

AA から AE の列が出力され、各 VM の情報が示されます。

![出力列 AA から AE](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>例
例として、テーブルに示した値を持つ 6 つの VM に最も一致する Azure VM および Azure ストレージ要件が、ツールによって計算されて割り当てられます。

![Workload Qualification の割り当て](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* 出力例では、以下に注意してください。

  * 最初の列は、VM、ディスク、および変更頻度の検証列です。
  * 5 つの VM に Standard Storage アカウントが 2 つと Premium Storage アカウントが 1 つ必要でした。
  * 1 つ以上のディスクが 1 TB 以上であるために、VM3 は保護の対象とはなりません。
  * VM1 と VM2 は、最初の Standard Storage アカウントを使用できます。
  * VM4 は、2 つ目の Standard Storage アカウントを使用できます。
  * VM5 と VM6 には、Premium Storage アカウントが必要で、いずれも単一のアカウントを使用できます。

    > [!NOTE]
    > Standard Storage および Premium Storage の IOPS は、ディスク レベルではなく VM レベルで計算されます。 Standard VM はディスクあたり最大 500 IOPS を処理できます。 ディスクの IOPS が 500 より大きい場合は、Premium Storage が必要になります。 ディスクの IOPS が 500 を超えても VM ディスクの合計の IOPS がサポートされる Standard の Azure VM の制限内にある場合、プランナーは DS または GS シリーズではなく Standard VM を選択します (Azure VM の制限は、VM サイズ、ディスク数、アダプター数、CPU、メモリです)。適切な DS または GS シリーズ VM を使用して Azure サイズ セルのマッピングを手動で更新する必要があります。


すべての情報を入力したら、**[Submit data to the planner tool]\(データをプランナー ツールに送信\)** を選択して、Capacity Planner を開きます。 ワークロードが保護対象かどうかは強調表示によって示されます。

### <a name="submit-data-in-capacity-planner"></a>Capacity Planner でのデータの送信
1. **Capacity Planner** ワークシートを開くと、指定した設定に基づいてデータが入力されます。 **[Infra inputs source]\(Infra 入力ソース\)** セルに "Workload" という語が表示され、この入力が **Workload Qualification** ワークシートのものであることを示します。

2. 変更する場合、**Workload Qualification** ワークシートを変更する必要があります。 その後、**[Submit data to the planner tool]\(データをプランナー ツールに送信\)** をもう一度選択します。

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

## <a name="next-steps"></a>次の手順
Capacity Planner ツールを[実行する方法について説明します](site-recovery-capacity-planning-for-hyper-v-replication.md)。
