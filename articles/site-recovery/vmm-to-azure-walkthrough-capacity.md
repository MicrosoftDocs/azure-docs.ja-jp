---
title: "Azure Site Recovery を使用した Azure への Hyper-V VM レプリケーション (VMM あり) の容量とスケーリングを計画する | Microsoft Docs"
description: "この記事では、Azure Site Recovery で VMM クラウド内の Hyper-V VM を Azure にレプリケートする場合の容量とスケーリングを計画する方法について説明します"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 41c3c83e-6b1a-496a-8179-498c552ef0c7
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/23/2017
ms.author: raynew
ms.openlocfilehash: ab1dc21a829140f8cd2e57837d83a05b0d71bcdf
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="step-3-plan-capacity-and-scaling-for-hyper-v-with-vmm-to-azure-replication"></a>手順 3: Azure への Hyper-V レプリケーション (VM あり) の容量とスケーリングを計画する

[展開の前提条件](vmm-to-azure-walkthrough-prerequisites.md)を確認した後で、この記事に従って、[Azure Site Recovery](site-recovery-overview.md) を使って、System Center Virtual Machine Manager (VMM) クラウド内のオンプレミスの Hyper-V VM を Azure にレプリケートする場合の容量とスケーリングを計画します。

コメントはこの記事の末尾に投稿し、技術的な質問は [Azure Recovery Services フォーラム](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)に投稿してください。


## <a name="how-do-i-start-capacity-planning"></a>容量計画はどのように開始すればよいか


レプリケーション環境に関する情報を収集し、この記事で強調されている考慮事項と共にこのデータを使用して容量を計画します。


## <a name="gather-information"></a>情報を収集する

1. VM、VM あたりのディスク数、ディスクあたりのストレージなど、レプリケーション環境の情報を収集する。
2. レプリケートされたデータの 1 日の変更 (チャーン) 率を識別します。 [Hyper-V 容量計画ツール](https://www.microsoft.com/download/details.aspx?id=39057)をダウンロードして変更率を得ます。 このツールは 1 週間に渡って実行して平均をキャプチャすることをお勧めします。
 

## <a name="figure-out-capacity"></a>容量を調べる

集めた情報を基に [Site Recovery Capacity Planner ツール](http://aka.ms/asr-capacity-planner-excel)を実行して、ソース環境やワークロードを分析し、必要な帯域幅、ソースの場所に必要なサーバー リソース、およびターゲットの場所に必要な (仮想マシンやストレージなどの) リソースを評価します。 このツールは、いくつかのモードで実行できます。

- クイック プランニング: VM、ディスク、ストレージ、変更率の数値の平均に基づいてネットワークとサーバーを予測するには、このモードでツールを実行します。
- 詳細なプランニング: このモードでツールを実行し、VM レベルで各ワークロードの詳細を提供します。 VM の互換性が分析され、ネットワークおよびサーバーが予測されます。

ツールを実行します。

1. [ツール](http://aka.ms/asr-capacity-planner-excel)をダウンロードします。
2. クイック プランナーを実行するには、[こちらの手順](site-recovery-capacity-planner.md#run-the-quick-planner)に従い、シナリオとして **Hyper-V から Azure** を選びます。
3. 詳細なプランナーを実行するには、[こちらの手順](site-recovery-capacity-planner.md#run-the-detailed-planner)に従い、シナリオとして **Hyper-V から Azure** を選びます。

## <a name="control-network-bandwidth"></a>ネットワーク帯域幅の管理

必要な帯域幅を計算した後、レプリケーションに使われる帯域幅の量を制御するには 2 つのオプションがあります。

* **帯域幅を調整する**: Azure にレプリケートされる Hyper-V トラフィックは、特定の Hyper-V ホストを経由します。 このホスト サーバーの帯域幅をスロットルできます。
* **帯域幅に影響を与える**: レジストリ キーをいくつか使用して、レプリケーションに使用される帯域幅に影響を与えることができます。

### <a name="throttle-bandwidth"></a>帯域幅を調整する
1. Hyper-V ホスト サーバーの Microsoft Azure Backup MMC スナップインを開きます。 既定では、Microsoft Azure Backup のショートカットが、デスクトップか C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin にあります。
2. スナップインで **[プロパティの変更]**をクリックします。
3. **[調整]** タブで、**[バックアップ操作用のインターネット使用帯域幅の調整を有効にする]** チェック ボックスをオンにし、勤務時間用と勤務時間外用の制限値を設定します。 有効な範囲は、1 秒あたり 512 Kbps ～ 102 Mbps です。

    ![帯域幅を調整する](./media/vmm-to-azure-walkthrough-capacity/throttle2.png)

[Set-OBMachineSetting](https://technet.microsoft.com/library/hh770409.aspx) コマンドレットを使用して、スロットルを設定することもできます。 サンプルを次に示します。

    $mon = [System.DayOfWeek]::Monday
    $tue = [System.DayOfWeek]::Tuesday
    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)

**Set-OBMachineSetting -NoThrottle** は、スロットルが不要であることを示します。

### <a name="influence-network-bandwidth"></a>ネットワーク帯域幅に影響を与える
1. レジストリで、**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication** に移動します。
   * レプリケートするディスクで帯域幅のトラフィックに影響を与えるには、 **UploadThreadsPerVM**値を変更するか、このキーが存在しない場合は作成します。
   * Azure からのフェールバックのトラフィックの帯域幅に影響を与えるには、 **DownloadThreadsPerVM**値を変更します。
2. 既定値は 4 ですが、 "プロビジョニング超過" 状態のネットワークの場合、このレジストリ キーを既定値から変更する必要があります。 最大値は 32 です。 トラフィックを監視して値を最適化できます。

## <a name="next-steps"></a>次のステップ

「[手順 4: ネットワークを計画する](vmm-to-azure-walkthrough-network.md)」に進みます。
