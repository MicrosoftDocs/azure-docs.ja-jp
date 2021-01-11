---
title: Azure Migrate Server Migration についてよく寄せられる質問
description: Azure Migrate Server Migration を使用したマシンの移行についてよく寄せられる質問の回答を示します。
ms.topic: conceptual
ms.date: 05/04/2020
ms.openlocfilehash: af40aecaa1614542074cf87ce95eb81492233bdc
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87321226"
---
# <a name="azure-migrate-server-migration-common-questions"></a>Azure Migrate Server Migration:一般的な質問

この記事では、Azure Migrate についてよくある質問の回答を示します。サーバー移行ツール。 その他の質問については、次のリソースを確認してください。

- Azure Migrate に関する[一般的な質問](resources-faq.md)
- [Azure Migrate アプライアンス](common-questions-appliance.md)に関する一般的な質問
- [検出、評価、依存関係の視覚化](common-questions-discovery-assessment.md) に関する質問
- [Azure Migrate フォーラム](https://aka.ms/AzureMigrateForum)で質問の回答を示します。

## <a name="what-geographies-are-supported-for-migration-with-azure-migrate"></a>Azure Migrate を使用した移行はどの地域でサポートされていますか?

[パブリック](migrate-support-matrix.md#supported-geographies-public-cloud)と [Government クラウド](migrate-support-matrix.md#supported-geographies-azure-government)でサポートされている地域を確認してください。

## <a name="how-does-agentless-vmware-replication-work"></a>エージェントレス VMware レプリケーションどのようなしくみになっていますか?

VMware のエージェントレス レプリケーション方式では、VMware スナップショット、および VMware の変更ブロックのトラッキング (CBT) が使用されます。

プロセスは次のとおりです。

1. レプリケーションを開始すると、初期レプリケーション サイクルがスケジュールされます。 最初のサイクルで、VM のスナップショットが取得されます。 このスナップショットは、VM VMDK (ディスク) をレプリケートするために使用されます。 
2. 初期レプリケーション サイクルが終了すると、差分レプリケーション サイクルが定期的にスケジュールされます。
    - 差分レプリケーション中には、スナップショットが作成され、前のレプリケーション サイクル以降に変更されたデータ ブロックがレプリケートされます。
    - 最後のサイクル以降に変更されたブロックを特定するには、VMware の CBT が使用されます。
    - 定期的なレプリケーション サイクルの頻度は、同じデータ ストアから同時にレプリケートされている他の VM またはディスクの数に応じて、Azure Migrate によって自動的に管理されます。 最適な条件では、レプリケーションは最終的に各 VM で 1 時間あたり 1 サイクルに収束されます。

移行時には、残りのデータをキャプチャするために、オンデマンドのレプリケーション サイクルがマシンに対してスケジュールされます。 移行中にマシンをシャットダウンすることを選択すれば、データの損失をなくし、アプリケーションの整合性を確保することができます。

## <a name="why-isnt-resynchronization-exposed"></a>再同期が公開されないのはなぜですか?

エージェントレスの移行中、すべての差分サイクルで、現在のスナップショットと以前に作成されたスナップショットの差が書き込まれます。 これは常にスナップショットの違いであるため、データは折りたたまれます。 スナップショット間で特定のセクターが *N* 回書き込まれた場合、関心があるのは最後の同期のみであるので、最後の書き込みだけが転送される必要があります。このプロセスは、すべての書き込みを追跡して適用するエージェントベースのレプリケーションとは異なります。 このプロセスでは、すべての差分サイクルは再同期です。 そのため、再同期オプションは公開されていません。 障害が原因でディスクが同期されていない場合は、次回のサイクルで修正されます。 

## <a name="how-does-churn-rate-affect-agentless-replication"></a>チャーン レートはエージェントレス レプリケーションにどのように影響しますか?

エージェントレス レプリケーションでは日付が折りたたまれるため、*チャーン レート*より*チャーン パターン*が重要です。 ファイルが繰り返し書き込まれる場合、そのレートはあまり影響を与えません。 ただし、セクターが 1 つおきに書き込まれるパターンでは、次回のサイクルでチャーンが高くなります。 転送するデータの量を最小限に抑えるため、次回のサイクルをスケジュールする前に、できるだけ多くのデータを折りたためるようにします。  

## <a name="how-frequently-is-a-replication-cycle-scheduled"></a>レプリケーション サイクルはどのくらいの頻度でスケジュールされますか?

次のレプリケーション サイクルをスケジュールする数式は、(以前のサイクル時間/2) または1時間のいずれか長い方です。

たとえば、VM で差分サイクルに 4 時間かかった場合、次回のサイクルは次の 1 時間ではなく、2 時間後にスケジュールされます。 このプロセスは、最初のレプリケーションの直後とは異なります。その場合は、最初の差分サイクルがすぐにスケジュールされます。

## <a name="how-does-agentless-replication-affect-vmware-servers"></a>エージェントレス レプリケーションは VMware サーバーにどのように影響しますか?

エージェントレス レプリケーションでは、VMware vCenter Server と VMware ESXi ホストのパフォーマンスに一部影響が生じます。 エージェントレス レプリケーションではスナップショットが使用されるので、ストレージに対する IOP が消費され、一部の IOPS ストレージ帯域幅が必要になります。 ご利用の環境内でストレージまたは IOP に制約がある場合は、エージェントレス レプリケーションを使用しないことをお勧めします。

## <a name="can-i-do-agentless-migration-of-uefi-vms-to-azure-gen-2"></a>UEFI VM を Azure Gen 2 にエージェントレスで移行できますか?

いいえ。 これらの VM を Gen 2 Azure VM に移行するには、Azure Site Recovery を使用してください。 

## <a name="can-i-pin-vms-to-azure-availability-zones-when-i-migrate"></a>移行時に VM を Azure Availability Zones にピン留めすることはできますか?

いいえ。 Azure Availability Zones は Azure Migrate 移行ではサポートされていません。

## <a name="what-transport-protocol-does-azure-migrate-use-during-replication"></a>レプリケーション時に Azure Migrate で使用されるトランスポート プロトコルは何ですか?

Azure Migrate では、TLS 暗号化に Network Block Device (NBD) プロトコルが使用されます。

## <a name="how-is-the-data-transmitted-from-on-prem-environment-to-azure-is-it-encrypted-before-transmission"></a>オンプレミス環境から Azure にはどのようにしてデータが送信されますか? 送信前に暗号化されますか? 
エージェントレス レプリケーションの場合の Azure Migrate アプライアンスでは、アップロード前にデータを圧縮して暗号化します。 データはセキュリティで保護された通信チャネルを介して HTTPS で送信され、TLS 1.2 以降を使用します。 さらに、Azure Storage では、データはクラウドに永続化されるときに自動的に暗号化されます (保存時暗号化)。  

## <a name="what-is-the-minimum-vcenter-server-version-required-for-migration"></a>移行に必要な最低限の vCenter Server バージョンは何ですか?

vCenter Server 5.5 および vSphere ESXi ホスト バージョン 5.5 以上が必要です。

## <a name="can-customers-migrate-their-vms-to-unmanaged-disks"></a>お客様は VM をアンマネージド ディスクに移行できますか?

いいえ。 Azure Migrate では、マネージド ディスク (Standard HDD、Premium SSD) への移行のみがサポートされています。

## <a name="how-many-vms-can-i-replicate-at-one-time-by-using-agentless-migration"></a>エージェントレスの移行を使用して、一度にレプリケートできる VM の数はいくつですか?

現在、お客様は vCenter Server のインスタンスごとに 300 台の VM を同時に移行できます。 10 台の VM のバッチで移行します。

## <a name="how-do-i-throttle-replication-in-using-azure-migrate-appliance-for-agentless-vmware-replication"></a>エージェントレス VMware レプリケーションに対する Azure Migrate アプライアンスの使用においてレプリケーションを調整するにはどうすればよいですか?  

NetQosPolicy を使用して調整できます。 次に例を示します。

NetQosPolicy で使用する AppNamePrefix は "GatewayWindowsService.exe" です。 Azure Migrate アプライアンスで次のようなポリシーを作成することによって、アプライアンスからのレプリケーション トラフィックを調整できます:
 
New-NetQosPolicy -Name "ThrottleReplication" -AppPathNameMatchCondition "GatewayWindowsService.exe" -ThrottleRateActionBitsPerSecond 1MB

## <a name="can-i-migrate-vms-that-are-already-being-replicated-to-azure"></a>既に Azure にレプリケートされている VM を移行できますか? 

VM が他の方法で既に Azure にレプリケートされている場合は、それらのマシンを Azure Migrate Server 移行を使用して VM として移行することはできません。 回避策として、VM を物理サーバーとして扱い、[サポートされている物理サーバーの移行](migrate-support-matrix-physical-migration.md)に従ってそれらを移行できます。

## <a name="when-do-i-migrate-machines-as-physical-servers"></a>マシンを物理サーバーとして Azure に移行するのはいつですか?

マシンを物理サーバーとして扱うことによって移行することは、さまざまなシナリオで役立ちます。

- オンプレミスの物理サーバーを移行している場合。
- Xen、KVM などのプラットフォームで仮想化された VM を移行している場合。
- Hyper-V または VMware VM を移行するには (なんらかの理由で、[Hyper-V](tutorial-migrate-hyper-v.md) の標準移行プロセスや [VMware ](server-migrate-overview.md) 移行を使用できない場合)。 たとえば、VMware vCenter を実行しておらず、ESXi ホストのみを使用している場合です。
- プライベート クラウドで現在実行されている VM を Azure に移行するには
- アマゾン ウェブ サービス (AWS) や Google Cloud Platform (GCP) などのパブリック クラウドで実行されている VM を移行する場合。

## <a name="i-deployed-two-or-more-appliances-to-discover-vms-in-my-vcenter-server-however-when-i-try-to-migrate-the-vms-i-only-see-vms-corresponding-to-one-of-the-appliance"></a>VCenter Server 内の VM を検出するために、2 つ (またはそれ以上) のアプライアンスをデプロイしました。 しかし、VM を移行しようとすると、いずれかのアプライアンスに対応する VM のみが表示されます。

複数のアプライアンスがセットアップされている場合は、指定された vCenter アカウントの VM 間に重複がないことが必要です。 このような重複が検出されるのは、サポートされていないシナリオです。

## <a name="do-i-need-vmware-vcenter-to-migrate-vmware-vms"></a>Vmware VM を移行するには VMware vCenter が必要ですか?
Vmware エージェントベースまたはエージェントレス移行を使用して [VMware Vm](server-migrate-overview.md) を移行するには、VM が配置されている ESXi ホストを vCenter Server で管理する必要があります。 VCenter Server がない場合は、物理サーバーとして移行することで VMware VM を移行できます。 [詳細については、こちらを参照してください](migrate-support-matrix-physical-migration.md)。
 
## <a name="next-steps"></a>次のステップ

[Azure Migrate の概要](migrate-services-overview.md)を確認します。
