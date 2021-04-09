---
title: Azure VM 上のバックアップされた SAP HANA データベースを管理する
description: この記事では、Azure 仮想マシン上で実行されている SAP HANA データベースを管理および監視するための一般的なタスクについて説明します。
ms.topic: conceptual
ms.date: 11/12/2019
ms.openlocfilehash: 54d3341a83873ad3cc50815f04a0b252bb44438e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703768"
---
# <a name="manage-and-monitor-backed-up-sap-hana-databases"></a>バックアップされた SAP HANA データベースを管理および監視する

この記事では、Azure 仮想マシン (VM) 上で実行され、[Azure Backup](./backup-overview.md) サービスによって Azure Backup Recovery Services コンテナーにバックアップされた SAP HANA データベースを管理および監視するための一般的なタスクについて説明します。 ジョブやアラートを監視したり、オンデマンド バックアップをトリガーしたり、ポリシーを編集したり、データベースの保護を停止および再開したり、バックアップから VM を登録解除したりする方法について説明します。

SAP HANA データベースのバックアップをまだ構成していない場合は、「[Azure VM での SAP HANA データベースのバックアップ](./backup-azure-sap-hana-database.md)」を参照してください。

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>ポータルで手動バックアップ ジョブを監視する

Azure Backup では、手動でトリガーされたすべてのジョブが Azure portal の **[バックアップ ジョブ]** セクションに表示されます。

![[バックアップ ジョブ] セクション](./media/sap-hana-db-manage/backup-jobs.png)

このポータルに表示されるジョブには、データベースの検出と登録、バックアップ操作、復元操作などがあります。 スケジュールされたジョブ (ログ バックアップを含む) はこのセクションに表示されません。 また、SAP HANA ネイティブ クライアント (Studio/Cockpit/DBA Cockpit) から手動でトリガーされたバックアップもここには表示されません。

![[バックアップ ジョブ] 一覧](./media/sap-hana-db-manage/backup-jobs-list.png)

監視の詳細については、[Azure portal での監視](./backup-azure-monitoring-built-in-monitor.md)に関するページおよび [Azure Monitor を使用した監視](./backup-azure-monitoring-use-azuremonitor.md)に関するページを参照してください。

## <a name="view-backup-alerts"></a>バックアップ アラートを表示する

アラートは、SAP HANA データベースのバックアップを監視するための簡単な手段です。 アラートは、バックアップによって生成される多数のイベントに惑わされることなく、最も関心があるイベントに焦点を絞るために役立ちます。 Azure Backup ではアラートを設定することができ、それを次のように監視できます。

