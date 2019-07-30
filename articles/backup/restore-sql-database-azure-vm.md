---
title: Azure Backup を使用して Azure VM 上でバックアップ済みの SQL Server データベースを復元する | Microsoft Docs
description: この記事では、Azure VM 上で実行されており、Azure Backup でバックアップしてある SQL Server データベースを復元する方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 05/22/2019
ms.author: raynew
ms.openlocfilehash: f6bd9ea70a3334fbf3843a6b5d44d10a929e20ae
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68467209"
---
# <a name="restore-sql-server-databases-on-azure-vms"></a>Azure VM 上の SQL Server データベースを復元する

この記事では、Azure 仮想マシン (VM) 上で実行されており、[Azure Backup](backup-overview.md) サービスで Azure Backup Recovery Services コンテナーにバックアップしてある SQL Server データベースを復元する方法について説明します。

この記事では、SQL Server データベースを復元する方法について説明します。 詳細については、[Azure VM で SQL Server データベースをバックアップする](backup-azure-sql-database.md)方法に関するページをご覧ください。

## <a name="restore-to-a-time-or-a-recovery-point"></a>特定の時刻または復旧ポイントに復元する

Azure Backup は、Azure VM 上で実行されている SQL Server データベースを次のように復元できます。

- トランザクション ログ バックアップを使用して、特定の日付または時刻 (秒) に復元します。 Azure Backup は、選択された時刻に基づいて復元するために必要な、適切な完全または差分バックアップおよび一連のログ バックアップを自動的に判定します。
- 特定の復旧ポイントに復元するには、特定の完全または差分バックアップを復元します。


## <a name="prerequisites"></a>前提条件

データベースを復元する前に、次のことに注意してください。

