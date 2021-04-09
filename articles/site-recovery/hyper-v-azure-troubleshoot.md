---
title: Azure Site Recovery を使用した Hyper-V のディザスター リカバリーのトラブルシューティング
description: Azure Site Recovery を使用した Hyper-V から Azure へのレプリケーションに関するディザスター リカバリーの問題をトラブルシューティングする方法について説明します。
services: site-recovery
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/14/2019
ms.author: sharrai
ms.openlocfilehash: c804e13029dcec42a43885cbf0d9b227b3d0338f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96750804"
---
# <a name="troubleshoot-hyper-v-to-azure-replication-and-failover"></a>Hyper-V から Azure へのレプリケーションおよびフェールオーバーをトラブルシューティングする

この記事では、[Azure Site Recovery](site-recovery-overview.md) を使用して、オンプレミスの Hyper-V VM を Azure にレプリケートするときに発生する可能性のある一般的な問題について説明します。

## <a name="enable-protection-issues"></a>保護を有効にしたときの問題

Hyper-V VM の保護を有効にしたときに問題が発生する場合は、次の推奨事項を確認してください。

1. Hyper-V ホストおよび VM が、すべての[要件および前提条件](hyper-v-azure-support-matrix.md)を満たしていることを確認します。
2. Hyper-V サーバーが System Center Virtual Machine Manager (VMM) クラウド内にある場合は、[VMM サーバー](hyper-v-prepare-on-premises-tutorial.md#prepare-vmm-optional)が準備されていることを確認します。
3. Hyper-V Virtual Machine Management サービスが Hyper-V ホスト上で実行されていることを確認します。
4. VM への Hyper-V-VMMS\Admin サインインに表示される問題を確認します。 このログは **Applications and Services Logs** > **Microsoft** > **Windows** にあります。
5. ゲスト VM 上で、WMI が有効になっており、アクセス可能であることを確認します。
   - 基本的な WMI テスト[について学習します](https://techcommunity.microsoft.com/t5/ask-the-performance-team/bg-p/AskPerf)。
   - WMI を[トラブルシューティングします](/windows/win32/wmisdk/wmi-troubleshooting)。
   - WMI スクリプトおよびサービスに関する問題を[トラブルシューティングします](/previous-versions/tn-archive/ff406382(v=msdn.10)#H22)。
6. ゲスト VM 上で、最新バージョンの Integration Services が実行されていることを確認します。
    - 最新バージョンを使用していることを[確認します](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services)。
    - Integration Services を最新の状態に[維持します](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date)。

### <a name="cannot-enable-protection-as-the-virtual-machine-is-not-highly-available-error-code-70094"></a>仮想マシンに高可用性が確保されていないため、保護を有効にできません (エラー コード 70094)

マシンのレプリケーションを有効にしているときに、マシンの高可用性が確保されていないためにレプリケーションを有効にできないというエラーが発生した場合は、次の手順を実行してください。

- VMM サーバー上で VMM サービスを再起動します。
- クラスターから仮想マシンを削除して、もう一度追加します。

### <a name="the-vss-writer-ntds-failed-with-status-11-and-writer-specific-failure-code-0x800423f4"></a>VSS ライター NTDS がステータス 11、ライター固有のエラー コード0x800423F4 で失敗しました

レプリケーションを有効にしようとすると、NTDS が失敗したためにレプリケーションの有効化に失敗したというメッセージが表示されることがあります。 この問題の考えられる原因の 1 つは、仮想マシンのオペレーティング システムが Windows Server 2012 であって、Windows Server 2012 R2 でないことです。 この問題を解決するには、下の手順を試してください。

- 4072650が適用された Windows Server R2 にアップグレードします。
- Hyper-v ホストも Windows 2016 以降であることを確認します。

## <a name="replication-issues"></a>レプリケーションの問題

初期および進行中のレプリケーションに関する問題を次のようにトラブルシューティングします。

1. [最新バージョン](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)の Site Recovery サービスを実行していることを確認します。
2. レプリケーションが一時停止されているかどうかを確認します。
   - Hyper-V マネージャー コンソールで VM の正常性状態を確認します。
   - それが重大である場合は、[VM] > **[レプリケーション]**  >  **[View Replication Health (レプリケーションの正常性の表示)]** を右クリックします。
   - レプリケーションが一時停止されている場合は、 **[Resume Replication (レプリケーションの再開)]** をクリックします。
3. 必要なサービスが実行されていることを確認します。 そうでない場合は、それらを再起動します。
    - VMM なしで Hyper-V をレプリケートしている場合は、次のサービスが Hyper-V ホスト上で実行されていることを確認します。
        - Virtual Machine Management サービス
        - Microsoft Azure Recovery Services Agent サービス
        - Microsoft Azure Site Recovery サービス
        - WMI Provider Host サービス
    - 環境内の VMM を使用してレプリケートしている場合は、次のサービスが実行されていることを確認します。
        - Hyper-V ホスト上で、Virtual Machine Management サービス、Microsoft Azure Recovery Services Agent、および WMI Provider Host サービスが実行されていることを確認します。
        - VMM サーバー上で、System Center Virtual Machine Manager サービスが実行されていることを確認します。
4. Hyper-V サーバーと Azure の間の接続を確認します。 接続を確認するには、Hyper V ホスト上でタスク マネージャーを開きます。 **[パフォーマンス]** タブで、 **[リソース モニターを開く]** をクリックします。 **[ネットワーク]** タブ > **[ネットワーク活動のプロセス]** で、cbengine.exe が大量 (数 MB) のデータをアクティブに送信しているかどうかを確認します。
5. Hyper-V ホストが Azure ストレージ BLOB の URL に接続できるかどうかを確認します。 ホストが接続できるかどうかを確認するには、**cbengine.exe** を選択して確認します。 ホストから Azure ストレージ BLOB への接続を確認するには、 **[TCP 接続]** を表示します。
6. 次の説明に従って、パフォーマンスの問題を確認します。
    
### <a name="performance-issues"></a>パフォーマンスの問題

ネットワーク帯域幅の制限によってレプリケーションが影響を受ける場合があります。 問題を次のようにトラブルシューティングします。

1. 環境内に帯域幅または調整の制約が存在するかどうかを[確認します](https://support.microsoft.com/help/3056159/how-to-manage-on-premises-to-azure-protection-network-bandwidth-usage)。
2. [Deployment Planner プロファイラー](hyper-v-deployment-planner-run.md)を実行します。
3. このプロファイラーを実行した後、[帯域幅](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input)と[ストレージ](hyper-v-deployment-planner-analyze-report.md#vm-storage-placement-recommendation)の推奨事項に従います。
4. [データ チャーンの制限](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)を確認します。 VM 上のデータ チャーンが高いことが確認できた場合は、次を実行します。
   - VM が再同期にマークされているかどうかを確認します。
   - [次の手順](https://techcommunity.microsoft.com/t5/virtualization/bg-p/Virtualization)に従って、チャーンのソースを調査します。
   - チャーンは、HRL ログ ファイルが使用可能なディスク容量の 50% を超えると発生する場合があります。 これが問題である場合は、問題が発生しているすべての VM により多くの記憶域スペースをプロビジョニングします。
   - レプリケーションが一時停止されていないことを確認します。 一時停止されていると、hrl ファイルへの変更の書き込みが続行されるため、そのサイズの増加につながる場合があります。
 

## <a name="critical-replication-state-issues"></a>レプリケーション状態の重大な問題

1. レプリケーションの正常性を確認するには、オンプレミスの Hyper-V マネージャー コンソールに接続し、VM を選択して正常性を確認します。

    ![レプリケーションの正常性](media/hyper-v-azure-troubleshoot/replication-health1.png)
    

2. **[View Replication Health (レプリケーションの正常性の表示)]** をクリックして詳細を表示します。

    - レプリケーションが一時停止されている場合は、[VM] > **[レプリケーション]**  >  **[Resume Replication (レプリケーションの再開)]** を右クリックします。
    - Site Recovery で構成された Hyper-V ホスト上の VM が同じクラスター内の別の Hyper-V ホストまたはスタンドアロン マシンに移行された場合、その VM のレプリケーションは影響を受けません。 新しい Hyper-V ホストがすべての前提条件を満たし、Site Recovery で構成されていることを確認します。

## <a name="app-consistent-snapshot-issues"></a>アプリ整合性スナップショットの問題

アプリ整合性スナップショットは、VM 内のアプリケーション データのポイントインタイム スナップショットです。 ボリューム シャドウ コピー サービス (VSS) によって、スナップショットが作成されたとき、VM 上のアプリが整合性のある状態に維持されます。  このセクションでは、発生する可能性のあるいくつかの一般的な問題について詳細に説明します。

### <a name="vss-failing-inside-the-vm"></a>VM 内で VSS が失敗する

1. 最新バージョンの Integration Services がインストールされ、実行されていることを確認します。  Hyper-V ホスト上の管理者特権での PowerShell プロンプトからコマンド **get-vm | select Name, State, IntegrationServicesState** を実行することによって、更新プログラムが入手可能かどうかを確認します。
2. VSS サービスが実行され、正常であることを確認します。
   - サービスを確認するには、ゲスト VM にサインインします。 次に、管理者のコマンド プロンプトを開き、次のコマンドを実行して、すべての VSS ライタが正常かどうかを確認します。
       - **Vssadmin list writers**
       - **Vssadmin list shadows**
       - **Vssadmin list providers**
   - 出力を確認します。 ライタが失敗の状態にある場合は、次を実行します。
       - VM 上のアプリケーション イベント ログに VSS 操作エラーがないかどうかを確認します。
   - 失敗したライタに関連付けられた次のサービスの再起動を試みます。
     - ボリューム シャドウ コピー
       - Azure Site Recovery VSS プロバイダー
   - これを行った後、数時間待って、アプリ整合性スナップショットが正常に生成されたかどうかを確認します。
   - 最後の手段として、VM の再起動を試みます。 これにより、無応答の状態にあるサービスが解決されることがあります。
3. VM 内にダイナミック ディスクが存在しないを確認します。 これは、アプリ整合性スナップショットでサポートされていません。 [ディスクの管理] (diskmgmt.msc) で確認できます。

    ![ダイナミック ディスク](media/hyper-v-azure-troubleshoot/dynamic-disk.png)
    
4. VM に接続された iSCSI ディスクが存在しないことを確認します。 これはサポートされていません。
5. Backup サービスが有効になっていることを確認します。 **[Hyper-V settings]\(Hyper-V の設定\)**  >  **[Integration Services]** で、サービスが有効になっていることを確認します。
6. VSS スナップショットを作成しているアプリと競合していないことを確認します。 複数のアプリが同時に VSS スナップショットを作成しようとしている場合は、競合が発生することがあります。 たとえば、レプリケーション ポリシーで Site Recovery がスナップショットを作成するようにスケジュールされているときに、Backup アプリが VSS スナップショットを作成している場合です。   
7. VM で高いチャーン レートが発生しているかどうかを確認します。
    - Hyper-V ホスト上のパフォーマンス カウンターを使用して、ゲスト VM での 1 日のデータ変更レートを測定できます。 データ変更レートを測定するには、次のカウンターを有効にします。 5 ～ 15 分間の VM ディスクにまたがるこの値のサンプルを集計して、VM のチャーンを取得します。
        - カテゴリ:"Hyper-V 仮想ストレージ デバイス"
        - カウンター:"Write Bytes / Sec"</br>
        - このデータ チャーン レートは、VM またはそのアプリがどれだけビジー状態にあるかに応じて、上昇するか、または高いレベルにとどまります。
        - ソース ディスクの平均データ チャーンは、Site Recovery の標準ストレージで 2 MB/秒です。 [詳細情報](hyper-v-deployment-planner-analyze-report.md#azure-site-recovery-limits)
    - さらに、[ストレージのスケーラビリティ ターゲットを確認](../storage/common/scalability-targets-standard-account.md)できます。
8. Linux ベースのサーバーを使用する場合は、アプリの整合性が有効になっていることを確認してください。 [詳細情報](./site-recovery-faq.md#replication)
9. [Deployment Planner](hyper-v-deployment-planner-run.md) を実行します。
10. [ネットワーク](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input)と[ストレージ](hyper-v-deployment-planner-analyze-report.md#recommendations-with-available-bandwidth-as-input)の推奨事項を確認してください。


### <a name="vss-failing-inside-the-hyper-v-host"></a>Hyper-V ホスト内で VSS が失敗する

1. VSS エラーのイベント ログと推奨事項を確認します。
    - Hyper-V ホスト サーバー上で、**Event Viewer** > **Applications and Services Logs** > **Microsoft** > **Windows** > **Hyper-V** > **Admin** にある Hyper-V Admin イベント ログを開きます。
    - アプリ整合性スナップショットの失敗を示す何らかのイベントが存在するかどうかを確認します。
    - 代表的なエラーは次のとおりです:"Hyper-V は仮想マシン 'XYZ' の VSS スナップショット セットを生成できませんでした:ライターで一過性ではないエラーが発生しました。 サービスが無応答である場合は、VSS サービスの再起動によって問題が解決される可能性があります。"

2. VM の VSS スナップショットを生成するには、その VM に Hyper-V Integration Services がインストールされており、Backup (VSS) Integration Services が有効になっていることを確認します。
    - ゲスト上で Integration Services VSS サービス/デーモンが実行され、 **[OK]** の状態になっていることを確認します。
    - これは、Hyper-V ホスト上の管理者特権での PowerShell セッションからコマンド **Get-VMIntegrationService -VMName\<VMName>-Name VSS** を使用して確認できます。この情報はまた、ゲスト VM にログインすることによっても取得できます。 [詳細については、こちらを参照してください](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services)。
    - VM 上の Backup/VSS Integration Services が実行され、正常な状態にあることを確認します。 そうでない場合は、次のサービス、および Hyper-V ホスト サーバー上の Hyper-V ボリューム シャドウ コピー リクエスター サービスを再起動します。

### <a name="common-errors"></a>一般的なエラー

**エラー コード** | **メッセージ** | **詳細**
--- | --- | ---
**0x800700EA** | "Hyper-V は仮想マシンの VSS スナップショット セットを生成できませんでした:詳細なデータを入手できます。 (0x800700EA)。 バックアップ操作が進行中の場合は、VSS スナップショット セットの生成が失敗する可能性があります。<br/><br/> 仮想マシンのレプリケーション操作が失敗しました:詳細なデータを入手できます。" | VM でダイナミック ディスクが有効になっているかどうかを確認します。 これはサポートされていません。
**0x80070032** | "Hyper-V ボリューム シャドウ コピー リクエスタは、バージョンが Hyper-V で予測されたバージョンに一致しないため、仮想マシン <./VMname> に接続できませんでした | 最新の Windows 更新プログラムがインストールされているかどうかを確認します。<br/><br/> 最新バージョンの Integration Services への[アップグレード](/windows-server/virtualization/hyper-v/manage/manage-hyper-v-integration-services#keep-integration-services-up-to-date)。



## <a name="collect-replication-logs"></a>レプリケーション ログを収集する

Hyper-V レプリケーション イベントはすべて、**Applications and Services Logs** > **Microsoft** > **Windows** にある Hyper-V-VMMS\Admin ログに記録されています。 さらに、次のように、Hyper-V Virtual Machine Management サービスの分析ログを有効にすることができます。

1. イベント ビューアーに分析およびデバッグ ログを表示できるようにします。 ログを使用可能にするには、イベント ビューアーで、 **[表示]**  >  **[分析およびデバッグ ログの表示]** をクリックします。 分析ログは、**Hyper-V-VMMS** の下に表示されます。
2. **[操作]** ウィンドウで **[ログを有効にする]** をクリックします。 

    ![ログを有効にする](media/hyper-v-azure-troubleshoot/enable-log.png)
    
3. 有効にすると、 **[パフォーマンス モニター]** の **[データ コレクター セット]** の下に **[イベント トレース セッション]** として表示されます。 
4. 収集された情報を表示するには、ログを無効にしてトレース セッションを停止します。 次に、ログを保存し、イベント ビューアーで再度開くか、または他のツールを使用して必要に応じて変換します。


### <a name="event-log-locations"></a>イベント ログの場所

**イベント ログ** | **詳細** |
--- | ---
**Applications and Service Logs/Microsoft/VirtualMachineManager/Server/Admin** (VMM サーバー) | VMM の問題をトラブルシューティングするためのログ。
**Applications and Service Logs/MicrosoftAzureRecoveryServices/Replication** (Hyper-V ホスト) | Microsoft Azure Recovery Services Agent の問題をトラブルシューティングするためのログ。 
**Applications and Service Logs/Microsoft/Azure Site Recovery/Provider/Operational** (Hyper-V ホスト)| Microsoft Azure Site Recovery サービスの問題をトラブルシューティングするためのログ。
**Applications and Service Logs/Microsoft/Windows/Hyper-V-VMMS/Admin** (Hyper-V ホスト) | Hyper-V VM の管理の問題をトラブルシューティングするためのログ。

### <a name="log-collection-for-advanced-troubleshooting"></a>高度なトラブルシューティングのためのログ収集

これらのツールは、高度なトラブルシューティングに役立ちます。

-   VMM の場合は、[サポート診断プラットフォーム (SDP) ツール](https://social.technet.microsoft.com/wiki/contents/articles/28198.asr-data-collection-and-analysis-using-the-vmm-support-diagnostics-platform-sdp-tool.aspx)を使用して Site Recovery のログ収集を実行します。
-   VMM なしの Hyper-V の場合は、[このツールをダウンロードし](https://dcupload.microsoft.com/tools/win7files/DIAG_ASRHyperV_global.DiagCab)、Hyper-V ホスト上でそれを実行してログを収集します。