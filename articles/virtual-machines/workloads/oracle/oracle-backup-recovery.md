---
title: Azure Linux 仮想マシンでの Oracle Database 12c データベースのバックアップと回復 | Microsoft Docs
description: Azure 環境で Oracle Database 12c データベースをバックアップおよび回復する方法について説明します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 461f6127111e745fe4a81958aaa225ed1dc4392a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707717"
---
# <a name="back-up-and-recover-an-oracle-database-12c-database-on-an-azure-linux-virtual-machine"></a>Azure Linux 仮想マシンでの Oracle Database 12c データベースのバックアップと回復

Azure CLI を使用すると、コマンド プロンプトで、またはスクリプトを使用して Azure リソースを作成および管理できます。 この記事では、Azure CLI スクリプトを使用して、Azure Marketplace ギャラリー イメージから Oracle Database 12c データベースをデプロイします。

始める前に、Azure CLI がインストールされていることを確認します。 詳細については、[Azure CLI のインストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)を参照してください。

## <a name="prepare-the-environment"></a>環境の準備

### <a name="step-1-prerequisites"></a>手順 1:前提条件

*   バックアップと回復プロセスを実行するには、インストール済みの Oracle Database 12c のインスタンスを使用して、Linux VM を作成する必要があります。 VM の作成に使用する Marketplace イメージは、*Oracle:Oracle-Database-Ee:12.1.0.2:latest* と呼ばれます。

    Oracle データベースの作成手順については、[Oracle データベースのクイック作成ガイド](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-database-quick-create)のページを参照してください。


### <a name="step-2-connect-to-the-vm"></a>手順 2:VM に接続します

*   VM で Secure Shell (SSH) セッションを作成するには、次のコマンドを使用します。 IP アドレスとのホスト名の組み合わせを VM の `publicIpAddress` の値で置き換えます。

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-3-prepare-the-database"></a>手順 3:データベースを準備する

