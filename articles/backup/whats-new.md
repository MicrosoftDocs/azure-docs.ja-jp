---
title: Azure Backup の新着情報
description: Azure Backup の新機能について説明しています。
ms.topic: conceptual
ms.date: 11/11/2020
ms.openlocfilehash: 48c8c189d956c4003d24511a54975fe86a982832
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102095716"
---
# <a name="whats-new-in-azure-backup"></a>Azure Backup の新着情報

Azure Backup は常に改善が行われ、Azure でのデータの保護を強化する新機能がリリースされています。 これらの新機能により、データ保護が新しいワークロードの種類に拡張され、セキュリティが強化されて、バックアップ データの可用性が向上します。 また、管理、監視、自動化に関する新しい機能も追加されます。

このページをブックマークするか、[こちらで更新情報をサブスクライブする](https://azure.microsoft.com/updates/?query=backup)ことにより、新しいリリースについての詳細を入手できます。

## <a name="updates-summary"></a>更新の概要

- 2021 年 3 月
  - [バックアップ センターの一般提供開始](#backup-center-is-now-generally-available)
  - [Azure Backup のアーカイブ層のサポート (プレビュー)](#archive-tier-support-for-azure-backup-in-preview)
- 2021 年 2 月
  - [Azure BLOB のバックアップ (プレビュー)](#backup-for-azure-blobs-in-preview)
- 2021 年 1 月
  - [Azure ディスク バックアップ (プレビュー)](#azure-disk-backup-in-preview)
  - [カスタマー マネージド キーを使用した保存時の暗号化 (一般提供)](#encryption-at-rest-using-customer-managed-keys)
- 2020 年 11 月
  - [Azure ファイル共有 (AFS) バックアップ用の Azure Resource Manager テンプレート](#azure-resource-manager-template-for-afs-backup)
  - [Azure VM 上の SAP HANA データベースの増分バックアップ (プレビュー段階)](#incremental-backups-for-sap-hana-databases-in-preview)
- 2020 年 9 月
  - [バックアップ センター (プレビュー段階)](#backup-center-in-preview)
  - [Azure Database for PostgreSQL のバックアップ (プレビュー段階)](#backup-azure-database-for-postgresql-in-preview)
  - [ディスクの選択的なバックアップと復元](#selective-disk-backup-and-restore)
  - [Azure VM 上の SQL Server データベースと SAP HANA データベースのリージョン間の復元 (プレビュー段階)](#cross-region-restore-for-sql-server-and-sap-hana-in-preview)
  - [最大 32 個のディスクを使用する VM のバックアップのサポート (一般提供)](#support-for-backup-of-vms-with-up-to-32-disks)
  - [Azure VM での SQL のバックアップ構成エクスペリエンスの簡素化](#simpler-backup-configuration-for-sql-in-azure-vms)
  - [RHEL の Azure 仮想マシンでの SAP HANA のバックアップ (プレビュー段階)](#backup-sap-hana-in-rhel-azure-virtual-machines-in-preview)
  - [バックアップ データ用のゾーン冗長ストレージ (ZRS) (プレビュー段階)](#zone-redundant-storage-zrs-for-backup-data-in-preview)
  - [Azure VM での SQL Server および SAP HANA ワークロードの論理的な削除](#soft-delete-for-sql-server-and-sap-hana-workloads)

## <a name="backup-center-is-now-generally-available"></a>バックアップ センターの一般提供開始

バックアップ センターは、1 つの中央コンソールからバックアップ管理を検出、管理、監視、操作、最適化できるようにすることで、データ保護の管理を容易にします。

詳細については、「[バックアップ センターの概要](backup-center-overview.md)」を参照してください。

## <a name="archive-tier-support-for-azure-backup-in-preview"></a>Azure Backup のアーカイブ層のサポート (プレビュー)

Azure Backup で、Azure 仮想マシンと Azure 仮想マシンの SQL Server にアーカイブ層を利用できるようになったため、長期保存のバックアップのコストを削減できるようになりました。

詳細については、「[アーカイブ層のサポート (プレビュー)](archive-tier-support.md)」を参照してください。

## <a name="backup-for-azure-blobs-in-preview"></a>Azure BLOB のバックアップ (プレビュー)

BLOB の運用バックアップは、管理されたローカル データ保護ソリューションです。これを使用すると、破損、BLOB の削除、誤ったストレージ アカウントの削除などのさまざまなデータ損失のシナリオからブロック BLOB を保護できます。 データはソース ストレージ アカウント自体にローカルに格納され、必要に応じて選択した時点に復旧できます。 これにより、BLOB を保護するためのシンプルで安全かつコスト効率の高い手段が提供されます。

BLOB の運用バックアップは、他のバックアップ管理機能の中でも特にバックアップ センターと統合され、大規模なバックアップを管理、監視、運用、分析するための単一のウィンドウを提供します。

詳細については、「[Azure BLOB の運用バックアップの概要 (プレビュー)](blob-backup-overview.md)」を参照してください。

## <a name="azure-disk-backup-in-preview"></a>Azure ディスク バックアップ (プレビュー)

Azure ディスク バックアップは、スナップショットの定期的な作成を自動化し、バックアップ ポリシーを使用して構成された期間にわたってそのスナップショットを保持することで [Azure マネージド ディスク](../virtual-machines/managed-disks-overview.md)のスナップショット ライフサイクル管理を提供する、ターンキー ソリューションを提供します。 インフラストラクチャ コストなしでディスク スナップショットを管理でき、カスタム スクリプトも管理費用も必要ありません。 これは、1 日に複数のバックアップをサポートする[増分スナップショット](../virtual-machines/disks-incremental-snapshots.md)を使用してマネージド ディスクの特定時点のバックアップを作成する、クラッシュ整合性バックアップ ソリューションです。 また、エージェントレスのソリューションでもあり、運用アプリケーションのパフォーマンスに影響しません。 実行中の Azure 仮想マシンに OS とデータ ディスク (共有ディスクを含む) が現在接続されているかどうかにかかわらず、この両方のバックアップと復元がサポートされます。

詳細については、「[Azure ディスク バックアップ (プレビュー)](disk-backup-overview.md)」を参照してください。

## <a name="encryption-at-rest-using-customer-managed-keys"></a>カスタマー マネージド キーを使用した保存時の暗号化

カスタマー マネージド キーを使用した保存時の暗号化サポートの一般提供が開始されました。 これにより、Azure Key Vault に格納されている独自のキーを使用して、Recovery Services コンテナー内のバックアップ データを暗号化できます。 Recovery Services コンテナーでバックアップの暗号化に使用される暗号化キーは、ソースの暗号化に使用されるものとは異なる場合があります。 データは、AES 256 ベースのデータ暗号化キー (DEK) を使用して保護され、このキーは、Key Vault に格納されているご自身のキーを使用して保護されます。 プラットフォーム マネージド キー (既定で利用可能) を使用した暗号化と比較すると、キーをより細かく制御できるため、コンプライアンスのニーズをより適切に満たすことができます。

詳細については、「[カスタマー マネージド キーを使用したバックアップ データの暗号化](encryption-at-rest-with-cmk.md)」を参照してください。

## <a name="azure-resource-manager-template-for-afs-backup"></a>AFS バックアップ用の Azure Resource Manager テンプレート

Azure Backup では、Azure Resource Manager (ARM) テンプレートを使用した既存の Azure ファイル共有のバックアップの構成をサポートするようになりました。 このテンプレートでは、Recovery Services コンテナーとバックアップ ポリシーの適切な詳細を指定することによって、既存の Azure ファイル共有の保護を構成できます。 必要に応じて、新しい Recovery Services コンテナーとバックアップ ポリシーが作成され、ファイル共有を含むストレージ アカウントが Recovery Services コンテナーに登録されます。

詳細については、「[Azure Backup 用 Azure Resource Manager テンプレート](backup-rm-template-samples.md)」を参照してください。

## <a name="incremental-backups-for-sap-hana-databases-in-preview"></a>SAP HANA データベースの増分バックアップ (プレビュー段階)

Azure VM でホストされている SAP HANA データベースの増分バックアップが、Azure Backup でサポートされるようになりました。 これにより、SAP HANA データのバックアップを、いっそう高速に、コスト効率よく行うことができます。

詳細については、[バックアップポリシーの作成時に使用できるさまざまなオプション](sap-hana-faq-backup-azure-vm.md#policy)および [SAP HANA データベース用のバックアップ ポリシーを作成する方法](tutorial-backup-sap-hana-db.md#creating-a-backup-policy)に関するページを参照してください。

## <a name="backup-center-in-preview"></a>バックアップ センター (プレビュー段階)

Azure Backup で、中央コンソールからバックアップ資産全体を管理するための新しいネイティブ管理機能を使用できるようになりました。 バックアップ センターを使用すると、Azure のネイティブ管理エクスペリエンスと一貫性のある統合された方法で、大規模なデータ保護を監視、操作、制御、最適化できます。

バックアップ センターからは、Azure Lighthouse を使用して、サブスクリプション、場所、リソース グループ、コンテナー、さらにはテナントまで横断的に、インベントリの集約されたビューを取得できます。 バックアップ センターはアクション センターでもあり、バックアップの構成、復元、ポリシーやコンテナーの作成など、バックアップに関連するすべてのアクティビティを、1 つの場所からトリガーできます。 さらに、Azure Policy とのシームレスな統合により、環境の管理やコンプライアンスの追跡を、バックアップの観点から行うことができるようになりました。 また、Azure Backup に固有の組み込みの Azure ポリシーを使用して、大規模なバックアップを構成することもできます。

詳細については、「[バックアップ センターの概要](backup-center-overview.md)」を参照してください。

## <a name="backup-azure-database-for-postgresql-in-preview"></a>Azure Database for PostgreSQL のバックアップ (プレビュー段階)

Azure Backup と Azure Database Services の連携により、Azure PostgreSQL 向けのエンタープライズ クラスのバックアップ ソリューションが構築されます (現在はプレビュー)。 お客様が管理するバックアップ ポリシーを使用して、データの保護とコンプライアンスのニーズを満たすことができるようになり、バックアップを最大 10 年間保持できます。 これにより、個々のデータベース レベルでバックアップと復元の操作の管理をきめ細かく制御できます。 同様に、PostgreSQL の異なるバージョン間で、または Blob Storage にも、簡単に復元できます。

詳細については、[Azure Database for PostgreSQL のバックアップ](backup-azure-database-postgresql.md)に関するページを参照してください。

## <a name="selective-disk-backup-and-restore"></a>ディスクの選択的なバックアップと復元

Azure Backup では、仮想マシン バックアップ ソリューションを使用して、VM 内のすべてのディスク (オペレーティング システムとデータ) をまとめてバックアップすることがサポートされています。 現在、選択的なディスク バックアップと復元の機能を使用して、VM 内のデータ ディスクのサブセットをバックアップできるようになっています。 これにより、バックアップと復元のニーズに応じた効率的で費用対効果の高いソリューションが提供されます。 各復旧ポイントには、バックアップ操作に含まれるディスクだけが含まれます。

詳細については、「[Azure 仮想マシンの選択的なディスク バックアップと復元](selective-disk-backup-restore.md)」を参照してください。

## <a name="cross-region-restore-for-sql-server-and-sap-hana-in-preview"></a>SQL Server と SAP HANA のリージョン間の復元 (プレビュー段階)

リージョン間の復元の導入により、セカンダリ リージョンで復元を自由に開始できるようになり、環境のプライマリ リージョンでの実際のダウンタイムの問題が軽減されます。 これにより、お客様はセカンダリ リージョンでの復元を完全に制御できます。 そのような復元の場合、セカンダリ リージョンにレプリケートされたバックアップ データが Azure Backup によって使用されます。

Azure 仮想マシンのリージョン間の復元に加えて、この機能は、Azure 仮想マシン内の SQL データベースと SAP HANA データベースの復元にも拡張されています。

詳細については、[SQL データベースのリージョン間の復元](restore-sql-database-azure-vm.md#cross-region-restore)および [SAP HANA データベースのリージョン間の復元](sap-hana-db-restore.md#cross-region-restore)に関するページを参照してください。

## <a name="support-for-backup-of-vms-with-up-to-32-disks"></a>最大 32 個のディスクを使用する VM のバックアップのサポート

これまでは、Azure Backup でサポートされるマネージド ディスクは VM ごとに 16 個でした。 現在は、VM あたり最大 32 個のマネージド ディスクのバックアップが、Azure Backup でサポートされるようになっています。

詳細については、[VM のストレージのサポート マトリックス](backup-support-matrix-iaas.md#vm-storage-support)に関するページを参照してください。

## <a name="simpler-backup-configuration-for-sql-in-azure-vms"></a>Azure VM 内の SQL のバックアップ構成の簡素化

Azure VM 内の SQL Server のバックアップの構成が、Azure portal の VM ペインに統合されたインライン バックアップ構成により、いっそう簡単になりました。 わずか数ステップで、SQL Server のバックアップを有効にして、既存のすべてのデータベースと、今後追加されるデータベースを保護することができます。

詳細については、「[VM ウィンドウから SQL Server をバックアップする](backup-sql-server-vm-from-vm-pane.md)」を参照してください。

## <a name="backup-sap-hana-in-rhel-azure-virtual-machines-in-preview"></a>RHEL の Azure 仮想マシンでの SAP HANA のバックアップ (プレビュー段階)

Azure Backup は、Azure 用のネイティブ バックアップ ソリューションであり、SAP によって認定された BackInt です。 SAP HANA の実行に最も広く使用されている Linux オペレーティング システムの 1 つである Red Hat Enterprise Linux (RHEL) のサポートが、Azure Backup に追加されました。

詳細については、[SAP HANA データベースのバックアップ シナリオのサポート マトリックス](sap-hana-backup-support-matrix.md#scenario-support)に関するページを参照してください。

## <a name="zone-redundant-storage-zrs-for-backup-data-in-preview"></a>バックアップ データ用のゾーン冗長ストレージ (ZRS) (プレビュー段階)

Azure Storage を使用すると、さまざまな冗長性オプションにより、ハイ パフォーマンス、高可用性、高データ回復性の優れたバランスが提供されます。 Azure Backup を使用すると、バックアップをローカル冗長ストレージ (LRS) と geo 冗長ストレージ (GRS) に格納するオプションにより、これらの利点をデータのバックアップまで拡張できます。 現在は、ゾーン冗長ストレージ (ZRS) のサポートの追加により、持続性オプションが追加されています。

詳細については、[Recovery Services コンテナー用のストレージ冗長性の設定](backup-create-rs-vault.md#set-storage-redundancy)に関するページを参照してください。

## <a name="soft-delete-for-sql-server-and-sap-hana-workloads"></a>SQL Server および SAP HANA ワークロードの論理的な削除

マルウェア、ランサムウェア、侵入などのセキュリティ問題への懸念が高まっています。 これらのセキュリティ問題は、金銭とデータの両方の観点からコストがかかる可能性があります。 このような攻撃から保護するため、Azure Backup には、削除後もバックアップ データを保護するためのセキュリティ機能が用意されています。

このような機能の 1 つに、論理的な削除があります。 論理的な削除を使用すると、悪意のあるアクターによってバックアップが削除 (またはバックアップ データが誤って削除) された場合でも、バックアップ データは追加で 14 日間保持されるので、データを失うことなくバックアップ項目を回復できます。 バックアップ データが "論理的な削除" 状態にあるこの追加の 14 日間のリテンション期間中は、お客様にコストは発生しません。

現在は、Azure VM の論理的な削除のサポートに加えて、Azure VM の SQL Server と SAP HANA ワークロードも、論理的な削除によって保護されています。

詳細については、「[Azure VM での SQL Server の論理的な削除、および Azure VM ワークロードの SAP HANA の論理的な削除](soft-delete-sql-saphana-in-azure-vm.md)」を参照してください。

## <a name="next-steps"></a>次のステップ

- [Azure Backup のガイダンスとベスト プラクティス](guidance-best-practices.md)