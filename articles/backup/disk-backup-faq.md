---
title: Azure ディスク バックアップに関してよく寄せられる質問
description: Azure ディスク バックアップに関してよく寄せられる質問への回答を紹介します
ms.topic: conceptual
ms.date: 01/07/2021
ms.openlocfilehash: 3ef18a7d178075194e24889477768583f05f0cdd
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98734564"
---
# <a name="frequently-asked-questions-about-azure-disk-backup-in-preview"></a>Azure ディスク バックアップ (プレビュー段階) に関してよく寄せられる質問

>[!IMPORTANT]
>Azure ディスク バックアップは、サービス レベル アグリーメントのないプレビュー段階であるため、運用ワークロードにはお勧めできません。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。 利用可能なリージョンについては、[サポート マトリックス](disk-backup-support-matrix.md)に関するページをご覧ください。
>
>プレビューにサインアップするには、[このフォームに記入](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR1vE8L51DIpDmziRt_893LVUNFlEWFJBN09PTDhEMjVHS05UWFkxUlUzUS4u)してください。

この記事では、Azure ディスク バックアップに関してよく寄せられる質問に回答します。 [Azure ディスク バックアップ](disk-backup-overview.md)の利用可能なリージョン、サポートされるシナリオ、制限事項の詳細については、[サポート マトリックス](disk-backup-support-matrix.md)に関するページをご覧ください。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="can-i-back-up-the-disk-using-the-azure-disk-backup-solution-if-the-same-disk-is-backed-up-using-azure-virtual-machine-backup"></a>Azure 仮想マシンのバックアップを使用して、ディスクがバックアップされている場合、Azure ディスク バックアップ ソリューションを使用して、同じディスクをバックアップできますか?

Azure Backup では、ディスク バックアップと [Azure VM バックアップ](backup-azure-vms-introduction.md) ソリューションを使用したマネージド ディスクのバックアップの並列サポートが提供されます。 これは、仮想マシンの 1 日 1 回のアプリケーション整合性バックアップと、OS ディスクまたは特定のデータ ディスクのさらに頻繁なバックアップも必要とする場合に便利です。これにより、運用アプリケーションのパフォーマンスに影響を与えることなくクラッシュ整合性が確保されます。

### <a name="how-do-i-find-the-snapshot-resource-group-that-i-used-to-configure-backup-for-a-disk"></a>ディスクのバックアップを構成するために使用したスナップショット リソース グループを見つけるにはどうすればよいですか?

**[バックアップ インスタンス]** 画面の、 **[Essentials]** セクションでスナップショット リソース グループ フィールドを見つけることができます。 バックアップ センターまたはバックアップ コンテナーから、対応するディスクのバックアップ インスタンスを検索して選択できます。

![スナップショット リソース グループ フィールド](./media/disk-backup-faq/snapshot-resource-group.png)

### <a name="what-is-a-snapshot-resource-group"></a>スナップショット リソース グループとは?

