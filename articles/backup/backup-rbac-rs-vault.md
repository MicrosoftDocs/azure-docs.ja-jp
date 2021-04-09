---
title: Azure ロールベースのアクセス制御を使用してバックアップを管理する
description: Azure ロールベースのアクセス制御を使用して、Recovery Services コンテナーのバックアップ管理操作へのアクセスを管理します。
ms.reviewer: utraghuv
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 0b321a5f33bd75ce8615d6d2a90442a83d9fff67
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/10/2021
ms.locfileid: "102613444"
---
# <a name="use-azure-role-based-access-control-to-manage-azure-backup-recovery-points"></a>Azure ロール ベースのアクセス制御を使用して Azure Backup 復旧ポイントを管理する

Azure のロールベースのアクセス制御 (Azure RBAC) を使用すると、Azure のきめ細かなアクセス管理が可能になります。 Azure RBAC を使用して、チーム内で職務を分離し、職務に必要なアクセス許可のみをユーザーに付与します。

> [!IMPORTANT]
> Azure Backup によって提供されるロールは、Azure portal 内で実行できるアクションか、REST API または Recovery Services コンテナーの PowerShell または CLI コマンドレットを介して実行できるアクションに制限されています。 Azure Backup エージェント クライアント UI、System Center Data Protection Manager UI、または Azure Backup Server UI で実行されるアクションは、これらのロールの制御の範囲外です。

Azure Backup では、バックアップの管理操作を制御する 3 つの組み込みロールが提供されます。 [Azure の組み込みロール](../role-based-access-control/built-in-roles.md)について説明します

