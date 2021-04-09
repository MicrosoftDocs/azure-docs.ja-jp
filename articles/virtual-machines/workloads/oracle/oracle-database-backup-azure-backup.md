---
title: Azure Backup を使用して Azure Linux VM で Oracle Database 19c データベースをバックアップおよび回復する
description: Azure Backup サービスを使用して Oracle Database 19c データベースをバックアップおよび回復する方法について説明します。
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: 90f86a198ad36c2961f77336092d863953ee45ba
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/02/2021
ms.locfileid: "101673895"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-backup"></a>Azure Backup を使用して Azure Linux VM で Oracle Database 19c データベースをバックアップおよび回復する

この記事では、Azure Backup を使用して、データベース ファイルと高速回復領域が含まれている、VM ディスクのディスク スナップショットを作成する方法について説明します。 Azure Backup を使用すると、バックアップとして適した完全なディスク スナップショットを作成できます。これらは [Recovery Services コンテナー](../../../backup/backup-azure-recovery-services-vault-overview.md)に格納されます。  また、Azure Backup は、アプリケーション整合性バックアップを提供することで、追加の修正なしでデータを復元できるようにもします。 アプリケーション整合性データの復元により復元時間が短縮され、迅速に実行状態に戻ることができます。

> [!div class="checklist"]
>
> * アプリケーション整合性バックアップを使用してデータベースをバックアップする
> * 復旧ポイントからデータベースを復元および復旧する
> * 復旧ポイントから VM を復元する

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

バックアップと復旧プロセスを実行するには、最初にインストール済みの Oracle Database 19c のインスタンスがある Linux VM を作成する必要があります。 VM の作成に現在使用されている Marketplace イメージは、**Oracle:oracle-database-19-3:oracle-database-19-0904:latest** です。 このチュートリアルを完了するには、「[Oracle データベースのクイック作成ガイド](./oracle-database-quick-create.md)」の手順に従って Oracle データベースを作成してください。


## <a name="prepare-the-environment"></a>環境の準備

環境を準備するには、次の手順に従います。

1. VM に接続します
1. データベースを準備します。

### <a name="connect-to-the-vm"></a>VM に接続します

1. VM で Secure Shell (SSH) セッションを作成するには、次のコマンドを使用します。 IP アドレスとのホスト名の組み合わせを VM の `<publicIpAddress>` の値で置き換えます。
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
1. *ルート* ユーザーに切り替えます。

   ```bash
   sudo su -
   ```
    
1. oracle ユーザーを */etc/sudoers* ファイルに追加します。

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

### <a name="prepare-the-database"></a>データベースを準備する

この手順では、*vmoracle19c* という仮想マシンで実行されている Oracle インスタンス (*test*) があることを前提としています。

1. ユーザーを *oracle* ユーザーに切り替えます。
 
   ```bash
    sudo su - oracle
    ```
    
2. 接続する前に、環境変数 ORACLE_SID を設定する必要があります。
    
    ```bash
    export ORACLE_SID=test;
    ```

    また、次のコマンドを使用して、今後のサインインのために `oracle` ユーザーの `.bashrc` ファイルに ORACLE_SID 変数を追加する必要があります。

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
3. Oracle リスナーがまだ実行されていない場合は、起動します。

    ```output
    $ lsnrctl start
    ```

    出力は次の例のようになるはずです。

    ```bash
    LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 18-SEP-2020 03:23:49

    Copyright (c) 1991, 2019, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    System parameter file is /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))

    Connecting to (DESCRIPTION=(ADDRESS=(PROTOCOL=TCP)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
    Start Date                18-SEP-2020 03:23:49
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Parameter File   /u01/app/oracle/product/19.0.0/dbhome_1/network/admin/listener.ora
    Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
    Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
    (DESCRIPTION=(ADDRESS=(PROTOCOL=ipc)(KEY=EXTPROC1521)))
    The listener supports no services
    The command completed successfully
    ```

4.  高速回復領域 (FRA) の場所を作成します。

    ```bash
    mkdir /u02/fast_recovery_area
    ```

5.  データベースに接続します。

    ```bash
    SQL> sqlplus / as sysdba
    ```

6.  データベースがまだ実行されていない場合は、起動します。

    ```bash
    SQL> startup
    ```

7.  高速回復領域のデータベース環境変数を設定します。

    ```bash
    SQL> alter system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
8.  データベースが、オンライン バックアップを有効にするためのアーカイブ ログ モードになっていることを確認します。

    最初にログ アーカイブの状態を確認します。

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    NOARCHIVELOG モードになっている場合は、次のコマンドを実行します。

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

9.  テーブルを作成して、バックアップおよび復元操作をテストします。

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

10. VM ディスクに配置されている高速回復領域にバックアップするように RMAN を構成します。

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/u02/fast_recovery_area/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/u02/fast_recovery_area/%d/Full_%d_%U_%T_%s'; 
    ```

