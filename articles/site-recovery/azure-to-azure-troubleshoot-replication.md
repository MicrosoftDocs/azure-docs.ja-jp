---
title: Azure Site Recovery を使用した Azure VM のレプリケーションのトラブルシューティング
description: Azure Site Recovery を使用して Azure VM のディザスター リカバリーでのレプリケーションのトラブルシューティングを行います
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 04/03/2020
ms.openlocfilehash: dc14334668b76ee8cbb81e48abfe1eecf17fa138
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96007360"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Azure VM のディザスター リカバリーでのレプリケーションのトラブルシューティング

この記事では、リージョン間で Azure 仮想マシン (VM) をレプリケートおよび復旧するときに、Azure Site Recovery で発生する一般的な問題について説明します。 また、一般的な問題のトラブルシューティング方法についても説明します。 サポートされる構成の詳細については、[Azure VM をレプリケートするためのサポート マトリックス](./azure-to-azure-support-matrix.md)に関するページをご覧ください。

Azure Site Recovery は、一貫してソース リージョンからディザスター リカバリー リージョンにデータをレプリケートします。 5 分ごとにクラッシュ整合性復旧ポイントも作成されます。 Site Recovery で復旧ポイントが作成されずに 60 分が経過すると、次の情報が通知されます。

```plaintext
Error message: "No crash consistent recovery point available for the VM in the last 60 minutes."

Error ID: 153007
```

以下のセクションでは、原因と解決策について説明します。

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>ソース仮想マシンにおけるデータ変更率が高い

Azure Site Recovery がイベントを作成するのは、ソース仮想マシン上のデータ変更率が、サポートされている制限よりも高い場合です。 問題が高チャーンによるものかどうかを確認するには、 **[レプリケートされたアイテム]**  >  **[VM]**  >  **[Events -last 72 hours]\(イベント - 過去 72 時間\)** に移動します。
**[データ変更率がサポートされている制限を超えています]** というイベントが表示されるはずです。

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png" alt-text="データ変更率が高すぎることを示す Azure Site Recovery ページ。":::

イベントを選択すると、ディスクの正確な情報が表示されます。

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png" alt-text="データ変更率イベントの詳細を示すページ。":::

### <a name="azure-site-recovery-limits"></a>Azure Site Recovery の制限

以下の表は、Azure Site Recovery の制限を示したものです。 これらの制限は、Microsoft のテストに基づいていますが、アプリケーションの入出力 (I/O) として想定されるすべての組み合わせを網羅したものではありません。 実際の結果は、ご使用のアプリケーションで発生するさまざまな I/O によって異なることが考えられます。

ここで考慮していただきたい制限が 2 つあります。ディスクあたりのデータ チャーンと仮想マシンあたりのデータ チャーンです。 たとえば、次の表の Premium P20 ディスクを見てみましょう。 1 つの VM の場合、Site Recovery はディスクあたり 5 MB/秒のチャーンを処理できます。そのようなディスクが最大で 5 つあります。 Site Recovery には、VM あたりのチャーンの合計が 54 MB/秒という制限があります。

**レプリケーション先のストレージ** | **ソース ディスクの平均 I/O サイズ** |**ソース ディスクの平均データ チャーン** | **ソース データ ディスクの 1 日あたりの合計データ チャーン**
---|---|---|---
Standard Storage | 8 KB    | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 8 KB    | 2 MB/秒 | (ディスクあたり) 168 GB
Premium P10 または P15 ディスク | 16 KB | 4 MB/秒 |    (ディスクあたり) 336 GB
Premium P10 または P15 ディスク | 32 KB 以上 | 8 MB/秒 | (ディスクあたり) 672 GB
Premium P20、P30、P40、または P50 ディスク | 8 KB    | 5 MB/s | (ディスクあたり) 421 GB
Premium P20、P30、P40、または P50 ディスク | 16 KB 以上 |20 MB/秒 | (ディスクあたり) 1,684 GB

### <a name="solution"></a>解決策

Azure Site Recovery にはデータ変更率に制限があり、これはディスクの種類に基づいています。 この問題が繰り返されているか一時的なものかを判断するには、影響を受ける仮想マシンのデータ変更率を確認します。 ソース仮想マシンに移動し、 **[監視]** の下のメトリックを探して、次のスクリーンショットに示すメトリックを追加します。

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/churn.png" alt-text="データ変更率を見つけるための 3 段階のプロセスを示すページ。":::

