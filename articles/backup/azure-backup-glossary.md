---
title: Azure Backup 用語集
description: この記事では、Azure Backup を使用する際に役立つ用語を定義します。
ms.topic: conceptual
ms.date: 12/21/2020
ms.openlocfilehash: fb46415c8bdb463556d57004e37d741c1b9a9b57
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502027"
---
# <a name="azure-backup-glossary"></a>Azure Backup 用語集

この用語集は、Azure Backup を使用する場合に役立ちます。

>[!NOTE]
>
> - "(ワークロード固有の用語)" というプレフィックスでマークされている用語は、Azure Backup がサポートするワークロードの特定のサブセットのコンテキストにのみ関連する用語を指します。
> - Azure Backup ドキュメントで一般的に使用されているものの、他の Azure サービスを指す用語については、関連する Azure サービスのドキュメントへの直接リンクが提供されています。

## <a name="afs-azure-file-shares"></a>AFS (Azure ファイル共有)

[Azure Files のドキュメント](../storage/files/storage-files-introduction.md)を参照してください。

## <a name="alternate-location-recovery"></a>別の場所の復旧

復旧ポイントからバックアップが作成された元の場所以外の場所に対して実行される復旧。 Azure VM バックアップを使用する場合、これは、バックアップが作成された元のサーバーとは別のサーバーに VM を復元することを意味します。 Azure ファイル共有のバックアップを使用する場合、これは、バックアップされたファイル共有とは異なるファイル共有にデータを復元することを意味します。

## <a name="application-consistent-backup"></a>アプリケーション整合性バックアップ

(ワークロード固有の用語)

