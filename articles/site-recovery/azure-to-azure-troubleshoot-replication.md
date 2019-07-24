---
title: Azure 間レプリケーションの継続的な問題に関する Azure Site Recovery トラブルシューティング | Microsoft Docs
description: ディザスター リカバリーのために Azure 仮想マシンをレプリケートするときに発生するエラーや問題に関するトラブルシューティング
services: site-recovery
author: asgang
manager: rochakm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/27/2018
ms.author: asgang
ms.openlocfilehash: 9ff756270c368d39b7ef78d7c1046f7c91169668
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/15/2019
ms.locfileid: "56299601"
---
# <a name="troubleshoot-ongoing-problems-in-azure-to-azure-vm-replication"></a>Azure 間の VM レプリケーションに関する継続的な問題のトラブルシューティング

この記事では、リージョン間で Azure 仮想マシンをレプリケートおよび復旧するときに、Azure Site Recovery で発生する一般的な問題について説明します。 また、そのトラブルシューティング方法についても説明します。 サポートされる構成の詳細については、[Azure VM をレプリケートするためのサポート マトリックス](site-recovery-support-matrix-azure-to-azure.md)に関するページをご覧ください。

Azure Site Recovery では、一貫してソース リージョンからディザスター リカバリー リージョンにデータをレプリケートし、クラッシュ整合性復旧ポイントを 5 分ごとに作成します。 Site Recovery で復旧ポイントが作成されずに 60 分が経過すると、次の情報が通知されます。

エラー メッセージ:"No crash consistent recovery point available for the VM in the last 60 minutes. (VM で使用できる 60 分以内のクラッシュ整合性復旧ポイントがありません。)"</br>
エラー ID: 153007 </br>

以下のセクションでは、原因と解決策について説明します。

## <a name="high-data-change-rate-on-the-source-virtal-machine"></a>ソース仮想マシンにおけるデータ変更率が高い
Azure Site Recovery がイベントを起動するのは、ソース仮想マシン上のデータ変更率が、サポートされている制限よりも高い場合です。 問題が高チャーンによるものかどうかを確認するには、**[レプリケートされたアイテム]** > **[VM]** > **[Events -last 72 hours]\(イベント - 過去 72 時間\)** に移動します。
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
Azure Site Recovery にはデータ変更率に制限があり、これはディスクの種類に基づいています。 この問題が繰り返されているか一時的なものかを判断するには、影響を受ける仮想マシンのデータ変更率を確認します。 ソース仮想マシンに移動し、**[監視]** の下のメトリックを探して、次のスクリーンショットに示すメトリックを追加します。

![データ変更率を見つけるための 3 段階のプロセス](./media/site-recovery-azure-to-azure-troubleshoot/churn.png)

1. **[メトリックの追加]** を選択し、**[OS Disk Write Bytes/Sec]\(OS ディスク書き込みバイト/秒\)** と **[Data Disk Write Bytes/Sec]\(データ ディスク書き込みバイト/秒\)** を追加します。
2. スクリーンショットに表示される値の急上昇を監視します。
3. OS ディスクと結合されたすべてのデータ ディスクで発生している書き込み操作の合計を確認します。 これらのメトリックは、個々のディスク レベルの情報ではなく、データ変更率の合計のパターンを示しています。

データの急激な増加が時折しか発生せず、データ変更率が一時的に 10 MB/秒 (Premium の場合) と 2 MB/秒 (Standard の場合) を超えてから低下する場合、レプリケーションは追いつきます。 ただし、ほとんどの時間において、サポートされる制限をチャーンが大きく下回っている場合、可能であれば、次のいずれかのオプションを検討してください。

* **高データ変更率を引き起こしているディスクを除外する**:[PowerShell](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-powershell#replicate-azure-virtual-machine) を使ってディスクを除外できます。
* **ディザスター リカバリー ストレージ ディスクの階層を変更する**:このオプションを使用できるのは、ディスクのデータ チャーンが 10 MB/秒未満の場合だけです。 たとえば、P10 ディスクを含む VM で、8 MB/秒を超えて 10 MB/秒未満のデータ チャーンが発生しているとします。 顧客が保護中にターゲット ストレージのために P30 ディスクを使用できる場合、問題は解決できます。

## <a name="Network-connectivity-problem"></a>ネットワーク接続の問題

### <a name="network-latency-to-a-cache-storage-account"></a>キャッシュ ストレージ アカウントへのネットワーク待ち時間
Site Recovery では、キャッシュ ストレージ アカウントにレプリケートされたデータを送信します。 仮想マシンからキャッシュ ストレージ アカウントへのデータ アップロード速度が 3 秒あたり 4 MB よりも遅い場合、ネットワーク待ち時間が発生することがあります。 

待ち時間に関連する問題があるかどうかをチェックするには、[azcopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) を使用して、仮想マシンからキャッシュ ストレージ アカウントにデータをアップロードします。 待ち時間が長い場合は、VM からの送信ネットワーク トラフィックを制御するためのネットワーク仮想アプライアンス (NVA) を使用しているかどうかを確認します。 この場合、すべてのレプリケーション トラフィックが NVA を通過すると、アプライアンスがスロットルされる可能性があります。 

レプリケーション トラフィックが NVA に送られないように、"ストレージ" 用の仮想ネットワーク内にネットワーク サービス エンドポイントを作成することをお勧めします。 詳細については、「[ネットワーク仮想アプライアンスの構成](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#network-virtual-appliance-configuration)」をご覧ください。

### <a name="network-connectivity"></a>ネットワーク接続
Site Recovery レプリケーションを動作させるには、VM から特定の URL または IP 範囲への送信接続が必要です。 VM がファイアウォールの内側にあるか、ネットワーク セキュリティ グループ (NSG) ルールを使用して送信接続を制御している場合は、次のいずれかの問題に直面することがあります。 すべての URL が接続されていることを確認するには、[Site Recovery URL に対する送信接続](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-about-networking#outbound-connectivity-for-ip-address-ranges)に関する記事をご覧ください。 