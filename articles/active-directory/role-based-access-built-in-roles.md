---
title: "Actions と NotActions - Azure ロールベースのアクセス制御 (RBAC) | Microsoft Docs"
description: "このトピックでは、ロール ベースのアクセス制御 (RBAC) の組み込みのロールについて説明します。 ロールは継続的に追加されるので、ドキュメントが最新かどうか確認してください。"
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
editor: 
ms.assetid: b547c5a5-2da2-4372-9938-481cb962d2d6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/28/2017
ms.author: andredm
ms.reviewer: 
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9a5de00793621cfdecea887c53a22d482a25d1b8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="built-in-roles-for-azure-role-based-access-control"></a>Azure ロールベースのアクセス制御の組み込みロール
Azure のロールベースのアクセス制御 (RBAC) には、ユーザー、グループ、サービスに割り当てられる次の組み込みのロールが用意されています。 組み込みのロールの定義は変更できません。 ただし、組織の具体的なニーズに合うように [Azure RBAC のカスタム ロール](role-based-access-control-custom-roles.md) を作成することができます。

## <a name="roles-in-azure"></a>Azure におけるロール
次の表に、組み込みのロールについての簡単な説明を示します。 ロール名をクリックすると、そのロールの **actions** と **notactions** の詳細な一覧を確認できます。 **actions** プロパティは、Azure リソースに対して許可するアクションを指定します。 アクションの文字列にワイルドカード文字を使用できます。 **notactions** プロパティは、許可するアクションから除外されるアクションを指定します。

アクションは、指定したリソースの種類で実行できる操作の種類を定義します。 次に例を示します。
- **Write** を使用すると、PUT、POST、PATCH、および DELETE 操作を実行できます。
- **Read** を使用すると、GET 操作を実行できます。

この記事では、現在存在するさまざまなロールについてのみ説明します。 ユーザーにロールを割り当てるとき、許可されているアクションをさらを制限するには、スコープを定義します。 これは、1 つのリソース グループについてのみ、あるユーザーを Web サイトの共同作業者として指定する場合に便利です。

> [!NOTE]
> Azure のロール定義は常に進化しています。 この記事は、最新の状態であることを心掛けておりますが、Azure PowerShell で常に最新のロール定義を見つけることができます。 現在のロールの一覧を表示するには、[Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) コマンドレットを使用します。 特定のロールの詳細を確認するには、適宜 `(get-azurermroledefinition "<role name>").actions` または `(get-azurermroledefinition "<role name>").notactions` を使用します。 [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) を使用すると、特定の Azure リソース プロバイダーの操作が一覧表示されます。


