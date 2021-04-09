---
title: RMAN および Azure Storage を使用して Azure Linux VM で Oracle Database 19c データベースをバックアップする
description: Oracle Database 19c データベースを Azure クラウド ストレージにバックアップする方法について説明します。
author: cro27
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 01/28/2021
ms.author: cholse
ms.reviewer: dbakevlar
ms.openlocfilehash: a6ce5446bd6470ef7a829925646d486801b28ebc
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670010"
---
# <a name="back-up-and-recover-an-oracle-database-19c-database-on-an-azure-linux-vm-using-azure-storage"></a>Azure Storage を使用して Azure Linux VM で Oracle Database 19c データベースをバックアップおよび復旧する

この記事では、Azure VM で実行されている Oracle データベースをバックアップおよび復元するためのメディアとしての Azure Storage の使用方法について説明します。 SMB プロトコルを使用して VM にマウントされた Azure File Storage に、Oracle RMAN を使用してデータベースをバックアップします。 バックアップ メディアに Azure ストレージを使用すると、コスト効率とパフォーマンスが非常に高くなります。 ただし、非常に大規模なデータベースの場合は、Azure Backup の方が優れたソリューションとなります。

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../includes/azure-cli-prepare-your-environment.md)]

- バックアップと回復プロセスを実行するには、インストール済みの Oracle Database 19c のインスタンスを使用して、Linux VM を作成する必要があります。 VM の作成に現在使用されている Marketplace イメージは、**Oracle:oracle-database-19-3:oracle-database-19-0904:latest** です。 このチュートリアルを完了するには、[Oracle データベースの作成のクイックスタート](./oracle-database-quick-create.md)に関する記事の手順に従って Oracle データベースを作成してください。

## <a name="prepare-the-database-environment"></a>データベース環境を準備する

1. VM で Secure Shell (SSH) セッションを作成するには、次のコマンドを使用します。 IP アドレスとのホスト名の組み合わせを VM の `publicIpAddress` の値で置き換えます。
    
   ```bash
   ssh azureuser@<publicIpAddress>
   ```
   
2. ***ルート*** ユーザーに切り替えます。
 
   ```bash
   sudo su -
   ```
    
3. oracle ユーザーを ***/etc/sudoers*** ファイルに追加します。

   ```bash
   echo "oracle   ALL=(ALL)      NOPASSWD: ALL" >> /etc/sudoers
   ```

4. この手順では、*vmoracle19c* という仮想マシンで実行されている Oracle インスタンス (test) があることを前提としています。

   ユーザーを *oracle* ユーザーに切り替えます。

   ```bash
   sudo su - oracle
   ```
    
5. 接続する前に、環境変数 ORACLE_SID を設定する必要があります。
    
    ```bash
    export ORACLE_SID=test;
    ```
   
   また、次のコマンドを使用して、今後のサインインのために `oracle` ユーザーの `.bashrc` ファイルに ORACLE_SID 変数を追加する必要があります。

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```
    
6. Oracle リスナーがまだ実行されていない場合は、起動します。
    
   ```bash
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

7.  高速回復領域 (FRA) の場所を作成します。

    ```bash
    mkdir /u02/fast_recovery_area
    ```

8.  データベースに接続します。

    ```bash
    sqlplus / as sysdba
    ```

9.  データベースがまだ実行されていない場合は、起動します。

    ```bash
    SQL> startup
    ```

10. 高速回復領域のデータベース環境変数を設定します。

    ```bash
    SQL>  system set db_recovery_file_dest_size=4096M scope=both;
    SQL> alter system set db_recovery_file_dest='/u02/fast_recovery_area' scope=both;
    ```
    
11. データベースが、オンライン バックアップを有効にするためのアーカイブ ログ モードになっていることを確認します。
    最初にログ アーカイブの状態を確認します。

    ```bash
    SQL> SELECT log_mode FROM v$database;

    LOG_MODE
    ------------
    NOARCHIVELOG
    ```

    NOARCHIVELOG モードになっている場合は、sqlplus で次のコマンドを実行します。

    ```bash
    SQL> SHUTDOWN IMMEDIATE;
    SQL> STARTUP MOUNT;
    SQL> ALTER DATABASE ARCHIVELOG;
    SQL> ALTER DATABASE OPEN;
    SQL> ALTER SYSTEM SWITCH LOGFILE;
    ```

