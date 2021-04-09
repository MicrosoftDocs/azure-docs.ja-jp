---
title: Azure VM 上の SAP HANA データベースの復元
description: この記事では、Azure Virtual Machines 上で実行されている SAP HANA データベースを復元する方法について説明します。 [リージョンをまたがる復元] を使用して、データベースをセカンダリ リージョンに復元することもできます。
ms.topic: conceptual
ms.date: 11/7/2019
ms.openlocfilehash: c502b7741acd343baefe5e2bf8b95cfc02e46688
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96021675"
---
# <a name="restore-sap-hana-databases-on-azure-vms"></a>Azure VM 上の SAP HANA データベースの復元

この記事では、Azure 仮想マシン (VM) 上で実行されていて、Azure Backup サービスによって Recovery Services コンテナーにバックアップされた SAP HANA データベースを復元する方法について説明します。 復元を使用すると、開発/テスト シナリオ用にデータのコピーを作成することや、以前の状態に戻すことができます。

SAP HANA データベースのバックアップ方法について詳しくは、「[Azure VM 上の SAP HANA データベースのバックアップ](./backup-azure-sap-hana-database.md)」を参照してください。

## <a name="restore-to-a-point-in-time-or-to-a-recovery-point"></a>特定の時点 (ポイントインタイム) または復旧ポイントに復元する

Azure Backup は、Azure VM 上で実行されている SAP HANA データベースを次のように復元できます。

* ログ バックアップを使用して、特定の日付または時刻 (秒) に復元する。 選択された時刻を基に、Azure Backup は完全、差分、または一連のログ バックアップから、必要になる適切なバックアップを自動的に判別します。

* 特定の完全バックアップまたは差分バックアップに復元することで、特定の復旧ポイントに復元する。

## <a name="prerequisites"></a>前提条件

データベースを復元する前に、次のことに注意してください。

* データベースの復元先にできるのは、同じリージョン内の SAP HANA インスタンスのみです。

* ターゲット インスタンスは、ソースと同じコンテナーに登録されている必要があります。

* Azure Backup は、同じ VM 上にある 2 つの異なる SAP HANA インスタンスを識別できません。 そのため、同じ VM 上の一方のインスタンスから他方のインスタンスにデータを復元することはできません。

* ターゲットの SAP HANA インスタンスが復元の準備ができているか確認するには、そのインスタンスの **バックアップの準備** 状態を確認します。

  1. ターゲット SAP HANA インスタンスが登録されているコンテナーを開きます。

  1. コンテナー ダッシュボードで、 **[作業の開始]** の下にある **[バックアップ]** を選択します。

      ![コンテナー ダッシュボードでのバックアップ](media/sap-hana-db-restore/getting-started-backup.png)

  1. **[バックアップ]** で、 **[何をバックアップしますか?]** の下の **[Azure VM の SAP HANA]** を選択します。

      ![[Azure VM の SAP HANA] を選択する](media/sap-hana-db-restore/sap-hana-backup.png)

  1. **[VM 内のデータベースを検出]** で **[詳細の表示]** を選択します。

      ![詳細を表示する](media/sap-hana-db-restore/view-details.png)

  1. ターゲット VM の **[バックアップの準備]** を確認します。

      ![保護されたサーバー](media/sap-hana-db-restore/protected-servers.png)