Azure ディスク バックアップでは、マネージド ディスクの運用階層バックアップが提供されます。 つまり、スケジュールされたバックアップ操作およびオンデマンド バックアップ操作中に作成されたスナップショットが、サブスクリプション内のリソース グループに格納されます。 増分スナップショットがサブスクリプション内に格納されるため、Azure Backup では、インスタント リストアが提供されます。 このリソース グループは、スナップショット リソース グループと呼ばれます。 詳細については、「[バックアップの構成](backup-managed-disks.md#configure-backup)」を参照してください。

### <a name="why-must-the-snapshot-resource-group-be-in-same-subscription-as-that-of-the-disk-being-backed-up"></a>スナップショット リソース グループをバックアップ対象のディスクと同じサブスクリプションに含める必要があるのはなぜですか?

特定のディスクの増分スナップショットを、そのディスクのサブスクリプションの外部で作成することはできません。 そのため、バックアップするディスクと同じサブスクリプション内のリソース グループを選択してください。 詳細については、マネージド ディスクの[増分スナップショット](../virtual-machines/disks-incremental-snapshots.md#restrictions)に関するページをご覧ください。

### <a name="why-do-i-need-to-provide-role-assignments-to-be-able-to-configure-backups-perform-scheduled-and-on-demand-backups-and-restore-operations"></a>バックアップの構成、スケジュールされたバックアップとオンデマンド バックアップの実行、および復元操作を可能にするために、ロールの割り当てを行う必要があるのはなぜですか?

Azure ディスク バックアップでは、最小限の特権アプローチを使用して、サブスクリプションのマネージド ディスクを検出、保護、および復元します。 これを実現するために、Azure Backup では、[バックアップ コンテナー](backup-vault-overview.md)のマネージド ID を使用して、他の Azure リソースにアクセスします。 システム割り当てマネージド ID は、1 つのリソースにつき 1 つに限定されており、このリソースのライフサイクルに関連付けられています。 マネージド ID には、Azure ロールベースのアクセス制御 (Azure RBAC) を使用してアクセス許可を付与できます。 マネージド ID は、Azure リソースでのみ使用できる、特殊な種類のサービス プリンシパルです。 [マネージド ID](../active-directory/managed-identities-azure-resources/overview.md) の詳細を確認してください。 既定で、バックアップ コンテナーには、バックアップ対象のディスクへのアクセス、定期的なスナップショットの作成、リテンション期間後のスナップショットの削除、およびバックアップからのディスクの復元を行うアクセス許可がありません。 バックアップ コンテナーのマネージド ID にロールの割り当てを明示的に付与することで、サブスクリプションのリソースに対するアクセス許可を管理することができます。

### <a name="why-does-backup-policy-limit-the-retention-duration"></a>バックアップ ポリシーによってリテンション割引期間を制限するのはなぜですか?

Azure ディスク バックアップでは、ディスクあたりのスナップショット数が 200 に制限された増分スナップショットが使用されます。 スケジュールされたバックアップとは別にオンデマンド バックアップを作成するには、バックアップ ポリシーによって合計バックアップ数を 180 に制限します。 詳細については、マネージド ディスクの[増分スナップショット](../virtual-machines/disks-incremental-snapshots.md#restrictions)に関するページをご覧ください。

### <a name="how-does-the-hourly-and-daily-backup-frequency-work-in-the-backup-policy"></a>バックアップ ポリシーでは、時間単位と日単位のバックアップ頻度がどのように機能しますか?

Azure ディスク バックアップでは、1 日に複数のバックアップを作成できます。 頻繁にバックアップする必要がある場合は、 **[毎時]** バックアップ頻度を選択します。 **[時間]** で選択した間隔に基づいてバックアップがスケジュールされます。 たとえば、 **[4 時間ごと]** を選択した場合、バックアップが 1 日のうちで均等に分散されるように、ほぼ 4 時間ごとにバックアップが作成されます。 1 日 1 回のバックアップで十分な場合は、 **[毎日]** バックアップ頻度を選択します。 毎日のバックアップ頻度では、バックアップを作成する時刻を指定できます。 この時刻は、バックアップが完了する時刻ではなく、バックアップの開始時刻を示していることに注意してください。 バックアップ操作を完了するために必要な時間は、連続するバックアップ間のチャーン率など、さまざまな要因によって異なります。 ただし、Azure ディスク バックアップは[増分スナップショット](../virtual-machines/disks-incremental-snapshots.md)を使用するエージェントレス バックアップであり、実稼働アプリケーションのパフォーマンスには影響しません。

### <a name="why-does-the-backup-vaults-redundancy-setting-not-apply-to-the-backups-stored-in-operational-tier-the-snapshot-resource-group"></a>バックアップ コンテナーの冗長性設定が、運用階層 (スナップショット リソース グループ) に格納されているバックアップに適用されないのはなぜですか?

Azure Backup では、マネージド ディスクの[増分スナップショット](../virtual-machines/disks-incremental-snapshots.md#restrictions)が使用され、親ディスクのストレージの種類に関係なく、最後のスナップショット以降のディスクの差分変更のみが Standard HDD ストレージに保存されます。 信頼性を向上させるため、ゾーン冗長ストレージ (ZRS) をサポートするリージョンで、既定で増分スナップショットが ZRS に保存されます。 Azure Disk Backup では現在、バックアップ コンテナー ストレージにバックアップをコピーしないマネージド ディスクの運用バックアップがサポートされています。 そのため、バックアップ コンテナーのバックアップ ストレージの冗長性設定は回復ポイントに適用されません。

### <a name="can-i-use-backup-center-to-configure-backups-and-manage-backup-instances-for-azure-disks"></a>バックアップ センターを使用して、バックアップを構成し、Azure ディスクのバックアップ インスタンスを管理できますか?

はい。Azure ディスク バックアップは、[バックアップ センター](backup-center-overview.md)に統合され、企業が大規模にバックアップを管理、監視、操作、分析するために、Azure で **1 つに統合された管理エクスペリエンス** が実現されます。 また、バックアップ コンテナーを使用して、コンテナー内で保護されているバックアップ インスタンスをバックアップ、復元、および管理することもできます。

### <a name="why-do-i-need-to-create-a-backup-vault-and-not-use-a-recovery-services-vault"></a>Recovery Services コンテナーを使用せずに、バックアップ コンテナーを作成する必要があるのはなぜですか?

バックアップ コンテナーは、Azure Backup によってサポートされる特定の新しいワークロードのバックアップ データを格納する Azure のストレージ エンティティです。 バックアップ コンテナーを使用すると、Azure Database for PostgreSQL サーバー、Azure Disks、および Azure Backup によってサポートされる新しいワークロードなど、さまざまな Azure サービスのバックアップ データを保持できます。 バックアップ コンテナーを使用すると、管理オーバーヘッドを最小限に抑えながら、バックアップ データを簡単に整理できます。 詳細については、[バックアップ コンテナー](./backup-vault-overview.md)に関するページを参照してください。

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-subscriptions"></a>バックアップ対象のディスクとバックアップ コンテナーを異なるサブスクリプションに含めることができますか?

はい。バックアップ対象のソースマネージド ディスクとバックアップ コンテナーは異なるサブスクリプションに含めることができます。

### <a name="can-the-disk-to-be-backed-up-and-the-backup-vault-be-in-different-regions"></a>バックアップ対象のディスクとバックアップ コンテナーを異なるリージョンに配置できますか?

いいえ。現在のところ、バックアップ対象のソースマネージド ディスクとバックアップコンテナーは同じリージョンに置く必要があります。

### <a name="can-i-restore-a-disk-into-a-different-subscription"></a>ディスクを別のサブスクリプションに復元することはできますか?

はい。ディスクは、バックアップの作成元のソースマネージド ディスクとは別のサブスクリプションに復元できます。

### <a name="can-i-back-up-multiple-disks-together"></a>複数のディスクをまとめてバックアップできますか?

いいえ。仮想マシンに接続されている複数のディスクの特定時点のスナップショットはサポートされていません。 詳細については、「[バックアップの構成](backup-managed-disks.md#configure-backup)」を参照してください。制限の詳細については、[サポート マトリックス](disk-backup-support-matrix.md)に関するページを参照してください。

### <a name="what-are-my-options-to-back-up-disks-across-multiple-subscriptions"></a>複数のサブスクリプション間でディスクをバックアップするためのオプションは何ですか?

現在のところ、Azure portal を使用したディスクのバックアップの構成は、同じサブスクリプションの最大 20 個のディスクに制限されています。

### <a name="what-is-a-target-resource-group"></a>ターゲット リソース グループとは?

復元操作中に、ディスクの復元先にするサブスクリプションとリソース グループを選択できます。 Azure Backup によって、選択したリソース グループの回復ポイントから新しいディスクが作成されます。 これはターゲット リソース グループと呼ばれます。 バックアップ コンテナーのマネージド ID には、復元操作を正常に実行できるように、ターゲット リソース グループへのロールの割り当てが必要です。 詳細については、[復元に関するドキュメント](restore-managed-disks.md)を参照してください。

### <a name="what-are-the-permissions-used-by-azure-backup-during-backup-and-restore-operation"></a>バックアップおよび復元操作中に Azure Backup で使用されるアクセス許可は何ですか?

バックアップ対象の **ディスク** に割り当てられる **ディスク バックアップ リーダー** ロールで使用されるアクションを次に示します。

"Microsoft.Compute/disks/read"

"Microsoft.Compute/disks/beginGetAccess/action"

**スナップショット リソース グループ** に割り当てられる **ディスク スナップショット共同作成者** ロールで使用されるアクションを次に示します。

"Microsoft.Compute/snapshots/delete"

"Microsoft.Compute/snapshots/write"

"Microsoft.Compute/snapshots/read"

"Microsoft.Storage/storageAccounts/write"

"Microsoft.Storage/storageAccounts/read"

"Microsoft.Storage/storageAccounts/delete"

"Microsoft.Resources/subscriptions/resourceGroups/read"

"Microsoft.Storage/storageAccounts/listkeys/action"

"Microsoft.Compute/snapshots/beginGetAccess/action"

"Microsoft.Compute/snapshots/endGetAccess/action"

"Microsoft.Compute/disks/beginGetAccess/action"

**ターゲット リソース グループ** に割り当てられる **ディスク復元オペレーター** で使用されるアクションを次に示します。

"Microsoft.Compute/disks/write"

"Microsoft.Compute/disks/read"

"Microsoft.Resources/subscriptions/resourceGroups/read"

>[!NOTE]
>これらのロールのアクセス許可は、Azure Backup サービスによって追加される機能に基づいて、将来変更される可能性があります。

## <a name="next-steps"></a>次の手順

- [Azure ディスク バックアップのサポート マトリックス](disk-backup-support-matrix.md)