アプリケーション整合性バックアップによって、メモリの内容と保留中の I/O 操作がキャプチャされます。 アプリ整合性スナップショットでは、バックアップが発生する前にアプリ データの整合性を保証するために、[VSS](#vss-windows-volume-shadow-copy-service) ライター (または Linux の事前/事後スクリプト) が使用されます。 [詳細については、こちらを参照してください](backup-azure-vms-introduction.md)。

## <a name="azure-resource-manager-arm-templates"></a>Azure Resource Manager (ARM) テンプレート

[ARM テンプレートのドキュメント](../azure-resource-manager/templates/overview.md)を参照してください。

## <a name="autoprotection-for-databases"></a>自動保護 (データベースの場合)

(ワークロード固有の用語)

自動保護は、スタンドアロンの SQL Server インスタンスまたは SQL Server Always On 可用性グループのすべてのデータベースを自動的に保護できる機能です。 これにより、既存のデータベースのバックアップが有効になるだけでなく、将来追加されるすべてのデータベースも保護されます。

## <a name="availability-storage-replication-types"></a>可用性 (ストレージ レプリケーションの種類)

Azure Backup では、ストレージとデータの高可用性を維持するため、次の 3 種類のレプリケーションが提供されます。

### <a name="lrs"></a>LRS

[ローカル冗長ストレージ (LRS)](../storage/common/storage-redundancy.md#locally-redundant-storage) では、データセンターのストレージ スケール ユニットにバックアップ データが 3 回レプリケートされます (バックアップ データのコピーが 3 つ作成されます)。 バックアップ データのすべてのコピーは、同じリージョン内に存在します。 LRS は、ローカル ハードウェアの障害からバックアップ データを保護するための低コストのオプションです。

### <a name="grs"></a>GRS

[geo 冗長ストレージ (GRS)](../storage/common/storage-redundancy.md#geo-redundant-storage) は、既定の推奨レプリケーション オプションです。 GRS では、セカンダリ リージョン (ソース データのプライマリの場所から数百マイル離れた場所) にバックアップ データがレプリケートされます。 GRS は LRS よりもコストがかかりますが、地域的な障害が発生しても、高いレベルの持続性がバックアップ データに提供されます。

>[!NOTE]
>リージョン間の復元機能が有効になっている GRS コンテナーの場合、バックアップ ストレージが GRS から RA-GRS (読み取りアクセス geo 冗長ストレージ) にアップグレードされます。

### <a name="zrs"></a>ZRS

[ゾーン冗長ストレージ (ZRS)](../storage/common/storage-redundancy.md#zone-redundant-storage) は、[可用性ゾーン](../availability-zones/az-overview.md#availability-zones)内のバックアップ データをレプリケートし、同じリージョン内でバックアップ データ所在地と回復性を保証します。 そのため、[データ所在地](https://azure.microsoft.com/resources/achieving-compliant-data-residency-and-security-with-azure/)を必要とするクリティカルなワークロードは、ZRS にバックアップすることができます。

## <a name="azure-command-line-interface-cli"></a>Azure コマンド ライン インターフェイス (CLI)

[Azure CLI のドキュメント](/cli/azure/what-is-azure-cli)を参照してください。

## <a name="azure-policy"></a>Azure Policy

[Azure Policy のドキュメント](../governance/policy/overview.md)を参照してください。

## <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell のドキュメント](/powershell/azure/)を参照してください。

## <a name="azure-resource-manager-arm"></a>Azure Resource Manager (ARM)

[Azure Resource Manager のドキュメント](../azure-resource-manager/management/overview.md)を参照してください。

## <a name="azure-disk-encryption-ade"></a>Azure Disk Encryption (ADE)

[Azure Disk Encryption のドキュメント](../security/fundamentals/azure-disk-encryption-vms-vmss.md)を参照してください。

## <a name="backend-storage--cloud-storage--backup-storage"></a>バックエンド ストレージ/クラウド ストレージ/バックアップ ストレージ

バックアップ インスタンスによって使用される実際のストレージ。 バックアップ インスタンスに存在するすべての保有ポイント (バックアップとアイテム保持ポリシーによって定義) のサイズが含まれます。

## <a name="bare-metal-backup"></a>ベア メタル バックアップ

オペレーティング システム ファイルとクリティカルなボリューム上のすべてのデータ (ユーザー データを除く) のバックアップ。 定義上、ベア メタル バックアップには、システム状態のバックアップが含まれます。 これは、コンピューターが起動せず、すべてを復旧する必要があるときの保護を提供します。 [詳細については、こちらを参照してください](backup-mabs-system-state-and-bmr.md)。

## <a name="backup-extensions--vm-extensions"></a>バックアップ拡張機能/VM 拡張機能

(Azure VM ワークロードの種類に固有)

Azure 仮想マシン (VM) 拡張機能は、Azure VM でのデプロイ後の構成と自動タスクを提供する複数の小さなアプリケーションです。 Azure Backup では、マシンで実行されている Azure VM エージェントに拡張機能をインストールすることで、Azure VM がバックアップされます。 Azure Backup はこれらの拡張機能を自動的に管理するため、ユーザーはこれらの拡張機能を手動で更新する必要がありません。

## <a name="backup-instance--backup-item"></a>バックアップ インスタンス/バックアップ項目

特定のバックアップとアイテム保持ポリシーを使用してコンテナーにバックアップされたデータソースによって、バックアップ インスタンスまたはバックアップ項目が構成されます。

## <a name="backup-rule--backup-policy"></a>バックアップ規則/バックアップ ポリシー

バックアップ規則は、データソースでバックアップを実行するタイミングと頻度を指定する、ユーザー定義の規則です。 ワークロードの種類によっては、バックアップ ポリシーによって、データソースに適用されるスナップショット方法 (完全、増分、差分) を指定する方法も用意されています。 バックアップ ポリシーは、多くの場合、バックアップ規則と保持規則の組み合わせとして作成されます。

## <a name="backup-vault"></a>バックアップ資格情報コンテナー

*Microsoft.DataProtection/BackupVaults* 型の Azure Resource Manager リソースです。 現時点では、バックアップ コンテナーは、PostgreSQL サーバーに Azure データベースをバックアップするために使用されます。 [バックアップ コンテナーに関する詳細情報を参照してください](backup-azure-recovery-services-vault-overview.md)。

## <a name="bcdr-business-continuity-and-disaster-recovery"></a>BCDR (事業継続とディザスター リカバリー)

BCDR には、計画された、または計画外のサービスや Azure の停止時にアプリとワークロードを確実に稼働させ、ビジネスの中断を最小限に抑えるために、組織が採用する必要がある一連のプロセスが含まれます。 Azure が提供する、適切な BCDR 戦略の作成に役立つさまざまなサービスについて、[詳細を確認してください](https://azure.microsoft.com/solutions/backup-and-disaster-recovery/#overview)。

## <a name="churn"></a>チャーン

2 つの連続するバックアップの間でバックアップされているデータの変更の割合。 これは、新しいデータの追加、または既存のデータの変更または削除が原因である可能性があります。

## <a name="crash-consistent-backup"></a>クラッシュ整合性バックアップ

(ワークロード固有の用語)

クラッシュ整合性スナップショットは、通常、バックアップ時に Azure VM がシャットダウンした場合に発生します。 バックアップ時にディスクに既に存在していたデータのみが、キャプチャされバックアップされます。 [詳細については、こちらを参照してください](backup-azure-vms-introduction.md#snapshot-consistency)。

## <a name="cross-region-restore-crr"></a>リージョンをまたがる復元 (CRR)

[復元オプション](backup-azure-arm-restore-vms.md#restore-options)の 1 つである、リージョンをまたがる復元 (CRR) を使用すると、セカンダリ リージョン ([Azure のペアになっているリージョン](../best-practices-availability-paired-regions.md#what-are-paired-regions)) でバックアップ項目を復元できます。

## <a name="data-box"></a>Data Box

[Data Box のドキュメント](../databox/data-box-overview.md)を参照してください。

## <a name="datasource"></a>データソース

バックアップの候補であるリソース (Azure リソース、プロキシ リソース、またはオンプレミスのリソース)。 たとえば、Azure VM や Azure ファイル共有などです。

## <a name="dpm-data-protection-manager"></a>DPM (Data Protection Manager)

(ワークロード固有の用語)

[DPM のドキュメント](/system-center/dpm/dpm-overview)を参照してください。

## <a name="expressroute"></a>ExpressRoute

[ExpressRoute のドキュメント](../expressroute/expressroute-introduction.md)を参照してください。

## <a name="file-system-consistent-backup"></a>ファイル システム整合性バックアップ

(ワークロード固有の用語)

ファイル システム整合性バックアップでは、同時にすべてのファイルのスナップショットを作成することで整合性が提供されます。 [詳細については、こちらを参照してください](backup-azure-vms-introduction.md#snapshot-consistency)。

## <a name="frontend-storage--source-size"></a>フロントエンド ストレージ/ソース サイズ

データソースにバックアップするデータのサイズ。 データソースのフロントエンドのサイズによって、その[保護されたインスタンス](#protected-instance)の数が決まります。

## <a name="full-backup"></a>完全バックアップ

完全バックアップでは、すべてのバックアップでデータ ソース全体のコピーが格納されます。

## <a name="gfs-backup-policy"></a>GFS バックアップ ポリシー

GFS (Grandfather-father-son) バックアップ ポリシーは、毎日のバックアップ スケジュールに加えて、週単位、月単位、および年単位のバックアップ スケジュールを定義できるようにするものです。 週単位のバックアップは "son (息子)" と呼ばれ、月単位のバックアップは "father (父)" と呼ばれ、年単位のバックアップは "grandfather (祖父)" と呼ばれます。 これらのバックアップ コピーの各セットは、さまざまな期間で保持されるように構成できます。これにより、バックアップ コピーの保持期間の選択をさらにカスタマイズできます。 GFS ポリシーは、より保存効率の高い方法でバックアップを長期間保持するのに役立ちます。

## <a name="iaas-vms--azure-vms"></a>IaaS VM/Azure VM

[Azure VM のドキュメント](../virtual-machines/index.yml)を参照してください。

## <a name="incremental-backup"></a>増分バックアップ

増分バックアップでは、前回のバックアップ以降に変更されたブロックのみが格納されます。

## <a name="instant-restore"></a>インスタント復元

(ワークロード固有の用語) インスタント復元では、コンテナー内のスナップショットのコピーからではなく、バックアップ スナップショットから直接マシンが復元されます。 インスタント復元は、コンテナーからの復元よりも高速です。 使用できるインスタント復元ポイントの数は、スナップショット用に構成された保持期間によって異なります。 現時点では、Azure VM のバックアップにのみ適用されます。

## <a name="iops"></a>IOPS

1 秒あたりの入出力処理。

## <a name="item-level-restore"></a>項目レベルの復元

(ワークロード固有の用語)

マシン内の復旧ポイントからの個々のファイルまたはフォルダーの復元。

## <a name="job"></a>ジョブ

ユーザーまたは Azure Backup サービスによって作成されるバックアップ関連タスク。 ジョブは、スケジュールする、またはオンデマンド (アドホック) にすることができます。 ジョブには、バックアップ、復元、保護の構成など、さまざまな種類があります。 [ジョブに関する詳細情報を参照してください](backup-azure-monitoring-built-in-monitor.md#backup-jobs-in-recovery-services-vault)。

## <a name="mabs--azure-backup-server"></a>MABS/Azure Backup Server

(ワークロード固有の用語)

Azure Backup Server を使用すると、単一のコンソールから Hyper-V VM、Microsoft SQL Server、SharePoint Server、Microsoft Exchange、Windows クライアントなどのアプリケーションのワークロードを保護することができます。 DPM からワークロードのバックアップ機能の多くを継承していますが、いくつかの違いがあります。 [詳細情報](backup-azure-microsoft-azure-backup.md)

## <a name="managed-disks"></a>マネージド ディスク

[マネージド ディスクのドキュメント](../virtual-machines/managed-disks-overview.md)を参照してください。

## <a name="mars-agent"></a>MARS エージェント

(ワークロード固有の用語)

MARS エージェントは、**Azure Backup エージェント** や **Recovery Services エージェント** とも呼ばれ、オンプレミスのマシンや Azure VM のデータを Azure の Recovery Services バックアップ コンテナーにバックアップするために Azure Backup によって使用されます。 [詳細については、こちらを参照してください](backup-support-matrix-mars-agent.md)。

## <a name="nsg-network-security-group"></a>NSG (ネットワーク セキュリティ グループ)

[NSG のドキュメント](../virtual-network/network-security-groups-overview.md)を参照してください。

## <a name="offline-seeding"></a>オフライン シード処理

オフラインシード処理とは、ネットワーク帯域幅を使用せずに、初期 (完全) バックアップをオフラインで転送するプロセスを指します。 バックアップ データを物理ストレージ デバイスにコピーし、それが近くの Azure データセンターに発送され、Recovery Services コンテナーにアップロードされるというメカニズムが提供されます。 [詳細については、こちらを参照してください](offline-backup-overview.md)。

## <a name="on-demand-backup--ad-hoc-backup"></a>オンデマンド バックアップ/アドホック バックアップ

ユーザーが 1 回限りのニーズに基づいてトリガーするバックアップ ジョブであり、リソースに対して構成されているバックアップ スケジュール (ポリシー) には基づきません。

## <a name="original-location-recovery-olr"></a>元の場所への復旧 (OLR)

復元ポイントから、バックアップが作成されたソースの場所に対して実行される復旧。復旧ポイントに格納されている状態によって、それが置き換えられます。 Azure VM バックアップを使用する場合、これは、バックアップが作成された元のサーバーに VM を復元することを意味します。 Azure ファイル共有のバックアップを使用する場合、これは、バックアップされたファイル共有にデータを復元することを意味します。

## <a name="passphrase"></a>Passphrase

(ワークロード固有の用語)

パスフレーズは、MARS エージェントを使用して Azure との間でオンプレミスまたはローカル マシンをバックアップまたは復元するときに、データを暗号化および復号化するために使用されます。

## <a name="private-endpoint"></a>プライベート エンドポイント

[プライベート エンドポイントのドキュメント](../private-link/private-endpoint-overview.md)を参照してください。

## <a name="protected-instance"></a>保護されたインスタンス

保護されたインスタンスとは、Azure へのバックアップを構成するために使用するコンピューター、物理または仮想サーバーを指します。  **課金の観点** からは、マシンの保護されたインスタンスの数はそのフロントエンドのサイズと相関しています。 このため、1 つのバックアップ インスタンス (Azure にバックアップされた VM など) が、フロントエンドのサイズに応じて、複数の保護されたインスタンスに対応する可能性があります。 [詳細については、こちらを参照してください](https://azure.microsoft.com/pricing/details/backup/)。

## <a name="rbac-role-based-access-control"></a>RBAC (ロールベースのアクセス制御)

[RBAC のドキュメント](../role-based-access-control/overview.md)を参照してください。

## <a name="recovery-point-restore-point-retention-point--point-in-time-pit"></a>復旧ポイント/復元ポイント/保有ポイント/ポイントインタイム (PIT)

バックアップされている元のデータのコピーです。 保有ポイントはタイムスタンプに関連付けられているので、これを使用して特定の時点に項目を復元できます。

## <a name="recovery-services-vault"></a>Recovery Services コンテナー

*Microsoft.RecoveryServices/vaults* 型の Azure Resource Manager リソースです。 現時点では、Recovery Services コンテナーを使用して、次のワークロードがバックアップされます: Azure VM、Azure VM 内の SQL、Azure VM 内の SAP HANA、Azure ファイル共有。 また、MARS、Azure Backup Server (MABS)、および System Center DPM を使用して、オンプレミスのワークロードをバックアップするためにも使用されます。 [Recovery Services コンテナーに関する詳細情報を参照してください](backup-azure-recovery-services-vault-overview.md)。

## <a name="resource-group"></a>Resource group

[Azure Resource Manager のドキュメント](../azure-resource-manager/management/manage-resource-groups-portal.md#what-is-a-resource-group)を参照してください。

## <a name="rest-api"></a>REST API

[Azure REST API のドキュメント](/rest/api/azure/)を参照してください。

## <a name="retention-rule"></a>保持規則

バックアップを保持する期間を指定するユーザー定義の規則。

## <a name="rpo-recovery-point-objective"></a>RPO (復旧ポイントの目標)

RPO は、データ損失のシナリオで想定されるデータ損失の最大値を示します。 これは、バックアップの頻度によって決まります。

## <a name="rto-recovery-time-objective"></a>RTO (目標復旧時間)

RTO は、データ損失シナリオの後、最後の使用可能な時点にデータが復元されるまでに想定される最大時間を示します。

## <a name="scheduled-backup"></a>スケジュールされたバックアップ

指定された項目に対して構成されたバックアップ ポリシーによって自動的にトリガーされるバックアップ ジョブ。

## <a name="secondary-region--paired-region"></a>セカンダリ リージョン/ペアになっているリージョン

リージョン ペアは、同じ地域内の 2 つのリージョンで構成されます。 1 つはプライマリ リージョンで、もう 1 つはセカンダリ リージョンです。 一部の Azure サービス (GRS 設定を使用した Azure Backup を含む) は、ビジネスの継続性を確保し、データの損失を防ぐために、ペアになっているリージョンを使用します。 [詳細については、こちらを参照してください](../best-practices-availability-paired-regions.md)。

## <a name="soft-delete"></a>論理的な削除

論理的な削除は、バックアップ データが誤って削除されるのを防ぐのに役立つ機能です。 論理的な削除を使用すると、悪意のあるアクターによってバックアップが削除 (またはバックアップ データが誤って削除) された場合でも、バックアップ データはさらに一定期間保持されるので、データを失うことなくバックアップ項目を復旧できます。 [詳細については、こちらを参照してください](backup-azure-security-feature-cloud.md)。

## <a name="snapshot"></a>スナップショット

スナップショットは、仮想ハード ドライブ (VHD) または Azure ファイル共有の完全な読み取り専用コピーです。 [ディスク スナップショット](../virtual-machines/windows/snapshot-copy-managed-disk.md)および[ファイル スナップショット](../storage/files/storage-snapshots-files.md)に関する詳細情報を参照してください。

## <a name="storage-account"></a>ストレージ アカウント

[ストレージ アカウントのドキュメント](../storage/common/storage-account-overview.md)を参照してください。

## <a name="subscription"></a>サブスクリプション

Azure サブスクリプションは、Azure でリソースをプロビジョニングするために使用される論理コンテナーです。 仮想マシン (VM) やデータベースなど、すべてのリソースの詳細が保持されます。

## <a name="system-state-backup"></a>システム状態のバックアップ

(ワークロード固有の用語)

オペレーティング システム ファイルをバックアップします。 このバックアップでは、コンピューターの起動時に回復を行えます。ただし、システム ファイルとレジストリは失われます。 [詳細については、こちらを参照してください](backup-mabs-system-state-and-bmr.md)。

## <a name="tenant"></a>Tenant

テナントは、組織を表したものです。 これは、組織やアプリの開発者が、Azure、Microsoft Intune、または Microsoft 365 へのサインアップのような Microsoft とのリレーションシップを作成するときに受け取る、Azure AD の専用インスタンスです。

## <a name="tier"></a>レベル

現在のところ、Azure Backup では次のバックアップ ストレージ層がサポートされています。

### <a name="snapshot-tier"></a>スナップショット層

(ワークロード固有の用語) VM バックアップの最初の段階では、作成されたスナップショットはディスクと共に保存されます。 この形態のストレージはスナップショット層と呼ばれています。 スナップショット層の復元は (コンテナーから復元するより) 速くなります。復元を始動させる前にスナップショットがコンテナーからコピーされるのを待つ時間がなくなるためです。

### <a name="vault-standard-tier"></a>Vault-Standard 層

Azure Backup でサポートされているすべてのワークロードのバックアップ データは、Azure Backup で管理されるストレージ アカウントの自動スケーリング セットであるバックアップ ストレージを保持するコンテナーに保存されます。 Vault-Standard 層は、Microsoft が管理するテナントにバックアップ データのコピーを隔離保存することを可能にするオンライン ストレージ層です。つまり、保護層が一段厚くなります。 スナップショット層がサポートされるワークロードの場合、スナップショット層と Vault-Standard 層の両方でバックアップ データがコピーされます。 Vault-Standard 層によって、バックアップ対象のデータソースが削除されたり、危険にさらされたりした場合でも、バックアップ データを確実に利用できます。

## <a name="unmanaged-disk"></a>アンマネージド ディスク

[アンマネージド ディスクのドキュメント](../storage/common/storage-disaster-recovery-guidance.md#azure-unmanaged-disks)を参照してください。

## <a name="vault"></a>コンテナー

バックアップ データを格納する Azure のストレージ エンティティ。 また、RBAC と課金の単位でもあります。 現在、コンテナーには、Recovery Services コンテナーとバックアップ コンテナーの 2 種類があります。

## <a name="vault-credentials"></a>コンテナー資格情報

コンテナー資格情報ファイルは、コンテナーごとにポータルによって生成される証明書です。 これは、オンプレミス　サーバーをコンテナーに登録するときに使用されます。 [詳細については、こちらを参照してください](backup-azure-dpm-introduction.md)。

## <a name="vnet-virtual-network"></a>VNET (Virtual Network)

[VNET のドキュメント](../virtual-network/virtual-networks-overview.md)を参照してください。

## <a name="vss-windows-volume-shadow-copy-service"></a>VSS (Windows ボリューム シャドウ コピー サービス)

[VSS のドキュメント](/windows-server/storage/file-server/volume-shadow-copy-service)を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Backup の概要](backup-overview.md)
- [Azure Backup のアーキテクチャとコンポーネント](backup-architecture.md)