11. 構成変更の詳細を確認します。

    ```bash
    RMAN> show all;
    ```    

12.  ここで、バックアップを実行します。 次のコマンドで、アーカイブ ログファイルを含む完全なデータベース バックアップが、圧縮形式のバックアップセットとして取得されます。

     ```bash
     RMAN> backup as compressed backupset database plus archivelog;
     ```

## <a name="using-azure-backup-preview"></a>Azure Backup の使用 (プレビュー)

Azure Backup サービスは、データをバックアップし、それを Microsoft Azure クラウドから回復するための、シンプルで安全かつコスト効率の高いソリューションを提供します。 Azure Backup では、元のデータが誤って破壊されることを防ぐために、独立して分離されたバックアップを提供しています。 バックアップは、復旧ポイントの管理機能をビルトインで備えた Recovery Services コンテナーに格納されます。 構成とスケーラビリティは単純で、バックアップは最適化され、必要に応じて簡単に復元することができます。

Azure Backup サービスでは、Oracle、MySQL、Mongo DB、PostGreSQL などのさまざまなアプリケーションの Windows および Linux VM のバックアップ中に、アプリケーションの整合性を確保するための[フレームワーク](../../../backup/backup-azure-linux-app-consistent.md)が提供されます。 これには、アプリケーションを通常モードに戻すために、ディスクのスナップショットを作成する前に事前スクリプトを呼び出す (アプリケーションを停止するため) ことと、スナップショットの完了後に事後スクリプト (アプリケーションを停止するためのコマンド) を呼び出すことが含まれます。 サンプルの事前スクリプトと事後スクリプトは GitHub に用意されていますが、これらのスクリプトの作成と保守はお客様の責任となります。

Azure Backup では、拡張された事前スクリプトと事後スクリプトのフレームワーク (**現時点ではプレビュー段階**) が提供されるようになりました。Azure Backup サービスによって、選択したアプリケーションのパッケージ化された事前スクリプトと事後スクリプトが提供されます。 Azure Backup ユーザーはアプリケーションに名前を付けるだけで済み、Azure VM バックアップで関連する事前および事後スクリプトが自動的に呼び出されます。 パッケージ化された事前スクリプトと事後スクリプトは Azure Backup チームによって管理されるので、ユーザーにはこれらのスクリプトのサポート、所有権、および有効性が保証されています。 現在のところ、拡張フレームワークでサポートされているアプリケーションは *Oracle* と *MySQL* です。

このセクションでは、Azure Backup の拡張フレームワークを使用して、実行中の VM と Oracle データベースのアプリケーション整合性スナップショットを作成します。 このデータベースはバックアップ モードになるため、Azure Backup で VM ディスクのスナップショットを作成している間、トランザクション全体で一貫性のあるオンライン バックアップを実行できます。 スナップショットはストレージの完全なコピーであり、増分または書き込み時コピーのスナップショットではないため、データベースの復元先として有効なメディアです。 Azure Backup アプリケーション整合性スナップショットを使用する利点は、データベースのサイズに関係なく非常に高速であり、スナップショットが Recovery Services コンテナーに転送されるのを待たずに、作成後すぐに復元操作に使用できることです。

Azure Backup を使用してデータベースをバックアップするには、次の手順を実行します。

1. アプリケーション整合性バックアップ用に環境を準備します。
1. アプリケーション整合性バックアップを設定します。
1. VM のアプリケーション整合性バックアップをトリガーします。

### <a name="prepare-the-environment-for-an-application-consistent-backup"></a>アプリケーション整合性バックアップ用に環境を準備する

1. *ルート* ユーザーに切り替えます。

   ```bash
   sudo su -
   ```

1. 新しいバックアップ ユーザーを作成します。

   ```bash
   useradd -G backupdba azbackup
   ```
   
2. バックアップ ユーザー環境を設定します。

   ```bash
   echo "export ORACLE_SID=test" >> ~azbackup/.bashrc
   echo export ORACLE_HOME=/u01/app/oracle/product/19.0.0/dbhome_1 >> ~azbackup/.bashrc
   echo export PATH='$ORACLE_HOME'/bin:'$PATH' >> ~azbackup/.bashrc
   ```
   
