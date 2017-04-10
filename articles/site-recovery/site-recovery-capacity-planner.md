---
title: "Azure でのレプリケーション容量の見積 | Microsoft Docs"
description: "Azure Site Recovery を使用してレプリケーションを行う場合は、この記事に従って容量を見積もってください"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 0a1cd8eb-a8f7-4228-ab84-9449e0b2887b
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/06/2017
ms.author: nisoneji
translationtype: Human Translation
ms.sourcegitcommit: d4183b73bcb0441c9ad5f12e7a3a1e4d8e31f4b5
ms.openlocfilehash: 243fbea75c4ba9b280c65a378d6f2d069add1098
ms.lasthandoff: 03/01/2017


---
# <a name="plan-capacity-for-protecting-virtual-machines-and-physical-servers-in-azure-site-recovery"></a>Azure Site Recovery で仮想マシンおよび物理サーバーを保護するための容量計画

Azure Site Recovery Capacity Planner ツールを使用すると、Azure Site Recovery で Hyper-V VM、VMware VM、Windows および Linux 物理サーバーをレプリケートするために必要な容量を算出できます。

Site Recovery Capacity Planner では、ソース環境やワークロードを分析できるほか、必要な帯域幅、ソースの場所に必要なサーバー リソース、およびターゲットの場所に必要な (仮想マシンやストレージなどの) リソースを評価することができます。

このツールは、いくつかのモードで実行できます。

* **クイック プランニング**: VM、ディスク、ストレージ、変更率の数値の平均に基づいてネットワークとサーバーを予測するには、このモードでツールを実行します。
* **詳細なプランニング**: このモードでツールを実行し、VM レベルで各ワークロードの詳細を提供します。 VM の互換性が分析され、ネットワークおよびサーバーが予測されます。

## <a name="before-you-start"></a>開始する前に


