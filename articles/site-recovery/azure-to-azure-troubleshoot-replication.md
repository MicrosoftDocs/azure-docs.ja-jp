---
title: Azure 間レプリケーションの継続的な問題に関する Azure Site Recovery トラブルシューティング | Microsoft Docs
description: ディザスター リカバリーのために Azure 仮想マシンをレプリケートするときに発生するエラーや問題に関するトラブルシューティング
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 8/2/2019
ms.author: asgang
ms.openlocfilehash: 02f3dff4c9649beeadade942f4b32595f8543c2d
ms.sourcegitcommit: d060947aae93728169b035fd54beef044dbe9480
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/02/2019
ms.locfileid: "68742548"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Azure 間の VM レプリケーションに関する継続的な問題のトラブルシューティング

この記事では、リージョン間で Azure 仮想マシンをレプリケートおよび復旧するときに、Azure Site Recovery で発生する一般的な問題について説明します。 また、そのトラブルシューティング方法についても説明します。 サポートされる構成の詳細については、[Azure VM をレプリケートするためのサポート マトリックス](site-recovery-support-matrix-azure-to-azure.md)に関するページをご覧ください。

Azure Site Recovery では、一貫してソース リージョンからディザスター リカバリー リージョンにデータをレプリケートし、クラッシュ整合性復旧ポイントを 5 分ごとに作成します。 Site Recovery で復旧ポイントが作成されずに 60 分が経過すると、次の情報が通知されます。

エラー メッセージ:"No crash consistent recovery point available for the VM in the last 60 minutes. (VM で使用できる 60 分以内のクラッシュ整合性復旧ポイントがありません。)"</br>
エラー ID: 153007 </br>

以下のセクションでは、原因と解決策について説明します。

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>ソース仮想マシンにおけるデータ変更率が高い
Azure Site Recovery がイベントを起動するのは、ソース仮想マシン上のデータ変更率が、サポートされている制限よりも高い場合です。 問題が高チャーンによるものかどうかを確認するには、 **[レプリケートされたアイテム]**  >  **[VM]**  >  **[Events -last 72 hours]\(イベント - 過去 72 時間\)** に移動します。
[データ変更率がサポートされている制限を超えています] というイベントが表示されるはずです。

![data_change_rate_high](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png)

イベントを選択すると、ディスクの正確な情報が表示されます。

![data_change_rate_event](./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png)


### <a name="azure-site-recovery-limits"></a>Azure Site Recovery の制限
以下の表は、Azure Site Recovery の制限を示したものです。 これらの制限は、Microsoft のテストに基づいて公開されていますが、アプリケーション I/O として想定されるすべての組み合わせを網羅したものではありません。 実際の結果は、ご使用のアプリケーションで発生するさまざまな I/O によって異なることが考えられます。 

ここで考慮していただきたい制限が 2 つあります。ディスクあたりのデータ チャーンと仮想マシンあたりのデータ チャーンです。 たとえば、次の表の Premium P20 ディスクを見てみましょう。 Site Recovery で処理できるのは、ディスクあたり 5 MB/秒のチャーンで、そのようなディスクを VM あたり 5 つまでです。これは、VM あたりの合計チャーンが 25 MB/秒に制限されているためです。

**レプリケーション先のストレージ** | **ソース ディスクの平均 I/O サイズ** |**ソース ディスクの平均データ チャーン** | **ソース データ ディスクの 1 日あたりの合計データ チャーン**
---|---|---|---
Standard Storage | 8 KB | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 8 KB  | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 16 KB | 4 MB/秒 |  (ディスクあたり) 336 GB
Premium P10 または P15 ディスク | 32 KB 以上 | 8 MB/秒 | (ディスクあたり) 672 GB
Premium P20、P30、P40、または P50 ディスク | 8 KB    | 5 MB/s | (ディスクあたり) 421 GB
Premium P20、P30、P40、または P50 ディスク | 16 KB 以上 |10 MB/s | (ディスクあたり) 842 GB

### <a name="solution"></a>解決策
Azure Site Recovery にはデータ変更率に制限があり、これはディスクの種類に基づいています。 この問題が繰り返されているか一時的なものかを判断するには、影響を受ける仮想マシンのデータ変更率を確認します。 ソース仮想マシンに移動し、 **[監視]** の下のメトリックを探して、次のスクリーンショットに示すメトリックを追加します。