1.  *myVM* という仮想マシンで実行されている Oracle インスタンス (cdb1) があることを前提としています。

    *oracle* スーパーユーザー ルートを実行してから、リスナーを初期化します。

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    Copyright (c) 1991, 2014, Oracle.  All rights reserved.

    Starting /u01/app/oracle/product/12.1.0/dbhome_1/bin/tnslsnr: please wait...

    TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Log messages written to /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))

    Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
    STATUS of the LISTENER
    ------------------------
    Alias                     LISTENER
    Version                   TNSLSNR for Linux: Version 12.1.0.2.0 - Production
    Start Date                23-MAR-2017 15:32:08
    Uptime                    0 days 0 hr. 0 min. 0 sec
    Trace Level               off
    Security                  ON: Local OS Authentication
    SNMP                      OFF
    Listener Log File         /u01/app/oracle/diag/tnslsnr/myVM/listener/alert/log.xml
    Listening Endpoints Summary...
    (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=myVM.twltkue3xvsujaz1bvlrhfuiwf.dx.internal.cloudapp.net)(PORT=1521)))
    The listener supports no services
    The command completed successfully
    ```

2.  データベースが、アーカイブ ログ モードになっていることを確認します (省略可能)。

    ```bash
    $ sqlplus / as sysdba
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG

    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```
3.  コミットをテストするテーブルを作成します (省略可能) 。

    ```bash
    SQL> alter session set "_ORACLE_SCRIPT"=true ;
    Session altered.
    SQL> create user scott identified by tiger;
    User created.
    SQL> grant create session to scott;
    Grant succeeded.
    SQL> grant create table to scott;
    Grant succeeded.
    SQL> alter user scott quota 100M on users;
    User altered.
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    Table created.
    SQL> insert into scott_Table VALUES(1,'Line 1');
    1 row created.
    SQL> commit;
    Commit complete.
    ```
4.  バックアップ ファイルの場所とサイズを確認または変更します。

    ```bash
    $ sqlplus / as sysdba
    SQL> show parameter db_recovery
    NAME                                 TYPE        VALUE
    ------------------------------------ ----------- ------------------------------
    db_recovery_file_dest                string      /u01/app/oracle/fast_recovery_area
    db_recovery_file_dest_size           big integer 4560M
    ```
5. Oracle Recovery Manager (RMAN) を使用して、データベースをバックアップします。

    ```bash
    $ rman target /
    RMAN> backup database plus archivelog;
    ```

### <a name="step-4-application-consistent-backup-for-linux-vms"></a>手順 4:Linux VM のアプリケーション整合性バックアップ

これは Azure Backup の新機能で、 ユーザーが VM スナップショットの前および後に実行するスクリプト (事前および事後) を作成し、選択できます。

1. JSON ファイルをダウンロードします。

    [https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig ) から VMSnapshotScriptPluginConfig.json をダウンロードします。 ファイルの内容は、次のようになります。

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "",
        "postScriptLocation" : "",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

2. VM に /etc/azure フォルダーを作成します。

    ```bash
    $ sudo su -
    # mkdir /etc/azure
    # cd /etc/azure
    ```

3. JSON ファイルをコピーします。

    VMSnapshotScriptPluginConfig.json ファイルを /etc/azure フォルダーにコピーします。

4. JSON ファイルを編集します。

    VMSnapshotScriptPluginConfig.json ファイルを編集して、`PreScriptLocation` と `PostScriptlocation` パラメーターを含めます。 例:

    ```azurecli
    {
        "pluginName" : "ScriptRunner",
        "preScriptLocation" : "/etc/azure/pre_script.sh",
        "postScriptLocation" : "/etc/azure/post_script.sh",
        "preScriptParams" : ["", ""],
        "postScriptParams" : ["", ""],
        "preScriptNoOfRetries" : 0,
        "postScriptNoOfRetries" : 0,
        "timeoutInSeconds" : 30,
        "continueBackupOnFailure" : true,
        "fsFreezeEnabled" : true
    }
    ```

5. 事前と事後のスクリプト ファイルを作成します。

    "コールド バックアップ" (シャットダウンして再起動することによるオフライン バックアップ) の事前スクリプトと事後スクリプトの例を次に示します。

    /etc/azure/pre_script.sh の場合

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" > /etc/azure/pre_script_$v_date.log
    ```

    /etc/azure/post_script.sh の場合

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" > /etc/azure/post_script_$v_date.log
    ```

    "ホット バックアップ" (オンライン バックアップ) の事前スクリプトと事後スクリプトの例を次に示します。

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/pre_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    /etc/azure/post_script.sh の場合

    ```bash
    v_date=`date +%Y%m%d%H%M`
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
    ORA_OWNER=oracle
    su - $ORA_OWNER -c "sqlplus / as sysdba @/etc/azure/post_script.sql" > /etc/azure/pre_script_$v_date.log
    ```

    /etc/azure/pre_script.sql の場合、要件に合わせてファイルの内容を変更します。

    ```bash
    alter tablespace SYSTEM begin backup;
    ...
    ...
    alter system switch logfile;
    alter system archive log stop;
    ```

    /etc/azure/post_script.sql の場合、要件に合わせてファイルの内容を変更します。

    ```bash
    alter tablespace SYSTEM end backup;
    ...
    ...
    alter system archive log start;
    ```

6. ファイルのアクセス許可を変更します。

    ```bash
    # chmod 600 /etc/azure/VMSnapshotScriptPluginConfig.json
    # chmod 700 /etc/azure/pre_script.sh
    # chmod 700 /etc/azure/post_script.sh
    ```

7. スクリプトをテストします。

    スクリプトをテスト (ルートとしてサインイン) して、 エラーがないことを確認します。

    ```bash
    # /etc/azure/pre_script.sh
    # /etc/azure/post_script.sh
    ```

詳細については、「[Application consistent backup for Linux VMs](https://azure.microsoft.com/blog/announcing-application-consistent-backup-for-linux-vms-using-azure-backup/)」(Linux VM のアプリケーション整合性バックアップ) を参照してください。


### <a name="step-5-use-azure-recovery-services-vaults-to-back-up-the-vm"></a>手順 5:Azure Recovery Services コンテナーを使用して VM をバックアップする

1.  Azure Portal で **Recovery Services コンテナー**を検索します。

    ![Recovery Services コンテナー ページ](./media/oracle-backup-recovery/recovery_service_01.png)

2.  **[Recovery Services コンテナー]** ブレードで **[追加]** をクリックして、新しいコンテナーを追加します。

    ![Recovery Services コンテナーの追加ページ](./media/oracle-backup-recovery/recovery_service_02.png)

3.  続行するには、 **[myVault]** をクリックします。

    ![Recovery Services コンテナーの詳細ページ](./media/oracle-backup-recovery/recovery_service_03.png)

4.  **myVault**ブレードで、 **[バックアップ]** をクリックします。

    ![Recovery Services コンテナーのバックアップ ページ](./media/oracle-backup-recovery/recovery_service_04.png)

5.  **[バックアップの目標]** ブレードには、既定の **[Azure]** と **[仮想マシン]** の値を使用します。 Click **OK**.

    ![Recovery Services コンテナーの詳細ページ](./media/oracle-backup-recovery/recovery_service_05.png)

6.  **[バックアップ ポリシー]** には、 **[DefaultPolicy]** または **[新しいポリシーの作成]** を使用します。 Click **OK**.

    ![Recovery Services コンテナーのバックアップ ポリシーの詳細ページ](./media/oracle-backup-recovery/recovery_service_06.png)

7.  **[仮想マシンの選択]** ブレードで、 **[myVM1]** チェック ボックスをオンし、 **[OK]** をクリックします。 **[バックアップの有効化]** ボタンをクリックします。

    ![バックアップする Recovery Services コンテナー項目の詳細ページ](./media/oracle-backup-recovery/recovery_service_07.png)

    > [!IMPORTANT]
    > **[バックアップの有効化]** をクリックしても、スケジュールされた時間に達するまでバックアップ プロセスは開始されません。 即時バックアップをセットアップするには、次の手順を完了します。

8.  **myVault のバックアップ項目**ブレードの **[バックアップ項目数]** の下で、バックアップ項目の数を選択します。

    ![Recovery Services コンテナー myVault の詳細ページ](./media/oracle-backup-recovery/recovery_service_08.png)

9.  **バックアップ項目 (Azure 仮想マシン)** ブレードのページ右側で省略記号 ( **...** ) ボタンをクリックし、 **[今すぐバックアップ]** をクリックします。

    ![Recovery Services コンテナーの [今すぐバックアップ] コマンド](./media/oracle-backup-recovery/recovery_service_09.png)

10. **[バックアップ]** ボタンをクリックします。 バックアップ プロセスが完了するまで待ってから、 「[手順 6: データベース ファイルを削除する](#step-6-remove-the-database-files)」に移動します。

    バックアップ ジョブの状態を表示するには、 **[ジョブ]** をクリックします。

    ![Recovery Services コンテナーのジョブ ページ](./media/oracle-backup-recovery/recovery_service_10.png)

    次のイメージには、バックアップ ジョブの状態が表示されます。

    ![状態が表示された Recovery Services コンテナーのジョブ ページ](./media/oracle-backup-recovery/recovery_service_11.png)

11. アプリケーション整合バックアップのため、ログ ファイル内のすべてのエラーに対処します。 ログ ファイルは /var/log/azure/Microsoft.Azure.RecoveryServices.VMSnapshotLinux/1.0.9114.0 にあります。

### <a name="step-6-remove-the-database-files"></a>手順 6:データベース ファイルを削除する 
回復プロセスのテスト方法については、この記事の後半で学習します。 回復プロセスをテストするには、先にデータベース ファイルを削除する必要があります。

1.  テーブルスペースとバックアップ ファイルを削除します。

    ```bash
    $ sudo su - oracle
    $ cd /u01/app/oracle/oradata/cdb1
    $ rm -f *.dbf
    $ cd /u01/app/oracle/fast_recovery_area/CDB1/backupset
    $ rm -rf *
    ```
    
2.  Oracle インスタンスをシャットダウンします (省略可能)。

    ```bash
    $ sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