3. 新しいバックアップ ユーザーの外部認証を設定します。 バックアップ ユーザーは、外部認証を使用してデータベースにアクセスできる必要があるため、パスワードの入力が求められないようにする必要があります。

   まず、*oracle* ユーザーに切り替えます。

   ```bash
   su - oracle
   ```

   sqlplus を使用してデータベースにログインし、外部認証の既定の設定を確認します。
   
   ```bash
   sqlplus / as sysdba
   SQL> show parameter os_authent_prefix
   SQL> show parameter remote_os_authent
   ```
   
   出力は次の例のようになります。 

   ```output
   NAME                                 TYPE        VALUE
   ------------------------------------ ----------- ------------------------------
   os_authent_prefix                    string      ops$
   remote_os_authent                    boolean     FALSE
   ```

   次に、外部で認証されたデータベース ユーザー *azbackup* を作成し、sysbackup 特権を付与します。
   
   ```bash
   SQL> CREATE USER ops$azbackup IDENTIFIED EXTERNALLY;
   SQL> GRANT CREATE SESSION, ALTER SESSION, SYSBACKUP TO ops$azbackup;
   ```

   > [!IMPORTANT] 
   > `GRANT` ステートメントの実行時にエラー `ORA-46953: The password file is not in the 12.2 format.` が発生した場合は、次の手順に従って、orapwd ファイルを 12.2 形式に移行します。
   >
   > 1. sqlplus を終了します。
   > 1. 古い形式のパスワード ファイルを新しい名前に変えます。
   > 1. パスワード ファイルを移行します。
   > 1. 古いファイルを削除します。
   > 1. 次のコマンドを実行します。
   >
   >    ```bash
   >    mv $ORACLE_HOME/dbs/orapwtest $ORACLE_HOME/dbs/orapwtest.tmp
   >    orapwd file=$ORACLE_HOME/dbs/orapwtest input_file=$ORACLE_HOME/dbs/orapwtest.tmp
   >    rm $ORACLE_HOME/dbs/orapwtest.tmp
   >    ```
   >
   > 1. sqlplus で `GRANT` 操作を再実行します。
   >
   
4. バックアップ メッセージをデータベース警告ログに記録するストアド プロシージャを作成します。

   ```bash
   sqlplus / as sysdba
   SQL> GRANT EXECUTE ON DBMS_SYSTEM TO SYSBACKUP;
   SQL> CREATE PROCEDURE sysbackup.azmessage(in_msg IN VARCHAR2)
   AS
     v_timestamp     VARCHAR2(32);
   BEGIN
     SELECT TO_CHAR(SYSDATE, 'YYYY-MM-DD HH24:MI:SS')
     INTO v_timestamp FROM DUAL;
     DBMS_OUTPUT.PUT_LINE(v_timestamp || ' - ' || in_msg);
     SYS.DBMS_SYSTEM.KSDWRT(SYS.DBMS_SYSTEM.ALERT_FILE, in_msg);
   END azmessage;
   /
   SQL> SHOW ERRORS
   SQL> QUIT
   ```
   
### <a name="set-up-application-consistent-backups"></a>アプリケーション整合性バックアップを設定する  

1. *ルート* ユーザーに切り替えます。

   ```bash
   sudo su -
   ```

2. "etc/azure" フォルダーがあるかどうかを確認します。 存在しない場合は、アプリケーション整合性バックアップの作業ディレクトリを作成します。

   ```bash
   if [ ! -d "/etc/azure" ]; then
      sudo mkdir /etc/azure
   fi
   ```

3. フォルダー内に "workload.conf" があるかどうかを確認します。 存在しない場合は、 */etc/azure* ディレクトリに、次の内容を含む *workload.conf* という名前のファイルを作成します。これは `[workload]` で始まる必要があります。 ファイルが既に存在する場合は、単に次の内容に一致するようにフィールドを編集してください。 そうでない場合は、次のコマンドを実行すると、ファイルが作成され、内容が設定されます。

   ```bash
   echo "[workload]
   workload_name = oracle
   command_path = /u01/app/oracle/product/19.0.0/dbhome_1/bin/
   timeout = 90
   linux_user = azbackup" > /etc/azure/workload.conf
   ```

### <a name="trigger-an-application-consistent-backup-of-the-vm"></a>VM のアプリケーション整合性バックアップをトリガーする

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1.  Azure portal で、リソース グループ **rg-oracle** に移動し、仮想マシン **vmoracle19c** をクリックします。

2.  **[バックアップ]** ブレードで、リソース グループ **rg-oracle** に **myVault** という名前の新しい **Recovery Services コンテナー** を作成します。
    **[バックアップ ポリシーの選択]** では、 **[(新規) DailyPolicy]** を使用します。 バックアップの頻度または保持期間の範囲を変更する場合は、代わりに **[新しいポリシーの作成]** を選択します。

    ![Recovery Services コンテナーの追加ページ](./media/oracle-backup-recovery/recovery-service-01.png)

3.  続行するには、 **[バックアップの有効化]** をクリックします。

    > [!IMPORTANT]
    > **[バックアップの有効化]** をクリックしても、スケジュールされた時間に達するまでバックアップ プロセスは開始されません。 即時バックアップをセットアップするには、次の手順を完了します。

4. リソース グループ ページで、新しく作成した Recovery Services コンテナー **myVault** をクリックします。 ヒント:ページを更新して表示する必要がある場合があります。

5.  **myVault のバックアップ項目** ブレードの **[バックアップ項目数]** の下で、バックアップ項目の数を選択します。

    ![Recovery Services コンテナー myVault の詳細ページ](./media/oracle-backup-recovery/recovery-service-02.png)