12. テーブルを作成して、バックアップおよび復元操作をテストします。

    ```bash
    SQL> create user scott identified by tiger quota 100M on users;
    SQL> grant create session, create table to scott;
    SQL> connect scott/tiger
    SQL> create table scott_table(col1 number, col2 varchar2(50));
    SQL> insert into scott_table VALUES(1,'Line 1');
    SQL> commit;
    SQL> quit
    ```

## <a name="back-up-to-azure-files"></a>Azure Files にバックアップする

Azure Files にバックアップするには、これらの手順を実行します。

1. Azure File Storage を設定します。
1. Azure Storage ファイル共有を VM にマウントします。
1. データベースをバックアップします。
1. データベースを復元して復旧します。

### <a name="set-up-azure-file-storage"></a>Azure File Storage を設定する

この手順では、Oracle Recovery Manager (RMAN) を使用して Azure File Storage に Oracle データベースをバックアップします。 Azure ファイル共有は、クラウド内に存在するフル マネージドのファイル共有です。 これらはサーバー メッセージ ブロック (SMB) プロトコルまたはネットワーク ファイル システム (NFS) プロトコルのどちらかを使用してアクセスできます。 この手順では、SMB プロトコルを使用して VM にマウントするファイル共有の作成について説明します。 NFS を使用してマウントする方法の詳細については、[NFS 3.0 プロトコルを使用した Blob Storage のマウント](../../../storage/blobs/network-file-system-protocol-support-how-to.md)に関する記事を参照してください。

Azure Files をマウントする場合は、`cache=none` を使用してファイル共有データのキャッシュを無効にします。 また、共有に作成されたファイルが oracle ユーザーによって所有されるようにするには、`uid=oracle` オプションと `gid=oinstall` オプションも設定します。 

# <a name="portal"></a>[ポータル](#tab/azure-portal)

まず、ストレージ アカウントを設定します。

1. Azure portal で File Storage を構成する

    Azure portal で、* **[+ リソースの作成]** _ を選択し、_ *_[ストレージ アカウント]_** を検索して選択します。
    
    ![リソースを作成してストレージ アカウントを選択する場所を示すスクリーンショット。](./media/oracle-backup-recovery/storage-1.png)
    
2. [ストレージ アカウントの作成] ページで、既存のリソース グループ ***rg-oracle** _ を選択し、ストレージ アカウントに _*_oracbkup1_*_ という名前を付けて、[アカウントの種類] として _*_[Storage V2 (generalpurpose v2)]\(Storage V2 (汎用 V2)\)_*_ を選択します。 [レプリケーション] を _*_[ローカル冗長ストレージ (LRS)]_*_ に変更し、[パフォーマンス] を _* _[標準]_ ** に設定します。 [場所] が、リソース グループ内の他のすべてのリソースと同じリージョンに設定されていることを確認します。 
    
    ![既存のリソース グループを選択する場所を示すスクリーンショット。](./media/oracle-backup-recovery/file-storage-1.png)
   
   
3. * **[詳細設定]** _ タブをクリックし、Azure Files で、_*_[大きいファイルの共有]_*_ を _* _[有効]_ ** に設定します。 [確認および作成] をクリックして、 [作成] をクリックします。
    
    ![大きいファイルの共有を有効に設定する場所を示すスクリーンショット。](./media/oracle-backup-recovery/file-storage-2.png)
    
    
4. ストレージ アカウントが作成されたら、リソースに移動し、***[ファイル共有]*** を選択します。
    
    ![ファイル共有を選択する場所を示すスクリーンショット。](./media/oracle-backup-recovery/file-storage-3.png)
    
5. * **[+ ファイル共有]** _ をクリックし、_*_[新しいファイル共有]_*_ ブレードで、ファイル共有に _*_orabkup1_*_ という名前を付けます。 _*_[クォータ]_*_ を _*_10240_*_ GiB に設定し、階層として、_*_[トランザクション最適化]_*_ をチェックします。 クォータは、ファイル共有を拡張できる上限を反映します。 Standard Storage を使用しているため、リソースは従量課金制であり、プロビジョニングされていないことから、10 TiB に設定しても、使用した分を超えるコストは発生しません。 バックアップ戦略で、より多くのストレージが必要な場合は、すべてのバックアップを保持する適切なレベルにクォータを設定する必要があります。   [新しいファイル共有] ブレードが完了したら、_* _[作成]_ ** をクリックします。
    
    ![新しいファイル共有を追加する場所が示されているスクリーンショット。](./media/oracle-backup-recovery/file-storage-4.png)
    
    