## <a name="restore-the-deleted-files-from-the-recovery-services-vaults"></a>Recovery Services コンテナーから削除されたファイルを復元します。
削除されたファイルを復元するには、次の手順を実行します。

1. Azure Portal で、*myVault* Recovery Services コンテナー項目を検索します。 **[概要**] ブレードの **[バックアップ項目]** で、項目数を選択します。

    ![Recovery Services コンテナー myVault のバックアップ項目](./media/oracle-backup-recovery/recovery_service_12.png)

2. **[バックアップ項目の数]** で、項目の数を選択します。

    ![Recovery Services コンテナー Azure Virtual Machine のバックアップ項目の数](./media/oracle-backup-recovery/recovery_service_13.png)

3. **[myvm1]** ブレードで、 **[ファイルの回復 (プレビュー)]** をクリックします。

    ![Recovery Services コンテナーファイル回復ページのスクリーンショット](./media/oracle-backup-recovery/recovery_service_14.png)

4. **[ファイルの回復 (プレビュー)]** ウィンドウで、 **[スクリプトのダウンロード]** をクリックします。 次に、クライアント コンピューター上のフォルダーにダウンロードされた (.sh) ファイルを保存します。

    ![スクリプト ファイルのダウンロードの保存オプション](./media/oracle-backup-recovery/recovery_service_15.png)