* [Azure portal](https://portal.azure.com/) にサインインします。
* コンテナー ダッシュボードで、 **[バックアップ アラート]** を選択します。

  ![コンテナー ダッシュボードの [バックアップ アラート]](./media/sap-hana-db-manage/backup-alerts-dashboard.png)

* アラートを表示できるようになります。

  ![バックアップ アラートの一覧](./media/sap-hana-db-manage/backup-alerts-list.png)

* アラートを選択すると、その詳細が表示されます。

  ![[アラートの詳細]](./media/sap-hana-db-manage/alert-details.png)

Azure Backup では現在、アラートを電子メール経由で送信できます。 これらのアラートは次のように処理されます。

* すべてのバックアップ エラーに対してトリガーされます。
* エラー コードごとにデータベース レベルで統合されます。
* データベースの最初のバックアップ エラーに対してのみ送信されます。

監視の詳細については、[Azure portal での監視](./backup-azure-monitoring-built-in-monitor.md)に関するページおよび [Azure Monitor を使用した監視](./backup-azure-monitoring-use-azuremonitor.md)に関するページを参照してください。

## <a name="management-operations"></a>管理操作

Azure Backup では、サポートされる管理操作が豊富なため、バックアップされた SAP HANA データベースの管理が簡単になります。 以降のセクションでは、これらの操作について詳細に説明します。

### <a name="run-an-on-demand-backup"></a>オンデマンド バックアップを実行する

バックアップは、ポリシー スケジュールに従って実行されます。 次のように、バックアップ オンデマンを実行できます。

1. コンテナー メニューで **[バックアップ項目]** を選択します。
2. **[バックアップ項目]** で、SAP HANA データベースを実行している VM を選択してから、 **[今すぐバックアップ]** を選択します。
3. **[今すぐバックアップ]** で、実行するバックアップの種類を選択します。 **[OK]** をクリックします。 このバックアップ項目に関連付けられているポリシーに従って、このバックアップが保持されます。
4. ポータルの通知を監視します。 コンテナー ダッシュボードの **[バックアップ ジョブ]**  >  **[進行中]** でジョブの進行状況を監視できます。 データベースのサイズによっては、最初のバックアップの作成に時間がかかる場合があります。

既定では、オンデマンドバックアップの保持期間は 45 日です。

### <a name="hana-native-client-integration"></a>HANA ネイティブ クライアントの統合

#### <a name="backup"></a>バックアップ

HANA ネイティブ クライアントから (**Backint** に) トリガーされたオンデマンドのバックアップは、 **[バックアップ項目]** ページのバックアップ リストに表示されます。

![最後に実行されたバックアップ](./media/sap-hana-db-manage/last-backups.png)

**[バックアップ ジョブ]** ページから [これらのバックアップを監視する](#monitor-manual-backup-jobs-in-the-portal)こともできます。

これらのオンデマンドのバックアップは、復元のための復元ポイント一覧にも表示されます。

![復元ポイントの一覧](./media/sap-hana-db-manage/list-restore-points.png)

#### <a name="restore"></a>復元

同じマシンに復元するために (**Backint** を使用して) HANA ネイティブ クライアントからトリガーされた復元は、 **[バックアップ ジョブ]** ページから [監視](#monitor-manual-backup-jobs-in-the-portal)することができます。

### <a name="run-sap-hana-native-client-backup-to-local-disk-on-a-database-with-azure-backup-enabled"></a>Azure Backup が有効になっているデータベースでローカル ディスクへの SAP HANA ネイティブ クライアント バックアップを実行する

Azure Backup でバックアップされているデータベースの (HANA Studio/Cockpit を使用した) ローカル バックアップを取得する場合は、次の操作を行います。

1. データベースの完全バックアップまたはログ バックアップがすべて完了するまで待ちます。 SAP HANA Studio/Cockpit で状態を確認します。
2. 関連する DB で、以下を行います
    1. backint パラメーターを設定解除します。 これを行うには、**systemdb** >  **[構成]**  >  **[データベースの選択]**  >  **[Filter (Log)]\(フィルター (ログ)\)** の順にダブルクリックします。
        * enable_auto_log_backup:いいえ
        * log_backup_using_backint:False
        * catalog_backup_using_backint:False
3. データベースのオンデマンド完全バックアップを作成します
4. その後、手順を逆にします。 上記と同じ関連 DB について、以下を行います
    1. backint パラメーターを再度有効にします
        1. catalog_backup_using_backint:True
        1. log_backup_using_backint:True
        1. enable_auto_log_backup:はい

### <a name="manage-or-clean-up-the-hana-catalog-for-a-database-with-azure-backup-enabled"></a>Azure Backup が有効になっているデータベースの HANA カタログを管理またはクリーンアップする

バックアップ カタログを編集またはクリーンアップする場合は、次の手順を実行します。

1. データベースの完全バックアップまたはログ バックアップがすべて完了するまで待ちます。 SAP HANA Studio/Cockpit で状態を確認します。
2. 関連する DB で、以下を行います
    1. backint パラメーターを設定解除します。 これを行うには、**systemdb** >  **[構成]**  >  **[データベースの選択]**  >  **[Filter (Log)]\(フィルター (ログ)\)** の順にダブルクリックします。
        * enable_auto_log_backup:いいえ
        * log_backup_using_backint:False
        * catalog_backup_using_backint:False
3. カタログを編集して古いエントリを削除します
4. その後、手順を逆にします。 上記と同じ関連 DB について、以下を行います
    1. backint パラメーターを再度有効にします
        1. catalog_backup_using_backint:True
        1. log_backup_using_backint:True
        1. enable_auto_log_backup:はい

### <a name="change-policy"></a>ポリシーを変更する

SAP HANA バックアップ項目のための、基になるポリシーを変更できます。

* コンテナー ダッシュボードで、 **[バックアップ項目]** に移動します。

  ![バックアップ項目を選択します](./media/sap-hana-db-manage/backup-items.png)

* **[Azure VM の SAP HANA]** を選択します

  ![[Azure VM の SAP HANA] を選択する](./media/sap-hana-db-manage/sap-hana-in-azure-vm.png)

* 変更対象の、基になるポリシーを含むバックアップ項目を選択します
* 既存のバックアップ ポリシーを選択します。

  ![既存のバックアップ ポリシーを選択します](./media/sap-hana-db-manage/existing-backup-policy.png)

* リストから選択して、ポリシーを変更します。 必要に応じて、[新しいバックアップ ポリシーを作成](./backup-azure-sap-hana-database.md#create-a-backup-policy)します。

  ![ドロップダウン リストからポリシーを選択します](./media/sap-hana-db-manage/choose-backup-policy.png)

* 変更を保存します

  ![変更を保存します](./media/sap-hana-db-manage/save-changes.png)

* ポリシーの変更は、関連付けられているすべてのバックアップ項目に影響し、対応する **保護の構成** ジョブをトリガーします。

>[!NOTE]
> 保持期間の変更は、新しい復旧ポイントだけでなく古い復旧ポイントすべてにもさかのぼって適用されます。

### <a name="modify-policy"></a>ポリシーの変更

バックアップの種類、頻度、保持期間を変更するには、ポリシーを変更します。

>[!NOTE]
>保有期間の変更は、新しい復旧ポイントだけでなく、すべての古いものにもさかのぼって適用されます。

1. コンテナーのダッシュボードで、 **[管理] > [バックアップ ポリシー]** に移動し、編集するポリシーを選択します。

   ![編集するポリシーを選択する](./media/sap-hana-db-manage/manage-backup-policies.png)

1. **[変更]** を選択します。

   ![[変更] を選択する](./media/sap-hana-db-manage/modify-policy.png)

1. バックアップの種類の頻度を選択します。

   ![バックアップの頻度を選択する](./media/sap-hana-db-manage/choose-frequency.png)

ポリシーの変更は、関連するすべてのバックアップ項目に影響し、対応する **保護の構成** ジョブをトリガーします。

### <a name="inconsistent-policy"></a>不整合なポリシー

場合によっては、ポリシーの変更操作によって、一部のバックアップ項目に **不整合な** ポリシーのバージョンができてしまうことがあります。 これは、ポリシーの変更操作がトリガーされた後に、対応する **保護の構成** ジョブがバックアップ項目に対して失敗した場合に発生します。 バックアップ項目のビューには、次のように表示されます。

![不整合なポリシー](./media/sap-hana-db-manage/inconsistent-policy.png)

次のように、1 回のクリックで、影響を受けるすべての項目のポリシー バージョンを修正できます。

![ポリシーのバージョンを修正する](./media/sap-hana-db-manage/fix-policy-version.png)

### <a name="stop-protection-for-an-sap-hana-database"></a>SAP HANA データベースの保護を停止する

SAP HANA データベースの保護は、次のいくつかの方法で停止できます。

* 今後のバックアップ ジョブすべてを停止し、すべての復旧ポイントを削除する。
* 今後のすべてのバックアップ ジョブを停止し、復旧ポイントはそのままにする。

復旧ポイントをそのままにする場合、以下の項目を念頭に置いてください。

* 復旧ポイントはすべて永久に変更されず、削除はすべて保護の停止で停止し、データを保持します
* 保護されたインスタンスと使用されたストレージに対して課金されます。 詳細については、「[Azure Backup の価格](https://azure.microsoft.com/pricing/details/backup/)」をご覧ください。
* バックアップを停止しないでデータ ソースを削除すると、新しいバックアップは失敗します。

データベースの保護を停止するには、次の手順を実行します。

* コンテナー ダッシュボードで **[バックアップ項目]** を選択します。
* **[バックアップの管理の種類]** で、 **[Azure VM の SAP HANA]** を選択します。

  ![[Azure VM の SAP HANA] を選択する](./media/sap-hana-db-manage/sap-hana-azure-vm.png)

* 保護を停止するデータベースを選択します。

  ![保護を停止するデータベースを選択する](./media/sap-hana-db-manage/select-database.png)

* データベース メニューで、 **[バックアップの停止]** を選択します。

  ![[バックアップの停止] を選択する](./media/sap-hana-db-manage/stop-backup.png)

* **[バックアップの停止]** メニューで、データを保持するか削除するかを選択します。 必要に応じて、理由とコメントを入力します。

  ![データの保持または削除を選択する](./media/sap-hana-db-manage/retain-backup-data.png)

* **[バックアップの停止]** を選択する

### <a name="resume-protection-for-an-sap-hana-database"></a>SAP HANA データベースの保護を再開する

SAP HANA データベースの保護を停止するときに **[バックアップ データの保持]** オプションを選択した場合は、後で保護を再開できます。 バックアップされたデータを保持しない場合は、保護を再開できません。

SAP HANA データベースの保護を再開するには:

* バックアップ項目を開き、 **[バックアップの再開]** を選択します。

   ![[バックアップの再開] を選択する](./media/sap-hana-db-manage/resume-backup.png)

* **[バックアップ ポリシー]** メニューで、ポリシーを選択して、 **[保存]** を選択します。

### <a name="upgrading-from-sdc-to-mdc"></a>SDC から MDC にアップグレードする

[SDC から MDC へのアップグレードの後に](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-a-change-in-sid) SAP HANA データベースのバックアップを続行する方法について学習してください。

### <a name="upgrading-from-sdc-to-mdc-without-a-sid-change"></a>SID を変更せずに SDC から MDC にアップグレードする

[SDC から MDC へのアップグレード後に SID が変更されていない](backup-azure-sap-hana-database-troubleshoot.md#sdc-to-mdc-upgrade-with-no-change-in-sid) SAP HANA データベースのバックアップを続行する方法について学習してください。

### <a name="upgrading-to-a-new-version-in-either-sdc-or-mdc"></a>SDC または MDC で新しいバージョンにアップグレードする

[バージョンがアップグレードされた](backup-azure-sap-hana-database-troubleshoot.md#sdc-version-upgrade-or-mdc-version-upgrade-on-the-same-vm) SAP HANA データベースのバックアップを続行する方法について学習してください。

### <a name="unregister-an-sap-hana-instance"></a>SAP HANA インスタンスを登録解除する

SAP HANA インスタンスの登録解除は、保護を無効にした後、コンテナーを削除する前に行います。

* コンテナー ダッシュボードの **[管理]** で、 **[バックアップ インフラストラクチャ]** を選択します。

   ![[バックアップ インフラストラクチャ] を選択する](./media/sap-hana-db-manage/backup-infrastructure.png)

* **[バックアップの管理の種類]** を **[Workload in Azure VM] (Azure VM のワークロード)** として選択します。

   ![[バックアップの管理の種類] を [Workload in Azure VM] (Azure VM のワークロード) として選択する](./media/sap-hana-db-manage/backup-management-type.png)

* **[保護されたサーバー]** で、登録解除するインスタンスを選択します。 コンテナーを削除するには、すべてのサーバー/インスタンスを登録解除する必要があります。

* 保護されたインスタンスを右クリックし、 **[登録解除]** を選択します。

   ![[登録解除] を選択する](./media/sap-hana-db-manage/unregister.png)

### <a name="re-register-extension-on-the-sap-hana-server-vm"></a>SAP HANA サーバー VM の拡張機能を再登録する

VM のワークロード拡張機能が何らかの理由で影響を受けることがあります。 そのような場合、VM 上でトリガーされるすべての操作が失敗するようになります。 そこで、場合によっては、VM で拡張を再登録する必要があります。 再登録操作によって、操作を続行させるために、VM にワークロード バックアップ拡張機能が再インストールされます。

このオプションは注意して使用してください。既に正常な拡張機能がある VM でこの操作がトリガーされると、拡張機能が再起動されます。 それにより、進行中のジョブがすべて失敗することがあります。 再登録操作をトリガーする前に、[こちらの兆候](backup-azure-sap-hana-database-troubleshoot.md#re-registration-failures)がないか確認してください。

## <a name="next-steps"></a>次のステップ

* [SAP HANA データベースをバックアップする場合の一般的な問題をトラブルシューティングする](./backup-azure-sap-hana-database-troubleshoot.md)方法について学習します。