1. VM、VM あたりのディスク数、ディスクあたりのストレージなど、環境の情報を収集します。
2. レプリケートされたデータの&1; 日の変更 (チャーン) 率を識別します。 これを行うには、次の手順を実行します。

   * Hyper-V の VM をレプリケートする場合、[Hyper-V 容量計画ツール](https://www.microsoft.com/download/details.aspx?id=39057) をダウンロードして変更率を得ます。 [こちら](site-recovery-capacity-planning-for-hyper-v-replication.md) を参照してください。 このツールは&1; 週間に渡って実行して平均をキャプチャすることをお勧めします。
   * VMware 仮想マシンをレプリケートする場合、[Azure Site Recovery Deployment Planner](./site-recovery-deployment-planner.md) を使用してチャーン率を算出します。
   * 物理サーバーをレプリケートする場合は、手動で評価を行う必要があります。

## <a name="run-the-quick-planner"></a>クイック プランナーの実行
1. [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) ツールをダウンロードして起動します。 マクロを実行する必要があるので、求められたら編集の有効化とコンテンツの有効化を選択します。
2. **[Select a planner type (プランナーの種類を選択する)]** リスト ボックスから **[Quick Planner]** を選択します。

   ![使用の開始](./media/site-recovery-capacity-planner/getting-started.png)
3. **Capacity Planner** ワークシートに、必要な情報を入力します。 以下のスクリーンショットの赤の丸がついているすべてのフィールドに、入力を行う必要があります。

   * **[シナリオの選択]** で **[Hyper-V から Azure]** または **[VMware/物理から Azure]** を選択します。
   * **[Average daily data change rate (%) (1 日の平均データ変更率 (%))]** に [Hyper-V 容量計画ツール](site-recovery-capacity-planning-for-hyper-v-replication.md)または [Azure Site Recovery Deployment Planner](./site-recovery-deployment-planner.md) を使用して収集した情報を入力します。  
   * **[圧縮]** は、Azure に VMware の VM または物理サーバーをレプリケートする場合の圧縮のみに該当します。 30% 以上を見積もりますが、必要に応じて設定を変更することができます。 Hyper-V の VM を Azure の圧縮にレプリケートする場合は、Riverbed などのサードパーティのアプライアンスを使用できます。
   * **[Retention Inputs (保持の入力)]** には、レプリカを保持する期間を指定します。 VMware または物理サーバーをレプリケートする場合は、日数で値を入力します。 Hyper-V をレプリケートする場合は、時間単位で時間を入力します。
   * **[Number of hours in which initial replication for the batch of virtual machines should complete (仮想マシンのバッチの初期レプリケーションを完了させる時間)]** と **[Number of virtual machines per initial replication batch (初期レプリケーションのバッチあたりの仮想マシンの数)]** には、初期レプリケーションの要件の計算に使用した設定を入力します。  Site Recovery をデプロイする際には、初期データ セット全体をアップロードする必要があります。

   ![入力](./media/site-recovery-capacity-planner/inputs.png)
4. ソース環境の値を入力した後には、以下などが出力されます。

   * **[デルタ レプリケーションに必要な帯域幅** (MB/秒)]。 1 日の平均データ変更率に基づいてデルタ レプリケーションのネットワーク帯域幅が計算されます。
   * **[初期レプリケーションに必要な帯域幅** (MB/秒)]。 入力した初期レプリケーションの値に基づいて初期レプリケーションのネットワーク帯域幅が計算されます。
   * **[必要なストレージ (単位: GB)]** は Azure で必要なストレージの合計です。
   * **[Standard Storage アカウントの IOPS 合計]** は、Standard Storage アカウントの合計での IOPS が 8K の単位サイズで計算されます。  Quick Planner の場合は、この数値はすべてのソース VM ディスクおよび&1; 日のデータ変更率に基づいて計算されます。 Detailed Planner の場合、この数値は、標準の Azure VM にマップされている VM 数の合計およびそれらの VM でのデータ変更率に基づいて計算されます。
   * **[Standard Storage アカウントの数]** は、VM の保護に必要な Standard Storage アカウントの総数を示します。 Standard Storage アカウントの場合は、Standard Storage のすべての VM で最大 20000 の IOPS まで対応でき、またディスクごとでは最大 500 の IOPS まで対応できます。
   * **[必要な BLOB ディスク数]** は、Azure ストレージに作成されるディスクの数を示します。
   * **[必要な Premium Storage アカウント数]** は、VM の保護に必要な Premium Storage アカウントの総数を示します。 IOPS が多い (20000 を超える) ソース VM には、Premium Storage アカウントが必要です。 Premium Storage アカウントでは、最大 80000 の IOPS に対応できます。
   * **[Premium Storage の IOPS 合計]** は、Premium Storage アカウントの合計での IOPS が 256K の単位サイズで計算されます。  Quick Planner の場合は、この数値はすべてのソース VM ディスクおよび&1; 日のデータ変更率に基づいて計算されます。 Detailed Planner の場合、この数値は、Premium の Azure VM (DS および GS シリーズ) にマップされている VM 数の合計およびそれらの VM でのデータ変更率に基づいて計算されます。
   * **[Number of configuration servers required (必要な構成サーバーの数)]** は、デプロイに必要な構成サーバーの数を示します。
   * **[Number of additional process servers required (必要なその他のプロセス サーバーの数)]** は、既定で構成サーバーで実行しているプロセス サーバーの他に追加のプロセス サーバーが必要かどうかを示しています。
   * **[ソースに&100;% の追加ストレージ]** は、ソースの場所に追加ストレージが必要かどうかを示しています。

   ![出力](./media/site-recovery-capacity-planner/output.png)

## <a name="run-the-detailed-planner"></a>詳細なプランナーの実行

1. [Azure Site Recovery Capacity Planner](http://aka.ms/asr-capacity-planner-excel) ツールをダウンロードして起動します。 マクロを実行する必要があるので、求められたら編集の有効化とコンテンツの有効化を選択します。
2. **[Select a planner type (プランナーの種類を選択する]** リスト ボックスから **[Detailed Planner (詳細なプランナー)]** を選択します。

   ![Getting Started (概要)](./media/site-recovery-capacity-planner/getting-started-2.png)
3. **Workload Qualification** ワークシートに、必要な情報を入力します。 マークが付いているフィールドはすべて入力する必要があります。

   * **[プロセッサ コア]** には、ソース サーバー上のコアの総数を指定します。
   * **[Memory allocation in MB (メモリの割り当て (MB))]** には、ソース サーバーの RAM サイズを指定します。
   * **[NIC 数]** には、ソース サーバー上のネットワーク アダプターの数を指定します。
   * **[ストレージ合計 (GB)]** には、VM ストレージの合計サイズを指定します。 たとえば、ソース サーバーにそれぞれ 500 GB のディスクが 3 つある場合、ストレージの合計サイズは 1500 GB になります。
   * **[Number of disks attached (接続されているディスクの数)]** には、ソース サーバーのディスクの合計数を指定します。
   * **[Disk capacity utilization (ディスク容量の使用率)]** には、平均使用率を指定します。
   * **[日次変更率 (%)]** には、ソース サーバーの&1; 日のデータ変更率を指定します。
   * **[Mapping Azure size (マッピングする Azure サイズ)]** には、マップする Azure VM のサイズを入力します。 **[Compute IaaS VMs (IaaS VM を計算する)]** をクリックすると、この操作を手動で行わずに済みます。手動で設定を入力し、[Compute IaaS VMs (IaaS VM を計算する)] をクリックすると、最も一致する Azure VM のサイズが計算プロセスによって自動的に特定されるため、手動の設定が上書きされることがあります。

   ![Workload Qualification ](./media/site-recovery-capacity-planner/workload-qualification.png)
4. **[IaaS VM を計算する]** をクリックすると、以下が実行されます。

   * 必須の入力が検証されます。
   * IOPS が計算され、Azure へのレプリケーション対象である各 VM に最も一致する Azure VM のサイズが提案されます。 Azure VM の適切なサイズを検出できない場合、エラーが発行されます。 たとえば、接続されているディスク数が 65 の場合、Azure VM の最大サイズは 64 であるため、エラーが発行されます。
   * Azure VM に使用できるストレージ アカウントが推奨されます。
   * ワークロードに必要な Standard Storage アカウントおよび Premium Storage アカウントの総数が算出されます。 下にスクロールして、ソース サーバーに使用できる Azure のストレージ タイプとストレージ アカウントを表示します。
   * VM に割り当てられた (Standard または Premium の) 必要なストレージの種類に基づき、残りのテーブルが完了およびソートされます。 Azure のバックアップ要件を満たすすべての VM では、列 **[対象 VM]** に **[はい]** が表示されます。 VM を Azure にバックアップできない場合は、エラーが表示されます。

AA から AE の列が出力され、各 VM の情報が示されます。

![Workload Qualification ](./media/site-recovery-capacity-planner/workload-qualification-2.png)

### <a name="example"></a>例
例として、テーブルに示した値を持つ&6; つの VM に最も一致する Azure VM および Azure ストレージ要件が、ツールによって計算されて割り当てられます。

![Workload Qualification ](./media/site-recovery-capacity-planner/workload-qualification-3.png)

* 出力例では、以下に注意してください。

  * 最初の列は、VM、ディスク、およびチャーンの検証列です。
  * 5 つの VM に Standard Storage アカウントが&2; つと Premium Storage アカウントが&1; つ必要でした。
  * 1 つ以上のディスクが 1 TB 以上であるため、VM3 は保護の対象とはなりません。
  * VM1 と VM2 は、最初の Standard Storage アカウントを使用できます。
  * VM4 は、2 つ目の Standard Storage アカウントを使用できます。
  * VM5 と VM6 には、Premium Storage アカウントが必要で、いずれも単一のアカウントを使用できます。

    > [!NOTE]
    > Standard Storage および Premium Storage の IOPS は、ディスク レベルではなく VM レベルで計算されます。 標準的な仮想マシンはディスクあたり最大 500 IOPS を処理できます。 ディスクの IOPS が 500 より大きい場合は、Premium Storage が必要になります。 ただし、ディスクの IOPS が 500 を超えても VM ディスクの合計の IOPS がサポートされる標準 Azure VM の制限 (VM サイズ、ディスク数、アダプター数、CPU、メモリ) 内にある場合、プランナーは DS または GS シリーズではなく標準の VM を選択します。 適切な DS または GS シリーズ VM を使用して Azure サイズ セルのマッピングを手動で更新する必要があります。


すべての詳細を定義したら、**[Submit data to the planner tool (データをプランナー ツールに送信)]** をクリックして、**Capacity Planner** を開きます。ワークロードが保護対象かどうかは強調表示によって示されます。

### <a name="submit-data-in-the-capacity-planner"></a>Capacity Planner でのデータの送信
1. **Capacity Planner** ワークシートを開くと、指定した設定に基づいてデータが入力されます。 **Infra 入力ソース** セルに 'Workload' という語が表示され、この入力が **Workload Qualification** ワークシートのものであることを示します。
2. 変更する必要がある場合、**Workload Qualification** ワークシートを変更して、**[Submit data to the planner tool (データをプランナー ツールに送信)]** をもう一度クリックします。  

   ![Capacity Planner](./media/site-recovery-capacity-planner/capacity-planner.png)

