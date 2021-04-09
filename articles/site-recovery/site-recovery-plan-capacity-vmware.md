---
title: Azure Site Recovery を使用する VMware ディザスター リカバリーの容量を計画する
description: この記事は、Azure Site Recovery を用いた Azure への VMware VM のディザスター リカバリーを設定するときに、容量とスケーリングを計画するのに役立ちます。
author: nsoneji
manager: garavd
ms.service: site-recovery
ms.date: 4/9/2019
ms.topic: conceptual
ms.author: ramamill
ms.openlocfilehash: 4b86d0c189bcf0687a703f2338188df2090feaf0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368028"
---
# <a name="plan-capacity-and-scaling-for-vmware-disaster-recovery-to-azure"></a>Azure への VMware ディザスター リカバリーの容量とスケーリングを計画する

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用してオンプレミスの VMware VM と物理サーバーを Azure にレプリケートするときの、容量とスケーリングの計画について説明します。

## <a name="how-do-i-start-capacity-planning"></a>容量計画はどのように開始すればよいか

Azure Site Recovery インフラストラクチャの要件を理解するには、VMware のレプリケーションに対して [Azure Site Recovery Deployment Planner](./site-recovery-deployment-planner.md) を実行することで、レプリケーション環境に関する情報を収集します。 詳しくは、「[VMware から Azure 用の Azure Site Recovery Deployment Planner について](site-recovery-deployment-planner.md)」をご覧ください。 

Site Recovery Deployment Planner では、互換および非互換の VM、VM あたりのディスク数、およびディスクあたりのデータ チャーンに関するレポートが提供されます。 また、このツールでは、ターゲットの RPO を満たすためにネットワーク帯域幅の要件を要約し、レプリケーションとテスト フェールオーバーを正常に実行するために必要な Azure インフラストラクチャについても要約されます。

## <a name="capacity-considerations"></a>容量に関する考慮事項

コンポーネント | 詳細
--- | ---
**レプリケーション** | **日次変更率の上限**: 保護されたマシンが使用できるプロセス サーバーは 1 つだけです。 1 台のプロセス サーバーでは、1 日あたり最大 2 TB の変化率を処理できます。 したがって、保護されたマシンでサポートされるデータの日次変化率の上限は 2 TB になります。<br /><br /> **最大スループット**: レプリケートされたマシンは、Azure の 1 つのストレージ アカウントに属することができます。 Standard Azure Storage アカウントでは、1 秒間に最大 20,000 要求を処理できます。 ソース マシン全体の 1 秒あたりの入出力操作 (IOPS) 数を、20,000 以下に制限することをお勧めします。 たとえば、5 個のディスクが搭載されたソース マシンで、各ディスクから 120 IOPS (8K サイズ) が生成される場合、Azure 内のディスクごとの IOPS 制限は 500 になります。 (必要なストレージ アカウントの数は、ソース マシンの合計 IOPS を 20000 で割った値です。)
**構成サーバー** | 構成サーバーは、保護されたマシンで実行されているすべてのワークロードの日次変更率容量を処理できる必要があります。 構成マシンには、Azure Storage にデータを継続的にレプリケートできる十分な帯域幅が必要です。<br /><br /> ベスト プラクティスとして、保護対象のマシンと同じネットワークおよび LAN セグメントに構成サーバーを配置します。 構成サーバーを別のネットワークに配置することもできますが、保護対象のマシンにはレイヤー 3 のネットワーク可視性が必要です。<br /><br /> 構成サーバーのサイズの推奨事項を次のセクションの表に示します。
**プロセス サーバー** | 最初のプロセス サーバーは、構成サーバーに既定でインストールされます。 追加のプロセス サーバーをデプロイして環境を拡張できます。 <br /><br /> プロセス サーバーは、保護対象のマシンからレプリケーション データを受け取ります。 プロセス サーバーでは、キャッシュ、圧縮、暗号化によってデータが最適化されます。 その後、プロセス サーバーはデータを Azure に送信します。 プロセス サーバー マシンには、これらのタスクを実行できる十分なリソースが必要です。<br /><br /> プロセス サーバーは、ディスクベースのキャッシュを使用します。 ネットワークのボトルネックや障害が発生した場合に、格納されているデータの変更を処理できるように、600 GB 以上のキャッシュ ディスクを別に使用します。

