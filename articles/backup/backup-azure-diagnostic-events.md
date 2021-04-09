---
title: Recovery Services コンテナーの診断設定を使用する
description: この記事では、Azure Backup の新旧の診断イベントの使用方法を説明します。
ms.topic: conceptual
ms.date: 10/30/2019
ms.openlocfilehash: b2130f06e17dd2b5cf8461d4e58342ee41c14f96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100575415"
---
# <a name="use-diagnostics-settings-for-recovery-services-vaults"></a>Recovery Services コンテナーの診断設定を使用する

Azure Backup を使用して、分析、アラート、レポートの目的で収集して使用できる診断イベントを送信できます。

Azure portal を使用して Recovery Services コンテナーの診断設定を構成できます。それには、コンテナーに移動し、 **[診断設定]** を選択します。 **[+ 診断設定の追加]** を選択することで、1 つ以上の診断イベントをストレージ アカウント、イベント ハブ、または Log Analytics (LA) ワークスペースに送信できます。

![[診断設定] ウィンドウ](./media/backup-azure-diagnostics-events/diagnostics-settings-blade.png)

## <a name="diagnostics-events-available-for-azure-backup-users"></a>Azure Backup ユーザーが利用できる診断イベント

Azure Backup には、次の診断機能が用意されています。 各イベントによって、バックアップ関連成果物の特定のセットに関する詳細なデータが提供されます。

* CoreAzureBackup
* AddonAzureBackupAlerts
* AddonAzureBackupProtectedInstance
* AddonAzureBackupJobs
* AddonAzureBackupPolicy
* AddonAzureBackupStorage