6. 作成したら、[ファイル共有の設定] ページの ***[orabkup1]*** をクリックします。 
    * **[接続]** _ タブをクリックして [接続] ブレードを開き、_ *_[Linux]_** タブをクリックします。SMB プロトコルを使用してファイル共有をマウントするために提供されているコマンドをコピーします。 
    
    ![ストレージ アカウントの追加ページ](./media/oracle-backup-recovery/file-storage-5.png)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

ストレージ アカウントとファイル共有を設定するには、Azure CLI で次のコマンドを実行します。

1. お使いの VM と同じリソース グループと場所にストレージ アカウントを作成します。
   ```azurecli
   az storage account create -n orabackup1 -g rg-oracle -l eastus --sku Standard_LRS --enable-large-file-share
   ```

2. 10 TiB のクォータで、ストレージ アカウントにファイル共有を作成します。

   ```azurecli
   az storage share create --account-name orabackup1 --name orabackup --quota 10240
   ```

3. ファイル共有を VM にマウントするときに必要なストレージ アカウント主キー (key1) を取得します。

   ```azurecli
   az storage account keys list --resource-group rg-oracle --account-name orabackup1
   ```

---

### <a name="mount-the-azure-storage-file-share-to-your-vm"></a>Azure Storage ファイル共有を VM にマウントする

1. マウント ポイントを作成します。

   ```bash
   sudo mkdir /mnt/orabackup
   ```

2. 資格情報を設定します。

   ```bash
   if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
   fi
   ```

   次のコマンドを実行する前に、前に取得したストレージ アカウント キーで `<Your Storage Account Key1>` を置き換えます。
   ```bash
   if [ ! -f "/etc/smbcredentials/orabackup1.cred" ]; then
     sudo bash -c 'echo "username=orabackup1" >> /etc/smbcredentials/orabackup1.cred'
     sudo bash -c 'echo "password=<Your Storage Account Key1>" >> /etc/smbcredentials/orabackup1.cred'
   fi
   ```

3. 資格情報ファイルのアクセス許可を変更します。

   ```bash
   sudo chmod 600 /etc/smbcredentials/orabackup1.cred
   ```

4. そのマウントを /etc/fstab に追加します。

   ```bash
   sudo bash -c 'echo "//orabackup1.file.core.windows.net/orabackup /mnt/orabackup cifs nofail,vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall" >> /etc/fstab'
   ```

5. SMB プロトコルを使用して Azure Files ストレージをマウントするコマンドを実行します。

   ```bash
   sudo mount -t cifs //orabackup1.file.core.windows.net/orabackup /mnt/orabackup -o vers=3.0,credentials=/etc/smbcredentials/orabackup1.cred,dir_mode=0777,file_mode=0777,serverino,cache=none,uid=oracle,gid=oinstall
   ```

   次のようなエラーが表示された場合:

   ```output
   mount: wrong fs type, bad option, bad superblock on //orabackup1.file.core.windows.net/orabackup 
   ```

   その場合、CIFS パッケージが Linux ホストにインストールされていない可能性があります。 
   
   CIS がインストールされているかどうかを確認するには、次のコマンドを実行します。

   ```bash
   sudo rpm -qa|grep cifs-utils
   ```

   コマンドから出力が返されない場合は、次のコマンドを使用して CIFS パッケージをインストールします。

   ```bash 
   sudo yum install cifs-utils
   ```

   ここで、上の mount コマンドを再実行して、Azure Files ストレージをマウントします。

6. 次のコマンドを使用して、ファイル共有が適切にマウントされていることを確認します。

   ```bash
   df -h
   ```

   出力は次のようになります。

   ```output
   $ df -h
   Filesystem                                    Size  Used Avail Use% Mounted on
   devtmpfs                                      3.3G     0  3.3G   0% /dev
   tmpfs                                         3.3G     0  3.3G   0% /dev/shm
   tmpfs                                         3.3G   17M  3.3G   1% /run
   tmpfs                                         3.3G     0  3.3G   0% /sys/fs/cgroup
   /dev/sda2                                      30G  9.1G   19G  34% /
   /dev/sdc1                                      59G  2.7G   53G   5% /u02
   /dev/sda1                                     497M  199M  298M  41% /boot
   /dev/sda15                                    495M  9.7M  486M   2% /boot/efi
   tmpfs                                         671M     0  671M   0% /run/user/54321
   /dev/sdb1                                      14G  2.1G   11G  16% /mnt/resource
   tmpfs                                         671M     0  671M   0% /run/user/54322
   //orabackup1.file.core.windows.net/orabackup   10T     0   10T   0% /mnt/orabackup
   ```