6.  **バックアップ項目 (Azure 仮想マシン)** ブレードのページ右側で省略記号 (**...**) ボタンをクリックし、**[今すぐバックアップ]** をクリックします。

    ![Recovery Services コンテナーの [今すぐバックアップ] コマンド](./media/oracle-backup-recovery/recovery-service-03.png)

7.  [バックアップの保持期間] の既定値をそのまま使用し、 **[OK]** ボタンをクリックします。 バックアップ プロセスが完了するまで待ってから、 

    バックアップ ジョブの状態を表示するには、 **[バックアップ ジョブ]** をクリックします。

    ![Recovery Services コンテナーのジョブ ページ](./media/oracle-backup-recovery/recovery-service-04.png)

    次のイメージには、バックアップ ジョブの状態が表示されます。

    ![状態が表示された Recovery Services コンテナーのジョブ ページ](./media/oracle-backup-recovery/recovery-service-05.png)
    
    スナップショットの実行には数秒しかかかりませんが、コンテナーへの転送には時間がかかることがあります。また、転送が完了するまで、バックアップ ジョブは完了しません。

8. アプリケーション整合バックアップのため、ログ ファイル内のすべてのエラーに対処します。 ログ ファイルは /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/extension.log にあります。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. Recovery Services コンテナーを作成します。

   ```azurecli
   az backup vault create --location eastus --name myVault --resource-group rg-oracle
   ```

2. VM のバックアップ保護を有効にします。

   ```azurecli
   az backup protection enable-for-vm \
      --resource-group rg-oracle \
      --vault-name myVault \
      --vm vmoracle19c \
      --policy-name DefaultPolicy
   ```

3. 既定のスケジュール (午前 5 時 (UTC)) でバックアップがトリガーされるのを待機するのではなく、今すぐ実行するようにバックアップをトリガーします。 

   ```azurecli
   az backup protection backup-now \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c 
   ```

   `az backup job list` と `az backup job show` を使用して、バックアップ ジョブの進行状況を監視できます。

---

## <a name="recovery"></a>Recovery

データベースを復旧するには、次の手順を実行します。

1. データベース ファイルを削除します。
1. Recovery Services コンテナーから復元スクリプトを生成します。
1. 復元ポイントをマウントします。
1. 復旧を実行します。

### <a name="remove-the-database-files"></a>データベース ファイルを削除する 

回復プロセスのテスト方法については、この記事の後半で学習します。 回復プロセスをテストするには、先にデータベース ファイルを削除する必要があります。

1.  Oracle インスタンスをシャットダウンします。

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  データファイルとバックアップを削除します。

    ```bash
    sudo su - oracle
    cd /u02/oradata/TEST
    rm -f *.dbf
    cd /u02/fast_recovery_area
    rm -f *
    ```

### <a name="generate-a-restore-script-from-the-recovery-services-vault"></a>Recovery Services コンテナーから復元スクリプトを生成する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure portal で、Recovery Services コンテナー項目 *myVault* を検索して選択します。

    ![Recovery Services コンテナー myVault のバックアップ項目](./media/oracle-backup-recovery/recovery-service-06.png)

2. **[概要]** ブレードで **[バックアップ項目]** を選択し、 **[Azure 仮想マシン]** を選択します。これには、0 以外のバックアップ項目の数が表示されている必要があります。

    ![Recovery Services コンテナー Azure Virtual Machine のバックアップ項目の数](./media/oracle-backup-recovery/recovery-service-07.png)

3. [バックアップ項目 (Azure Virtual Machines)] ページに、VM **vmoracle19c** が表示されます。 右側にある省略記号をクリックしてメニューを表示し、 **[ファイルの回復]** を選択します。

    ![Recovery Services コンテナーファイル回復ページのスクリーンショット](./media/oracle-backup-recovery/recovery-service-08.png)

4. [**ファイルの回復 (プレビュー)**] ウィンドウで、**[スクリプトのダウンロード]** をクリックします。 次に、クライアント コンピューター上のフォルダーにダウンロードされた (.py) ファイルを保存します。 スクリプトを実行するためのパスワードが生成されます。 後で使用するために、パスワードをファイルにコピーします。 

    ![スクリプト ファイルのダウンロードの保存オプション](./media/oracle-backup-recovery/recovery-service-09.png)

5. .py ファイルを VM にコピーします。

    次の例では、secure copy (scp) コマンドを使用してファイルを VM に移動する方法を示しています。 コンテンツをクリップボードにコピーしてから、VM に設定されている新しいファイルにコンテンツを貼り付けることもできます。

    > [!IMPORTANT]
    > 次の例では、IP アドレスとフォルダーの値を確実に更新します。 値は、ファイルが保存されているフォルダーにマップする必要があります。
    >

    ```bash
    $ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
    ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

VM の復旧ポイントをリストする場合は、az backup recoverypoint list を使用します。 この例では、myRecoveryServicesVault で保護されている myVM という名前の VM の最新の復旧ポイントを選択します。

```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
```

VM に復旧ポイントを接続またはマウントするスクリプトを取得する場合は、az backup restore files mount-rp を使用します。 次の例では、myRecoveryServicesVault で保護されている myVM という名前の VM のスクリプトを取得します。

myRecoveryPointName は、前述のコマンドで取得した復旧ポイントの名前に置き換えます。

```azurecli
   az backup restore files mount-rp \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --rp-name myRecoveryPointName