5. .sh ファイルを VM にコピーします。

    次の例では、secure copy (scp) コマンドを使用してファイルを VM に移動する方法を示しています。 コンテンツをクリップボードにコピーしてから、VM に設定されている新しいファイルにコンテンツを貼り付けることもできます。

    > [!IMPORTANT]
    > 次の例では、IP アドレスとフォルダーの値を確実に更新します。 値は、ファイルが保存されているフォルダーにマップする必要があります。

    ```bash
    $ scp Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh <publicIpAddress>:/<folder>
    ```
6. ファイルがルートによって所有されるように、ファイルを変更します。

    次の例では、ルートによって所有されるように、ファイルを変更します。 次に、アクセス許可を変更します。

    ```bash 
    $ ssh <publicIpAddress>
    $ sudo su -
    # chown root:root /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # chmod 755 /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    # /<folder>/Linux_myvm1_xx-xx-2017 xx-xx-xx PM.sh
    ```
    次の例では、前のスクリプトを実行した後に表示されるものを示しています。 続行するかどうかをたずねるメッセージが表示されたら、**Y** を入力します。

    ```bash
    Microsoft Azure VM Backup - File Recovery
    ______________________________________________
    The script requires 'open-iscsi' and 'lshw' to run.
    Do you want us to install 'open-iscsi' and 'lshw' on this machine?
    Please press 'Y' to continue with installation, 'N' to abort the operation. : Y
    Installing 'open-iscsi'....
    Installing 'lshw'....

    Connecting to recovery point using ISCSI service...

    Connection succeeded!

    Please wait while we attach volumes of the recovery point to this machine...

    ************ Volumes of the recovery point and their mount paths on this machine ************

    Sr.No.  |  Disk  |  Volume  |  MountPath

    1)  | /dev/sde  |  /dev/sde1  |  /root/myVM-20170517093913/Volume1

    2)  | /dev/sde  |  /dev/sde2  |  /root/myVM-20170517093913/Volume2

    ************ Open File Explorer to browse for files. ************

    After recovery, to remove the disks and close the connection to the recovery point, please click 'Unmount Disks' in step 3 of the portal.

    Please enter 'q/Q' to exit...
    ```

7. マウントされたボリュームへのアクセスが確定されます。

    終了するには、**q** を入力し、マウントされたボリュームを検索します。 追加されたボリュームのリストを作成するには、コマンド プロンプトで、**df -k** を入力します。

    ![df-k コマンド](./media/oracle-backup-recovery/recovery_service_16.png)

8. 次のスクリプトを使用して、不足しているファイルをフォルダーにコピーして戻します。

    ```bash
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cp *.bkp /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # cd /u01/app/oracle/fast_recovery_area/CDB1/backupset/2017_xx_xx
    # chown oracle:oinstall *.bkp
    # cd /root/myVM-2017XXXXXXX/Volume2/u01/app/oracle/oradata/cdb1
    # cp *.dbf /u01/app/oracle/oradata/cdb1
    # cd /u01/app/oracle/oradata/cdb1
    # chown oracle:oinstall *.dbf
    ```