1. **[メトリックの追加]** を選択し、 **[OS Disk Write Bytes/Sec]\(OS ディスク書き込みバイト/秒\)** と **[Data Disk Write Bytes/Sec]\(データ ディスク書き込みバイト/秒\)** を追加します。
1. スクリーンショットに表示される値の急上昇を監視します。
1. OS ディスクと結合されたすべてのデータ ディスクで発生している書き込み操作の合計を確認します。 これらのメトリックは、個々のディスク レベルの情報ではなく、データ変更率の合計のパターンを示しています。

一時的なデータ バーストにより、データ変更率が急増する可能性があります。 データ変更率が 10 MB/秒 (Premium の場合) または 2 MB/秒 (Standard の場合) を超えると、そこから低下した時点でレプリケーションが開始します。 ただし、サポートされる制限をチャーンが一貫して大きく上回っている場合は、次のいずれかのオプションを検討してください。

- 高データ変更率を引き起こしているディスクを除外する: まず、レプリケーションを無効にします。 その後、[PowerShell](azure-to-azure-exclude-disks.md) を使ってディスクを除外できます。
- ディザスター リカバリー ストレージ ディスクの階層を変更する: このオプションを使用できるのは、ディスクのデータ チャーンが 20 MB/秒未満の場合だけです。 たとえば、P10 ディスクを含む VM で、8 MB/秒以上 10 MB/秒未満のデータ チャーンが発生しているとします。 顧客が保護中にターゲット ストレージのために P30 ディスクを使用できる場合、問題は解決できます。 このソリューションは、Premium Managed Disks を使用しているマシンでのみ可能です。 次の手順に従います。

  1. 影響を受けたレプリケーション対象のマシンの **[ディスク]** に移動し、レプリカ ディスク名をコピーします。
  1. このマネージド ディスクのレプリカに移動します。
  1. **[概要]** に、SAS URL が生成されたことを示すバナーが表示される場合があります。 このバナーを選択して、エクスポートをキャンセルします。 バナーが表示されない場合は、このステップを無視してください。
  1. SAS URL が取り消されたら、すぐにマネージド ディスクの **[構成]** に移動します。 Site Recovery がソース ディスクで観測されたチャーン率をサポートするように、サイズを大きくします。

## <a name="network-connectivity-problems"></a>ネットワーク接続の問題

### <a name="network-latency-to-a-cache-storage-account"></a>キャッシュ ストレージ アカウントへのネットワーク待ち時間

Site Recovery では、キャッシュ ストレージ アカウントにレプリケートされたデータを送信します。 仮想マシンからキャッシュ ストレージ アカウントへのデータ アップロード速度が 3 秒あたり 4 MB よりも遅い場合、ネットワーク待ち時間が発生することがあります。

待ち時間に関連する問題を確認するには、[AzCopy](../storage/common/storage-use-azcopy-v10.md) を使用します。 このコマンドライン ユーティリティを使用して、仮想マシンからキャッシュ ストレージ アカウントにデータをアップロードすることができます。 待ち時間が長い場合は、VM からの送信ネットワーク トラフィックを制御するためのネットワーク仮想アプライアンス (NVA) を使用しているかどうかを確認します。 この場合、すべてのレプリケーション トラフィックが NVA を通過すると、アプライアンスがスロットルされる可能性があります。