* SAP HANA がサポートする復元の種類について詳しくは、SAP HANA ノート [1642148](https://launchpad.support.sap.com/#/notes/1642148) を参照してください。

## <a name="restore-a-database"></a>データベースを復元する

復元するには、次のアクセス許可が必要です。

* 復元を実行するコンテナー内の **バックアップ オペレーター** アクセス許可。
* バックアップされるソース VM への **共同作成者 (書き込み)** アクセス。
* ターゲット VM への **共同作成者 (書き込み)** アクセス:
  * 同じ VM に復元する場合は、これがソース VM になります。
  * 別の場所に復元する場合は、これが新しいターゲット VM になります。

1. 復元する SAP HANA データベースが登録されているコンテナーを開きます。

1. コンテナー ダッシュボードで、 **[保護された項目]** の下の **[バックアップ項目]** を選択します。

    ![[バックアップ項目]](media/sap-hana-db-restore/backup-items.png)

1. **[バックアップ項目]** で、 **[バックアップの管理の種類]** の下の **[Azure VM の SAP HANA]** を選択します。

    ![バックアップの管理の種類](media/sap-hana-db-restore/backup-management-type.png)

1. 復元するデータベースを選択します。

    ![復元するデータベース](media/sap-hana-db-restore/database-to-restore.png)

1. データベース メニューを確認します。 ここには、次のような、データベース バックアップに関する情報が表示されます。

    * 最古および最新の復元ポイント

    * データベースの過去 24 時間と過去 72 時間のログ バックアップの状態

    ![データベース メニュー](media/sap-hana-db-restore/database-menu.png)

1. **[DB の復元]** を選択します。

1. **[復元の構成]** で、データの復元先 (または復元方法) を指定します。

    * **別の場所**:別の場所にデータベースを復元し、元のソース データベースを保持します。

    * **DB の上書き**:元のソースと同じ SAP HANA インスタンスにデータを復元します。 このオプションでは、元のデータベースが上書きされます。

      ![復元の構成](media/sap-hana-db-restore/restore-configuration.png)

### <a name="restore-to-alternate-location"></a>別の場所に復元する

1. **[復元の構成]** メニューの **[復元先]** で、 **[別の場所]** を選択します。

    ![別の場所に復元する](media/sap-hana-db-restore/restore-alternate-location.png)

1. データベースを復元する先の SAP HANA のホスト名とインスタンス名を選択します。
1. **[バックアップの準備]** を確認することで、ターゲット SAP HANA インスタンスが復元の準備ができているかどうか確認します。 詳細については、「[前提条件](#prerequisites)」セクションを参照してください。
1. **[復元される DB 名]** ボックスに、ターゲット データベースの名前を入力します。

    > [!NOTE]
    > SDC (Single Database Container) の復元では、[確認事項](backup-azure-sap-hana-database-troubleshoot.md#single-container-database-sdc-restore)に従う必要があります。

1. 該当する場合は、 **[Overwrite if the DB with the same name already exists on selected HANA instance]\(選択した HANA インスタンスに既に同じ名前の DB が存在する場合に上書きする\)** を選択します。
1. **[OK]** を選択します。

    ![復元の構成 - 最後の画面](media/sap-hana-db-restore/restore-configuration-last.png)

1. **[復元ポイントの選択]** で、[特定の時点に復元する](#restore-to-a-specific-point-in-time) **[ログ (特定の時点)]** を選択します。 または、 **[完全および差分]** を選択して、[特定の復旧ポイントに復元](#restore-to-a-specific-recovery-point)します。

### <a name="restore-and-overwrite"></a>復元および上書き

1. **[復元の構成]** メニューの **[復元先]** で、 **[DB の上書き]**  > 、 **[OK]** の順に選択します。

    ![DB の上書き](media/sap-hana-db-restore/overwrite-db.png)

1. **[復元ポイントの選択]** で、[特定の時点に復元する](#restore-to-a-specific-point-in-time) **[ログ (特定の時点)]** を選択します。 または、 **[完全および差分]** を選択して、[特定の復旧ポイントに復元](#restore-to-a-specific-recovery-point)します。

### <a name="restore-as-files"></a>ファイルとして復元

バックアップ データをデータベースとしてではなくファイルとして復元するには、 **[ファイルとして復元]** を選択します。 指定されたパスにファイルがダンプされると、それらのファイルをデータベースとして復元したい任意の SAP HANA マシンにそれらを移すことができます。 これらのファイルを任意のマシンに移動できるので、サブスクリプションやリージョンをまたいでデータを復元できるようになりました。

1. **[復元の構成]** メニューの **[Where and how to Restore]\(復元先と方法\)** で、 **[ファイルとして復元]** を選択します。
1. バックアップ ファイルの復元先とする **ホスト**/HANA サーバーの名前を選択します。
1. **[サーバー上の宛先パス]** に、手順 2 で選択したサーバー上のフォルダー パスを入力します。 これは、必要なすべてのバックアップ ファイルをサービスがダンプする場所です。

    ダンプされるファイルは次のとおりです。

    * データベース バックアップ ファイル
    * カタログ ファイル
    * (関連するバックアップ ファイルごとの) JSON メタデータ ファイル

    通常、ネットワーク共有パスや、宛先パスとして指定されているマウントされた Azure ファイル共有のパスを使うと、同じネットワーク内の他のマシンや、それらにマウントされている同じ Azure ファイル共有でこれらのファイルに簡単にアクセスできます。

    >[!NOTE]
    >ターゲットとなる登録済み VM にマウントされている Azure ファイル共有でデータベース バックアップ ファイルを復元するには、Azure ファイル共有に対する読み取り/書き込みアクセス許可がルート アカウントに含まれていることを確認します。

    ![宛先パスの選択](media/sap-hana-db-restore/restore-as-files.png)

1. すべてのバックアップ ファイルとフォルダーの復元先に対応する **復元ポイント** を選択します。

    ![復元ポイントの選択](media/sap-hana-db-restore/select-restore-point.png)

1. 選択した復元ポイントに関連付けられているすべてのバックアップ ファイルが、この宛先パスにダンプされます。
1. 選択した復元ポイントの種類 (**ポイント イン タイム** または **完全および差分**) に基づいて、宛先パスに 1 つまたは複数のフォルダーが作成されるのを確認できます。 `Data_<date and time of restore>` という名前のフォルダーの 1 つには、完全および差分バックアップが含まれ、`Log` という名前の他のフォルダーにはログ バックアップが含まれます。
1. これらの復元されたファイルを、データベースとして復元する先の SAP HANA サーバーに移動します。
1. その後、次の手順に従います。
    1. 次のコマンドを使用して、バックアップ ファイルが格納されているフォルダーまたはディレクトリに対してアクセス許可を設定します。

        ```bash
        chown -R <SID>adm:sapsys <directory>
        ```

    1. `<SID>adm` として、次のコマンド セットを実行します。

        ```bash
        su - <sid>adm
        ```

    1. 復元用のカタログ ファイルを生成します。 完全バックアップの JSON メタデータ ファイルから **BackupId** を抽出します。これは後で行う復元操作で使用されます。 完全バックアップとログ バックアップがそれぞれ異なるフォルダーにあることを確認し、これらのフォルダー内のカタログ ファイルと JSON メタデータ ファイルを削除します。

        ```bash
        hdbbackupdiag --generate --dataDir <DataFileDir> --logDirs <LogFilesDir> -d <PathToPlaceCatalogFile>
        ```

        上記のコマンドでは:

        * `<DataFileDir>` - 完全バックアップが格納されているフォルダー
        * `<LogFilesDir>` - ログ バックアップが格納されているフォルダー
        * `<PathToPlaceCatalogFile>` - 生成されたカタログ ファイルを配置する必要があるフォルダー

    1. HANA Studio を通して新たに生成されたカタログ ファイルを使用して復元するか、この新しく生成されたカタログを使用して HDBSQL 復元クエリを実行します。 HDBSQL クエリを以下に示します。

    * 特定の時点まで復元するには:

        復元されたデータベースを新しく作成する場合は、HDBSQL コマンドを実行して新しいデータベース `<DatabaseName>` を作成してから、復元用のデータベースを停止します。 ただし、既存のデータベースのみを復元する場合は、HDBSQL コマンドを実行してそのデータベースを停止します。

        そして、次のコマンドを実行してデータベースを復元します。

        ```hdbsql
        RECOVER DATABASE FOR <DatabaseName> UNTIL TIMESTAMP '<TimeStamp>' CLEAR LOG USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING LOG PATH (' <LogFileDir>') USING DATA PATH ('<DataFileDir>') USING BACKUP_ID <BackupIdFromJsonFile> CHECK ACCESS USING FILE
        ```

        * `<DatabaseName>` - 復元する新しいデータベースまたは既存のデータベースの名前
        * `<Timestamp>` - ポイントインタイム リストアの正確なタイムスタンプ
        * `<DatabaseName@HostName>` - バックアップが復元に使用されるデータベースの名前と、このデータベースが存在する **ホスト**/SAP HANA サーバーの名前。 `USING SOURCE <DatabaseName@HostName>` オプションでは、データ バックアップ (復元に使用) が、ターゲットの SAP HANA マシンとは異なる SID または名前を持つデータベースであることが指定されます。 そのため、バックアップが作成されたのと同じ HANA サーバーで復元を実行する場合には、これを指定する必要はありません。
        * `<PathToGeneratedCatalogInStep3>` - **手順 C** で生成されたカタログ ファイルへのパス
        * `<DataFileDir>` - 完全バックアップが格納されているフォルダー
        * `<LogFilesDir>` - ログ バックアップが格納されているフォルダー
        * `<BackupIdFromJsonFile>` - **手順 C** で抽出された **BackupId**

    * 特定の完全または差分バックアップに復元するには、次のようにします。

        復元されたデータベースを新しく作成する場合は、HDBSQL コマンドを実行して新しいデータベース `<DatabaseName>` を作成してから、復元用のデータベースを停止します。 ただし、既存のデータベースのみを復元する場合は、HDBSQL コマンドを実行してそのデータベースを停止します:

        ```hdbsql
        RECOVER DATA FOR <DatabaseName> USING BACKUP_ID <BackupIdFromJsonFile> USING SOURCE '<DatabaseName@HostName>'  USING CATALOG PATH ('<PathToGeneratedCatalogInStep3>') USING DATA PATH ('<DataFileDir>')  CLEAR LOG
        ```

        * `<DatabaseName>` - 復元する新しいデータベースまたは既存のデータベースの名前
        * `<Timestamp>` - ポイントインタイム リストアの正確なタイムスタンプ
        * `<DatabaseName@HostName>` - バックアップが復元に使用されるデータベースの名前と、このデータベースが存在する **ホスト** / SAP HANA サーバー名。 `USING SOURCE <DatabaseName@HostName>` オプションでは、データ バックアップ (復元に使用) が、ターゲットの SAP HANA マシンとは異なる SID または名前を持つデータベースであることが指定されます。 そのため、バックアップが作成されたのと同じ HANA サーバーで復元を実行する場合には、これを指定する必要はありません。
        * `<PathToGeneratedCatalogInStep3>` - **手順 C** で生成されたカタログ ファイルへのパス
        * `<DataFileDir>` - 完全バックアップが格納されているフォルダー
        * `<LogFilesDir>` - ログ バックアップが格納されているフォルダー
        * `<BackupIdFromJsonFile>` - **手順 C** で抽出された **BackupId**

### <a name="restore-to-a-specific-point-in-time"></a>特定の時点に復元する

復元の種類として **[ログ (特定の時点)]** を選択した場合は、次の操作を行います。

1. ログ グラフから復旧ポイントを選択し、 **[OK]** を選択して復元ポイントを選択します。

    ![復元ポイント](media/sap-hana-db-restore/restore-point.png)

1. **[復元]** メニューで、 **[復元]** を選択して復元ジョブを開始します。

    ![[復元] を選択する](media/sap-hana-db-restore/restore-restore.png)

1. **[通知]** 領域で復元の進行状況を追跡します。またはデータベース メニューの **[復元ジョブ]** を選択して、復元の進行状況を追跡します。

    ![正常にトリガーされた復元](media/sap-hana-db-restore/restore-triggered.png)

### <a name="restore-to-a-specific-recovery-point"></a>特定の復旧ポイントに復元する

復元の種類として **[完全および差分]** を選択した場合は、次の操作を行います。

1. 一覧から復旧ポイントを選択し、 **[OK]** を選択して復元ポイントを選択します。

    ![特定の復旧ポイントの復元](media/sap-hana-db-restore/specific-recovery-point.png)

1. **[復元]** メニューで、 **[復元]** を選択して復元ジョブを開始します。

    ![復元ジョブの開始](media/sap-hana-db-restore/restore-specific.png)

1. **[通知]** 領域で復元の進行状況を追跡します。またはデータベース メニューの **[復元ジョブ]** を選択して、復元の進行状況を追跡します。

    ![復元の進行状況](media/sap-hana-db-restore/restore-progress.png)

    > [!NOTE]
    > Multiple Database Container (MDC) の復元では、システム DB がターゲット インスタンスに復元された後で、事前登録スクリプトを再実行する必要があります。 その後でのみ、後続のテナント DB の復元が成功します。 詳細については、[トラブルシューティング – MDC 復元](backup-azure-sap-hana-database-troubleshoot.md#multiple-container-database-mdc-restore)に関するページを参照してください。

## <a name="cross-region-restore"></a>リージョンをまたがる復元

復元オプションの 1 つである、リージョンをまたがる復元 (CRR) を使用すると、セカンダリ リージョン (Azure のペアになっているリージョン) で Azure VM 上でホストされている SAP HANA データベースを復元できます。

プレビュー期間中にこの機能にオンボードするには、「[作業を開始する前に](./backup-create-rs-vault.md#set-cross-region-restore)」セクションをお読みください。

CRR が有効になっているかどうかを確認するには、「[リージョンをまたがる復元の構成](backup-create-rs-vault.md#configure-cross-region-restore)」の手順に従ってください。

### <a name="view-backup-items-in-secondary-region"></a>セカンダリ リージョンのバックアップ項目を表示する

CRR が有効になっている場合は、セカンダリ リージョンのバックアップ項目を表示できます。

1. ポータルから **[Recovery Services コンテナー]** 、 **[バックアップ項目]** の順に移動します。
1. セカンダリ リージョンの項目を表示するには、 **[セカンダリ リージョン]** を選択します。

>[!NOTE]
>CRR 機能をサポートする種類のバックアップ管理のみが一覧に表示されます。 現時点では、セカンダリ リージョン データをセカンダリ リージョンに復元することのみが許可されています。

![セカンダリ リージョンのバックアップ項目](./media/sap-hana-db-restore/backup-items-secondary-region.png)

![セカンダリ リージョンのデータベース](./media/sap-hana-db-restore/databases-secondary-region.png)

### <a name="restore-in-secondary-region"></a>セカンダリ リージョンに復元する

セカンダリ リージョンに復元するユーザー エクスペリエンスは、プライマリ リージョンに復元するユーザー エクスペリエンスに似ています。 [復元の構成] ペインで復元の詳細を構成するときに、セカンダリ リージョンのパラメーターのみを指定するように求められます。

![復元する場所と方法](./media/sap-hana-db-restore/restore-secondary-region.png)

>[!NOTE]
>セカンダリ リージョンの仮想ネットワークは一意に割り当てる必要があり、そのリソース グループの他の VM には使用できません。

![復元進行中通知をトリガーする](./media/backup-azure-arm-restore-vms/restorenotifications.png)

>[!NOTE]
>
>* 復元がトリガーされた後、データ転送フェーズでは、復元ジョブを取り消すことができません。
>* セカンダリ リージョンに復元するために必要な Azure ロールは、プライマリ リージョンにおけるものと同じです。

### <a name="monitoring-secondary-region-restore-jobs"></a>セカンダリ リージョンの復元ジョブの監視

1. ポータルから **[Recovery Services コンテナー]**  >  **[バックアップ ジョブ]** に移動します。
1. セカンダリ リージョンの項目を表示するには、 **[セカンダリ リージョン]** を選択します。

    ![フィルター処理されたバックアップ ジョブ](./media/sap-hana-db-restore/backup-jobs-secondary-region.png)

## <a name="next-steps"></a>次のステップ

* [Azure Backup を使用してバックアップされた SAP HANA データベースを管理する](sap-hana-db-manage.md)方法を学習する
