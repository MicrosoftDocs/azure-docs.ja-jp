---
title: Recovery Services コンテナーの概要
description: Recovery Services コンテナーの概要です。
ms.topic: conceptual
ms.date: 08/17/2020
ms.openlocfilehash: 0ed37446e1ccf0780f924143c8f063964adf0004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755127"
---
# <a name="recovery-services-vaults-overview"></a>Recovery Services コンテナーの概要

この記事では、Recovery Services コンテナーの機能について説明します。 Recovery Services コンテナーは、データを格納する Azure のストレージ エンティティです。 データは通常、データのコピーであるか、仮想マシン (VM)、ワークロード、サーバー、ワークステーションのいずれかの構成情報です。 Recovery Services コンテナーを使用すると、IaaS VM (Linux または Windows) や Azure SQL データベースなどのさまざまな Azure サービスのバックアップ データを保持できます。 Recovery Services コンテナーは、System Center DPM、Windows Server、Azure Backup Server などをサポートします。 Recovery Services コンテナーでは、管理オーバーヘッドを最小限に抑えながら、バックアップ データを簡単に整理できます。 Recovery Services コンテナーは、Azure の Azure Resource Manager モデルに基づいていて、以下のような機能を提供します。

- **強化されたバックアップ データの保護機能**:Recovery Services コンテナーの場合、Azure Backup によってクラウド バックアップを保護するセキュリティ機能が提供されます。 このセキュリティ機能により、バックアップをセキュリティで保護することができ、運用サーバーとバックアップ サーバーが侵害された場合でもデータを安全に回復できます。 [詳細情報](backup-azure-security-feature.md)