レプリケーション トラフィックが NVA に送られないように、"ストレージ" 用の仮想ネットワーク内にネットワーク サービス エンドポイントを作成することをお勧めします。 詳細については、「[ネットワーク仮想アプライアンスの構成](azure-to-azure-about-networking.md#network-virtual-appliance-configuration)」をご覧ください。

### <a name="network-connectivity"></a>ネットワーク接続

Site Recovery レプリケーションを動作させるには、VM で特定の URL または IP 範囲への送信接続を提供する必要があります。 VM がファイアウォールの内側にあるか、ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御している場合があります。 その場合、イシューが発生する可能性があります。 すべての URL が接続されていることを確認するには、[URL に対する送信接続](azure-to-azure-about-networking.md#outbound-connectivity-for-urls)に関する記事をご覧ください。

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>エラー ID 153006 - 過去 "X" 分間に、VM で使用可能なアプリ整合性復旧ポイントはありません

最も一般的なイシューの一部を次に示します。

### <a name="known-issue-in-sql-server-20082008-r2"></a>SQL Server 2008/2008 R2 での既知の問題

**修正方法:** SQL Server 2008/2008 R2 には、既知のイシューがあります。 記事「[SQL Server 2008 R2 をホストしているサーバーで Azure Site Recovery エージェントまたはその他の非コンポーネント VSS バックアップが失敗する](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)」を参照してください。

### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>AUTO_CLOSE DB があるいずれかのバージョンの SQL Server インスタンスをホストするサーバーで Azure Site Recovery ジョブが失敗します

**修正方法:** 記事「[AUTO_CLOSE DB を使用して SQL Server インスタンスをホストしているサーバーで Azure Site Recovery ジョブなどの非コンポーネント VSS バックアップが失敗する](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser)」を参照してください。

### <a name="known-issue-in-sql-server-2016-and-2017"></a>SQL Server 2016 および 2017 での既知の問題

**修正方法**: 「[SQL Server 2017 の累積更新プログラム 16](https://support.microsoft.com/help/4508218/cumulative-update-16-for-sql-server-2017)」の記事を参照してください。

### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Azure 記憶域スペース ダイレクト構成を使用しています

**修正方法**: Azure Site Recovery では、記憶域スペース ダイレクト構成のアプリケーション整合性復旧ポイントを作成できません。 [レプリケーション ポリシーを構成します](azure-to-azure-how-to-enable-replication-s2d-vms.md)。

### <a name="app-consistency-not-enabled-on-linux-servers"></a>Linux サーバーでアプリの整合性が有効になっていない

**修正方法**: Linux オペレーティング システム用の Azure Site Recovery では、アプリの整合性のためのアプリケーション カスタム スクリプトがサポートされています。 プリオプションとポストオプションを含むカスタム スクリプトが、アプリの整合性のために Azure Site Recovery の Mobility Agent によって使用されます。 これを有効にする手順は、[こちら](./site-recovery-faq.md#replication)をご覧ください。

### <a name="more-causes-because-of-vss-related-issues"></a>VSS 関連のイシューに起因するその他の原因:

さらにトラブルシューティングを続けるには、ソース マシン上のファイルを確認して、失敗の正確なエラー コードを取得します。

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log`

エラーを見つけるには、テキスト エディターで _vacp.log_ ファイルを開いて、**vacpError** という文字列を検索します。

```plaintext
Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|
```

上の例では、**2147754994** がエラー コードであり、この文の後にエラーについての情報が示されます。

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS ライターがインストールされていません - エラー 2147221164

**修正方法**: アプリケーション整合性タグを生成するために、Azure Site Recovery ではボリューム シャドウ コピー サービス (VSS) が使用されます。 Site Recovery で、アプリ整合性スナップショットを作成する操作のために、VSS プロバイダーがインストールされます。 Azure Site Recovery は、この VSS プロバイダーをサービスとしてインストールします。 VSS プロバイダーがインストールされていない場合、アプリケーション整合性スナップショットの作成は失敗します。 **エラー ID 0x80040154 "クラスが登録されていません"** が表示されます。 [VSS ライターのインストールのトラブルシューティングに関する記事](vmware-azure-troubleshoot-push-install.md#vss-installation-failures)を参照してください。

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS ライターが無効です - エラー 2147943458

**修正方法**: アプリケーション整合性タグを生成するために、Azure Site Recovery では VSS が使用されます。 Site Recovery で、アプリ整合性スナップショットを作成する操作のために、VSS プロバイダーがインストールされます。 この VSS プロバイダーは、サービスとしてインストールされます。 VSS プロバイダー サービスが有効になっていない場合、アプリケーション整合性スナップショットの作成は失敗します。 次のエラーが表示されます。**指定されたサービスは無効になっているため、開始できません (0x80070422)** 。

VSS が無効になっている場合:

- VSS プロバイダー サービスのスタートアップの種類が **[自動]** に設定されていることを確認します。
- 次のサービスを再起動します。
  - VSS サービス。
  - Azure Site Recovery VSS プロバイダー。
  - VDS サービス。

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - エラー 2147754756

**修正方法**: アプリケーション整合性タグを生成するために、Azure Site Recovery では VSS が使用されます。 Azure Site Recovery VSS プロバイダー サービスがインストールされているかどうかを確認してください。

次のコマンドを使用して、VSS プロバイダーをインストールします。

1. 既存のプロバイダーのアンインストール:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd"`

1. VSS プロバイダーの再インストール:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

VSS プロバイダー サービスのスタートアップの種類が **[自動]** に設定されていることを確認します。

次のサービスを再起動します。

- VSS サービス。
- Azure Site Recovery VSS プロバイダー。
- VDS サービス。