9. 次のスクリプトでは、RMAN を使用してデータベースを復旧します。

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```
    
10. ディスクをマウントを解除します。

    ドライブをマウント解除するには、Azure Portal の **[ファイルの回復 (プレビュー)]** ブレードで **[ディスクのマウント解除]** をクリックします。

    ![[ディスクのマウント解除] コマンド](./media/oracle-backup-recovery/recovery_service_17.png)

## <a name="restore-the-entire-vm"></a>VM 全体の復元

削除されたファイルを Recovery Services コンテナーから復元する代わりに、VM 全体を復元することができます。

### <a name="step-1-delete-myvm"></a>手順 1:myVM を削除する

*   Azure Portal にサインインし、**myVM1** コンテナーに移動して、 **[削除]** を選択します。

    ![コンテナー [削除] コマンド](./media/oracle-backup-recovery/recover_vm_01.png)

### <a name="step-2-recover-the-vm"></a>手順 2:VM を復旧する

1.  **[Recovery Services コンテナー]** に移動して、 **[myVault]** を選択します。

    ![myVault エントリ](./media/oracle-backup-recovery/recover_vm_02.png)

2.  **[概要**] ブレードの **[バックアップ項目]** で、項目数を選択します。

    ![myVault のバックアップ項目](./media/oracle-backup-recovery/recover_vm_03.png)

3.  **[バックアップ項目 (Azure Virtual Machin)]** ブレードで、 **[myvm1]** を選択します。

    ![VM の復元ページ](./media/oracle-backup-recovery/recover_vm_04.png)

4.  **[myvm1]** ブレードで、省略記号 ( **...** ) ボタンをクリックし、 **[VM の復元]** をクリックします。

    ![[VM の復元] コマンド](./media/oracle-backup-recovery/recover_vm_05.png)

5.  **[復元ポイントの選択]** ブレードで、復元する項目を選択し、 **[OK]** をクリックします。

    ![復元ポイントを選択します。](./media/oracle-backup-recovery/recover_vm_06.png)

    アプリケーション整合性バックアップを有効にしている場合は、縦棒が青色で表示されます

6.  **[構成の復元]** ブレードで、仮想マシン名を選択し、リソース グループを選択し、 **[OK]** をクリックします。

    ![構成値の復元](./media/oracle-backup-recovery/recover_vm_07.png)

7.  VM を復元するには、 **[復元]** ボタンをクリックします。

8.  復元プロセスの状態を表示するには、 **[ジョブ]** をクリックし、 **[バックアップジョブ]** をクリックします。

    ![バックアップ ジョブの状態コマンド](./media/oracle-backup-recovery/recover_vm_08.png)

    次の図は、復元プロセスの状態を示しています。

    ![復元プロセスの状態](./media/oracle-backup-recovery/recover_vm_09.png)

### <a name="step-3-set-the-public-ip-address"></a>手順 3:パブリック IP アドレスを設定する
VM を復元したら、パブリック IP アドレスを設定します。

1.  検索ボックスで**パブリック IP アドレス**を検索します。

    ![パブリック IP アドレスのリスト](./media/oracle-backup-recovery/create_ip_00.png)

2.  **[パブリック IP アドレス]** ブレードで、 **[追加]** をクリックします。 **[パブリック IP アドレスの作成]** ブレードの **[名前]** で、パブリック IP 名を選択します。 **[リソース グループ]** では、 **[既存のものを使用]** を選択します。 **[作成]** をクリックします。

    ![IP アドレスを作成する](./media/oracle-backup-recovery/create_ip_01.png)

3.  パブリック IP アドレスを仮想マシンのネットワーク インターフェイスに関連付けるには、**myVMip** を検索して選択します。 次に、 **[関連付け]** をクリックします。

    ![IP アドレスを関連付ける](./media/oracle-backup-recovery/create_ip_02.png)

4.  **[リソースの種類]** で **[ネットワーク インターフェイス]** を選択します。 myVM インスタンスで使用されるネットワーク インターフェイスを選択し、 **[OK]** をクリックします。

    ![リソースの種類と NIC の値を選択する](./media/oracle-backup-recovery/create_ip_03.png)

5.  ポータルから移植される myVM インスタンスを検索して開きます。 仮想マシンに関連付けられている IP アドレスが、myVM **[概要]** ブレードに表示されます。

    ![IP アドレスの値](./media/oracle-backup-recovery/create_ip_04.png)

### <a name="step-4-connect-to-the-vm"></a>手順 4:VM に接続します

*   VM に接続するには、次のスクリプトを使用します。

    ```bash 
    ssh <publicIpAddress>
    ```

### <a name="step-5-test-whether-the-database-is-accessible"></a>手順 5:データベースがアクセス可能かどうかをテストする
*   アクセシビリティをテストするには、次のスクリプトを使用します。

    ```bash 
    $ sudo su - oracle
    $ sqlplus / as sysdba
    SQL> startup
    ```

    > [!IMPORTANT]
    > データベースの **startup** コマンドでエラーが発生する場合、データベースを復旧するには、「[手順 6: RMAN を使用してデータベースを復旧する](#step-6-optional-use-rman-to-recover-the-database)」を参照してください。

### <a name="step-6-optional-use-rman-to-recover-the-database"></a>手順 6:(省略可能) RMAN を使用してデータベースを復旧する
*   データベースを復旧するには、次のスクリプトを使用します。

    ```bash
    # sudo su - oracle
    $ rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open resetlogs;
    RMAN> SELECT * FROM scott.scott_table;
    ```

これで、Azure Linux VM での Oracle 12c のデータベースのバックアップと復旧が完了しました。

## <a name="delete-the-vm"></a>VM の削除

VM が必要なくなったら、次のコマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

[チュートリアル:高可用性 VM の作成](../../linux/create-cli-complete.md)

[VM デプロイ Azure CLI サンプルを探索する](../../linux/cli-samples.md)



