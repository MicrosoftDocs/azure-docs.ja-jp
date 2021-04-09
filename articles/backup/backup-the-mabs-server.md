---
title: MABS サーバーのバックアップ
description: Microsoft Azure Backup サーバー (MABS) をバックアップする方法について説明します。
ms.topic: conceptual
ms.date: 09/24/2020
ms.openlocfilehash: 81a6ee005e15b1d7ab7b11a938b8ab14143818f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92172127"
---
# <a name="back-up-the-mabs-server"></a>MABS サーバーのバックアップ

Microsoft Azure Backup Server (MABS) で障害が発生した場合にデータを確実に復旧できるようにするには、MABS サーバーをバックアップするための戦略が必要です。 バックアップされていない場合は、障害発生後に手動で再構築する必要があり、ディスクベースの回復ポイントは復旧できません。 MABS データベースをバックアップすることで、MABS サーバーをバックアップすることができます。

## <a name="back-up-the-mabs-database"></a>MABS データベースをバックアップする

MABS バックアップ戦略の一環として、MABS データベースをバックアップする必要があります。 MABS データベースは DPMDB という名前です。 このデータベースには、MABS の構成と MABS のバックアップに関するデータが含まれています。 障害が発生した場合は、データベースの最新のバックアップを使用して、MABS サーバーのほとんどの機能を再構築できます。 データベースを復元できると仮定すると、テープベースのバックアップにアクセスすることができます。また、すべての保護グループ設定とバックアップ スケジュールが維持されます。 MABS 記憶域プール ディスクが障害の影響を受けなかった場合は、再構築後にディスクベースのバックアップも使用できます。 いくつかの異なる方法を使用して、データベースをバックアップすることができます。