- このデータベースを同じ Azure リージョン内の SQL Server のインスタンスに復元することができます。
- 宛先サーバーがソースと同じコンテナーに登録されている必要があります。
- TDE で暗号化されたデータベースを別の SQL Server に復元するには、まず[証明書を宛先サーバーに復元する](https://docs.microsoft.com/sql/relational-databases/security/encryption/move-a-tde-protected-database-to-another-sql-server?view=sql-server-2017)必要があります。
- "マスター" データベースを復元する前に、スタートアップ オプション **-m AzureWorkloadBackup** を使用して、シングル ユーザー モードで SQL Server インスタンスを起動します。
    - **-m** の値はクライアントの名前です。
    - 指定されたクライアント名のみで接続を開くことができます。
- すべてのシステム データベース (モデル、マスター、msdb) の場合は、復元をトリガーする前に SQL Server Agent サービスを停止してください。
- これらのいずれかのデータベースへの接続を奪おうとする可能性があるアプリケーションはすべて閉じます。
- サーバーで複数のインスタンスが実行している場合、すべてのインスタンスが稼働している必要があり、そうでないと、データベースを復元するための宛先サーバーの一覧にサーバーが表示されないことがあります。

## <a name="restore-a-database"></a>データベースを復元する

復元するには、次のアクセス許可が必要です。

* 復元を実行するコンテナー内の**バックアップ オペレーター** アクセス許可。
* バックアップされるソース VM への**共同作成者 (書き込み)** アクセス。
* ターゲット VM への**共同作成者 (書き込み)** アクセス。
    - 同じ VM に復元する場合は、これがソース VM になります。
    - 別の場所に復元する場合は、これが新しいターゲット VM になります。

次のように復元します。
1. SQL Server VM が登録されているコンテナーを開きます。
2. コンテナー ダッシュボードで、 **[使用状況]** の **[バックアップ項目]** を選択します。
3. **[バックアップ項目]** で、 **[バックアップの管理の種類]** の **[Azure VM 内の SQL]** を選択します。

    ![[SQL in Azure VM]\(Azure VM 内の SQL\) を選択する](./media/backup-azure-sql-database/sql-restore-backup-items.png)

4. 復元するデータベースを選択します。

    ![復元するデータベースの選択](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

5. データベース メニューを確認します。 ここには、次のような、データベース バックアップに関する情報が表示されます。

    * 最古および最新の復元ポイント
    * 完全復旧モードと一括ログ復旧モードのデータベースで、トランザクション ログ バックアップ用に構成されているデータベースに関する過去 24 時間のログ バックアップの状態。

6. **[DB の復元]** を選択します。

    ![[DB の復元] を選択する](./media/backup-azure-sql-database/restore-db-button.png)

7. **[復元の構成]** で、データの復元先を指定します。
   - **別の場所**:別の場所にデータベースを復元し、元のソース データベースを保持します。
   - **DB の上書き**:元のソースと同じ SQL Server インスタンスにデータを復元します。 このオプションでは、元のデータベースが上書きされます。

     > [!Important]
     > 選択したデータベースが Always On 可用性グループに属している場合、SQL Server はデータベースの上書きを許可しません。 **[別の場所]** のみを使用できます。
     >

     ![[復元の構成] メニュー](./media/backup-azure-sql-database/restore-restore-configuration-menu.png)

### <a name="restore-to-an-alternate-location"></a>別の場所に復元する

1. **[復元の構成]** メニューの **[復元先]** で、 **[別の場所]** を選択します。
2. データベースを復元する先の SQL Server の名前とインスタンスを選択します。
3. **[復元される DB 名]** ボックスに、ターゲット データベースの名前を入力します。
4. 該当する場合は、 **[選択した SQL インスタンスに既に同じ名前の DB が存在する場合に上書きする]** を選択します。
5. **[OK]** を選択します。

    ![[復元の構成] メニューの値を指定する](./media/backup-azure-sql-database/restore-configuration-menu.png)

2. **[復元ポイントの選択]** で、[特定の時点に復元する](#restore-to-a-specific-point-in-time)のか、[特定の復旧ポイント](#restore-to-a-specific-restore-point)に復元するのかを選択します。

    > [!NOTE]
    > 特定の時点への復元は、完全復旧モードと一括ログ復旧モードのデータベースのログ バックアップに対してのみ使用できます。

### <a name="restore-and-overwrite"></a>復元および上書き

1. **[復元の構成]** メニューの **[復元先]** で、 **[DB の上書き]**  > 、 **[OK]** の順に選択します。

    ![[DB の上書き] を選択する](./media/backup-azure-sql-database/restore-configuration-overwrite-db.png)

2. **[復元ポイントの選択]** で、[特定の時点に復元する](#restore-to-a-specific-point-in-time) **[ログ (特定の時点)]** を選択します。 あるいは、 **[完全および差分]** を選択し、[特定の復旧ポイント](#restore-to-a-specific-restore-point)に復元します。

    > [!NOTE]
    > 特定の時点への復元は、完全復旧モードと一括ログ復旧モードのデータベースのログ バックアップに対してのみ使用できます。

### <a name="restore-to-a-specific-point-in-time"></a>特定の時点に復元する

復元の種類として **[ログ (特定の時点)]** を選択した場合は、次の操作を行います。

1.  **[復元の日付/時刻]** でカレンダーを開きます。 カレンダーでは、復旧ポイントが含まれる日付が太字で表示され、現在の日付が強調表示されます。
1. 復元ポイントのある日付を選択します。 復元ポイントのない日付は選択できません。

    ![カレンダーを開く](./media/backup-azure-sql-database/recovery-point-logs-calendar.png)

1. 日付を選択すると、タイムライン グラフに連続した範囲の使用可能な復旧ポイントが表示されます。
1. タイムライン グラフで復旧の時刻を指定するか、時刻を選択します。 **[OK]** をクリックします。

    ![復元時刻を選択する](./media/backup-azure-sql-database/recovery-point-logs-graph.png)


1. **[詳細な構成]** メニューで、復元後にデータベースを非運用状態のままにする場合は、 **[NORECOVERY を使用して復元]** を有効にします。
1. 宛先サーバー上の復元場所を変更する場合は、新しいターゲット パスを入力します。
1. **[OK]** を選択します。

    ![[詳細な構成] メニュー](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. **[復元]** メニューで、 **[復元]** を選択して復元ジョブを開始します。
1. **[通知]** 領域で復元の進行状況を追跡します。またはデータベース メニューの **[Restore jobs]\(復元ジョブ\)** を選択して復元の進行状況を追跡します。

    ![復元ジョブの進行状況](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-to-a-specific-restore-point"></a>特定の復元ポイントに復元する

復元の種類として **[完全および差分]** を選択した場合は、次の操作を行います。

1. 一覧から復旧ポイントを選択し、 **[OK]** を選択して復元ポイントの手順を完了します。

    ![完全復旧ポイントを選択する](./media/backup-azure-sql-database/choose-fd-recovery-point.png)

1. **[詳細な構成]** メニューで、復元後にデータベースを非運用状態のままにする場合は、 **[NORECOVERY を使用して復元]** を有効にします。
1. 宛先サーバー上の復元場所を変更する場合は、新しいターゲット パスを入力します。
1. **[OK]** を選択します。

    ![[詳細な構成] メニュー](./media/backup-azure-sql-database/restore-point-advanced-configuration.png)

1. **[復元]** メニューで、 **[復元]** を選択して復元ジョブを開始します。
1. **[通知]** 領域で復元の進行状況を追跡します。またはデータベース メニューの **[Restore jobs]\(復元ジョブ\)** を選択して復元の進行状況を追跡します。

    ![復元ジョブの進行状況](./media/backup-azure-sql-database/restore-job-notification.png)

### <a name="restore-databases-with-large-number-of-files"></a>多数のファイルがあるデータベースを復元する

データベース内のファイルの文字列サイズの合計が[特定の制限](backup-sql-server-azure-troubleshoot.md#size-limit-for-files)より大きい場合、Azure Backup で、データベース ファイルの一覧が、別の PIT コンポーネントに格納されるため、ユーザーは復元操作中にターゲット復元パスを設定できません。 代わりに、ファイルが SQL の既定のパスに復元されます。

  ![大きなファイルがあるデータベースを復元する](./media/backup-azure-sql-database/restore-large-files.jpg)


## <a name="next-steps"></a>次の手順

Azure Backup によってバックアップされた SQL Server データベースを[管理および監視します](manage-monitor-sql-database-backup.md)。