![データ変更率を見つけるための 3 段階のプロセス](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. **[メトリックの追加]** を選択し、 **[OS Disk Write Bytes/Sec]\(OS ディスク書き込みバイト/秒\)** と **[Data Disk Write Bytes/Sec]\(データ ディスク書き込みバイト/秒\)** を追加します。
2. スクリーンショットに表示される値の急上昇を監視します。
3. OS ディスクと結合されたすべてのデータ ディスクで発生している書き込み操作の合計を確認します。 これらのメトリックは、個々のディスク レベルの情報ではなく、データ変更率の合計のパターンを示しています。

データの急激な増加が時折しか発生せず、データ変更率が一時的に 10 MB/秒 (Premium の場合) と 2 MB/秒 (Standard の場合) を超えてから低下する場合、レプリケーションは追いつきます。 ただし、ほとんどの時間において、サポートされる制限をチャーンが大きく下回っている場合、可能であれば、次のいずれかのオプションを検討してください。

* **高データ変更率を引き起こしているディスクを除外する**:[PowerShell](./azure-to-azure-exclude-disks.md) を使用して、ディスクを除外できます。ディスクを除外するには、まず、レプリケーションを無効にする必要があります。 
* **ディザスター リカバリー ストレージ ディスクの階層を変更する**:このオプションを使用できるのは、ディスクのデータ チャーンが 20 MB/秒未満の場合だけです。 たとえば、P10 ディスクを含む VM で、8 MB/秒を超えて 10 MB/秒未満のデータ チャーンが発生しているとします。 顧客が保護中にターゲット ストレージのために P30 ディスクを使用できる場合、問題は解決できます。 このソリューションは、Premium Managed Disks を使用しているコンピューターでのみ可能です。 次の手順に従ってください。
    - 影響を受けたレプリケーション対象のマシンの [ディスク] ブレードに移動し、レプリカ ディスク名をコピーします
    - このレプリカ マネージド ディスクに移動します
    - [概要] ブレードに、SAS URL が生成されていることを示すバナーが表示される場合があります。 このバナーをクリックして、エクスポートをキャンセルします。 バナーが表示されない場合は、この手順を無視してください。
    - SAS URL が取り消されたらすぐに、マネージド ディスクの [構成] ブレードにアクセスし、ASR がソース ディスク上で測定済みチャーン レートをサポートできるように、サイズを増やします

## <a name="Network-connectivity-problem"></a>ネットワーク接続の問題

### <a name="network-latency-to-a-cache-storage-account"></a>キャッシュ ストレージ アカウントへのネットワーク待ち時間
Site Recovery では、キャッシュ ストレージ アカウントにレプリケートされたデータを送信します。 仮想マシンからキャッシュ ストレージ アカウントへのデータ アップロード速度が 3 秒あたり 4 MB よりも遅い場合、ネットワーク待ち時間が発生することがあります。 

待ち時間に関連する問題があるかどうかをチェックするには、[azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) を使用して、仮想マシンからキャッシュ ストレージ アカウントにデータをアップロードします。 待ち時間が長い場合は、VM からの送信ネットワーク トラフィックを制御するためのネットワーク仮想アプライアンス (NVA) を使用しているかどうかを確認します。 この場合、すべてのレプリケーション トラフィックが NVA を通過すると、アプライアンスがスロットルされる可能性があります。 

レプリケーション トラフィックが NVA に送られないように、"ストレージ" 用の仮想ネットワーク内にネットワーク サービス エンドポイントを作成することをお勧めします。 詳細については、「[ネットワーク仮想アプライアンスの構成](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)」をご覧ください。

### <a name="network-connectivity"></a>ネットワーク接続
Site Recovery レプリケーションを動作させるには、VM から特定の URL または IP 範囲への送信接続が必要です。 VM がファイアウォールの内側にあるか、ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御している場合は、次のいずれかの問題に直面することがあります。 すべての URL が接続されていることを確認するには、[Site Recovery URL に対する送信接続](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)に関する記事をご覧ください。 

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-last-xxx-minutes"></a>エラー ID 153006 - 過去 "XXX" 分間に、VM 使用可能なアプリ整合性復旧ポイントはありません

最も一般的な問題の一部を次に示します

#### <a name="cause-1-known-issue-in-sql-server-20082008-r2"></a>原因 1:SQL Server 2008/2008 R2 での既知の問題 
**修正方法**: SQL Server 2008/2008 R2 には、既知の問題があります。 サポート技術情報の「[Azure Site Recovery Agent or other non-component VSS backup fails for a server hosting SQL Server 2008 R2 (SQL Server 2008 R2 をホストしているサーバーで Azure Site Recovery エージェントまたはその他の非コンポーネント VSS バックアップが失敗する)](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)」を参照してください

#### <a name="cause-2-azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>原因 2:AUTO_CLOSE DB があるいずれかのバージョンの SQL Server インスタンスをホストするサーバーで Azure Site Recovery ジョブが失敗します 
**修正方法**: サポート技術情報の[記事](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)を参照してください 


#### <a name="cause-3-known-issue-in-sql-server-2016-and-2017"></a>原因 3:SQL Server 2016 および 2017 での既知の問題
**修正方法**: サポート技術情報の[記事](https://support.microsoft.com/help/4493364/fix-error-occurs-when-you-back-up-a-virtual-machine-with-non-component)を参照してください 

#### <a name="cause-4-you-are-using-storage-spaces-direct-configuration"></a>原因 4:記憶域スペース ダイレクト構成を使用しています
**修正方法**: Azure Site Recovery では、記憶域スペース ダイレクト構成のアプリケーション整合性復旧ポイントを作成できません。 正しく[レプリケーション ポリシーを構成](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-how-to-enable-replication-s2d-vms)するには、記事を参照してください

### <a name="more-causes-due-to-vss-related-issues"></a>VSS 関連の問題に起因するその他の原因:

さらにトラブルシューティングを続けるには、ソース マシン上のファイルを確認して、失敗の正確なエラー コードを取得します。
    
    C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log

ファイル内のエラーは、どのように探せば良いでしょうか。
エディターで vacp.log ファイルを開いて、"vacpError" という文字列を検索します
        
    Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|

上の例では、**2147754994** がエラー コードであり、以下のようなエラーについての情報を示しています

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS ライターがインストールされていません - エラー 2147221164 

*修正方法*: アプリケーション整合性タグを生成するために、Azure Site Recovery では Microsoft ボリューム シャドウ コピー サービス (VSS) が使用されます。 これによって、アプリ整合性スナップショットを作成する操作のために、VSS プロバイダーがインストールされます。 この VSS プロバイダーは、サービスとしてインストールされます。 VSS プロバイダー サービスがインストールされていない場合は、アプリケーション整合性スナップショットの作成が失敗し、エラー ID 0x80040154 "クラスが登録されていません" というエラーが発生します。 </br>
[VSS ライターのインストールのトラブルシューティングに関する記事](https://docs.microsoft.com/azure/site-recovery/vmware-azure-troubleshoot-push-install#vss-installation-failures)を参照してください 

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS ライターが無効です - エラー 2147943458

**修正方法**: アプリケーション整合性タグを生成するために、Azure Site Recovery では Microsoft ボリューム シャドウ コピー サービス (VSS) が使用されます。 これによって、アプリ整合性スナップショットを作成する操作のために、VSS プロバイダーがインストールされます。 この VSS プロバイダーは、サービスとしてインストールされます。 VSS プロバイダー サービスが無効になっている場合は、アプリケーション整合性スナップショットの作成が失敗し、エラー ID "指定したサービスは無効であるため、開始できません (0x80070422)" というエラーが発生します。 </br>

- VSS が無効になっている場合は、
    - VSS プロバイダー サービスのスタートアップの種類が **[自動]** に設定されていることを確認します。
    - 次のサービスを再起動します。
        - VSS サービス
        - Azure Site Recovery VSS プロバイダー
        - VDS サービス

####  <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - エラー 2147754756

**修正方法**: アプリケーション整合性タグを生成するために、Azure Site Recovery では Microsoft ボリューム シャドウ コピー サービス (VSS) が使用されます。 Azure Site Recovery VSS プロバイダー サービスがインストールされているかどうかを確認してください。 </br>

- 以下のコマンドを使用して、プロバイダーのインストールを再試行します。
- 既存のプロバイダーのアンインストール: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd
- 再インストール: C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd
 
VSS プロバイダー サービスのスタートアップの種類が **[自動]** に設定されていることを確認します。
    - 次のサービスを再起動します。
        - VSS サービス
        - Azure Site Recovery VSS プロバイダー
        - VDS サービス