### <a name="back-up-the-database"></a>データベースをバックアップする

このセクションでは、Oracle Recovery Manager (RMAN) を使用してデータベースとアーカイブ ログの完全バックアップを作成し、このバックアップを、以前にマウントされた Azure ファイル共有にバックアップセットとして書き込みます。 

1. Azure Files マウント ポイントにバックアップするように RMAN を構成します。

    ```bash
    $ rman target /
    RMAN> configure snapshot controlfile name to '/mnt/orabkup/snapcf_ev.f';
    RMAN> configure channel 1 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s';
    RMAN> configure channel 2 device type disk format '/mnt/orabkup/%d/Full_%d_%U_%T_%s'; 
    ```

2. Azure Standard ファイル共有の最大ファイル サイズは 1 TiB であるため、RMAN バックアップの要素のサイズを 1 TiB に制限します。 (Premium ファイル共有のファイル サイズの上限は 4 TiB であることに留意してください。 詳細については、「[Azure Files のスケーラビリティおよびパフォーマンスのターゲット](../../../storage/files/storage-files-scale-targets.md)」を参照してください。

    ```bash
    RMAN> configure channel device type disk maxpiecesize 1000G;
    ```

3. 構成変更の詳細を確認します。

    ```bash
    RMAN> show all;
    ```

4. ここで、バックアップを実行します。 次のコマンドで、アーカイブ ログファイルを含む完全なデータベース バックアップが、圧縮形式のバックアップセットとして取得されます。   

    ```bash
    RMAN> backup as compressed backupset database plus archivelog;
    ```

これで、Oracle RMAN を使用してデータベースがオンラインでバックアップされました。バックアップは Azure File Storage に配置されています。 この方法は、他の VM からアクセスできる Azure File Storage にバックアップを格納しながら、RMAN の機能を利用するという利点を備えており、データベースを複製する必要がある場合に役立ちます。  
    
データベース バックアップに RMAN と Azure File Storage を使用することには多くの利点がありますが、バックアップと復元の時間はデータベースのサイズに関連しているため、非常に大規模なデータベースでは、これらの操作にかなりの時間がかかることがあります。  Azure Backup のアプリケーション整合性 VM バックアップを使用するもう 1 つ別のバックアップ メカニズムは、スナップショット テクノロジを使用してバックアップを実行します。これには、データベースのサイズに関係なく非常に高速なバックアップを実行できるという利点があります。 Recovery Services コンテナーとの統合により、他の VM や Azure リージョンからアクセスできる Azure クラウド ストレージ内で Oracle Database バックアップを安全に保管できます。 

### <a name="restore-and-recover-the-database"></a>データベースを復元して復旧する

1.  Oracle インスタンスをシャットダウンします。

    ```bash
    sqlplus / as sysdba
    SQL> shutdown abort
    ORACLE instance shut down.
    ```

2.  データファイルとバックアップを削除します。

    ```bash
    cd /u02/oradata/TEST
    rm -f *.dbf
    ```

3. 次のコマンドでは、RMAN を使用して、不足しているデータファイルを復元し、データベースを復旧します。

    ```bash
    rman target /
    RMAN> startup mount;
    RMAN> restore database;
    RMAN> recover database;
    RMAN> alter database open;
    ```
    
4. データベースの内容が完全に復旧されたことを確認します。

    ```bash
    RMAN> SELECT * FROM scott.scott_table;
    ```


これで、Azure Linux VM での Oracle Database 19c データベースのバックアップと復旧が完了しました。

## <a name="delete-the-vm"></a>VM の削除

VM が必要なくなったら、次のコマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name rg-oracle
```

## <a name="next-steps"></a>次のステップ

[チュートリアル:高可用性 VM の作成](../../linux/create-cli-complete.md)

[VM デプロイ Azure CLI サンプルを探索する](https://github.com/Azure-Samples/azure-cli-samples/tree/master/virtual-machine)