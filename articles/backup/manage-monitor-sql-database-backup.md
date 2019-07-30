---
title: Azure Backup によってバックアップされる Azure VM 上の SQL Server データベースを管理および監視する | Microsoft Docs
description: この記事では、Azure VM 上で実行されている SQL Server データベースを管理し、監視する方法について説明します。
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/14/2018
ms.author: raynew
ms.openlocfilehash: 0a864382e54f5c8641aa6da2369eb914011c712c
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68464868"
---
# <a name="manage-and-monitor-backed-up-sql-server-databases"></a>バックアップされる SQL Server データベースを管理および監視する

この記事では、[Azure Backup](backup-overview.md) サービスによって Azure Backup Recovery Services コンテナーにバックアップされる Azure 仮想マシン (VM) 上で実行されている SQL Server データベースを管理し、監視するための一般的なタスクについて説明します。 ジョブとアラートを監視し、データベース保護を停止し、再開し、バックアップ ジョブを実行し、バックアップから VM の登録を解除する方法を学習します。

SQL Server データベースにバックアップをまだ構成していない場合、[Azure VM 上の SQL Server データベースのバックアップ](backup-azure-sql-database.md)に関するページを参照してください

## <a name="monitor-manual-backup-jobs-in-the-portal"></a>ポータルで手動バックアップ ジョブを監視する

Azure Backup では、 **[バックアップ ジョブ]** ポータルに、手動でトリガーされたジョブがすべて表示されます。 このポータルに表示されるジョブには、データベースの検出と登録、バックアップ操作、復元操作などがあります。

![[バックアップ ジョブ] ポータル](./media/backup-azure-sql-database/jobs-list.png)

> [!NOTE]
> **[バックアップ ジョブ]** ポータルには、スケジュールされているバックアップ ジョブは表示されません。 次のセクションで説明されているように、スケジュールされたバックアップ ジョブを監視するには、SQL Server Management Studio を使用します。
>

監視シナリオの詳細については、[Arure portal における監視](backup-azure-monitoring-built-in-monitor.md)に関するページと [Azure Monitor を利用した監視](backup-azure-monitoring-use-azuremonitor.md)に関するページにお進みください。  


## <a name="view-backup-alerts"></a>バックアップ アラートを表示する

ログ バックアップは 15 分ごとに行われるため、バックアップ ジョブを適宜監視することは面倒に感じられる場合があります。 Azure Backup では、メール アラートを送信することで監視が容易になります。 メール アラートの特徴は次のとおりです。

- すべてのバックアップ エラーに対してトリガーされます。
- エラー コードごとにデータベース レベルで統合されます。
- データベースの最初のバックアップ エラーに対してのみ送信されます。

データベース バックアップ アラートを監視するには:

1. [Azure Portal](https://portal.azure.com) にサインインします。

2. コンテナー ダッシュボードで、 **[アラートとイベント]** を選択します。

   ![[アラートとイベント] を選択する](./media/backup-azure-sql-database/vault-menu-alerts-events.png)

3. **[アラートとイベント]** で、 **[バックアップ アラート]** を選択します。

   ![[バックアップ アラート] を選択する](./media/backup-azure-sql-database/backup-alerts-dashboard.png)

## <a name="stop-protection-for-a-sql-server-database"></a>SQL Server データベースの保護を停止する

複数の方法で SQL Server データベースのバックアップを停止できます。

* 今後のバックアップ ジョブすべてを停止し、すべての復旧ポイントを削除する。
* 今後のバックアップ ジョブすべてを停止し、復旧ポイントはそのままにする。

復旧ポイントをそのままにする場合、以下の項目を念頭に置いてください。

* 復旧ポイントはすべて永久に変更されず、削除はすべて保護の停止で停止し、データを保持します
* インスタンスの保護とストレージの使用に対して課金されます。 詳細については、「[Azure Backup の価格](https://azure.microsoft.com/pricing/details/backup/)」をご覧ください。
* バックアップを停止しないでデータ ソースを削除すると、新しいバックアップは失敗します。

データベースの保護を停止するには、次の手順を実行します。

1. コンテナー ダッシュボードで **[バックアップ項目]** を選択します。

2. **[バックアップの管理の種類]** で、 **[Azure VM 内の SQL]** を選択します。

    ![[SQL in Azure VM]\(Azure VM 内の SQL\) を選択する](./media/backup-azure-sql-database/sql-restore-backup-items.png)

3. 保護を停止するデータベースを選択します。

    ![保護を停止するデータベースを選択する](./media/backup-azure-sql-database/sql-restore-sql-in-vm.png)

4. データベース メニューで、 **[バックアップの停止]** を選択します。

    ![[バックアップの停止] を選択する](./media/backup-azure-sql-database/stop-db-button.png)


5. **[バックアップの停止]** メニューで、データを保持するか削除するかを選択します。 必要に応じて、理由とコメントを入力します。

    ![[バックアップの停止] メニューでデータを削除または保持する](./media/backup-azure-sql-database/stop-backup-button.png)

6. **[バックアップの停止]** を選択する


> [!NOTE]
>
データ削除オプションの詳細については、次の FAQ を参照してください。
* [自動保護されたインスタンスからデータベースを削除した場合、バックアップはどうなりますか?](faq-backup-sql-server.md#if-i-delete-a-database-from-an-autoprotected-instance-what-will-happen-to-the-backups)
* [自動保護されたデータベースのバックアップ操作の停止を実行した場合、その動作はどうなりますか?](faq-backup-sql-server.md#if-i-change-the-name-of-the-database-after-it-has-been-protected-what-will-be-the-behavior)
>
>


## <a name="resume-protection-for-a-sql-database"></a>SQL Database での保護の再開

SQL データベースの保護を停止するとき、 **[バックアップ データの保持]** オプションを選択した場合、後で保護を再開できます。 バックアップ データを保持しない場合、保護は再開できません。

SQL Database の保護を再開するには:

1. バックアップ項目を開き、 **[バックアップの再開]** を選択します。

    ![[バックアップの再開] を選択してデータベースの保護を再開する](./media/backup-azure-sql-database/resume-backup-button.png)

2. **[バックアップ ポリシー]** メニューで、ポリシーを選択して、 **[保存]** を選択します。

## <a name="run-an-on-demand-backup"></a>オンデマンド バックアップを実行する

異なる種類のオンデマンド バックアップを実行できます。

* 完全バックアップ
* コピーのみの完全バックアップ
* 差分バックアップ
* ログ バックアップ

コピーのみの完全バックアップにはリテンション期間を指定する必要がありますが、その他のバックアップの種類のリテンション範囲は、自動的に現在の時刻から 30 日間に設定されます。 <br/>
詳細については、[SQL Server バックアップの種類](backup-architecture.md#sql-server-backup-types)に関するページをご覧ください。

## <a name="unregister-a-sql-server-instance"></a>SQL Server インスタンスを登録解除する

保護を無効にした後、コンテナーを削除する前に、SQL Server インスタンスの登録を解除します。

1. コンテナー ダッシュボードの **[管理]** で、 **[バックアップ インフラストラクチャ]** を選択します。  

   ![[バックアップ インフラストラクチャ] を選択する](./media/backup-azure-sql-database/backup-infrastructure-button.png)

2. **[管理サーバー]** で、 **[保護されたサーバー]** を選択します。

   ![[保護されたサーバー] を選択する](./media/backup-azure-sql-database/protected-servers.png)

3. **[保護されたサーバー]** で、登録解除するサーバーを選択します。 コンテナーを削除するには、すべてのサーバーの登録を解除する必要があります。

4. 保護されたサーバーを右クリックし、 **[Unregister]** (登録解除) を選択します。

   ![[削除] を選択する](./media/backup-azure-sql-database/delete-protected-server.jpg)

## <a name="re-register-extension-on-the-sql-server-vm"></a>SQL Server VM で拡張を再登録する

VM のワークロード拡張が何らかの理由で影響を受けることがあります。 そのような場合、VM 上でトリガーされるすべての操作が失敗するようになります。 そこで、場合によっては、VM で拡張を再登録する必要があります。 **再登録**操作によって、操作を続行させるために、VM にワークロード バックアップ拡張が再インストールされます。  <br>

このオプションは慎重に使用することをお勧めします。正常な拡張の VM 上でこの操作がトリガーされると、拡張が再起動します。 その結果、進行中のジョブがすべて失敗することがあります。 再登録操作をトリガーする前に、[こちらの兆候](backup-sql-server-azure-troubleshoot.md#re-registration-failures)がないか確認してください。

## <a name="next-steps"></a>次の手順

詳細については、[SQL Server データベースのバックアップのトラブルシューティング](backup-sql-server-azure-troubleshoot.md)に関するページを参照してください。