## <a name="size-recommendations-for-the-configuration-server-and-inbuilt-process-server"></a>構成サーバーと組み込みのプロセス サーバーのサイズの推奨事項

ワークロードを保護するために、組み込みのプロセス サーバーを使用する構成サーバーでは、次の構成に基づいて、最大 200 台まで仮想マシンを処理できます。

CPU | メモリ | キャッシュ ディスク サイズ | データ変化率 | 保護対象コンピューター
--- | --- | --- | --- | ---
8 vCPU (2 ソケット * 4 コア \@ 2.5 GHz) | 16 GB | 300 GB | 500 GB 以下 | 100 台未満のマシンのレプリケートに使用する。
12 vCPU (2 ソケット * 6 コア \@ 2.5 GHz) | 18 GB | 600 GB | 501 GB ～ 1 TB | 100 ～ 150 台のマシンのレプリケートに使用する。
16 vCPU (2 ソケット * 8 コア \@ 2.5 GHz) | 32 GB | 1 TB (テラバイト) | 1 TB ～ 2 TB | 151 ～ 200 台のマシンのレプリケートに使用する。
[OVF テンプレート](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)を使用して別の構成サーバーをデプロイする。 | | | | レプリケートするマシンが 200 台より多い場合は、新しい構成サーバーをデプロイする。
別の[プロセス サーバー](vmware-azure-set-up-process-server-scale.md#download-installation-file)をデプロイする。 | | | > 2 TB| データ全体の日次変化率が 2 TB を超える場合は、新しいスケールアウト プロセス サーバーをデプロイする。

これらの構成は次のようなものです。

* 各ソース マシンは、それぞれ 100 GB のディスクを 3 個備えています。
* キャッシュ ディスクの測定には、RAID 10 で 10,000 RPM の Shared Access Signature ドライブが 8 個というベンチマーク用ストレージを使用しました。

## <a name="size-recommendations-for-the-process-server"></a>プロセス サーバーのサイズの推奨事項

プロセス サーバーは、Azure Site Recovery でデータのレプリケーションを処理するコンポーネントです。 日次変更率が 2 TB を超える場合は、レプリケーションの負荷を処理するスケールアウト プロセス サーバーを追加する必要があります。 スケールアウトするには、次の方法があります。

* [OVF テンプレート](vmware-azure-deploy-configuration-server.md#deploy-a-configuration-server-through-an-ova-template)を使ってデプロイすることで、構成サーバーの数を増やします。 たとえば、2 つの構成サーバーを使用すると最大 400 台のマシンを保護できます。
* [スケールアウト プロセス サーバー](vmware-azure-set-up-process-server-scale.md#download-installation-file)を追加します。 スケールアウト プロセス サーバーを使用して、構成サーバーの代わりに (または構成サーバーに加えて) レプリケーション トラフィックを処理します。

次の表で、このシナリオについて説明します。

* スケールアウト プロセス サーバーを設定します。
* スケールアウト プロセス サーバーを使用するように、保護対象の仮想マシンを構成します。
* 各保護対象ソース マシンは、それぞれ 100 GB のディスクを 3 個備えています。

追加のプロセス サーバー | キャッシュ ディスク サイズ | データ変化率 | 保護対象コンピューター
--- | --- | --- | ---
4 vCPU (2 ソケット * 2 コア \@ 2.5 GHz)、8 GB メモリ | 300 GB | 250 GB 以下 | 85 台以下のマシンのレプリケートに使用する。
8 vCPU (2 ソケット * 4 コア \@ 2.5 GHz)、12 GB メモリ | 600 GB | 251 GB ～ 1 TB | 86 ～ 150 台のマシンのレプリケートに使用する。
12 vCPU (2 ソケット * 6 コア \@ 2.5 GHz)、24 GB メモリ | 1 TB (テラバイト) | 1 TB ～ 2 TB | 151 ～ 225 台のマシンのレプリケートに使用する。

サーバーのスケーリングの方法は、スケールアップ モデルとスケールアウト モデルのどちらを選択するかによって異なります。 スケールアップするには、少数のハイエンド構成サーバーとプロセス サーバーをデプロイします。 スケールアウトするには、リソースが少ないサーバーを多数デプロイします。 たとえば、データ全体の日次変化率が 1.5 TB の 200 台のマシンを保護する必要がある場合、次のいずれかを行います。

* プロセス サーバーを 1 台設定します (16 vCPU、24 GB RAM)。
* プロセス サーバーを 2 台設定します (2 x 8 vCPU、2* 12 GB RAM)。

## <a name="control-network-bandwidth"></a>ネットワーク帯域幅の管理

[Site Recovery Deployment Planner](site-recovery-deployment-planner.md) を使用して、レプリケーションに必要な帯域幅 (初期レプリケーションと差分) を計算した後、次の 2 つのオプションを使用してレプリケーションに使用する帯域幅の量を制御できます。

* **帯域幅を調整する**: Azure にレプリケートされる VMware トラフィックは、特定のプロセス サーバーを経由します。 プロセス サーバーとして実行されているマシンの帯域幅を調整できます。
* **帯域幅に影響を与える**: レジストリ キーをいくつか使用して、レプリケーションに使用される帯域幅に影響を与えることができます。
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM** レジストリ値は、ディスクのデータ転送 (初期レプリケーションまたは差分レプリケーション) に使用されるスレッドの数を指定します。 値を大きくすると、レプリケーションに使用されるネットワーク帯域幅が増加します。
  * **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\DownloadThreadsPerVM** レジストリ値は、フェールバック時にデータ転送に使用されるスレッドの数を指定します。

### <a name="throttle-bandwidth"></a>帯域幅のスロットル

1. プロセス サーバーとして使用するマシンで、Azure Backup MMC スナップインを開きます。 既定では、Backup のショートカットが、デスクトップまたはフォルダー C:\Program Files\Microsoft Azure Recovery Services Agent\bin で使用できます。
2. スナップインで **[プロパティの変更]** を選択します。

    ![プロパティを変更する Azure Backup MMC スナップイン オプションのスクリーンショット](./media/site-recovery-vmware-to-azure/throttle1.png)
3. **[調整]** タブで、 **[バックアップ操作用のインターネット使用帯域幅の調整を有効にする]** をオンにします。 勤務時間用と勤務時間外用の制限値を設定します。 有効な範囲は、512 Kbps から 1,023 Mbps です。

    ![Azure Backup プロパティ ダイアログ ボックスのスクリーンショット](./media/site-recovery-vmware-to-azure/throttle2.png)

[Set-OBMachineSetting](/previous-versions/windows/powershell-scripting/hh770409(v=wps.640)) コマンドレットを使用して、スロットルを設定することもできます。 次に例を示します。

```azurepowershell-interactive
$mon = [System.DayOfWeek]::Monday
$tue = [System.DayOfWeek]::Tuesday
Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth  (512*1024) -NonWorkHourBandwidth (2048*1024)
```

**Set-OBMachineSetting -NoThrottle** は、スロットルが不要であることを示します。

### <a name="alter-the-network-bandwidth-for-a-vm"></a>VM のネットワーク帯域幅を変更する

1. VM のレジストリで、**HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication** に移動します。
   * レプリケートするディスクで帯域幅のトラフィックを変更するには、**UploadThreadsPerVM** の値を変更します。 存在しない場合はキーを作成します。
   * Azure からのフェールバックのトラフィックの帯域幅を変更するには、**DownloadThreadsPerVM** の値を変更します。
2. 各キーの既定値は **4** です。 "プロビジョニング超過" 状態のネットワークの場合、このレジストリ キーを既定値から変更する必要があります。 使用できる最大値は **32** です。 トラフィックを監視して値を最適化できます。

## <a name="set-up-the-site-recovery-infrastructure-to-protect-more-than-500-vms"></a>500 台を超える VM を保護するために Site Recovery インフラストラクチャを設定する

Site Recovery インフラストラクチャを設定する前に、その環境にアクセスして次の要因を測定する必要があります。互換性のある仮想マシン、データの日次変化率、達成する RPO に必要なネットワーク帯域幅、必要な Site Recovery コンポーネント、初期レプリケーションの完了にかかる時間などです。 次の手順を実行して、必要な情報を収集します。

1. これらのパラメーターを測定するには、環境で Site Recovery Deployment Planner を実行します。 役に立つガイドラインについては、「[VMware から Azure 用の Azure Site Recovery Deployment Planner について](site-recovery-deployment-planner.md)」をご覧ください。
2. [構成サーバーのサイズの推奨事項](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-configuration-server-and-inbuilt-process-server)を満たす構成サーバーをデプロイします。 運用ワークロードが仮想マシン 650 台を超える場合、別の構成サーバーをデプロイします。
3. 測定されたデータの日次変化率に基づき、[サイズのガイドライン](site-recovery-plan-capacity-vmware.md#size-recommendations-for-the-process-server)を参考にして、[スケールアウト プロセス サーバー](vmware-azure-set-up-process-server-scale.md#download-installation-file)をデプロイします。
4. ディスクの仮想マシン用にデータの変化率が 2 MBps を超えることが予期される場合は、Premium マネージド ディスクを使用してください。 Site Recovery Deployment Planner は、特定の期間について実行されます。 それ以外の時間帯のデータ変化率のピークは、レポートにキャプチャされていない可能性があります。
5. 実現する RPO に基づいて、[ネットワーク帯域幅を設定](site-recovery-plan-capacity-vmware.md#control-network-bandwidth)します。
6. インフラストラクチャをセットアップするときに、ワークロードのディザスター リカバリーを有効にします。 方法については、「[Azure への VMware レプリケーションのソース環境を設定する](vmware-azure-set-up-source.md)」をご覧ください。

## <a name="deploy-additional-process-servers"></a>追加のプロセス サーバーをデプロイする

ソース マシンが 200 台を超えるデプロイ、または合計日次変化率が 2 TB を超えるデプロイでスケールアウトする場合は、トラフィック ボリュームに対応するためにプロセス サーバーを追加する必要があります。 9.24 バージョンで製品を拡張し、スケールアウト プロセス サーバーをセットアップするときの[プロセス サーバーのアラート](vmware-physical-azure-monitor-process-server.md#process-server-alerts)を備えました。 [プロセス サーバーをセットアップして、](vmware-azure-set-up-process-server-scale.md)新しいソース マシンを保護するかまたは[負荷を分散させます](vmware-azure-manage-process-server.md#move-vms-to-balance-the-process-server-load)。

### <a name="migrate-machines-to-use-the-new-process-server"></a>マシンを移行して新しいプロセス サーバーを使用する

1. **[設定]**  >  **[Site Recovery servers]\(Site Recovery サーバー\)** を選択します。 構成サーバーを選択して、**[プロセス サーバー]** を展開します。

    ![プロセス サーバー ダイアログ ボックスのスクリーンショット](./media/site-recovery-vmware-to-azure/migrate-ps2.png)
2. 現在使用中のプロセス サーバーを右クリックし、**[切り替え]** を選択します。

    ![構成サーバー ダイアログ ボックスのスクリーンショット](./media/site-recovery-vmware-to-azure/migrate-ps3.png)
3. **[ターゲット プロセス サーバーの選択]** で、使用する新しいプロセス サーバーを選択します。 サーバーが処理する仮想マシンを選択します。 サーバーに関する情報を取得するには、情報アイコンを選択します。 負荷の決定に役立つように、選択された各仮想マシンを新しいプロセス サーバーにレプリケートするために必要な平均容量が表示されます。 チェック マークを選択して、新しいプロセス サーバーへのレプリケーションを開始します。

## <a name="deploy-additional-master-target-servers"></a>追加のマスター ターゲット サーバーをデプロイする

次のシナリオでは、複数のマスター ターゲット サーバーが必要です。

*   Linux ベースの仮想マシンを保護する。
*   構成サーバーで使用可能なマスター ターゲット サーバーから VM のデータストアにアクセスできない。
*   マスター ターゲット サーバー上のディスクの総数 (サーバー上のローカル ディスクの数と、保護対象ディスクの数を加えたもの) が、60 ディスクを超えている。

Linux ベースの仮想マシン用にマスター ターゲット サーバーを追加する方法については、「[フェールバック用の Linux マスター ターゲット サーバーをインストールする](vmware-azure-install-linux-master-target.md)」をご覧ください。

Windows ベースの仮想マシン用にマスター ターゲット サーバーを追加するには:

1. **[Recovery Services コンテナー]**  >  **[Site Recovery インフラストラクチャ]**  >  **[構成サーバー]** の順に移動します。
2. 必要な構成サーバーを選択し、**[マスター ターゲット サーバー]** を選択します。

    ![マスター ターゲット サーバーの追加ボタンを示すスクリーンショット](media/site-recovery-plan-capacity-vmware/add-master-target-server.png)
3. 統合セットアップ ファイルをダウンロードし、VM 上でファイルを実行して、マスター ターゲット サーバーを設定します。
4. **[Install master target]\(マスター ターゲットをインストールする\)**  >  **[次へ]** の順に選択します。

    ![マスター ターゲットのインストール オプションの選択を示すスクリーンショット](media/site-recovery-plan-capacity-vmware/choose-MT.PNG)
5. 既定のインストール場所を選択し、**[インストール]** を選択します。

     ![既定のインストール場所を示すスクリーンショット](media/site-recovery-plan-capacity-vmware/MT-installation.PNG)
6. 構成サーバーにマスター ターゲットを登録するには、**[Proceed to Configuration]\(構成に進む\)** を選択します。

    ![構成を続けるボタンを示すスクリーンショット](media/site-recovery-plan-capacity-vmware/MT-proceed-configuration.PNG)
7. 構成サーバーの IP アドレスとパスフレーズを入力します。 パスフレーズの生成方法については、「[構成サーバーのパスフレーズを生成する](vmware-azure-manage-configuration-server.md#generate-configuration-server-passphrase)」をご覧ください。 

    ![構成サーバーの IP アドレスとパスフレーズを入力する場所を示すスクリーンショット](media/site-recovery-plan-capacity-vmware/cs-ip-passphrase.PNG)
8. **[登録]** を選択します。 登録が終わったら、**[完了]** を選択します。

登録が正常に終わると、Azure portal の **[Recovery Services コンテナー]**  >  **[Site Recovery Infrastructure]\(Site Recovery インフラストラクチャ\)**  >  **[構成サーバー]** で、構成サーバーのマスター ターゲット サーバーの一覧に、サーバーが表示されます。

 > [!NOTE]
 > 最新バージョンの [Windows 用マスター ターゲット サーバー統合セットアップ ファイル](https://aka.ms/latestmobsvc)をダウンロードしてください。

## <a name="next-steps"></a>次のステップ

[Site Recovery Deployment Planner](https://aka.ms/asr-deployment-planner) をダウンロードして実行します。