|データベースのバックアップ方法|長所|短所|
|--------------------------|--------------|-----------------|
|[Azure へのバックアップ](#back-up-to-azure)|<li>MABS で簡単に構成および監視できます。<li>バックアップ データベース ファイルの複数の場所。<li>クラウド記憶域によって堅牢なディザスター リカバリー ソリューションを実現します。<li>データベースの記憶域は確実にセキュリティで保護されています。<li>120 個のオンライン回復ポイントをサポートします。|<li>Azure アカウントと追加の MABS 構成が必要です。 Azure ストレージにコストがかかります。<li> Azure Backup コンテナーに格納されている MABS バックアップにアクセスするには、Azure エージェントがインストールされた、サポートされているバージョンの Windows Server ベースのシステムが必要です。 別の MABS サーバーを指定することはできません。<li>データベースがローカルにホストされていて、二次的な保護を有効にする場合は、選択できません。 <li>準備と復旧にいくらか余分に時間が必要です。|
|[MABS 記憶域プールをバックアップしてデータベースをバックアップする](#back-up-the-database-by-backing-up-the-mabs-storage-pool)|<li>構成および監視が簡単に行えます。<li>バックアップは MABS 記憶域プール ディスクに保持され、ローカルで簡単にアクセスできます。<li>MABS のスケジュールされたバックアップでは、512 個の高速完全バックアップがサポートされます。 1 時間ごとにバックアップした場合、21 日分の完全なデータ保護を実現できます。|<li>ディザスター リカバリーに適したオプションではありません。 これはオンラインであり、MABS サーバーまたは記憶域プール ディスクに障害が発生した場合、復旧が期待どおりに機能しない可能性があります。<li>データベースがローカルにホストされていて、二次的な保護を有効にする場合は、選択できません。 <li>MABS サービスまたはコンソールが実行されていないか、または機能していない場合は、回復ポイントにアクセスするためにいくらかの準備と特別な手順が必要です。|
|[ネイティブ SQL Server バックアップによるローカル ディスクへのバックアップ](#back-up-with-native-sql-server-backup-to-a-local-disk)|<li>SQL Server に組み込まれています。<li>バックアップは、簡単にアクセスできるローカル ディスク上に保持されます。<li>これは必要な頻度で実行されるようスケジュールできます。<li>MABS からは完全に独立しています。<li>バックアップ ファイルのクリーンアップをスケジュールすることができます。|<li>バックアップがリモートの場所にコピーされない場合は、ディザスター リカバリーのオプションとして適切ではありません。<li>バックアップ用にローカル ストレージが必要です。これにより、保持と頻度が制限される場合があります。|
|[ネイティブ SQL バックアップと MABS 保護を使用して MABS によって保護された共有にバックアップする](#back-up-to-a-share-protected-by-mabs)|<li>MABS で簡単に監視できます。<li>バックアップ データベース ファイルの複数の場所。<li>ネットワーク上の任意の Windows コンピューターから簡単にアクセスできます。<li>場合によって、最速の復旧方法となります。|<li>64 個の回復ポイントしかサポートしません。<li>サイトのディザスター リカバリーに適したオプションではありません。 MABS サーバーまたは MABS 記憶域プールのディスク障害によって、復旧作業が妨げられる可能性があります。<li>MABS DB がローカルでホストされていて、二次的な保護を有効にする場合のオプションではありません。 <li>構成してテストするには、余分に準備が必要になります。<li>MABS サーバー自体がダウンしても、MABS 記憶域プール ディスクに問題がない場合は、準備と復旧にさらに時間がかかります。|

- MABS 保護グループを使用してバックアップする場合は、データベースの一意の保護グループを使用することをお勧めします。

    > [!NOTE]
    > 復元の目的から、MABS データベースで復元する MABS のインストールが MABS データベース自体のバージョンと一致する必要があります。  たとえば、復元対象のデータベースが MABS V3 更新プログラムのロールアップ 1 のインストールから復元される場合、MABS サーバーで同じバージョンの更新プログラムのロールアップ 1 を実行している必要があります。 つまり、データベースを復元するには、事前に MABS をアンインストールしてから互換性のあるバージョンに再インストールすることが必要な場合があります。  データベースのバージョンを確認するには、一時的なデータベース名に手動でマウントしてから、データベースに対して SQL クエリを実行して、メジャーおよびマイナーのバージョンに基づいて、最後にインストールされたロールアップを確認します。

- MABS データベースのバージョンを確認するには、次の手順を実行します。

    1. クエリを実行するには、SQL Management Studio を開き、MABS データベースを実行している SQL インスタンスに接続します。

    2. MABS データベースを選択し、新しいクエリを開始します。

    3. 次の SQL クエリをクエリ ペインに貼り付けて実行します。

        `Select distinct MajorVersionNumber,MinorVersionNumber ,BuildNumber, FileName FROM dbo.tbl\_AM\_AgentPatch order byMajorVersionNumber,MinorVersionNumber,BuildNumber`

    クエリ結果に何も返されない場合、または MABS サーバーが以前のバージョンからアップグレードされたが、その後に新しい更新プログラムのロールアップがインストールされていない場合、MABS の基本インストールのメジャー、マイナーのエントリはありません。 更新プログラムのロールアップに関連付けられている MABS バージョンを確認するには、[MABS のビルド番号一覧](https://social.technet.microsoft.com/wiki/contents/articles/36381.microsoft-azure-backup-server-list-of-build-numbers.aspx)に関するページを参照してください。

### <a name="back-up-to-azure"></a>Azure へのバックアップ

1. 開始する前に、スクリプトを実行して MABS レプリカ ボリューム マウント ポイントのパスを取得し、どの回復ポイントに MABS バックアップが含まれているかを確認する必要があります。 この作業は、Azure Backup での初期レプリケーション後に行ってください。 このスクリプトの `dplsqlservername%` を MABS データベースをホストしている SQL Server インスタンスの名前に置き換えます。

    ```SQL
    Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%dpmdb%'
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
    ```

    Azure Recovery Services エージェントがインストールされ、MABS サーバーが Azure Backup コンテナーに登録されたときに指定されたパスコードがあることを確認します。 バックアップを復元するには、このパスコードが必要になります。

2. Azure Backup 資格情報コンテナーを作成し、Azure Backup エージェントのインストール ファイルとコンテナーの資格情報をダウンロードします。 インストール ファイルを実行してエージェントを MABS サーバーにインストールし、コンテナーの資格情報を使用して MABS サーバーをコンテナーに登録します。 [詳細については、こちらを参照してください](backup-azure-microsoft-azure-backup.md)。

3. コンテナーを構成した後、MABS データベースを含む MABS 保護グループを設定します。 ディスクと Azure のどちらにバックアップするかを選択します。

#### <a name="recover-the-mabs-database-from-azure"></a>Azure から MABS データベースを復旧する

次のように、Azure Backup コンテナーに登録されている任意の MABS サーバーを使用して、Azure からデータベースを復旧できます。

1. MABS コンソールで、 **[復旧]**  >  **[Add External MABS]\(外部 MABS の追加\)** を選択します。

2. コンテナーの資格情報を指定します (Azure Backup コンテナーからダウンロードします)。 資格情報は 2 日間のみ有効であることに注意してください。

3. **[Select External MABS for Recovery]\(復旧する外部 MABS の選択\)** で、データベースを復旧する MABS サーバーを選択し、暗号化パスフレーズを入力し、 **[OK]** を選択します。

4. 利用可能なポイントの一覧から使用する回復ポイントを選択します。 **[Clear External MABS]\(外部 MABS のクリア\)** を選択してローカルの MABS ビューに戻ります。

## <a name="back-up-the-mabs-database-to-mabs-storage-pool"></a>MABS データベースを MABS 記憶域プールにバックアップする

> [!NOTE]  
> このオプションは、Modern Backup Storage を備えた MABS に適用できます。

1. MABS コンソールで、 **[保護]**  >  **[保護グループの作成]** を選択します。
2. **[保護グループの種類の選択]** ページで **[サーバー]** を選択します。
3. **[グループ メンバーの選択]** ページで、**DPM データベース** を選択します。 MABS サーバーを展開し、DPMDB を選択します。
4. **[データの保護方法の選択]** ページで、 **[ディスクを使用して短期的な保護を行う]** を選択します。 短期的な保護ポリシー オプションを指定します。
5. MABS データベースの最初のレプリケーションの後、次の SQL スクリプトを実行します。

```SQL
select AG.NetbiosName, DS.DatasourceName, V.AccessPath, LR.PhysicalReplicaId from tbl_IM_DataSource DS
join tbl_PRM_LogicalReplica as LR
on DS.DataSourceId = LR.DataSourceId
join tbl_AM_Server as AG
on DS.ServerId=AG.ServerId
join tbl_PRM_ReplicaVolume RV
on RV.ReplicaId = LR.PhysicalReplicaId
join tbl_STM_Volume V
on RV.StorageId = V.StorageId
where datasourcename like N'%dpmdb%' and ds.ProtectedGroupId is not null
and LR.Validity in (1,2)
and AG.ServerName like N'%<dpmsqlservername>%' -- <dpmsqlservername> is a placeholder, put netbios name of server hosting DPMDB
```

### <a name="recover-mabs-database"></a>MABS データベースを復旧する

同じ DB を使用して MABS を再構築するには、まず MABS データベースを復旧し、新しくインストールした MABS と同期する必要があります。

#### <a name="use-the-following-steps"></a>次の手順を実行します。

1. 管理コマンド プロンプトを開き、`psexec.exe -s powershell.exe` を実行して、システム コンテキストで PowerShell ウィンドウを起動します。
2. どの場所からデータベースを回復するかを決定します。

#### <a name="to-copy-the-database-from-the-last-backup"></a>最新のバックアップからデータベースをコピーする場合

1. レプリカ VHD パス `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PhysicalReplicaId\>` に移動します
2. `mount-vhd disk0.vhdx` コマンドを使用して、そこにある **disk0.vhdx** をマウントします。
3. レプリカ VHD がマウントされたら、`mountvol.exe` を使用し、SQL スクリプトの出力にある物理レプリカ ID を使用してレプリカ ボリュームにドライブ文字を割り当てます。 例: `mountvol X: \?\Volume{}\`

#### <a name="to-copy-the-database-from-a-previous-recovery-point"></a>以前の回復ポイントからデータベースをコピーする場合

1. DPMDB コンテナー ディレクトリ `\<MABSServer FQDN\>\<PhysicalReplicaId\>` に移動します。 作成された MABS DB に対応する回復ポイント以下に、いくつかの一意の GUID 識別子を持つ複数のディレクトリが表示されます。 他のディレクトリは、PIT または回復ポイントを表します。
2. `\<MABSServer FQDN\>\<PhysicalReplicaId\>\<PITId\>` などの任意の PIT vhd パスに移動し、`mount-vhd disk0.vhdx` コマンドを使用してそこにある **disk0.vhdx** をマウントします。
3. レプリカ VHD がマウントされたら、`mountvol.exe` を使用し、SQL スクリプトの出力にある物理レプリカ ID を使用してレプリカ ボリュームにドライブ文字を割り当てます。 例: `mountvol X: \?\Volume{}\`

   上記の手順で、角かっこで囲まれたすべての用語はプレースホルダーです。 次のように適切な値に置き換えてください。
   - **ReFSVolume** - SQL スクリプトの出力にあるアクセス パス
   - **MABSServer FQDN** - MABS サーバーの完全修飾型名
   - **PhysicalReplicaId** - SQL スクリプトの出力にある物理レプリカ ID
   - **PITId** - コンテナー ディレクトリ内の物理レプリカ ID 以外の GUID 識別子。
4. 別の管理コマンド プロンプトを開き、`psexec.exe -s cmd.exe` を実行してシステム コンテキストでコマンド プロンプトを開始します。
5. ディレクトリを X: ドライブに変更し、MABS データベース ファイルの場所に移動します。
6. それらのファイルを復元しやすい場所にコピーします。 コピーした後、psexec cmd ウィンドウを終了します。
7. 手順 1 で開いた psexec PowerShell ウィンドウに移動し、VHDX パスに移動し、コマンド `dismount-vhd disk0.vhdx` を使用して VHDX のマウントを解除します。
8. MABS サーバーを再インストールした後、`DPMSYNC-RESTOREDB` コマンドを実行することで、復元された DPMDB を使用して MABS サーバーにアタッチすることができます。
9. `DPMSYNC-RESTOREDB` が完了したら、`DPMSYNC-SYNC` を実行します。

### <a name="back-up-the-database-by-backing-up-the-mabs-storage-pool"></a>MABS 記憶域プールをバックアップしてデータベースをバックアップする

> [!NOTE]
> このオプションは、従来の記憶域を使用する MABS に適用できます。

開始する前に、スクリプトを実行して MABS レプリカ ボリューム マウント ポイントのパスを取得し、どの回復ポイントに MABS バックアップが含まれているかを確認する必要があります。 この作業は、Azure Backup での初期レプリケーション後に行ってください。 このスクリプトの `dplsqlservername%` を MABS データベースをホストしている SQL Server インスタンスの名前に置き換えます。

```SQL
Select ag.NetbiosName as ServerName,ds.DataSourceName,vol.MountPointPath
from tbl_IM_DataSource as ds
join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
and vol.Usage =1
and lr.Validity in (1,2)
where ds.datasourcename like '%dpmdb%'
and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB
```

1. MABS コンソールで、 **[保護]**  >  **[保護グループの作成]** を選択します。

2. **[保護グループの種類の選択]** ページで **[サーバー]** を選択します。

3. **[グループ メンバーの選択]** ページで、MABS データベースを選択します。 MABS サーバー項目を展開し、**DPMDB** を選択します。

4. **[データの保護方法の選択]** ページで、 **[ディスクを使用して短期的な保護を行う]** を選択します。 短期的な保護ポリシー オプションを指定します。 MABS データベースの推奨保持期間は 2 週間です。

#### <a name="recover-the-database"></a>データベースを回復する

MABS サーバーはまだ動作していて、記憶域プールには問題がない場合 (MABS サービスまたはコンソールの問題など)、次のようにレプリカ ボリュームまたはシャドウ コピーからデータベースをコピーします。

1. どの時点からデータベースを回復するかを決定します。

    - MABS レプリカ ボリュームから直接取得した最新のバックアップからデータベースをコピーする場合は、SQL スクリプトの出力から取得した GUID を使用してレプリカ ボリュームにドライブ文字を割り当てるために、**mountvol.exe** を使用します。 例: `C:\Mountvol X: \\?\Volume{d7a4fd76\-a0a8\-11e2\-8fd3\-001c23cb7375}\`

    - 以前の回復ポイントからデータベースをコピーする場合 (シャドウ コピー)、SQL スクリプトの出力にあるボリューム GUID を使用して、レプリカのすべてのシャドウ コピーを一覧表示する必要があります。 次のコマンドを使用すると、そのボリュームのシャドウ コピーが一覧表示されます。`C:\>Vssadmin list shadows /for\=\\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\`。 復旧する作成時刻とシャドウ コピー IDに注意してください。

2. 次に **diskshadow.exe** を使用して、シャドウ コピーを未使用のドライブ文字 X: にマウントします。その際、データベース ファイルをコピーできるようにシャドウ コピー ID を使用します。

3. 管理コマンド プロンプトを開き、`psexec.exe -s cmd.exe` を実行して、システム コンテキストでコマンド プロンプトを起動します。これで、レプリカ ボリューム (X:) に移動し、ファイルをコピーできるようになります。

4. ディレクトリを X: ドライブに変更し、MABS データベース ファイルの場所に移動します。 それらのファイルを復元しやすい場所にコピーします。 コピーが完了したら、psexec コマンド ウィンドウを終了し、**diskshadow.exe** を実行して、X: ボリュームを非公開にします。

5. これで、SQL Management Studio を使用するか、または **DPMSYNC\-RESTOREDB** を実行することにより、データベース ファイルを復元できるようになりました。

## <a name="back-up-with-native-sql-server-backup-to-a-local-disk"></a>ネイティブ SQL Server バックアップによるローカル ディスクへのバックアップ

MABS とは関係なく、ネイティブの SQL Server バックアップを使用して MABS データベースをローカル ディスクにバックアップすることができます。

- SQL Server のバックアップに関する概要については、[こちら](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)を参照してください。

- クラウドへの SQL Server のバックアップの詳細については、[こちら](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)を参照してください。

## <a name="back-up-to-a-share-protected-by-mabs"></a>MABS によって保護されている共有にバックアップする

このバックアップ オプションでは、ネイティブの SQL を使用して MABS データベースを共有にバックアップし、MABS を使用して共有を保護し、Windows VSS の以前のバージョンを使用して復元を容易にします。

### <a name="before-you-start"></a>開始する前に

1. SQL サーバー上の、バックアップの 1 つのコピーを保持できる十分な空き領域があるドライブにフォルダーを作成します。 (例: `C:\MABSBACKUP`)。

1. フォルダーを共有します。 たとえば、`C:\MABSBACKUP` フォルダーを *DPMBACKUP* として共有します。

1. 以下の OSQL コマンドをコピーし、メモ帳に貼り付けてから、`C:\MABSACKUP\bkupdb.cmd` という名前のファイルに保存します。 拡張子 .txt が付いていないことを確認してください。 MABS サーバーで使用されるインスタンスと DPMDB 名が一致するように、SQL_Instance_name と DPMDB_NAME を変更します。

    ```SQL
    OSQL -E -S localhost\SQL_INSTANCE_NAME -Q "BACKUP DATABASE DPMDB_NAME TO DISK='C:\DPMBACKUP\dpmdb.bak' WITH FORMAT"
    ```

1. メモ帳を使用して、MABS サーバーの `C:\Program Files\Microsoft System Center\DPM\DPM\Scripting` フォルダー以下にある **ScriptingConfig.xml** ファイルを開きます。

1. **ScriptingConfig.xml** を修正し、**DataSourceName=** が DPMDBBACKUP フォルダー/共有を含むドライブ文字になるように変更します。 PreBackupScript エントリを、手順 3 で保存した **bkupdb.cmd** の完全パスと名前に変更します。

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ScriptConfiguration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="https://schemas.microsoft.com/2003/dls/ScriptingConfig.xsd">
    <DatasourceScriptConfig DataSourceName="C:">
    <PreBackupScript>C:\MABSDBBACKUP\bkupdb.cmd</PreBackupScript>
    <TimeOut>120</TimeOut>
    </DatasourceScriptConfig>
    </ScriptConfiguration>
    ```

1. 変更内容を **ScriptingConfig.xml** に保存します。

1. MABS を使用して C:\ MABSBACKUP フォルダーまたは `\sqlservername\MABSBACKUP` 共有を保護し、最初のレプリカが作成されるまで待ちます。 実行されるバックアップ前スクリプトの結果として、MABS レプリカにコピーされた C:\MABSBACKUP フォルダーに **dpmdb.bak** が存在するはずです。

1. セルフ サービス回復を有効にしていない場合は、レプリカで MABSBACKUP フォルダーを共有するために、いくつかの追加の手順が必要です。

    1. MABS コンソール > **[保護]** で、MABSBACKUP データ ソースを見つけて選択します。 詳細セクションで、レプリカ パスへのリンクに対して **[クリックすると詳細が表示されます]** を選択し、パスをメモ帳にコピーします。 ソース パスは削除し、宛先パスは保持します。 パスは次のようになります。`C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP`。

    2. 共有名 **MABSSERVERNAME-DPMDB** を使用して、そのパスに対する共有を作成します。 管理コマンド プロンプトから以下の Net Share コマンドを使用することができます。

        ```cmd
        Net Share MABSSERVERNAME-dpmdb="C:\Program Files\Microsoft System Center\DPM\DPM\Volumes\Replica\File System\vol_c9aea05f-31e6-45e5-880c-92ce5fba0a58\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

### <a name="configure-the-backup"></a>バックアップを構成する

SQL Server のネイティブ バックアップを使用すると、他の SQL Server データベースと同じように MABS データベースをバックアップすることができます。

- SQL Server のバックアップに関する概要については、[こちら](/sql/relational-databases/backup-restore/back-up-and-restore-of-sql-server-databases)を参照してください。

- クラウドへの SQL Server のバックアップの詳細については、[こちら](/sql/relational-databases/backup-restore/sql-server-backup-and-restore-with-microsoft-azure-blob-storage-service)を参照してください。

### <a name="recover-the-mabs-database"></a>MABS データベースを復旧する

1. 任意の Windows コンピューターからエクスプローラーを使用して `\\MABSServer\MABSSERVERNAME-dpmdb` 共有に接続します。

2. **dpmdb.bak** ファイルを右クリックして、プロパティを表示します。 **[以前のバージョン]** タブには、選択してコピーできるすべてのバックアップが表示されます。 最新のバックアップも引き続き、簡単にアクセスできる C:\MABSBACKUP フォルダーに置かれます。

3. SAN 接続の MABS 記憶域プール ディスクを別のサーバーに移動してレプリカ ボリュームからの読み取りを可能にしたり、ローカルに接続されたディスクを読み取るために Windows を再インストールしたりする必要がある場合は、データベースのバックアップを保持するボリュームを認識できるように、MABS レプリカ ボリュームのマウント ポイント パスまたはボリューム GUID を事前に把握しておく必要があります。 以下の SQL スクリプトを使用すれば、初期の保護から復元が必要になるまで、その情報をいつでも抽出することができます。 `%dpmsqlservername%` を、データベースをホストしている SQL サーバーの名前に置き換えます。

    ```sql
    Select ag.NetbiosName as
    ServerName,ds.DataSourceName,vol.MountPointPath,vol.GuidName
    from tbl_IM_DataSource as ds
    join tbl_PRM_LogicalReplica as lr on ds.DataSourceId=lr.DataSourceId
    join tbl_AM_Server as ag on ds.ServerId=ag.ServerId
    join tbl_SPM_Volume as vol on lr.PhysicalReplicaId=vol.VolumeSetID
    and vol.Usage =1
    and lr.Validity in (1,2)
    where ds.datasourcename like '%C:\%' -- volume drive letter for DPMBACKUP
    and servername like '%dpmsqlservername%' --netbios name of server hosting DPMDB

    ```

4. MABS 記憶域プール ディスクまたは MABS サーバーの再構築後に復旧する必要がある場合:

    1. ボリューム GUID があります。したがって、そのボリュームを別の Windows サーバーにマウントするか、または MABS サーバーの再構築後にマウントする必要がある場合は、**mountvol.exe** を実行して、SQL スクリプトの出力のボリューム GUID を使用した次のドライブ文字をそのボリュームに割り当てます。`C:\Mountvol X: \\?\Volume{d7a4fd76-a0a8-11e2-8fd3-001c23cb7375}\`。

    2. フォルダー構造を表すドライブ文字とレプリカ パスの部分とを使用して、レプリカ ボリュームにある MABSBACKUP フォルダーに再び共有します。

        ```cmd
        net share SERVERNAME-DPMDB="X:\454d81a0-0d9d-4e07-9617-d49e3f2aa5de\Full\DPMBACKUP"
        ```

    3. 任意の Windows コンピューターからエクスプローラーを使用して `\\SERVERNAME\MABSSERVERNAME-dpmdb` 共有に接続します。

    4. **dpmdb.bak** ファイルを右クリックして、プロパティを表示します。 **[以前のバージョン]** タブには、選択してコピーできるすべてのバックアップが表示されます。

## <a name="using-dpmsync"></a>DPMSync の使用

**DpmSync** は、記憶域プール内のディスクの状態やインストールされている保護エージェントと MABS データベースを同期できるようにするコマンドライン ツールです。 DpmSync を使用すると、MABS データベースを復元し、MABS データベースを記憶域プール内のレプリカと同期し、レポート データベースを復元し、不足しているレプリカを再割り当てすることができます。

### <a name="parameters"></a>パラメーター

| パラメーター      | [説明]    |
|----------------|-----------------------------|
| **-RestoreDb**                       | 指定された場所から MABS データベースを復元します。|
| **-Sync**                            | 復元されたデータベースを同期します。 データベースを復元した後、**DpmSync -Sync** を実行する必要があります。 **DpmSync -Sync** を実行した後でも、一部のレプリカが不足しているとマークされる場合があります。 |
| **-DbLoc** *location*                | MABS データベースのバックアップの場所を指定します。|
| **-InstanceName** <br/>*server\instance*     | DPMDB を復元する必要があるインスタンスです。|
| **-ReallocateReplica**         | 同期しないで、すべての見つかっていないレプリカ ボリュームを再割り当てします。 |
| **-DataCopied**                      | 新しく割り当てられたレプリカ ボリュームへのデータの読み込みが完了したことを示します。 これは、クライアント コンピューターについてのみ適用されます。 |

**例 1:** MABS サーバー上のローカル バックアップ メディアから MABS データベースを復元するには、次のコマンドを実行します。

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak
```

MABS データベースを復元した後、データベースを同期するには、次のコマンドを実行します。

```cmd
DpmSync -Sync
```

MABS データベースを復元して同期した後、レプリカを復元する前に、次のコマンドを実行して、レプリカのディスク領域を再割り当てします。

```cmd
DpmSync -ReallocateReplica
```

**例 2:** リモート データベースから MABS データベースを復元するには、リモート コンピューター上で次のコマンドを実行します。

```cmd
DpmSync –RestoreDb -DbLoc G:\DPM\Backups\2005\November\DPMDB.bak –InstanceName contoso\ms$dpm
```

MABS データベースを復元した後、データベースを同期するには、MABS サーバー上で次のコマンドを実行します。

```cmd
DpmSync -Sync
```

MABS データベースを復元して同期した後、レプリカを復元する前に、MABS サーバー上で次のコマンドを実行して、レプリカのディスク領域を再割り当てします。

```cmd
DpmSync -ReallocateReplica
```

## <a name="next-steps"></a>次の手順

- [MABS のサポート マトリックス](backup-support-matrix-mabs-dpm.md)
- [MABS に関する FAQ](backup-azure-dpm-azure-server-faq.md)