---
title: DPM を使用した SQL Server ワークロード用 Azure Backup
description: Azure Backup サービスを使用した SQL Server データベースのバックアップの概要
author: kasinh
manager: vvithal
ms.service: backup
ms.topic: conceptual
ms.date: 01/30/2019
ms.author: kasinh
ms.openlocfilehash: 60afba5d094d730aab20f2a7d50206c063f390ff
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466641"
---
# <a name="back-up-sql-server-to-azure-as-a-dpm-workload"></a>SQL Server を Azure に DPM ワークロードとしてバックアップする
この記事では、Azure Backup を使用して SQL Server データベースのバックアップを構成する手順について説明します。

SQL Server データベースを Azure にバックアップするには、Azure アカウントが必要です。 アカウントがない場合は、無料試用版のアカウントを数分で作成することができます。 詳細については、 [Azure の無料試用版サイト](https://azure.microsoft.com/pricing/free-trial/)を参照してください。

SQL Server データベースの Azure へのバックアップと Azure からの回復の管理には、次の 3 つの手順が含まれます。

1. SQL Server データベースを保護するための Azure へのバックアップ ポリシーを作成します。
2. オンデマンドでのバックアップ コピーを Azure に作成します。
3. Azure からデータベースを回復します。

## <a name="before-you-start"></a>開始する前に
開始する前に、Microsoft Azure Backup を使用したワークロードの保護に関するすべての [前提条件](backup-azure-dpm-introduction.md#prerequisites-and-limitations) が満たされていることを確認します。 前提条件は、バックアップ コンテナーの作成、コンテナー資格情報のダウンロード、Azure Backup エージェントのインストール、コンテナーへのサーバーの登録などのタスクに関するものです。

## <a name="create-a-backup-policy-to-protect-sql-server-databases-to-azure"></a>SQL Server データベースを保護するための Azure へのバックアップ ポリシーの作成
1. DPM サーバーで、 **[保護]** ワークスペースをクリックします。
2. ツール リボンで、 **[新規]** をクリックし、新しい保護グループを作成します。

    ![Create Protection Group](./media/backup-azure-backup-sql/protection-group.png)
3. DPM のスタート画面に **保護グループ**の作成に関するガイダンスが表示されます。 **[次へ]** をクリックします。
4. **[サーバー]** を選択します。

    ![Select Protection Group Type - 'Servers'](./media/backup-azure-backup-sql/pg-servers.png)
5. バックアップ対象のデータベースが存在する SQL Server コンピューターを展開します。 DPM に、そのサーバーからバックアップ可能なさまざまなデータ ソースが表示されます。 **[すべての SQL 共有]** を展開し、バックアップ対象のデータベース (ここでは ReportServer$MSDPM2012 と ReportServer$MSDPM2012TempDB) を選択します。 **[次へ]** をクリックします。

    ![Select SQL DB](./media/backup-azure-backup-sql/pg-databases.png)
6. 保護グループの名前を指定し、 **[オンライン保護を利用する]** チェック ボックスをオンにします。

    ![Data Protection Method - short term disk & Online Azure](./media/backup-azure-backup-sql/pg-name.png)
7. **[短期的な目標値の指定]** 画面で、ディスクへのバックアップ ポイントを作成するために必要な入力を含めます。

    ここで、 **[保有期間の範囲]** が *5 日間*、 **[同期の頻度]** (バックアップが実行される頻度) が *15 分*ごとに設定されていることを確認します。 **[高速完全バックアップ]** は *午後 8 時 00 分*に設定されています。

    ![Short term goals](./media/backup-azure-backup-sql/pg-shortterm.png)

   > [!NOTE]
   > (画面の入力に従って) 毎日午後 8 時 00 分に、前日午後 8 時 00 分のバックアップ ポイントから変更されているデータを転送することにより、バックアップ ポイントが作成されます。 このプロセスは、 **高速完全バックアップ**と呼ばれます。 トランザクション ログは 15 分ごとに同期されますが、午後 9 時 00 分にデータベースを回復する必要がある場合は、最新の高速完全バックアップ ポイント (この例では午後 8 時) からログを再生することでポイントが作成されます。
   >
   >

8. **[次へ]** をクリックします

    DPM は、使用可能なストレージ領域全体と考えられるディスク領域使用率を表示します。

    ![Disk allocation](./media/backup-azure-backup-sql/pg-storage.png)

    既定では、DPM は、初期バックアップ コピー用に、データ ソース (SQL Server データベース) ごとに 1 つのボリュームを作成します。 この方法では、論理ディスク マネージャー (LDM) は、DPM 保護を 300 データ ソース (SQL Server データベース) に制限します。 この制限を避けるには、 **[DPM 記憶域プールにデータを併置する]** を選択します。 このオプションを使用すると、DPM では複数のデータ ソースに単一のボリュームを使用して、最大 2000 の SQL データベース を保護できます。

    **[ボリュームを自動的に拡大する]** オプションがオンになっている場合、運用データの増大に伴って DPM がバックアップ ボリュームを増加することができます。 **[ボリュームを自動的に拡大する]** がオフになっている場合は、保護グループ内のデータ ソースに使用するバックアップ ストレージが制限されます。
9. 管理者は、帯域幅の輻輳を避けるために手動 (オフ ネット) でこの初期バックアップを転送するか、ネットワーク経由で転送するかを選択できます。 最初の転送が行われるときに構成することもできます。 **[次へ]** をクリックします。

    ![Initial replication method](./media/backup-azure-backup-sql/pg-manual.png)

    初期バックアップ コピーでは、運用サーバー (SQL Server コンピューター) から DPM サーバーに、データ ソース (SQL Server データベース) 全体を転送する必要があります。 このデータは場合によっては大きくなり、ネットワーク経由で転送すると帯域幅を超える可能性があります。 そのために、管理者は、初回バックアップの転送では、帯域幅の輻輳を避けるために**手動**で転送するか (リムーバブル メディアを使用)、指定した時刻に**自動 (ネットワーク経由)** で転送するかを選択できます。

    初期バックアップが完了した後は、残りのバックアップは初期バックアップのコピーに対する増分バックアップになります。 増分バックアップは一般に非常に小さく、ネットワーク経由で容易に転送できます。
10. 整合性チェックをいつ実行するかを選択し、 **[次へ]** をクリックします。

    ![Consistency check](./media/backup-azure-backup-sql/pg-consistent.png)

    DPM は整合性チェックを実行して、バックアップ ポイントの整合性を確認します。 このチェックでは、運用サーバー (このシナリオでは SQL Server コンピューター) のバックアップ ファイルと DPM にバックアップされたこのファイルのデータのチェックサムを計算します。 競合がある場合は、DPM にバックアップされたファイルが破損していると見なされます。 DPM は、チェックサムの不一致に対応するブロックを送信することにより、バックアップされたデータを修正します。 整合性チェックは負荷の高い処理であるため、管理者はこのチェックをスケジュール設定するか、自動的に実行するかを選択できます。
11. データ ソースのオンライン保護を指定するには、Azure で保護されるデータベースを選択し、 **[次へ]** をクリックします。

    ![Select datasources](./media/backup-azure-backup-sql/pg-sqldatabases.png)
12. 管理者は、組織のポリシーに合わせてバックアップ スケジュールや保有ポリシーを選択できます。

    ![Schedule and Retention](./media/backup-azure-backup-sql/pg-schedule.png)

    この例では、バックアップは 1 日に 1 回、午後 12 時 00 分と午後 8 時 (画面の下部) に作成されます。

    > [!NOTE]
    > すばやく回復するために、ディスク上に短期的な回復ポイントをいくつか作っておくことをお勧めします。 この回復ポイントは、"オペレーショナル リカバリ" で使用されます。 Azure は、より高い SLA を提供し、可用性を確保するのに適したオフサイトの場所として機能します。
    >
    >

    **ベスト プラクティス**: DPM を使用したローカル ディスク バックアップの完了より後に Azure Backup がスケジュールされていることを確認します。 これにより、最新のディスク バックアップを Azure にコピーできるようになります。

13. 保有ポリシーのスケジュールを選択します。 保有ポリシーのしくみの詳細については、「 [Azure Backup を使用してテープのインフラストラクチャを置換する](backup-azure-backup-cloud-as-tape.md)」を参照してください。

    ![Retention Policy](./media/backup-azure-backup-sql/pg-retentionschedule.png)

    次の点に注意してください。

    * バックアップは 1 日に 1 回、午後 12 時 00 分と午後 8 時 (画面の下部) に作成され、180 日間保有されます。
    * 土曜日の午後 12 時 00 分のバックアップは、 104 週間保有されます。
    * 先週土曜日の午後 12 時 00 分のバックアップは、 60 か月間保有されます。
    * 3 月の最終土曜日の午後 12 時 00 分のバックアップは、 10 年間保有されます。
14. **[次へ]** をクリックし、初期バックアップのコピーを Azure に転送するための適切なオプションを選択します。 **[自動 (ネットワーク経由)]** または **[オフライン バックアップ]** を選択できます。

    * **[自動 (ネットワーク経由)]** の場合は、バックアップ用に選択されたスケジュールに従って、バックアップ データが Azure に転送されます。
    * **[オフライン バックアップ]** のしくみについては、「 [Azure Backup でのオフライン バックアップのワークフロー](backup-azure-backup-import-export.md)」を参照してください。

    初期バックアップのコピーを Azure に転送するための適切な転送メカニズムを選択し、 **[次へ]** をクリックします。
15. **[概要]** 画面でポリシーの詳細を確認したら、 **[グループの作成]** をクリックしてワークフローを完了します。 **[閉じる]** をクリックして、[監視] ワークスペースでジョブの進行状況を監視できます。

    ![Creation of Protection Group In-Progress](./media/backup-azure-backup-sql/pg-summary.png)

## <a name="on-demand-backup-of-a-sql-server-database"></a>SQL Server データベースのオンデマンド バックアップ
前の手順でバックアップ ポリシーを作成しましたが、"回復ポイント" は最初のバックアップ実行時にのみ作成されます。 次の手順は、スケジューラが開始するのを待つ代わりに、回復ポイントの作成を手動でトリガーします。

1. データベースの保護グループの状態に " **OK** " と表示されるのを待ってから、回復ポイントを作成します。

    ![Protection Group Members](./media/backup-azure-backup-sql/sqlbackup-recoverypoint.png)
2. データベースを右クリックし、 **[回復ポイントの作成]** を選択します。

    ![Create Online Recovery Point](./media/backup-azure-backup-sql/sqlbackup-createrp.png)
3. ドロップダウン メニューから **[オンライン保護]** を選択し、 **[OK]** をクリックします。 これにより、Azure での回復ポイントの作成が開始されます。

    ![[回復ポイントの作成]](./media/backup-azure-backup-sql/sqlbackup-azure.png)
4. 次の図に示すように、進行中のジョブが表示される **[監視]** ワークスペースで、ジョブの進行状況を確認できます。

    ![Monitoring console](./media/backup-azure-backup-sql/sqlbackup-monitoring.png)

## <a name="recover-a-sql-server-database-from-azure"></a>Azure からの SQL Server データベースの回復
保護されているエンティティ (SQL Server データベース) を Azure から回復するには、次の手順が必要です。

1. DPM サーバーの管理コンソールを開きます。 DPM によってバックアップされたサーバーを確認できる **[回復]** ワークスペースに移動します。 目的のデータベース (この場合は ReportServer$MSDPM2012) を参照します。 "**オンライン**" で終わる **[回復元]** の時間を選択します。

    ![Select Recovery point](./media/backup-azure-backup-sql/sqlbackup-restorepoint.png)
2. データベース名を右クリックし、 **[回復]** をクリックします。

    ![Recover from Azure](./media/backup-azure-backup-sql/sqlbackup-recover.png)
3. DPM に回復ポイントの詳細が表示されます。 **[次へ]** をクリックします。 データベースを上書きするには、回復のタイプとして **[元の SQL Server のインスタンスに回復する]** を選択します。 **[次へ]** をクリックします。

    ![Recover to Original Location](./media/backup-azure-backup-sql/sqlbackup-recoveroriginal.png)

    この例では、DPM はデータベースを別の SQL Server インスタンスまたはスタンドアロンのネットワーク フォルダーに回復できます。
4. **[回復オプションの指定]** 画面で、[ネットワークの使用帯域幅の調整] を選択して回復で使用される帯域幅を調整するなど、回復のオプションを選択できます。 **[次へ]** をクリックします。
5. **[概要]** 画面に、これまでに指定した回復の構成が表示されます。 **[回復]** をクリックします。

    回復の状態に、データベースが回復されていることが表示されます。 **[閉じる]** をクリックしてウィザードを閉じ、 **[監視]** ワークスペースで進行状況を確認できます。

    ![Initiate recovery process](./media/backup-azure-backup-sql/sqlbackup-recoverying.png)

    回復が完了すると、復元されたデータベースはアプリケーション コンシステントになります。

### <a name="next-steps"></a>次のステップ:
•   [Azure Backup の FAQ](backup-azure-backup-faq.md)
