---
title: Microsoft Azure Backup Server の新機能
description: Microsoft Azure Backup Server には、VM、ファイルとフォルダー、ワークロードなどを保護するための高度なバックアップ機能があります。
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 39050d0f658e29b82f270f1fe53026e2fb80bfa1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91332765"
---
# <a name="whats-new-in-microsoft-azure-backup-server-mabs"></a>Microsoft Azure Backup Server (MABS) の新機能

## <a name="whats-new-in-mabs-v3-ur1"></a>MABS V3 UR1 の新機能

Microsoft Azure Backup Server (MABS) バージョン 3 UR1 は最新のアップグレードであり、重大なバグ修正やその他の機能と機能強化が含まれています。 MABS V3 UR1 の修正されたバグの一覧とインストール手順については、KB 記事 [4534062](https://support.microsoft.com/help/4534062) を参照してください。

>[!NOTE]
>32 ビット保護エージェントのサポートは、MABS v3 UR1 で終了しました。 「[32 ビット保護エージェントの廃止](#32-bit-protection-agent-deprecation)」を参照してください。

### <a name="faster-backups-with-tiered-storage-using-ssds"></a>SSD を使用した階層型記憶域によりバックアップを高速化

MABS V2 は、[Modern Backup Storage](backup-mabs-add-storage.md) (MBS) が導入され、記憶域使用率とパフォーマンスが改善されています。 MBS は、基になるファイル システムとして ReFS を使用しており、階層型記憶域などのハイブリッド ストレージを活用するように設計されています。

MBS によるスケールとパフォーマンスを達成するために、MABS V3 UR1 で DPM HDD ストレージと共に階層化ボリュームとして使用するフラッシュ ストレージ (SSD) の割合を小さく (ストレージ全体の 4%) することをお勧めします。 階層型記憶域を使用した MABS V3 UR1 では、バックアップ速度が 50% から 70% 向上しています。 階層化ストレージを構成するステップについては、DPM の記事「[階層型記憶域で MBS をセットアップする](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage)」を参照してください。

### <a name="support-for-refs-volumes"></a>ReFS ボリュームのサポート

MABS V3 UR1 では、ReFS ボリュームとそこにデプロイされたワークロードをバックアップすることができます。 ReFS ボリュームにデプロイされた次のワークロードをバックアップできます。

* オペレーティング システム (64 ビット): Windows Server 2019、2016、2012 R2、2012。
* SQL Server:SQL Server 2019、SQL Server 2017、2016。
* Exchange: Exchange 2019、2016。
* SharePoint:SharePoint 2019、2016 (最新の SP がインストールされていること)。

>[!NOTE]
> ReFS ボリュームに格納されている Hyper-V VM のバックアップは、MABS V3 でサポートされます。

>[重要] 重複除去された ReFS ボリュームのバックアップに問題がいくつか見つかっています。 これらの修正に取り組んでおり、このセクションは、修正プログラムが利用可能になるとすぐに更新されます。 それまでは、重複除去された ReFS ボリュームのバックアップ サポートを MABSv3 UR1 から削除します。

### <a name="azure-vmware-solution-protection-support"></a>Azure VMware Solution の保護に対応

MABS v3 UR1 では、[Azure VMware Solution](../azure-vmware/index.yml) にデプロイされた仮想マシンを保護できるようになりました。

### <a name="vmware-parallel-backups"></a>VMware の並列バックアップ

MABS V3 UR1 では、1 つの保護グループ内のすべての VMware VM バックアップが並列化され、VM のバックアップが 25% 高速になります。
以前のバージョンの MABS では、並列バックアップは保護グループ間でのみ実行されていました。 MABS V3 UR1 では、VMware デルタ レプリケーション ジョブが並列で実行されます。 既定では、並列で実行されるジョブの数は 8 に設定されます。 詳細については、「[VMware の並列バックアップ](backup-azure-backup-server-vmware.md#vmware-parallel-backups)」を参照してください。

### <a name="disk-exclusion-for-vmware-vm-backup"></a>VMware VM バックアップでのディスクの除外

MABS V3 UR1 では、VMware VM のバックアップから特定のディスクを除外できます。 詳細については、[VMware VM バックアップからディスクを除外する](backup-azure-backup-server-vmware.md#exclude-disk-from-vmware-vm-backup)方法に関するページを参照してください。  

### <a name="support-for-additional-layer-of-authentication-to-delete-online-backup"></a>オンライン バックアップを削除するための追加の認証レイヤーをサポート

MABS V3 UR1 では、クリティカルな操作に関して新たな認証レイヤーが追加されています。 **保護の停止 (データの削除を含む)** 操作を実行する際に、セキュリティ PIN の入力を求められます。

### <a name="offline-backup-improvements"></a>オフライン バックアップの改善

MABS v3 UR1 では、Azure Import/Export サービスによってオフライン バックアップのエクスペリエンスが向上しています。 詳細については、[こちら](./backup-azure-backup-server-import-export.md)で最新の手順を参照してください。

>[!NOTE]
>このアップデートでは、MABS の Azure Data Box を使用したオフライン バックアップのプレビューも導入されます。 詳細については、[SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) にお問い合わせください。

### <a name="new-cmdlet-parameter"></a>新しいコマンドレット パラメーター

MABS V3 UR1 には、 **[-CheckReplicaFragmentation]** という新しいパラメーターが追加されています。 新しいパラメーターは、レプリカの断片化の割合を計算するもので、**Copy-DPMDatasourceReplica** コマンドレットに追加されています。

### <a name="32-bit-protection-agent-deprecation"></a>32 ビット保護エージェントの廃止

MABS v3 UR1 では、32 ビット保護エージェントがサポートされなくなりました。 MABS v3 サーバーを UR1 にアップグレードした後は、32 ビット ワークロードを保護することはできません。 既存の 32 ビット保護エージェントは無効状態となり、スケジュールされたバックアップは、**エージェントが無効** であることを示すエラーで失敗します。 それらのエージェントのバックアップ データを保持したい場合、データの保持オプションで保護を停止できます。 それ以外の場合、保護エージェントは削除することができます。

>[!NOTE]
>MABS UR 1 を使用した保護でサポートされるワークロードについては、[最新の保護マトリックス](./backup-mabs-protection-matrix.md)を参照してください。

## <a name="whats-new-in-mabs-v3-rtm"></a>MABS V3 RTM の新機能

Microsoft Azure Backup Server バージョン 3 (MABS V3) は、重大なバグ修正、Windows Server 2019 のサポート、SQL 2017 のサポート、その他の機能と機能強化が含まれています。 MABS V3 の修正されたバグの一覧とインストール手順については、KB 記事 [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3) を参照してください。

MABS V3 には、次の機能が含まれています。

### <a name="volume-to-volume-migration"></a>ボリュームからボリュームへの移行

MABS V2 の Modern Backup Storage (MBS) では、ワークロード対応ストレージが発表されており、ストレージのプロパティに基づいて、特定のワークロードを特定のストレージにバックアップするように構成することができます。 ただし、構成後に、リソースの使用状況を最適化するために、特定のデータ ソースのバックアップを他のストレージに移動する処理が必要になることがあります。 MABS V3 には、[3 つの手順](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842)で、バックアップを移行し、別のボリュームに格納するように構成する機能があります。

### <a name="prevent-unexpected-data-loss"></a>予想外のデータ損失を防ぐ

企業では、複数の管理者のチームが MABS を管理されます。 バックアップへの使用が推奨されるストレージのガイドラインはありますが、バックアップ ストレージとして MABS に不適切なボリュームを指定すると、重要なデータが失われる可能性があります。 MABS V3 では、[これらの PowerShell コマンドレット](./backup-mabs-add-storage.md)を使用して、このようなボリュームをストレージに使用できないボリュームとして構成することで、こうしたシナリオを防ぐことができます。

### <a name="custom-size-allocation"></a>カスタム サイズの割り当て

Modern Backup Storage (MBS) は、必要に応じてストレージの使用量を低下させます。 この処理を行うために、MABS では、保護用に構成されているときにバックアップされるデータのサイズが計算されます。 ただし、ファイル サーバーの場合のように、多数のファイルとフォルダーがまとめてバックアップされる場合は、サイズの計算に時間がかかることがあります。 MABS V3 を使用すると、各ファイルのサイズを計算するのではなく、既定としてボリューム サイズを受け入れるように MABS を構成することができ、時間の節約になります。

### <a name="optimized-cc-for-rct-vms"></a>RCT VM のための最適化された CC

MABS は RCT (Hyper-V のネイティブの変更追跡機能) を使用しているため、VM がクラッシュしたときのシナリオで時間のかかる一貫性チェックの必要性が少なくなります。 RCT には、VSS スナップショットベースのバックアップによって提供される変更追跡よりも優れた回復性があります。 MABS V3 は、一貫性チェック中に変更されたデータのみを転送することで、ネットワークとストレージの消費をさらに最適化します。

### <a name="support-to-tls-12"></a>TLS 1.2 のサポート

TLS 1.2 は Microsoft が提案するセキュリティで保護された通信方法であり、クラス最高レベルの暗号化を使用しています。 MABS は、証明書ベースの認証とクラウド バックアップ用に、MABS と保護されたサーバー間で TLS 1.2 通信をサポートするようになりました。

### <a name="vmware-vm-protection-support"></a>VMware VM 保護のサポート

運用デプロイでは、VMware VM バックアップがサポートされるようになりました。 MABS V3 は、VMware VM 保護のために以下を提供しています。

* vCenter および ESXi 6.5 のサポートと、5.5 および 6.0 のサポート
* VMware VM からクラウドへの自動保護。 新しい VMware VM が保護されたフォルダーに追加されると、ディスクおよびクラウドに対して自動的に保護されます。
* VMware の代替場所の復旧の場合に復旧効率を向上。

### <a name="sql-2017-support"></a>SQL 2017 のサポート

MABS V3 は、SQL 2017 と共に MABS データベースとしてインストールできます。 SQL Server を SQL 2016 から SQL 2017 にアップグレードするか、新規にインストールすることができます。 MABS V3 を使用すると、クラスター化された環境とクラスター化されていない環境の両方の SQL 2017 ワークロードをバックアップすることもできます。

### <a name="windows-server-2019-support"></a>Windows Server 2019 のサポート

MABS V3 は Windows Server 2019 にインストールすることができます。 WS2019 と共に MABS V3 を使用するには、OS を WS2019 にアップグレードしてから MABS V3 をインストールまたはアップグレードするか、WS2016 上に V3 をインストールまたはアップグレードした後に OS をアップグレードする方法があります。

MABS V3 は完全なリリースであり、Windows Server 2016、Windows Server 2019 に直接インストールするか、MABS V2 からアップグレードすることができます。 Backup Server V3 にアップグレードまたはインストールする前に、インストールの前提条件を確認してください。
MABS のインストールまたはアップグレード手順の詳細については、[こちら](./backup-azure-microsoft-azure-backup.md#software-package)を参照してください。

> [!NOTE]
>
> MABS には、System Center Data Protection Manager と同じコード ベースがあります。 MABS v3 は Data Protection Manager 1807 と同等です。 MABS v3 UR1 は Data Protection Manager 2019 UR1 と同等です。

## <a name="next-steps"></a>次のステップ

サーバーを準備する方法、またはワークロードの保護を開始する方法を説明します。

* [Backup Server ワークロードの準備](backup-azure-microsoft-azure-backup.md)
* [Backup Server を使用した VMware サーバーのバックアップ](backup-azure-backup-server-vmware.md)
* [Backup Server を使用した SQL Server のバックアップ](backup-azure-sql-mabs.md)
* [Backup Server での Modern Backup Storage の使用](backup-mabs-add-storage.md)