- **ハイブリッド IT 環境の一元監視**:Recovery Services コンテナーの場合、[Azure IaaS VM](backup-azure-manage-vms.md) だけでなく、[オンプレミス資産](backup-azure-manage-windows-server.md#manage-backup-items)も中央ポータルで監視することができます。 [詳細情報](backup-azure-monitoring-built-in-monitor.md)

- **Azure ロールベースのアクセス制御 (Azure RBAC)** : Azure RBAC を使用すると、Azure のアクセス制御を詳細に管理できます。 [Azure にはさまざまな組み込みのロールがあります](../role-based-access-control/built-in-roles.md)。また、Azure Backup には、[復旧ポイントを管理するための 3 つの組み込みのロールがあります](backup-rbac-rs-vault.md)。 Recovery Services コンテナーは、定義されたユーザー ロールのセットに対するバックアップと復元アクセスを制限する Azure RBAC と互換性があります。 [詳細情報](backup-rbac-rs-vault.md)

- **論理的な削除**:論理的な削除を使用すると、悪意のあるアクターによってバックアップが削除 (またはバックアップ データが誤って削除) された場合でも、バックアップ データは追加で 14 日間保持されるので、データを失うことなくバックアップ項目を回復できます。 バックアップ データが "論理的な削除" 状態にあるこの追加の 14 日間のリテンション期間中は、お客様にコストは発生しません。 [詳細については、こちらを参照してください](backup-azure-security-feature-cloud.md)。

- **リージョンをまたがる復元**:リージョンをまたがる復元 (CRR) を使用すると、Azure VM をセカンダリ リージョン (Azure のペアになっているリージョン) に復元できます。 [コンテナー レベル](backup-create-rs-vault.md#set-cross-region-restore)でこの機能を有効にすることで、レプリケートされたデータを、選択したときにいつでもセカンダリ リージョンに復元できます。 これにより、停止シナリオにおいて、(コンテナーの GRS 設定と異なり) Azure が障害を宣言するのを待たずに、監査コンプライアンスのためにセカンダリ リージョン データを復元できます。 [詳細については、こちらを参照してください](backup-azure-arm-restore-vms.md#cross-region-restore)。

## <a name="storage-settings-in-the-recovery-services-vault"></a>Recovery Services コンテナーのストレージ設定

Recovery Services コンテナーは、経時的に作成されたバックアップと復旧ポイントを格納するエンティティです。 Recovery Services コンテナーには、保護される仮想マシンに関連付けられたバックアップ ポリシーも含まれます。

- Azure Backup では、コンテナーのストレージが自動的に処理されます。 [ストレージ設定の変更](./backup-create-rs-vault.md#set-storage-redundancy)方法を確認してください。

- ストレージ冗長性の詳細については、[geo](../storage/common/storage-redundancy.md#geo-zone-redundant-storage)、[ローカル](../storage/common/storage-redundancy.md#locally-redundant-storage)、[ゾーン](../storage/common/storage-redundancy.md#zone-redundant-storage)の冗長性に関するこれらの記事を参照してください。

## <a name="encryption-settings-in-the-recovery-services-vault"></a>Recovery Services コンテナーの暗号化設定

この項では、Recovery Services コンテナーに格納されているバックアップ データを暗号化するために使用できるオプションについて説明します。

### <a name="encryption-of-backup-data-using-platform-managed-keys"></a>プラットフォーム マネージド キーを使用したバックアップ データの暗号化

既定では、すべてのデータが、プラットフォーム マネージド キーを使用して暗号化されます。 この暗号化を有効にするためにユーザーが明示的なアクションを実行する必要はありません。 これは、Recovery Services コンテナーにバックアップ中のすべてのワークロードに適用されます。

### <a name="encryption-of-backup-data-using-customer-managed-keys"></a>カスタマー マネージド キーを使用したバックアップ データの暗号化

自分が所有して管理している暗号化キーを使用してデータを暗号化することを選択できます。 Azure Backup を使用すると、自分のバックアップの暗号化に、Azure Key Vault に格納されているご自身の RSA キーを使用でききます。 バックアップの暗号化に使用される暗号化キーは、ソースに使用されているものと異なることがあります。 データは、AES 256 ベースのデータ暗号化キー (DEK) を使用して保護され、このキーは、ご自身のキーを使用して保護されます。 これにより、データとキーを完全に制御できます。 暗号化を許可するには、Recovery Services コンテナーに、Azure Key Vault の暗号化キーへのアクセスが許可されている必要があります。 キーの無効化とアクセスの取り消しは、必要に応じていつでも可能です。 ただし、コンテナーに対する項目の保護を試みるには、事前にキーを使って暗号化を有効にしておく必要があります。

[カスタマー マネージド キーを使用](encryption-at-rest-with-cmk.md)してバックアップ データを暗号化する方法に関する記事をお読みください。

## <a name="azure-advisor"></a>Azure Advisor

[Azure Advisor](../advisor/index.yml) は、Azure の使用を最適化するのに役立つ、カスタマイズされたクラウド コンサルタントです。 Azure の使用状況を分析し、デプロイを最適化してセキュリティで保護するためのタイムリーな推奨事項を提供します。 提供される推奨事項には、高可用性、セキュリティ、パフォーマンス、コストの 4 つのカテゴリがあります。

Azure Advisor では、バックアップされていない VM についての[推奨事項](../advisor/advisor-high-availability-recommendations.md#protect-your-virtual-machine-data-from-accidental-deletion)が 1 時間ごとに提供されるため、もう重要な VM のバックアップを忘れることはありません。 また、再通知を使用して推奨事項を制御することができます。  推奨事項を選択して、コンテナー (バックアップが格納される場所) とバックアップ ポリシー (バックアップのスケジュールとバックアップ コピーの保持期間) を指定することによって、インラインで VM のバックアップを有効にできます。

![Azure Advisor](./media/backup-azure-recovery-services-vault-overview/azure-advisor.png)

## <a name="additional-resources"></a>その他のリソース

- [コンテナーでサポートされているシナリオとサポートされていないシナリオ](backup-support-matrix.md#vault-support)
- [コンテナーについてよく寄せられる質問](backup-azure-backup-faq.md)

## <a name="next-steps"></a>次のステップ

次の記事を参照してください。

- [IaaS VM のバックアップ](backup-azure-arm-vms-prepare.md)
- [Azure Backup Server のバックアップ](backup-azure-microsoft-azure-backup.md)
- [Windows Server のバックアップ](backup-windows-with-mars-agent.md)