| ロール名 | Description |
| --- | --- |
| [API 管理サービスの共同作業者](#api-management-service-contributor) |API Management サービスと API を管理できます |
| [API Management サービスのオペレーター ロール](#api-management-service-operator-role) | API Management サービスを管理できます。ただし、API 自体を管理することはできません |
| [API Management サービスのリーダー ロール](#api-management-service-reader-role) | API Management のサービスと API への読み取り専用アクセス |
| [Application Insights コンポーネントの共同作業者](#application-insights-component-contributor) |Application Insights コンポーネントを管理できます |
| [オートメーション オペレーター](#automation-operator) |ジョブを開始、停止、中断、および再開できます |
| [バックアップの共同作業者](#backup-contributor) | Recovery Services コンテナーのバックアップを管理できます |
| [バックアップ オペレーター](#backup-operator) | バックアップの削除を除き、Recovery Services コンテナーのバックアップを管理できます |
| [バックアップ リーダー](#backup-reader) | すべてのバックアップ管理サービスを表示できます  |
| [課金リーダー](#billing-reader) | すべての課金情報を見ることができます  |
| [BizTalk の共同作業者](#biztalk-contributor) |BizTalk Services を管理できます |
| [ClearDB MySQL DB の共同作業者](#cleardb-mysql-db-contributor) |ClearDB MySQL データベースを管理できます |
| [共同作成者](#contributor) |アクセス権以外のすべてを管理できます。 |
| [Data Factory の共同作業者](#data-factory-contributor) |Data Factory と Data Factory に含まれる子リソースを作成および管理できます。 |
| [DevTest Labs ユーザー](#devtest-labs-user) |すべてを表示し、仮想マシンを接続、開始、再起動、シャットダウンできます |
| [DNS ゾーン共同作成者](#dns-zone-contributor) |DNS ゾーンとレコードを保護できます |
| [Azure Cosmos DB アカウントの共同作業者](#documentdb-account-contributor) |Azure Cosmos DB アカウントを管理できます |
| [Intelligent Systems アカウントの共同作業者](#intelligent-systems-account-contributor) |Intelligent Systems アカウントを管理できます |
| ロジック アプリの共同作成者 | ロジック アプリのあらゆる側面を管理できますが、新規作成はできません。 |
| ロジック アプリのオペレーター |ロジック アプリ内で定義されたワークフローを開始および停止できます。 |
| [監視リーダー](#monitoring-reader) |すべての監視データを読み取ることができます |
| [監視共同作業者](#monitoring-contributor) |監視データを読み取り、監視設定を編集できます |
| [ネットワークの共同作業者](#network-contributor) |すべてのネットワーク リソースを管理できます |
| [New Relic APM アカウントの共同作業者](#new-relic-apm-account-contributor) |New Relic Application Performance Management アカウントおよびアプリケーションを管理できます |
| [所有者](#owner) |アクセス権を含めすべてを管理できます |
| [閲覧者](#reader) |すべてを閲覧できますが、変更を加えることはできません |
| [Redis Cache の共同作業者](#redis-cache-contributor) |Redis キャッシュを管理できます |
| [Scheduler Job Collection の共同作業者](#scheduler-job-collections-contributor) |Scheduler Job Collection を管理できます |
| [Search サービスの共同作業者](#search-service-contributor) |Search サービスを管理できます |
| [セキュリティ管理者](#security-manager) |セキュリティ コンポーネント、セキュリティ ポリシー、および仮想マシンを管理できます |
| [Site Recovery 共同作成者](#site-recovery-contributor) | Recovery Services コンテナーの Site Recovery を管理できます |
| [Site Recovery オペレーター](#site-recovery-operator) | Recovery Services コンテナーでの Site Recovery のフェールオーバーとフェールバック操作を管理できます |
| [Site Recovery 閲覧者](#site-recovery-reader) | すべての Site Recovery 管理操作を表示できます  |
| [SQL DB の共同作業者](#sql-db-contributor) |SQL データベースを管理できますが、そのセキュリティ関連ポリシーは管理できません |
| [SQL セキュリティ管理者](#sql-security-manager) |SQL サーバーおよびデータベースのセキュリティ関連ポリシーを管理できます |
| [SQL Server の共同作業者](#sql-server-contributor) |SQL サーバーおよびデータベースを管理できますが、そのセキュリティ関連ポリシーは管理できません |
| [従来のストレージ アカウントの共同作業者](#classic-storage-account-contributor) |従来のストレージ アカウントを管理できます |
| [ストレージ アカウントの共同作業者](#storage-account-contributor) |ストレージ アカウントを管理できます |
| [Support request の共同作成者](#support-request-contributor) | Support request を作成し管理できます |
| [ユーザーアクセスの管理者](#user-access-administrator) |Azure リソースに対するユーザー アクセスを管理できます |
| [従来の仮想マシンの共同作業者](#classic-virtual-machine-contributor) |クラシック仮想マシンを管理できますが、その接続先の仮想ネットワークやストレージ アカウントは管理できません |
| [仮想マシンの共同作業者](#virtual-machine-contributor) |仮想マシンを管理できますが、その接続先の仮想ネットワークやストレージ アカウントは管理できません |
| [従来のネットワークの共同作業者](#classic-network-contributor) |従来の仮想ネットワークと予約済み IP を管理できます |
| [Web プランの共同作業者](#web-plan-contributor) |Web プランを管理できます |
| [Web サイトの共同作業者](#website-contributor) |Web サイトを管理できますが、その接続先の Web プランは管理できません |

## <a name="role-permissions"></a>ロールのアクセス許可
以降の表は、各ロールに割り当てられている具体的なアクセス許可の説明です。 アクセス許可を与える **Actions** のほか、それらを制限する **NotActions** が含まれている場合があります。

### <a name="api-management-service-contributor"></a>API 管理サービスの共同作業者
API Management サービスを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/* |API Management サービスの作成と管理 |
| Microsoft.Authorization/*/read |承認の読み取り |
| Microsoft.Insights/alertRules/* |アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |ロールとロール割り当ての読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="api-management-service-operator-role"></a>API Management サービスのオペレーター ロール
API Management サービスを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/*/read | API Management サービス インスタンスの読み取り |
| Microsoft.ApiManagement/Service/backup/action | ユーザーが指定したストレージ アカウント内の指定されたコンテナーへの API Management サービスのバックアップ |
| Microsoft.ApiManagement/Service/delete | API Management サービス インスタンスの削除 |
| Microsoft.ApiManagement/Service/managedeployments/action | SKU/ユニット数の変更、API Management サービスのリージョン デプロイの追加または削除 |
| Microsoft.ApiManagement/Service/read | API Management サービス インスタンスのメタデータの読み取り |
| Microsoft.ApiManagement/Service/restore/action | ユーザーが指定したストレージ アカウント内の指定されたコンテナーからの API Management サービスの復元 |
| Microsoft.ApiManagement/Service/updatehostname/action | API Management サービスのカスタム ドメイン名の設定、更新、または削除 |
| Microsoft.ApiManagement/Service/write | API Management サービスの新しいインスタンスの作成 |
| Microsoft.Authorization/*/read |承認の読み取り |
| Microsoft.Insights/alertRules/* |アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |ロールとロール割り当ての読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="api-management-service-reader-role"></a>API Management サービスのリーダー ロール
API Management サービスを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.ApiManagement/Service/*/read | API Management サービス インスタンスの読み取り |
| Microsoft.ApiManagement/Service/read | API Management サービス インスタンスのメタデータの読み取り |
| Microsoft.Authorization/*/read |承認の読み取り |
| Microsoft.Insights/alertRules/* |アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |ロールとロール割り当ての読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="application-insights-component-contributor"></a>Application Insights コンポーネントの共同作業者
Application Insights コンポーネントを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* |アラート ルールの作成と管理 |
| Microsoft.Insights/components/* |Insights コンポーネントの作成と管理 |
| Microsoft.Insights/webtests/* |Web テストの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="automation-operator"></a>オートメーション オペレーター
ジョブを開始、停止、中断、および再開できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |ロールとロール割り当ての読み取り |
| Microsoft.Automation/automationAccounts/jobs/read |オートメーション アカウント ジョブの読み取り |
| Microsoft.Automation/automationAccounts/jobs/resume/action |オートメーション アカウント ジョブの再開 |
| Microsoft.Automation/automationAccounts/jobs/stop/action |オートメーション アカウント ジョブの停止 |
| Microsoft.Automation/automationAccounts/jobs/streams/read |オートメーション アカウント ジョブ ストリームの読み取り |
| Microsoft.Automation/automationAccounts/jobs/suspend/action |オートメーション アカウント ジョブの中断 |
| Microsoft.Automation/automationAccounts/jobs/write |オートメーション アカウント ジョブの書き込み |
| Microsoft.Automation/automationAccounts/jobSchedules/read |オートメーション アカウント ジョブ スケジュールの読み取り |
| Microsoft.Automation/automationAccounts/jobSchedules/write |オートメーション アカウント ジョブ スケジュールの読み取り |
| Microsoft.Automation/automationAccounts/read |オートメーション アカウントの読み取り |
| Microsoft.Automation/automationAccounts/runbooks/read |Automation Runbook の読み取り |
| Microsoft.Automation/automationAccounts/schedules/read |オートメーション アカウント スケジュールの読み取り |
| Microsoft.Automation/automationAccounts/schedules/write |オートメーション アカウント スケジュールの書き込み |
| Microsoft.Insights/components/* |Insights コンポーネントの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="backup-contributor"></a>バックアップの共同作業者
Recovery Services コンテナーの作成と他のユーザーへのアクセス権の付与を除き、すべてのバックアップ管理アクションを管理できます

| **アクション** | |
| --- | --- |
| Microsoft.Network/virtualNetworks/read | 仮想ネットワークの読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/* | バックアップ管理操作の結果の管理 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/* | Recovery Services コンテナーのバックアップ ファブリック内でのバックアップ コンテナーの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | バックアップ ジョブの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Excel へのバックアップ ジョブのエクスポート |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/* | バックアップの管理に関連するメタ データの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | バックアップ管理操作の結果の作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/* | バックアップ ポリシーの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | バックアップできるアイテムの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/* | バックアップ アイテムの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/* | バックアップ アイテムを保持するコンテナーの作成および管理 |
| Microsoft.RecoveryServices/Vaults/certificates/* | Recovery Services コンテナー内のバックアップに関連する証明書の作成および管理 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | コンテナーに関連する拡張情報の作成および管理 |
| Microsoft.RecoveryServices/Vaults/read | Recovery Services コンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | 新しく作成されたコンテナーを取得するための検出操作の管理 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | 登録済み ID の管理 |
| Microsoft.RecoveryServices/Vaults/usages/* | Recovery Services コンテナーの使用状況の作成および管理 |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Storage/storageAccounts/read | ストレージ アカウントの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="backup-operator"></a>バックアップ オペレーター
コンテナーの作成、バックアップの削除、および他のユーザーへのアクセス権の付与を除き、すべてのバックアップ管理アクションを管理できます

| **アクション** | |
| --- | --- |
| Microsoft.Network/virtualNetworks/read | 仮想ネットワークの読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read | バックアップ管理操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read | 保護コンテナーに対する操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/backup/action | バックアップされたアイテムに対するオンデマンドのバックアップ操作の実行 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read | バックアップされたアイテムに対して実行された操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationStatus/read | バックアップされたアイテムに対して実行された操作の状態の読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read | バックアップされたアイテムの読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/read | バックアップされたアイテムの復旧ポイントの読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/recoveryPoints/restore/action | バックアップされた項目の復旧ポイントを使用した復元操作の実行 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/write | バックアップ アイテムの作成 |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read | バックアップ アイテムを保持するコンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/backupJobs/* | バックアップ ジョブの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Excel へのバックアップ ジョブのエクスポート |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read | バックアップの管理に関連するメタ データの読み取り |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/* | バックアップ管理操作の結果の作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read | バックアップ ポリシーに対して実行された操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read | バックアップ ポリシーの読み取り |
| Microsoft.RecoveryServices/Vaults/backupProtectableItems/* | バックアップできるアイテムの作成および管理 |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read | バックアップされたアイテムの読み取り |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read | バックアップ アイテムを保持するバックアップされたコンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | コンテナーに関連する拡張情報の読み取り |
| Microsoft.RecoveryServices/Vaults/extendedInformation/write | コンテナーに関連する拡張情報の書き込み |
| Microsoft.RecoveryServices/Vaults/read | Recovery Services コンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/refreshContainers/* | 新しく作成されたコンテナーを取得するための検出操作の管理 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | コンテナーの登録済みアイテムに対して実行された操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | コンテナーの登録済みアイテムの読み取り |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/write | コンテナーの登録済みアイテムの書き込み |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Storage/storageAccounts/read | ストレージ アカウントの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### <a name="backup-reader"></a>バックアップ リーダー
Recovery Services コンテナーでのバックアップ管理を監視できます

| **アクション** | |
| --- | --- |
| Microsoft.RecoveryServices/Vaults/backupFabrics/operationResults/read  | バックアップ管理操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/operationResults/read  | 保護コンテナーに対する操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationResults/read  | バックアップされたアイテムに対して実行された操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/operationStatus/read  | バックアップされたアイテムに対して実行された操作の状態の読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/protectedItems/read  | バックアップされたアイテムの読み取り |
| Microsoft.RecoveryServices/Vaults/backupFabrics/protectionContainers/read  | バックアップ アイテムを保持するコンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/backupJobs/operationResults/read  | バックアップ ジョブの結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupJobs/read  | バックアップ ジョブの読み取り |
| Microsoft.RecoveryServices/Vaults/backupJobsExport/action | Excel へのバックアップ ジョブのエクスポート |
| Microsoft.RecoveryServices/Vaults/backupManagementMetaData/read  | バックアップの管理に関連するメタ データの読み取り |
| Microsoft.RecoveryServices/Vaults/backupOperationResults/read  | バックアップの管理操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupPolicies/operationResults/read  | バックアップ ポリシーに対して実行された操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/backupPolicies/read  | バックアップ ポリシーの読み取り |
| Microsoft.RecoveryServices/Vaults/backupProtectedItems/read  |  バックアップされたアイテムの読み取り |
| Microsoft.RecoveryServices/Vaults/backupProtectionContainers/read  | バックアップ アイテムを保持するバックアップされたコンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read  | コンテナーに関連する拡張情報の読み取り |
| Microsoft.RecoveryServices/Vaults/read  | Recovery Services コンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read  | 新しく作成されたコンテナーを取得するための検出操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read  | コンテナーの登録済みアイテムに対して実行された操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read  | コンテナーの登録済みアイテムの読み取り |
| Microsoft.RecoveryServices/Vaults/usages/read  |  Recovery Services コンテナーの使用状況の読み取り |

### <a name="billing-reader"></a>課金リーダー
すべての課金情報を見ることができます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |ロールとロール割り当ての読み取り |
| Microsoft.Billing/*/read |課金情報の読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="biztalk-contributor"></a>BizTalk の共同作業者
BizTalk Services を管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |ロールとロール割り当ての読み取り |
| Microsoft.BizTalkServices/BizTalk/* |BizTalk Services の作成と管理 |
| Microsoft.Insights/alertRules/* |アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="cleardb-mysql-db-contributor"></a>ClearDB MySQL DB の共同作業者
ClearDB MySQL データベースを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* |アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |
| successbricks.cleardb/databases/* |ClearDB MySQL データベースの作成と管理 |

### <a name="contributor"></a>共同作成者
アクセス権以外のすべてを管理できます。

| **アクション** |  |
| --- | --- |
| * |あらゆる種類のリソースの作成と管理 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Authorization/*/Delete |ロールとロール割り当ては削除できません |
| Microsoft.Authorization/*/Write |ロールとロール割り当ては作成できません |

### <a name="data-factory-contributor"></a>Data Factory の共同作業者
Data Factory と Data Factory に含まれる子リソースを作成および管理します。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |ロールとロール割り当ての読み取り |
| Microsoft.DataFactory/dataFactories/* |Data Factory と Data Factory に含まれる子リソースを作成および管理します。 |
| Microsoft.Insights/alertRules/* |アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="devtest-labs-user"></a>DevTest Labs ユーザー
すべてを表示し、仮想マシンを接続、開始、再起動、シャットダウンできます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |ロールとロール割り当ての読み取り |
| Microsoft.Compute/availabilitySets/read |可用性セットのプロパティの読み取り |
| Microsoft.Compute/virtualMachines/*/read |仮想マシンのプロパティ (VM サイズ、実行時の状態、VM 拡張機能など) の読み取り |
| Microsoft.Compute/virtualMachines/deallocate/action |仮想マシンの割り当て解除 |
| Microsoft.Compute/virtualMachines/read |仮想マシンのプロパティの読み取り |
| Microsoft.Compute/virtualMachines/restart/action |仮想マシンの再起動 |
| Microsoft.Compute/virtualMachines/start/action |仮想マシンの開始 |
| Microsoft.DevTestLab/*/read |ラボのプロパティの読み取り |
| Microsoft.DevTestLab/labs/createEnvironment/action |ラボ環境の作成 |
| Microsoft.DevTestLab/labs/formulas/delete |数式の削除 |
| Microsoft.DevTestLab/labs/formulas/read |数式の読み取り |
| Microsoft.DevTestLab/labs/formulas/write |数式の追加または変更 |
| Microsoft.DevTestLab/labs/policySets/evaluatePolicies/action |ラボのポリシーの評価 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action |ロード バランサーのバックエンド アドレス プールの接続 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action |ロード バランサーの受信 NAT 規則の接続 |
| Microsoft.Network/networkInterfaces/*/read |ネットワーク インターフェイスのプロパティ (例: そのネットワーク インターフェイスが含まれているすべてのロード バランサー) の読み取り |
| Microsoft.Network/networkInterfaces/join/action |ネットワーク インターフェイスへの仮想マシンの接続 |
| Microsoft.Network/networkInterfaces/read |ネットワーク インターフェイスの読み取り |
| Microsoft.Network/networkInterfaces/write |ネットワーク インターフェイスの作成 |
| Microsoft.Network/publicIPAddresses/*/read |パブリック IP アドレスのプロパティの読み取り |
| Microsoft.Network/publicIPAddresses/join/action |パブリック IP アドレスの接続 |
| Microsoft.Network/publicIPAddresses/read |ネットワーク パブリック IP アドレスの読み取り |
| Microsoft.Network/virtualNetworks/subnets/join/action |仮想ネットワークの接続 |
| Microsoft.Resources/deployments/operations/read |デプロイ操作の読み取り |
| Microsoft.Resources/deployments/read |デプロイの読み取り |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Storage/storageAccounts/listKeys/action |ストレージ アカウント キーの一覧表示 |

### <a name="dns-zone-contributor"></a>DNS ゾーン共同作成者
DNS ゾーンとレコードを保護できます。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/\*/read |ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/\* |アラート ルールの作成と管理 |
| Microsoft.Network/dnsZones/\* |DNS ゾーンとレコードの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/\* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Support/\* |サポート チケットの作成と管理 |

### <a name="azure-cosmos-db-account-contributor"></a>Azure Cosmos DB アカウントの共同作業者
Azure Cosmos DB アカウントを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |ロールとロール割り当ての読み取り |
| Microsoft.DocumentDb/databaseAccounts/* |DocumentDB アカウントの作成と管理 |
| Microsoft.Insights/alertRules/* |アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="intelligent-systems-account-contributor"></a>Intelligent Systems アカウントの共同作業者
Intelligent Systems アカウントを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* |アラート ルールの作成と管理 |
| Microsoft.IntelligentSystems/accounts/* |Intelligent Systems アカウントの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="monitoring-reader"></a>監視閲覧者
すべての監視データ (メトリック、ログなど) を読み取ることができます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](/monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)も参照してください。

| **アクション** |  |
| --- | --- |
| */read |機密データを除くあらゆる種類のリソースの読み取り |
| Microsoft.OperationalInsights/workspaces/search/action |Log Analytics データの検索 |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="monitoring-contributor"></a>監視共同作業者
すべての監視データを読み取り、監視設定を編集できます。 [「Azure Monitor での役割、アクセス許可、およびセキュリティの概要」](/monitoring-and-diagnostics/monitoring-roles-permissions-security.md#built-in-monitoring-roles)も参照してください。

| **アクション** |  |
| --- | --- |
| */read |機密データを除くあらゆる種類のリソースの読み取り |
| Microsoft.Insights/AlertRules/* |アラート ルールの読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/components/* |Application Insights コンポーネントの読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/DiagnosticSettings/* |診断設定の読み取り/書き込み/削除を実行します。 |
| Microsoft.Insights/eventtypes/* |サブスクリプションのアクティビティ ログのイベント (管理イベント) を一覧表示します。 このアクセス許可は、アクティビティ ログへのプログラムによるアクセスとポータル アクセスの両方に適用されます。 |
| Microsoft.Insights/LogDefinitions/* |このアクセス許可は、ポータルを使用してアクティビティ ログにアクセスする必要があるユーザーに必要です。 アクティビティ ログのログのカテゴリを一覧表示します。 |
| Microsoft.Insights/MetricDefinitions/* |メトリック定義 (リソースの使用可能なメトリックの種類の一覧) を読み取ります。 |
| Microsoft.Insights/Metrics/* |リソースのメトリックを読み取ります。 |
| Microsoft.Insights/Register/Action |Microsoft Insights プロバイダーを登録します。 |
| Microsoft.Insights/webtests/* |Application Insights の Web テストの読み取り/書き込み/削除を行います。 |
| Microsoft.OperationalInsights/workspaces/intelligencepacks/* |Log Analytics ソリューション パックの読み取り/書き込み/削除を行います。 |
| Microsoft.OperationalInsights/workspaces/savedSearches/* |Log Analytics によって保存された検索の読み取り/書き込み/削除を行います。 |
| Microsoft.OperationalInsights/workspaces/search/action |Log Analytics ワークスペースを検索します。 |
| Microsoft.OperationalInsights/workspaces/sharedKeys/action |Log Analytics ワークスペースのキーを一覧表示します。 |
| Microsoft.OperationalInsights/workspaces/storageinsightconfigs/* |Log Analytics ストレージ インサイト構成の読み取り/書き込み/削除を行います。 |

### <a name="network-contributor"></a>ネットワークの共同作業者
すべてのネットワーク リソースを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* |アラート ルールの作成と管理 |
| Microsoft.Network/* |ネットワークの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="new-relic-apm-account-contributor"></a>New Relic APM アカウントの共同作業者
New Relic Application Performance Management アカウントおよびアプリケーションを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* |アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |
| NewRelic.APM/accounts/* |New Relic Application Performance Management アカウントの作成と管理 |

### <a name="owner"></a>所有者
アクセス権を含めすべてを管理できます

| **アクション** |  |
| --- | --- |
| * |あらゆる種類のリソースの作成と管理 |

### <a name="reader"></a>閲覧者
すべてを閲覧できますが、変更を加えることはできません

| **アクション** |  |
| --- | --- |
| */read |機密データを除くあらゆる種類のリソースの読み取り |

### <a name="redis-cache-contributor"></a>Redis Cache の共同作業者
Redis キャッシュを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |ロールとロール割り当ての読み取り |
| Microsoft.Cache/redis/* |Redis キャッシュの作成と管理 |
| Microsoft.Insights/alertRules/* |アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="scheduler-job-collections-contributor"></a>Scheduler Job Collection の共同作業者
Scheduler Job Collection を管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* |アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Scheduler/jobcollections/* |ジョブ コレクションの作成と管理 |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="search-service-contributor"></a>Search サービスの共同作業者
Search サービスを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* |アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Search/searchServices/* |検索サービスの作成と管理 |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="security-manager"></a>セキュリティ管理者
セキュリティ コンポーネント、セキュリティ ポリシー、および仮想マシンを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |ロールとロール割り当ての読み取り |
| Microsoft.ClassicCompute/*/read |従来のコンピューティング仮想マシンの構成情報の読み取り |
| Microsoft.ClassicCompute/virtualMachines/*/write |仮想マシンの構成の書き込み |
| Microsoft.ClassicNetwork/*/read |従来のネットワークに関する構成情報の読み取り |
| Microsoft.Insights/alertRules/* |アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Security/* |セキュリティ コンポーネントおよびポリシーの作成と管理 |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="site-recovery-contributor"></a>Site Recovery 共同作成者
Recovery Services コンテナーの作成と他のユーザーへのアクセス権の付与を除き、すべての Site Recovery 管理アクションを管理できます

| **アクション** | |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.Network/virtualNetworks/read | 仮想ネットワークの読み取り |
| Microsoft.RecoveryServices/Vaults/certificates/write | コンテナー資格情報証明書の更新 |
| Microsoft.RecoveryServices/Vaults/extendedInformation/* | コンテナーに関連する拡張情報の作成および管理 |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/*  | Recovery Services コンテナーのアラートの読み取り |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read  | Recovery Services コンテナーの通知構成の読み取り |
| Microsoft.RecoveryServices/Vaults/read | Recovery Services コンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | 新しく作成されたコンテナーを取得するための検出操作の管理 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/* | 登録済み ID の管理 |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/* | レプリケーションの警告設定の作成または更新 |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | レプリケーション イベントの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/* | レプリケーション ファブリックの作成と管理 |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | レプリケーション ジョブの作成と管理 |
| Microsoft.RecoveryServices/vaults/replicationPolicies/* | レプリケーション ポリシーの作成と管理 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/* | 復旧計画の作成と管理 |
| Microsoft.RecoveryServices/Vaults/storageConfig/* | Recovery Services コンテナーのストレージ構成の作成と管理 |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services コンテナーのトークン情報の読み取り |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細の読み取り |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Storage/storageAccounts/read | ストレージ アカウントの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="site-recovery-operator"></a>Site Recovery オペレーター
フェールオーバーとフェールバックを実行できますが、その他の Site Recovery 管理操作の実行や、他のユーザーへのアクセス権の割り当てはできません

| **アクション** | |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* | アラート ルールの作成と管理 |
| Microsoft.Network/virtualNetworks/read | 仮想ネットワークの読み取り |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read | コンテナーに関連する拡張情報の読み取り |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/*  | Recovery Services コンテナーのアラートの読み取り |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read  | Recovery Services コンテナーの通知構成の読み取り |
| Microsoft.RecoveryServices/Vaults/read | Recovery Services コンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read | 新しく作成されたコンテナーを取得するための検出操作の管理 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read | 操作の状態と送信された操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read | リソースに対する登録済みコンテナーの読み取り |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | レプリケーションの警告の設定の読み取り |
| Microsoft.RecoveryServices/vaults/replicationEvents/read | レプリケーション イベントの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/checkConsistency/action | ファブリックの一貫性の確認 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read | レプリケーション ファブリックの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ reassociateGateway/action | レプリケーション ゲートウェイの再関連付け |
| Microsoft.RecoveryServices/vaults/replicationFabrics/renewcertificate/action | レプリケーション ファブリックの証明書の書き換え |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read | レプリケーション ファブリック ネットワークの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read | レプリケーション ファブリック ネットワーク マッピングの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read | 保護コンテナーの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read | すべての保護可能な項目の一覧の取得 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ applyRecoveryPoint/action | 特定の復旧ポイントの適用 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ failoverCommit/action | フェールオーバーした項目に対するフェールオーバーのコミット |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ plannedFailover/action | 保護された項目に対する計画されたフェールオーバーの開始 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read | すべての保護された項目の一覧の取得 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read | 使用可能な回復ポイントの一覧の取得 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ repairReplication/action | 保護された項目のレプリケーションの修復 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/reProtect/action | 保護された項目の再保護の開始|
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/testFailover/action | 保護された項目のテスト フェールオーバーの開始 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ testFailoverCleanup/action | テスト フェールオーバーのクリーンアップの開始 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ unplannedFailover/action | 保護された項目の計画されていないフェールオーバーの開始 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/ updateMobilityService/action | モビリティ サービスの削除 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read | 保護コンテナー マッピングの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read | Recovery Services プロバイダーの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/refreshProvider/action | Recovery Services プロバイダーの更新 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read | レプリケーション ファブリック用のストレージの分類の読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read | ストレージの分類マッピングの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read | 登録済みの vCenter 情報の読み取り |
| Microsoft.RecoveryServices/vaults/replicationJobs/* | レプリケーション ジョブの作成と管理 |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read | レプリケーション ポリシーの読み取り |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ failoverCommit/action | 復旧計画のフェールオーバー用のフェールオーバーのコミット |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ plannedFailover/action | 復旧計画のフェールオーバーの開始 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read | 復旧計画の読み取り |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/reProtect/action | 復旧計画の再保護の開始 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/testFailover/action | 復旧計画のテスト フェールオーバーの開始 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ testFailoverCleanup/action | 復旧計画のテスト フェールオーバーのクリーンアップの開始 |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/ unplannedFailover/action | 復旧計画の計画されていないフェールオーバーの開始 |
| Microsoft.RecoveryServices/Vaults/storageConfig/read | Recovery Services コンテナーのストレージ構成の読み取り |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read | Recovery Services コンテナーのトークン情報の読み取り |
| Microsoft.RecoveryServices/Vaults/usages/read | Recovery Services コンテナーの使用状況の詳細の読み取り |
| Microsoft.ResourceHealth/availabilityStatuses/read | リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* | リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | リソース グループの読み取り |
| Microsoft.Storage/storageAccounts/read | ストレージ アカウントの読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |

### <a name="site-recovery-reader"></a>Site Recovery 閲覧者
Recovery Services コンテナーで Site Recovery の状態を監視し、サポート チケットを発生させることができます

| **アクション** | |
| --- | --- |
| Microsoft.Authorization/*/read | ロールとロール割り当ての読み取り |
| Microsoft.RecoveryServices/Vaults/extendedInformation/read  | コンテナーに関連する拡張情報の読み取り |
| Microsoft.RecoveryServices/Vaults/monitoringAlerts/read  | Recovery Services コンテナーのアラートの読み取り |
| Microsoft.RecoveryServices/Vaults/monitoringConfigurations/ notificationConfiguration/read  | Recovery Services コンテナーの通知構成の読み取り |
| Microsoft.RecoveryServices/Vaults/read  | Recovery Services コンテナーの読み取り |
| Microsoft.RecoveryServices/Vaults/refreshContainers/read  | 新しく作成されたコンテナーを取得するための検出操作の管理 |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/operationResults/read  | 操作の状態と送信された操作の結果の読み取り |
| Microsoft.RecoveryServices/Vaults/registeredIdentities/read  | リソースに対する登録済みコンテナーの読み取り |
| Microsoft.RecoveryServices/vaults/replicationAlertSettings/read | レプリケーションの警告の設定の読み取り |
| Microsoft.RecoveryServices/vaults/replicationEvents/read  | レプリケーション イベントの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/read  | レプリケーション ファブリックの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationNetworks/read  | レプリケーション ファブリック ネットワークの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationNetworks/replicationNetworkMappings/read  | レプリケーション ファブリック ネットワーク マッピングの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/read  |  保護コンテナーの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectableItems/read  | すべての保護可能な項目の一覧の取得 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/read  | すべての保護された項目の一覧の取得 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectedItems/recoveryPoints/read  | 使用可能な回復ポイントの一覧の取得 |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationProtectionContainers/replicationProtectionContainerMappings/read  | 保護コンテナー マッピングの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationRecoveryServicesProviders/read  | Recovery Services プロバイダーの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/read  | レプリケーション ファブリック用のストレージの分類の読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/ replicationStorageClassifications/replicationStorageClassificationMappings/read  |  ストレージの分類マッピングの読み取り |
| Microsoft.RecoveryServices/vaults/replicationFabrics/replicationvCenters/read  |  登録済みの vCenter 情報の読み取り |
| Microsoft.RecoveryServices/vaults/replicationJobs/read  |  レプリケーション ジョブの状態の読み取り |
| Microsoft.RecoveryServices/vaults/replicationPolicies/read  |  レプリケーション ポリシーの読み取り |
| Microsoft.RecoveryServices/vaults/replicationRecoveryPlans/read  |  復旧計画の読み取り |
| Microsoft.RecoveryServices/Vaults/storageConfig/read  |  Recovery Services コンテナーのストレージ構成の読み取り |
| Microsoft.RecoveryServices/Vaults/tokenInfo/read  |  Recovery Services コンテナーのトークン情報の読み取り |
| Microsoft.RecoveryServices/Vaults/usages/read  |  Recovery Services コンテナーの使用状況の詳細の読み取り |
| Microsoft.Support/*  |  サポート チケットの作成と管理 |

### <a name="sql-db-contributor"></a>SQL DB の共同作業者
SQL データベースを管理できますが、そのセキュリティ関連ポリシーは管理できません

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |ロールとロール割り当ての読み取り |
| Microsoft.Insights/alertRules/* |アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Sql/servers/databases/* |SQL データベースの作成と管理 |
| Microsoft.Sql/servers/read |SQL Server の読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/databases/auditingPolicies/* |監査ポリシーを編集することはできません |
| Microsoft.Sql/servers/databases/auditingSettings/* |監査設定を編集することはできません |
| Microsoft.Sql/servers/databases/auditRecords/read |監査レコードを読み取ることはできません |
| Microsoft.Sql/servers/databases/connectionPolicies/* |接続ポリシーを編集することはできません |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |データ マスク ポリシーを編集することはできません |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |セキュリティの警告のポリシーを編集することはできません |
| Microsoft.Sql/servers/databases/securityMetrics/* |セキュリティ基準を編集することはできません |

### <a name="sql-security-manager"></a>SQL セキュリティ管理者
SQL サーバーおよびデータベースのセキュリティ関連ポリシーを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |Microsoft 承認の読み取り |
| Microsoft.Insights/alertRules/* |Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Sql/servers/auditingPolicies/* |SQL サーバー監査ポリシーの作成と管理 |
| Microsoft.Sql/servers/auditingSettings/* |SQL サーバー監査設定の作成と管理 |
| Microsoft.Sql/servers/databases/auditingPolicies/* |SQL サーバー データベース監査ポリシーの作成と管理 |
| Microsoft.Sql/servers/databases/auditingSettings/* |SQL サーバー データベース監査設定の作成と管理 |
| Microsoft.Sql/servers/databases/auditRecords/read |監査レコードの読み取り |
| Microsoft.Sql/servers/databases/connectionPolicies/* |SQL サーバー データベース接続ポリシーの作成と管理 |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |SQL サーバー データベース データ マスク ポリシーの作成と管理 |
| Microsoft.Sql/servers/databases/read |SQL データベースの読み取り |
| Microsoft.Sql/servers/databases/schemas/read |SQL サーバー データベース スキーマの読み取り |
| Microsoft.Sql/servers/databases/schemas/tables/columns/read |SQL サーバー データベース テーブル列の読み取り |
| Microsoft.Sql/servers/databases/schemas/tables/read |SQL サーバー データベース テーブルの読み取り |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |SQL サーバー データベース セキュリティの警告のポリシーの作成と管理 |
| Microsoft.Sql/servers/databases/securityMetrics/* |SQL サーバー データベース セキュリティ基準の作成と管理 |
| Microsoft.Sql/servers/read |SQL Server の読み取り |
| Microsoft.Sql/servers/securityAlertPolicies/* |SQL サーバー セキュリティの警告のポリシーの作成と管理 |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="sql-server-contributor"></a>SQL Server の共同作業者
SQL サーバーおよびデータベースを管理できますが、そのセキュリティ関連ポリシーは管理できません

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |承認の読み取り |
| Microsoft.Insights/alertRules/* |Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Sql/servers/* |SQL サーバーの作成と管理 |
| Microsoft.Support/* |サポート チケットの作成と管理 |

| **NotActions** |  |
| --- | --- |
| Microsoft.Sql/servers/auditingPolicies/* |SQL サーバー監査ポリシーは編集できません |
| Microsoft.Sql/servers/auditingSettings/* |SQL サーバー監査設定は編集できません |
| Microsoft.Sql/servers/databases/auditingPolicies/* |SQL サーバー データベース監査ポリシーは編集できません |
| Microsoft.Sql/servers/databases/auditingSettings/* |SQL サーバー データベース監査設定は編集できません |
| Microsoft.Sql/servers/databases/auditRecords/read |監査レコードを読み取ることはできません |
| Microsoft.Sql/servers/databases/connectionPolicies/* |SQL サーバー データベース接続ポリシーは編集できません |
| Microsoft.Sql/servers/databases/dataMaskingPolicies/* |SQL サーバー データベース データ マスク ポリシーは編集できません |
| Microsoft.Sql/servers/databases/securityAlertPolicies/* |SQL サーバー データベース セキュリティの警告のポリシーは編集できません |
| Microsoft.Sql/servers/databases/securityMetrics/* |SQL サーバー データベース セキュリティ基準は編集できません |
| Microsoft.Sql/servers/securityAlertPolicies/* |SQL サーバー セキュリティの警告のポリシーは編集できません |

### <a name="classic-storage-account-contributor"></a>従来のストレージ アカウントの共同作業者
従来のストレージ アカウントを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |承認の読み取り |
| Microsoft.ClassicStorage/storageAccounts/* |ストレージ アカウントの作成と管理 |
| Microsoft.Insights/alertRules/* |Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="storage-account-contributor"></a>ストレージ アカウントの共同作業者
ストレージ アカウントを管理できますが、ストレージ アカウントにアクセスすることはできません。

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |あらゆる承認の読み取り |
| Microsoft.Insights/alertRules/* |Insights アラート ルールの作成と管理 |
| Microsoft.Insights/diagnosticSettings/* |診断設定の管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Storage/storageAccounts/* |ストレージ アカウントの作成と管理 |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="support-request-contributor"></a>Support request の共同作成者
サブスクリプションのスコープでのサポート チケットの作成と管理

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read | 承認の読み取り |
| Microsoft.Support/* | サポート チケットの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read | ロールとロール割り当ての読み取り |

### <a name="user-access-administrator"></a>ユーザーアクセスの管理者
Azure リソースに対するユーザー アクセスを管理できます

| **アクション** |  |
| --- | --- |
| */read |機密データを除くあらゆる種類のリソースの読み取り |
| Microsoft.Authorization/* |承認の管理 |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="classic-virtual-machine-contributor"></a>従来の仮想マシンの共同作業者
接続している仮想ネットワークやストレージ アカウント以外の従来の仮想マシンを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |承認の読み取り |
| Microsoft.ClassicCompute/domainNames/* |従来のコンピューティング ドメイン名の作成と管理 |
| Microsoft.ClassicCompute/virtualMachines/* |仮想マシンの作成と管理 |
| Microsoft.ClassicNetwork/networkSecurityGroups/join/action |ネットワーク セキュリティ グループの接続 |
| Microsoft.ClassicNetwork/reservedIps/link/action |予約済み IP へのリンク付け |
| Microsoft.ClassicNetwork/reservedIps/read |予約済み IP アドレスの読み取り |
| Microsoft.ClassicNetwork/virtualNetworks/join/action |仮想ネットワークの接続 |
| Microsoft.ClassicNetwork/virtualNetworks/read |仮想ネットワークの読み取り |
| Microsoft.ClassicStorage/storageAccounts/disks/read |ストレージ アカウント ディスクの読み取り |
| Microsoft.ClassicStorage/storageAccounts/images/read |ストレージ アカウント イメージの読み取り |
| Microsoft.ClassicStorage/storageAccounts/listKeys/action |ストレージ アカウント キーの一覧表示 |
| Microsoft.ClassicStorage/storageAccounts/read |従来のストレージ アカウントの読み取り |
| Microsoft.Insights/alertRules/* |Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="virtual-machine-contributor"></a>仮想マシンの共同作業者
接続している仮想ネットワークやストレージ アカウント以外の仮想マシンを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |承認の読み取り |
| Microsoft.Compute/availabilitySets/* |コンピューティング可用性セットの作成と管理 |
| Microsoft.Compute/locations/* |コンピューティングの場所の作成と管理 |
| Microsoft.Compute/virtualMachines/* |仮想マシンの作成と管理 |
| Microsoft.Compute/virtualMachineScaleSets/* |仮想マシン スケールセットの作成と管理 |
| Microsoft.Insights/alertRules/* |Insights アラート ルールの作成と管理 |
| Microsoft.Network/applicationGateways/backendAddressPools/join/action |ネットワーク アプリケーション ゲートウェイのバックエンド アドレス プールの接続 |
| Microsoft.Network/loadBalancers/backendAddressPools/join/action |ロード バランサーのバックエンド アドレス プールの接続 |
| Microsoft.Network/loadBalancers/inboundNatPools/join/action |ロード バランサーの受信 NAT プールの接続 |
| Microsoft.Network/loadBalancers/inboundNatRules/join/action |ロード バランサーの受信 NAT 規則の接続 |
| Microsoft.Network/loadBalancers/read |ロード バランサーの読み取り |
| Microsoft.Network/locations/* |ネットワークの場所の作成と管理 |
| Microsoft.Network/networkInterfaces/* |ネットワーク インターフェイスの作成と管理 |
| Microsoft.Network/networkSecurityGroups/join/action |ネットワーク セキュリティ グループの接続 |
| Microsoft.Network/networkSecurityGroups/read |ネットワーク セキュリティ グループの読み取り |
| Microsoft.Network/publicIPAddresses/join/action |ネットワーク パブリック IP アドレスの接続 |
| Microsoft.Network/publicIPAddresses/read |ネットワーク パブリック IP アドレスの読み取り |
| Microsoft.Network/virtualNetworks/read |仮想ネットワークの読み取り |
| Microsoft.Network/virtualNetworks/subnets/join/action |仮想ネットワーク サブネットの接続 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Storage/storageAccounts/listKeys/action |ストレージ アカウント キーの一覧表示 |
| Microsoft.Storage/storageAccounts/read |ストレージ アカウントの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="classic-network-contributor"></a>従来のネットワークの共同作業者
従来の仮想ネットワークと予約済み IP を管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |承認の読み取り |
| Microsoft.ClassicNetwork/* |従来のネットワークの作成と管理 |
| Microsoft.Insights/alertRules/* |Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |

### <a name="web-plan-contributor"></a>Web プランの共同作業者
Web プランを管理できます

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |承認の読み取り |
| Microsoft.Insights/alertRules/* |Insights アラート ルールの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |
| Microsoft.Web/serverFarms/* |サーバー ファームの作成と管理 |

### <a name="website-contributor"></a>Web サイトの共同作業者
Web サイトを管理できますが、接続されている Web プランは管理できません

| **アクション** |  |
| --- | --- |
| Microsoft.Authorization/*/read |承認の読み取り |
| Microsoft.Insights/alertRules/* |Insights アラート ルールの作成と管理 |
| Microsoft.Insights/components/* |Insights コンポーネントの作成と管理 |
| Microsoft.ResourceHealth/availabilityStatuses/read |リソースの正常性の読み取り |
| Microsoft.Resources/deployments/* |リソース グループ デプロイの作成と管理 |
| Microsoft.Resources/subscriptions/resourceGroups/read |リソース グループの読み取り |
| Microsoft.Support/* |サポート チケットの作成と管理 |
| Microsoft.Web/certificates/* |Web サイト証明書の作成と管理 |
| Microsoft.Web/listSitesAssignedToHostName/read |ホスト名に割り当てられたサイトの読み取り |
| Microsoft.Web/serverFarms/join/action |サーバー ファームの接続 |
| Microsoft.Web/serverFarms/read |サーバー ファームの読み取り |
| Microsoft.Web/sites/* |Web サイトの作成と管理 (サイト作成では、関連付けられた App Service プランに対する書き込みアクセス許可も必要です) |

## <a name="see-also"></a>関連項目
* [ロールベースのアクセス制御](role-based-access-control-configure.md): Azure Portal での RBAC の基本について説明します。
* [Azure RBAC のカスタム ロール](role-based-access-control-custom-roles.md): アクセスのニーズに合わせてカスタム ロールを作成する方法について説明します。
* [アクセス変更履歴レポートの作成](role-based-access-control-access-change-history-report.md): RBAC でのロール割り当ての変更を追跡します。
* [ロールベースのアクセス制御のトラブルシューティング](role-based-access-control-troubleshooting.md): 一般的な問題の修正に関する推奨事項を紹介します。