まだ[レガシ イベント](#legacy-event)の AzureBackupReport を使用している場合は、上記のイベントを使用するように切り替えることをお勧めします。

詳細については、「[Azure Backup 診断イベントのデータ モデル](./backup-azure-reports-data-model.md)」を参照してください。

これらのイベントのデータを、ストレージ アカウント、Log Analytics ワークスペース、またはイベント ハブに送信できます。 このデータを Log Analytics ワークスペースに送信する場合は、 **[診断設定]** 画面で **[リソース固有]** トグルを選択します。 詳細については、次のセクションを参照してください。

## <a name="use-diagnostics-settings-with-log-analytics"></a>Log Analytics での診断設定の使用

Azure Backup を使用して、コンテナー診断データをバックアップ用の専用 Log Analytics テーブルに送信できるようになりました。 これらのテーブルは、[リソース固有テーブル](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)と呼ばれます。

コンテナーの診断データを Log Analytics に送信するには、次のようにします。

1. お使いのコンテナーに移動し、 **[診断設定]** を選択します。 **[+ 診断設定を追加する]** を選択します。
1. 診断設定に名前を付けます。
1. **[Log Analytics に送信]** チェック ボックスをオンにし、Log Analytics ワークスペースを選択します。
1. トグルの **[リソース固有]** を選択し、次の 6 つのイベントを選択します。**CoreAzureBackup**、**AddonAzureBackupJobs**、**AddonAzureBackupAlerts**、**AddonAzureBackupPolicy**、**AddonAzureBackupStorage**、**AddonAzureBackupProtectedInstance**。
1. **[保存]** を選択します。

   ![リソース固有モード](./media/backup-azure-diagnostics-events/resource-specific-blade.png)

データが Log Analytics ワークスペースに送信されると、これらのイベントごとに専用のテーブルがワークスペース内に作成されます。 これらのテーブルのどれに対しても、直接クエリを実行できます。 また、必要に応じて、これらのテーブル間で JOIN または UNION を実行できます。

> [!IMPORTANT]
> 6 つのイベント (CoreAzureBackup、AddonAzureBackupJobs、AddonAzureBackupAlerts、AddonAzureBackupPolicy、AddonAzureBackupStorage、AddonAzureBackupProtectedInstance) は、[バックアップ レポート](./configure-reports.md)のリソース固有モードで "*のみ*" サポートされます。 "*これら 6 つのイベントのデータを Azure 診断モードで送信しようとした場合、データはバックアップ レポートに一切表示されません。* "

## <a name="legacy-event"></a>レガシ イベント

従来、コンテナーのバックアップ関連の診断データは、すべてが AzureBackupReport という名前の 1 つのイベントに含まれていました。 ここに示した 6 つのイベントは、実質的には、AzureBackupReport に含まれるすべてのデータを分解したものです。

現時点では、AzureBackupReport イベントは引き続きサポートされています。これは、ユーザーがこのイベントに対して既存のカスタム クエリを持っている場合の下位互換性を保つためです。 例として、カスタム ログ アラートやカスタム視覚化エフェクトがあります。 "*可能な限り早く [新しいイベント](#diagnostics-events-available-for-azure-backup-users)にアップグレードすることをお勧めします。* " 新しいイベントでは次のようになります。

* ログ クエリでデータをより簡単に操作できます。
* スキーマとその構造をより確実に発見できます。
* インジェスト待機時間とクエリ時間の両方でパフォーマンスが向上します。

"*Azure 診断モードのレガシ イベントは、最終的に非推奨になります。後で複雑な移行を回避するには、新しいイベントを選択することが役に立つ場合があります。* " Log Analytics を使用する[レポート ソリューション](./configure-reports.md)でも、レガシ イベントからのデータのサポートが停止されます。

### <a name="steps-to-move-to-new-diagnostics-settings-for-a-log-analytics-workspace"></a>Log Analytics ワークスペース用の新しい診断設定に移行するための手順

1. レガシ イベントを使用して Log Analytics ワークスペースにデータを送信しているコンテナーと、それらが属しているサブスクリプションを特定します。 各ワークスペースで次のクエリを実行して、これらのコンテナーとサブスクリプションを特定します。

    ````Kusto
    let RangeStart = startofday(ago(3d));
    let VaultUnderAzureDiagnostics = (){
        AzureDiagnostics
        | where TimeGenerated >= RangeStart | where Category == "AzureBackupReport" and OperationName == "Vault" and SchemaVersion_s == "V2"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
    let VaultUnderResourceSpecific = (){
        CoreAzureBackup
        | where TimeGenerated >= RangeStart | where OperationName == "Vault"
        | summarize arg_max(TimeGenerated, *) by ResourceId
        | project ResourceId, Category};
        // Some Workspaces will not have AzureDiagnostics Table, so you need to use isFuzzy
    let CombinedVaultTable = (){
        union isfuzzy = true
        (VaultUnderAzureDiagnostics() ),
        (VaultUnderResourceSpecific() )
        | distinct ResourceId, Category};
    CombinedVaultTable | where Category == "AzureBackupReport"
    | join kind = leftanti (
    CombinedVaultTable | where Category == "CoreAzureBackup"
    ) on ResourceId
    | parse ResourceId with * "SUBSCRIPTIONS/" SubscriptionId:string "/RESOURCEGROUPS" * "MICROSOFT.RECOVERYSERVICES/VAULTS/" VaultName:string
    | project ResourceId, SubscriptionId, VaultName
    ````

    以下に、あるワークスペースで実行されているクエリのスクリーンショットを示します。

    ![ワークスペース クエリ](./media/backup-azure-diagnostics-events/workspace-query.png)

2. Azure Backup に[組み込まれている Azure ポリシー定義](./azure-policy-configure-diagnostics.md)を使用して、指定したスコープ内のすべてのコンテナーに新しい診断設定を追加します。 このポリシーによって、診断設定がないか、または従来の診断設定しかないコンテナーに新しい診断設定が追加されます。 このポリシーは、一度にサブスクリプションまたはリソース グループ全体に割り当てることができます。 ポリシーを割り当てる各サブスクリプションに対して、所有者のアクセス権を持っている必要があります。

すべてのカスタム クエリを移行し終わり、新しいテーブルのデータを使用するようになるまで、AzureBackupReport と 6 つの新しいイベントに対して個別の診断設定を選択できます。 次の図に、2 つの診断設定を持つコンテナーの例を示します。 最初の **Setting1** という名前の設定では、AzureBackupReport イベントのデータが、Azure 診断モードで Log Analytics ワークスペースに送信されます。 2 つ目の **Setting2** という名前の設定では、6 つの新しい Azure Backup イベントのデータが、リソース固有モードで Log Analytics ワークスペースに送信されます。

![2 つの設定](./media/backup-azure-diagnostics-events/two-settings-example.png)

> [!IMPORTANT]
> AzureBackupReport イベントは、Azure 診断モードで "*のみ*" サポートされています。 "*このイベントのデータをリソース固有モードで送信しようとした場合、データはまったく Log Analytics ワークスペースに送信されません。* "

> [!NOTE]
> **[Azure diagnostics]\(Azure 診断\)** または **[リソース固有]** のトグルは、 **[Log Analytics への送信]** がオンの場合にのみ表示されます。 ストレージ アカウントまたはイベント ハブにデータを送信するには、必要な宛先を選択し、目的のイベントのチェック ボックスをオンにします。追加の入力はありません。 繰り返しになりますが、今後は、レガシ イベントである AzureBackupReport を選択しないことをお勧めします。

## <a name="send-azure-site-recovery-events-to-log-analytics"></a>Log Analytics に Azure Site Recovery イベントを送信する

Azure Backup イベントと Azure Site Recovery イベントは、同じ Recovery Services コンテナーから送信されます。 現在、リソース固有テーブルには Azure Site Recovery を使用できません。 図に示すように、Log Analytics に Azure Site Recovery イベントを送信するユーザーは、Azure 診断モード "*のみ*" 使用するように求められます。 "*Azure Site Recovery イベントに対してリソース固有モードを選択すると、必要なデータが Log Analytics ワークスペースに送信されなくなります*。"

![Site Recovery イベント](./media/backup-azure-diagnostics-events/site-recovery-settings.png)

まとめると次のようになります。

* Azure Diagnostics で既に Log Analytics 診断が設定されていて、カスタム クエリが記述されている場合は、新しいイベントのデータを使用するようにクエリを移行するまで、設定を "*そのまま*" にしておきます。
* 推奨されているように新しいテーブルにオンボードする場合は、**新しい** 診断設定を作成し、 **[リソース固有]** を選択して、6 つの新しいイベントを選択します。
* 現在 Azure Site Recovery イベントを Log Analytics に送信している場合は、それらのイベントに対してリソース固有モードを選択 "*しないでください*"。 そうしないと、これらのイベントのデータが Log Analytics ワークスペースに送られません。 代わりに、追加の診断設定を作成し、 **[Azure diagnostics]\(Azure 診断\)** を選択して、関連する Azure Site Recovery イベントを選択します。

次の図に、1 つのコンテナーに対して 3 つの診断設定を使用しているユーザーの例を示します。 最初の **Setting1** という名前の設定では、AzureBackupReport イベントのデータが、Azure 診断モードで Log Analytics ワークスペースに送信されます。 2 つ目の **Setting2** という名前の設定では、6 つの新しい Azure Backup イベントのデータが、リソース固有モードで Log Analytics ワークスペースに送信されます。 3 つ目の **Setting3** という名前の設定では、Azure Site Recovery イベントのデータが、Azure 診断モードで Log Analytics ワークスペースに送信されます。

![3 つの設定](./media/backup-azure-diagnostics-events/three-settings-example.png)

## <a name="next-steps"></a>次のステップ

[診断イベント用の Log Analytics データ モデルを確認する](./backup-azure-reports-data-model.md)