* [バックアップ共同作成者](../role-based-access-control/built-in-roles.md#backup-contributor) - このロールは、Recovery Services コンテナーの削除と他のロールへの権限付与を除き、バックアップの作成と管理のすべての権限を持ちます。 このロールは、すべてのバックアップ管理操作を実行できる、バックアップ管理の管理者と考えてください。
* [バックアップ オペレーター](../role-based-access-control/built-in-roles.md#backup-operator) - このロールは、バックアップの削除とバックアップ ポリシーの管理を除き、共同作成者が行うすべての操作の権限を持ちます。 このロールは共同作成者と同等ですが、データの削除によるバックアップの停止やオンプレミス リソースの登録解除など、削減する操作は実行できません。
* [バックアップ リーダー](../role-based-access-control/built-in-roles.md#backup-reader) - このロールは、すべてのバックアップ管理操作を見る権限を持ちます。 このロールは監視役と考えてください。

制御を強化するために独自のロールを定義する場合は、Azure RBAC で [カスタム ロールを作成](../role-based-access-control/custom-roles.md)する方法を参照してください。

## <a name="mapping-backup-built-in-roles-to-backup-management-actions"></a>バックアップ管理アクションへの組み込みバックアップ ロールのマッピング

### <a name="minimum-role-requirements-for-azure-vm-backup"></a>Azure VM バックアップの最小ロール要件

次の表に、バックアップ管理アクションと、その操作を実行するために必要な最小限の Azure ロールを示します。

| 管理操作 | 必要最小限 Azure ロール | 必要なスコープ | 代替手段 |
| --- | --- | --- | --- |
| Recovery Services コンテナーの作成 | Backup Contributor | コンテナーを含むリソース グループ |   |
| Azure VM のバックアップの有効化 | Backup Operator | コンテナーを含むリソース グループ |   |
| | Virtual Machine Contributor | VM リソース |  または、組み込みロールではなく、次のアクセス許可を持つカスタム ロールを検討できます: Microsoft.Compute/virtualMachines/write |
| VM のオンデマンド バックアップ | Backup Operator | Recovery Services コンテナー |   |
| VM の復元 | Backup Operator | Recovery Services コンテナー |   |
| | Contributor | VM がデプロイされるリソース グループ |   または、組み込みロールではなく、次のアクセス許可を持つカスタム ロールを検討できます:  Microsoft.Resources/subscriptions/resourceGroups/write Microsoft.DomainRegistration/domains/write、 Microsoft.Compute/virtualMachines/write  Microsoft.Network/virtualNetworks/read Microsoft.Network/virtualNetworks/subnets/join/action |
| | Virtual Machine Contributor | バックアップされたソース VM |   または、組み込みロールではなく、次のアクセス許可を持つカスタム ロールを検討できます: Microsoft.Compute/virtualMachines/write |
| アンマネージド ディスク VM バックアップの復元 | Backup Operator | Recovery Services コンテナー |
| | Virtual Machine Contributor | バックアップされたソース VM | または、組み込みロールではなく、次のアクセス許可を持つカスタム ロールを検討できます: Microsoft.Compute/virtualMachines/write |
| | Storage Account Contributor | ディスクの復元先となるストレージ アカウント リソース |   または、組み込みロールではなく、次のアクセス許可を持つカスタム ロールを検討できます: Microsoft.Storage/storageAccounts/write |
| VM バックアップからのマネージド ディスクの復元 | Backup Operator | Recovery Services コンテナー |
| | Virtual Machine Contributor | バックアップされたソース VM |    または、組み込みロールではなく、次のアクセス許可を持つカスタム ロールを検討できます: Microsoft.Compute/virtualMachines/write |
| | Storage Account Contributor | マネージド ディスクに変換する前にコンテナーからのデータを保持する目的で、復元の一部として選択された一時ストレージ アカウント |   または、組み込みロールではなく、次のアクセス許可を持つカスタム ロールを検討できます: Microsoft.Storage/storageAccounts/write |
| | Contributor | マネージド ディスクの復元先となるリソース グループ | または、組み込みロールではなく、次のアクセス許可を持つカスタム ロールを検討できます: Microsoft.Resources/subscriptions/resourceGroups/write|
| VM バックアップからの個々のファイルの復元 | Backup Operator | Recovery Services コンテナー |
| | Virtual Machine Contributor | バックアップされたソース VM | または、組み込みロールではなく、次のアクセス許可を持つカスタム ロールを検討できます: Microsoft.Compute/virtualMachines/write |
| リージョンをまたがる復元 | Backup Operator | Recovery Services コンテナーのサブスクリプション | これは、前述の復元アクセス許可に追加されます。 特に CRR の場合、組み込みのロールではなく、次のアクセス許可を持つカスタム ロールを検討できます:   "Microsoft.RecoveryServices/locations/backupAadProperties/read" "Microsoft.RecoveryServices/locations/backupCrrJobs/action"         "Microsoft.RecoveryServices/locations/backupCrrJob/action" "Microsoft.RecoveryServices/locations/backupCrossRegionRestore/action"          "Microsoft.RecoveryServices/locations/backupCrrOperationResults/read" "Microsoft.RecoveryServices/locations/backupCrrOperationsStatus/read" |
| Azure VM バックアップのバックアップ ポリシーの作成 | Backup Contributor | Recovery Services コンテナー |
| Azure VM バックアップのバックアップ ポリシーの変更 | Backup Contributor | Recovery Services コンテナー |
| Azure VM バックアップのバックアップ ポリシーの削除 | Backup Contributor | Recovery Services コンテナー |
| VM バックアップでのバックアップの停止 (データを保持またはデータを削除) | Backup Contributor | Recovery Services コンテナー |
| オンプレミスの Windows Server/クライアント/SCDPM または Azure Backup Server での登録 | Backup Operator | Recovery Services コンテナー |
| オンプレミスの Windows Server/クライアント/SCDPM または Azure Backup Server での登録解除 | Backup Contributor | Recovery Services コンテナー |

> [!IMPORTANT]
> VM リソースのスコープで VM 共同作成者を指定し、VM 設定の一部として **[バックアップ]** を選択すると、その VM が既にバックアップされている場合でも **[バックアップの有効化]** 画面が開きます。 これは、バックアップの状態を確認するための呼び出しがサブスクリプション レベルでしか機能しないためです。 これを回避するには、コンテナーに移動して VM のバックアップ項目ビューを開くか、またはサブスクリプション レベルで VM 共同作成者ロールを指定します。

### <a name="minimum-role-requirements-for-azure-workload-backups-sql-and-hana-db-backups"></a>Azure ワークロード バックアップ (SQL と HANA DB のバックアップ) での最小ロール要件

次の表に、バックアップ管理アクションと、その操作を実行するために必要な最小限の Azure ロールを示します。

| 管理操作 | 必要最小限 Azure ロール | 必要なスコープ | 代替手段 |
| --- | --- | --- | --- |
| Recovery Services コンテナーの作成 | Backup Contributor | コンテナーを含むリソース グループ |   |
| SQL または HANA データベースのバックアップの有効化 | Backup Operator | コンテナーを含むリソース グループ |   |
| | Virtual Machine Contributor | DB がインストールされている VM リソース |  または、組み込みロールではなく、次のアクセス許可を持つカスタム ロールを検討できます: Microsoft.Compute/virtualMachines/write |
| DB のオンデマンド バックアップ | Backup Operator | Recovery Services コンテナー |   |
| データベースを復元、またはファイルとして復元 | Backup Operator | Recovery Services コンテナー |   |
| | Virtual Machine Contributor | バックアップされたソース VM |   または、組み込みロールではなく、次のアクセス許可を持つカスタム ロールを検討できます: Microsoft.Compute/virtualMachines/write |
| | Virtual Machine Contributor | DB が復元される、またはファイルが作成されるターゲット VM |   または、組み込みロールではなく、次のアクセス許可を持つカスタム ロールを検討できます: Microsoft.Compute/virtualMachines/write |
| Azure VM バックアップのバックアップ ポリシーの作成 | Backup Contributor | Recovery Services コンテナー |
| Azure VM バックアップのバックアップ ポリシーの変更 | Backup Contributor | Recovery Services コンテナー |
| Azure VM バックアップのバックアップ ポリシーの削除 | Backup Contributor | Recovery Services コンテナー |
| VM バックアップでのバックアップの停止 (データを保持またはデータを削除) | Backup Contributor | Recovery Services コンテナー |

### <a name="minimum-role-requirements-for-the-azure-file-share-backup"></a>Azure ファイル共有バックアップに使用されるロールの最低要件

次の表に、バックアップ管理アクションと、Azure ファイル共有操作を実行するために必要な対応するロールを示します。

| 管理操作 | 必要なロール | リソース |
| --- | --- | --- |
| Azure ファイル共有のバックアップの有効化 | Backup Contributor |Recovery Services コンテナー |
| |ストレージ アカウント | 共同作成者ストレージ アカウント リソース |
| VM のオンデマンド バックアップ | Backup Operator | Recovery Services コンテナー |
| ファイル共有の復元 | Backup Operator | Recovery Services コンテナー |
| | Storage Account Contributor | 復元元と復元先のファイル共有が存在するストレージ アカウント リソース |
| 個々のファイルの復元 | Backup Operator | Recovery Services コンテナー |
| |Storage Account Contributor|復元元と復元先のファイル共有が存在するストレージ アカウント リソース |
| 保護の停止 |Backup Contributor | Recovery Services コンテナー |
| コンテナーからのストレージ アカウントの登録解除 |Backup Contributor | Recovery Services コンテナー |
| |Storage Account Contributor | ストレージ アカウント リソース|

## <a name="next-steps"></a>次のステップ

* [Azure ロールベースのアクセス制御 (Azure RBAC)](../role-based-access-control/role-assignments-portal.md): Azure portal で Azure RBAC の使用を開始します。
* 次の要素を使用したアクセス管理方法の詳細
  * [PowerShell](../role-based-access-control/role-assignments-powershell.md)
  * [Azure CLI](../role-based-access-control/role-assignments-cli.md)
  * [REST API](../role-based-access-control/role-assignments-rest.md)
* [Azure ロールベースのアクセス制御のトラブルシューティング](../role-based-access-control/troubleshooting.md):一般的な問題の修正に関する推奨事項を紹介します。