```

次の例のように、スクリプトがダウンロードされ、パスワードが表示されます。

```bash
   File downloaded: vmoracle19c_eus_4598131610710119312_456133188157_6931c635931f402eb543ee554e1cf06f102c6fc513d933.py. Use password c4487e40c760d29
```

.py ファイルを VM にコピーします。

次の例では、secure copy (scp) コマンドを使用してファイルを VM に移動する方法を示しています。 コンテンツをクリップボードにコピーしてから、VM に設定されている新しいファイルにコンテンツを貼り付けることもできます。

> [!IMPORTANT]
> 次の例では、IP アドレスとフォルダーの値を確実に更新します。 値は、ファイルが保存されているフォルダーにマップする必要があります。
>

```bash
$ scp vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py azureuser@<publicIpAddress>:/tmp
```
---

### <a name="mount-the-restore-point"></a>復元ポイントをマウントする

1. 復元マウント ポイントを作成し、それにスクリプトをコピーします。

    次の例では、スナップショットのマウント先の */restore* ディレクトリを作成し、ファイルをディレクトリに移動して、ルート ユーザーが所有し、実行可能ファイルになるように、ファイルを変更します。

    ```bash 
    ssh azureuser@<publicIpAddress>
    sudo su -
    mkdir /restore
    chmod 777 /restore
    cd /restore
    cp /tmp/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py /restore
    chmod 755 /restore/vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```
    
    ここで、スクリプトを実行してバックアップを復元します。 Azure portal で生成されたパスワードを入力するように求められます。 
  
   ```bash
    ./vmoracle19c_xxxxxx_xxxxxx_xxxxxx.py
    ```

    次の例では、前のスクリプトを実行した後に表示されるものを示しています。 続行するかどうかをたずねるメッセージが表示されたら、**Y** を入力します。

    ```output
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    Please enter the password as shown on the portal to securely connect to the recovery point. : b1ad68e16dfafc6

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sdc  |  /dev/sdc1  |  /restore/vmoracle19c-20201215123912/Volume1

    2)  | /dev/sdd  |  /dev/sdd1  |  /restore/vmoracle19c-20201215123912/Volume2

    3)  | /dev/sdd  |  /dev/sdd2  |  /restore/vmoracle19c-20201215123912/Volume3

    4)  | /dev/sdd  |  /dev/sdd15  |  /restore/vmoracle19c-20201215123912/Volume5

    The following partitions failed to mount since the OS couldn't identify the filesystem.

    ************ Volumes from unknown filesystem ************

    Sr.No.  |  Disk  |  Volume  |  Partition Type

    1)  | /dev/sdb  |  /dev/sdb14  |  BIOS Boot partition

    Please refer to '/restore/vmoracle19c-2020XXXXXXXXXX/Scripts/MicrosoftAzureBackupILRLogFile.log' for more details.

    ************ Open File Explorer to browse for files. ************

    After recovery, remove the disks and close the connection to the recovery point by clicking the 'Unmount Disks' button from the portal or by using the relevant unmount command in case of powershell or CLI.

    After unmounting disks, run the script with the parameter 'clean' to remove the mount paths of the recovery point from this machine.

    Please enter 'q/Q' to exit...
    ```

2. マウントされたボリュームへのアクセスが確定されます。

    終了するには、**q** を入力し、マウントされたボリュームを検索します。 追加されたボリュームのリストを作成するには、コマンド プロンプトで、**df -h** と入力します。
    
    ```
    [root@vmoracle19c restore]# df -h
    Filesystem      Size  Used Avail Use% Mounted on
    devtmpfs        3.8G     0  3.8G   0% /dev
    tmpfs           3.8G     0  3.8G   0% /dev/shm
    tmpfs           3.8G   17M  3.8G   1% /run
    tmpfs           3.8G     0  3.8G   0% /sys/fs/cgroup
    /dev/sdd2        30G  9.6G   18G  36% /
    /dev/sdb1       126G  736M  119G   1% /u02
    /dev/sda1       497M  199M  298M  41% /boot
    /dev/sda15      495M  9.7M  486M   2% /boot/efi
    tmpfs           771M     0  771M   0% /run/user/54322
    /dev/sdc1       126G  2.9G  117G   3% /restore/vmoracle19c-20201215123912/Volume1
    /dev/sdd1       497M  199M  298M  41% /restore/vmoracle19c-20201215123912/Volume2
    /dev/sdd2        30G  9.6G   18G  36% /restore/vmoracle19c-20201215123912/Volume3
    /dev/sdd15      495M  9.7M  486M   2% /restore/vmoracle19c-20201215123912/Volume5
    ```

### <a name="perform-recovery"></a>回復の実行

1. 不足しているバックアップ ファイルを高速回復領域にコピーします。

    ```bash
    cd /restore/vmoracle19c-2020XXXXXXXXXX/Volume1/fast_recovery_area/TEST
    cp * /u02/fast_recovery_area/TEST
    cd /u02/fast_recovery_area/TEST
    chown -R oracle:oinstall *
    ```

2. 次のコマンドでは、RMAN を使用して、不足しているデータファイルを復元し、データベースを復旧します。

    ```bash
    sudo su - oracle
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
3. データベースの内容が完全に復旧されたことを確認します。

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```

4. 復元ポイントのマウントを解除します。

   ```bash
   umount /restore/vmoracle19c-20210107110037/Volume*
   ```

    ドライブをマウント解除するには、Azure Portal の **[ファイルの回復 (プレビュー)]** ブレードで **[ディスクのマウント解除]** をクリックします。

    ![[ディスクのマウント解除] コマンド](./media/oracle-backup-recovery/recovery-service-10.png)
    
    また、 **-clean** オプションを使用して python スクリプトを再度実行して、回復ボリュームのマウントを解除することもできます。

## <a name="restore-the-entire-vm"></a>VM 全体の復元

削除されたファイルを Recovery Services コンテナーから復元する代わりに、VM 全体を復元することができます。

VM 全体を復元するには、次の手順を実行します。

1. vmoracle19c を停止して削除します。
1. VM を復旧します。
1. パブリック IP アドレスを設定します。
1. VM に接続します
1. データベースを起動してステージをマウントし、復旧を実行します。

### <a name="stop-and-delete-vmoracle19c"></a>vmoracle19c を停止して削除する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure portal で、**vmoracle19c** 仮想マシンに移動して、 **[停止]** を選択します。

1. 仮想マシンが実行中ではなくなったら、 **[削除]** を選択し、 **[はい]** を選択します。

   ![コンテナー [削除] コマンド](./media/oracle-backup-recovery/recover-vm-01.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. VM を停止し、割り当てを解除します。

    ```azurecli
    az vm deallocate --resource-group rg-oracle --name vmoracle19c
    ```

2. VM を削除します。 確認を求められたら「y」と入力します。

    ```azurecli
    az vm delete --resource-group rg-oracle --name vmoracle19c
    ```

---

### <a name="recover-the-vm"></a>VM を復旧する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. Azure portal でステージング用のストレージ アカウントを作成します。

   1. Azure portal で、 **[+ リソースの作成]** を選択し、 **[ストレージ アカウント]** を検索して選択します。
    
      ![リソースの作成場所を示すスクリーンショット。](./media/oracle-backup-recovery/storage-1.png)
    
    
   1. [ストレージ アカウントの作成] ページで、既存のリソース グループ **rg-oracle** を選択し、ストレージ アカウントに **oracrestore** という名前を付けて、[アカウントの種類] として **[ストレージ V2 (汎用 v2)]** を選択します。 [レプリケーション] を **[ローカル冗長ストレージ (LRS)]** に変更し、[パフォーマンス] を **[標準]** に設定します。 [場所] が、リソース グループ内の他のすべてのリソースと同じリージョンに設定されていることを確認します。 
    
      ![ストレージ アカウントの追加ページ](./media/oracle-backup-recovery/recovery-storage-1.png)
   
   1. [確認および作成] をクリックして、 [作成] をクリックします。

2. Azure portal で、Recovery Services コンテナー項目 *myVault* を検索してクリックします。

    ![Recovery Services コンテナー myVault のバックアップ項目](./media/oracle-backup-recovery/recovery-service-06.png)
    
3.  **[概要]** ブレードで **[バックアップ項目]** を選択し、 **[Azure 仮想マシン]** を選択します。これには、0 以外のバックアップ項目の数が表示されている必要があります。

    ![Recovery Services コンテナー Azure Virtual Machine のバックアップ項目の数](./media/oracle-backup-recovery/recovery-service-07.png)

4.  [バックアップ項目 (Azure Virtual Machines)] ページに、VM **vmoracle19c** が表示されます。 VM 名をクリックします。

    ![VM の復元ページ](./media/oracle-backup-recovery/recover-vm-02.png)

5.  **[vmoracle19c]** ブレードで、整合性の種類が **[アプリケーション整合性]** の復元ポイントを選択し、右側にある省略記号 ( **...** ) をクリックしてメニューを表示します。  メニューから、 **[VM の復元]** をクリックします。

    ![[VM の復元] コマンド](./media/oracle-backup-recovery/recover-vm-03.png)

6.  **[仮想マシンの復元]** ブレードで、 **[新規作成]** および **[新しい仮想マシンの作成]** を選択します。 仮想マシン名「**vmoracle19c**」を入力し、VNet **vmoracle19cVNET** を選択します。このサブネットは、VNet の選択に基づいて自動的に設定されます。 VM の復元プロセスでは、同じリソース グループとリージョンの Azure ストレージ アカウントが必要です。 先ほど設定したストレージ アカウント **orarestore** を選択できます。

    ![構成値の復元](./media/oracle-backup-recovery/recover-vm-04.png)

7.  VM を復元するには、**[復元]** ボタンをクリックします。

8.  復元プロセスの状態を表示するには、**[ジョブ]** をクリックし、**[バックアップジョブ]** をクリックします。

    ![バックアップ ジョブの状態コマンド](./media/oracle-backup-recovery/recover-vm-05.png)

    **[進行中]** 復元操作をクリックして、復元プロセスの状態を表示します。

    ![復元プロセスの状態](./media/oracle-backup-recovery/recover-vm-06.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

ストレージ アカウントとファイル共有を設定するには、Azure CLI で次のコマンドを実行します。

1. VM と同じリソース グループと場所にストレージ アカウントを作成します。

   ```azurecli
   az storage account create -n orarestore -g rg-oracle -l eastus --sku Standard_LRS
   ```

2. 使用可能な復旧ポイントの一覧を取得します。 

   ```azurecli
   az backup recoverypoint list \
      --resource-group rg-oracle \
      --vault-name myVault \
      --backup-management-type AzureIaasVM \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --query [0].name \
      --output tsv
   ```

3. ストレージ アカウントに復旧ポイントを復元します。 `<myRecoveryPointName>` を、前の手順で生成された一覧の復旧ポイントに置き換えます。

   ```azurecli
   az backup restore restore-disks \
      --resource-group rg-oracle \
      --vault-name myVault \
      --container-name vmoracle19c \
      --item-name vmoracle19c \
      --storage-account orarestore \
      --rp-name <myRecoveryPointName> \
      --target-resource-group rg-oracle
   ```

4. 復元ジョブの詳細を取得します。 次のコマンドでは、トリガーされた復元ジョブの詳細を取得します。これには、テンプレート URI を取得するために必要な名前も含まれます。 

   ```azurecli
   az backup job list \
       --resource-group rg-oracle \
       --vault-name myVault \
       --output table
   ```

   出力はこの `(Note down the name of the restore job)` のようになります。

   ```output
   Name                                  Operation        Status     Item Name    Start Time UTC                    Duration
   ------------------------------------  ---------------  ---------  -----------  --------------------------------  --------------
   c009747a-0d2e-4ac9-9632-f695bf874693  Restore          Completed  vmoracle19c  2021-01-10T21:46:07.506223+00:00  0:03:06.634177
   6b779c98-f57a-4db1-b829-9e8eab454a52  Backup           Completed  vmoracle19c  2021-01-07T10:11:15.784531+00:00  0:21:13.220616
   502bc7ae-d429-4f0f-b78e-51d41b7582fc  ConfigureBackup  Completed  vmoracle19c  2021-01-07T09:43:55.298755+00:00  0:00:30.839674
   ```

5. VM を再作成するために使用する URI の詳細を取得します。 前の手順の復元ジョブ名を `<RestoreJobName>` に置き換えます。

    ```azurecli
      az backup job show \
        -v myVault \
        -g rg-oracle \
        -n <RestoreJobName> \
        --query properties.extendedInfo.propertyBag
    ```

   出力は次のようになります。

   ```output
   {
   "Config Blob Container Name": "vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2",
   "Config Blob Name": "config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Config Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/config-vmoracle19c-c009747a-0d2e-4ac9-9632-f695bf874693.json",
   "Job Type": "Recover disks",
   "Recovery point time ": "1/7/2021 10:11:19 AM",
   "Target Storage Account Name": "orarestore",
   "Target resource group": "rg-oracle",
   "Template Blob Uri": "https://orarestore.blob.core.windows.net/vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2/azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json"
   }
   ```

   テンプレート BLOB の URI の末尾にあるテンプレートの名前 (この例では `azuredeployc009747a-0d2e-4ac9-9632-f695bf874693.json`) と、BLOB コンテナーの名前 (`vmoracle19c-75aefd4b34c64dd39fdcd3db579783f2`) が示されています。 

   次のコマンドでこれらの値を使用して、VM を作成するための準備として変数を割り当てます。 SAS キーは、30 分間の期間でストレージ コンテナーに対して生成されます。  


   ```azurecli
   expiretime=$(date -u -d "30 minutes" '+%Y-%m-%dT%H:%MZ')
   connection=$(az storage account show-connection-string \
    --resource-group rg-oracle \
    --name orarestore \
    --query connectionString)
   token=$(az storage blob generate-sas \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
   url=$(az storage blob url \
    --container-name <ContainerName> \
    --name <TemplateName> \
    --connection-string $connection \
    --output tsv)
   ```

   ここで、テンプレートをデプロイして VM を作成します。

   ```azurecli
   az deployment group create \
      --resource-group rg-oracle \
      --template-uri $url?$token
   ```

   VM の名前の入力を求めるメッセージが表示されます。

---

### <a name="set-the-public-ip-address"></a>パブリック IP アドレスを設定する

VM が復元されたら、元の IP アドレスを新しい VM に再割り当てする必要があります。

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1.  Azure portal で、仮想マシン **vmoracle19c** に移動します。 vmoracle19c-nic-XXXXXXXXXXXX のような新しいパブリック IP と NIC が割り当てられていますが、DNS アドレスはないことがわかります。 元の VM が削除されると、そのパブリック IP と NIC が保持され、次の手順で新しい VM に再アタッチされます。

    ![パブリック IP アドレスのリスト](./media/oracle-backup-recovery/create-ip-01.png)

2.  VM を停止する

    ![IP アドレスを作成する](./media/oracle-backup-recovery/create-ip-02.png)

3.  **[ネットワーク]** に移動します

    ![IP アドレスを関連付ける](./media/oracle-backup-recovery/create-ip-03.png)

4.  **[ネットワーク インターフェイスの接続]** をクリックし、元のパブリック IP アドレスがまだ関連付けられている元の NIC の **vmoracle19cVMNic を選択し、 **[OK]** をクリックします

    ![リソースの種類と NIC の値を選択する](./media/oracle-backup-recovery/create-ip-04.png)

5.  次に、VM 復元操作で作成された NIC はプライマリ インターフェイスとして構成されているため、デタッチする必要があります。 **[ネットワーク インターフェイスのデタッチ]** をクリックし、**vmoracle19c-nic-XXXXXXXXXXXX** のような新しい NIC を選択し、 **[OK]** をクリックします

    ![[ネットワーク インターフェイスのデタッチ] を選択する場所を示すスクリーンショット。](./media/oracle-backup-recovery/create-ip-05.png)
    
    再作成した VM には、元の IP アドレスとネットワーク セキュリティ グループの規則に関連付けられている元の NIC が使用されるようになります
    
    ![IP アドレスの値](./media/oracle-backup-recovery/create-ip-06.png)
    
6.  **[概要]** に戻り、 **[開始]** をクリックします 

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

1. VM を停止し、割り当てを解除します。

   ```azurecli
   az vm deallocate --resource-group rg-oracle --name vmoracle19c
   ```

2. 現在の、復元によって生成された VM NIC を一覧表示します

   ```azurecli
   az vm nic list --resource-group rg-oracle --vm-name vmoracle19c
   ```

   出力は次のようになります。復元によって生成された NIC 名が `vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf` として表示されます

   ```output
   {
    "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxx/resourceGroups/rg-oracle/providers/Microsoft.Network/networkInterfaces/vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf",
    "primary": true,
    "resourceGroup": "rg-oracle"
   }
   ```

3. `<VMName>VMNic` の名前 (この場合は `vmoracle19cVMNic`) を持つ元の NIC をアタッチします。 元のパブリック IP アドレスはこの NIC にまだアタッチされており、NIC が再アタッチされると VM に復元されます。 

   ```azurecli
   az vm nic add --nics vmoracle19cVMNic --resource-group rg-oracle --vm-name vmoracle19c
   ```

4. 復元によって生成された NIC をデタッチします

   ```azurecli
   az vm nic remove --nics vmoracle19cRestoredNICc2e8a8a4fc3f47259719d5523cd32dcf --resource-group rg-oracle --vm-name vmoracle19c
   ```

5. VM を起動します。

   ```azurecli
   az vm start --resource-group rg-oracle --name vmoracle19c
   ```

---

### <a name="connect-to-the-vm"></a>VM に接続します

VM に接続するには、次のスクリプトを使用します。

```azurecli
ssh <publicIpAddress>
```

### <a name="start-the-database-to-mount-stage-and-perform-recovery"></a>データベースを起動してステージをマウントし、復旧を実行します

1. 自動開始で VM の起動時にデータベースの起動が試みられたために、インスタンスが実行されていることがあります。 ただし、データベースには復旧が必要であり、マウント ステージでのみ発生する可能性があるため、最初に準備のシャットダウンを実行します。

    ```bash
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> shutdown immediate
    SQL> startup mount
    SQL> recover automatic database;
    SQL> alter database open;
    ```
    
1. データベースの内容が復旧されたことを確認します。

    ```bash
    SQL> select * from scott.scott_table;
    ```

これで、Azure Linux VM での Oracle Database 19c のデータベースのバックアップと復旧が完了しました。

## <a name="delete-the-vm"></a>VM の削除

VM が必要なくなったら、次のコマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>次のステップ

[チュートリアル:高可用性 VM の作成](../../linux/create-cli-complete.md)

[VM デプロイ Azure CLI サンプルを探索する](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)