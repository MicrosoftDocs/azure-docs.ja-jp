---
title: Azure VM での Oracle データベースの作成 | Microsoft Docs
description: Azure 環境で Oracle Database 12c データベースをすばやく起動して実行します。
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: quickstart
ms.date: 10/05/2020
ms.author: kegorman
ms.openlocfilehash: 8964248bb23b2b615c7e73e26d730fbd79b4e9e7
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/05/2021
ms.locfileid: "102184459"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Azure VM での Oracle データベースの作成

このガイドでは、Oracle 19c データベースを作成するために、Azure CLI を使用して [Oracle Marketplace ギャラリー イメージ](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview)から Azure 仮想マシンをデプロイする方法について説明します。 サーバーをデプロイすると、Oracle データベースを構成するために SSH 経由で接続します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループを作成する

[az group create](/cli/azure/group) コマンドを使用して、リソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、*rg-oracle* という名前のリソース グループを *eastus* の場所に作成します。

```azurecli-interactive
az group create --name rg-oracle --location eastus
```

## <a name="create-virtual-machine"></a>仮想マシンの作成

仮想マシン (VM) を作成するには、[az vm create](/cli/azure/vm) コマンドを使用します。 

次の例では、`vmoracle19c` という名前の VM を作成します。 また、既定のキーの場所にまだ SSH キーが存在しない場合は SSH キーも作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。  

```azurecli-interactive 
az vm create ^
    --resource-group rg-oracle ^
    --name vmoracle19c ^
    --image Oracle:oracle-database-19-3:oracle-database-19-0904:latest ^
    --size Standard_DS2_v2 ^
    --admin-username azureuser ^
    --generate-ssh-keys ^
    --public-ip-address-allocation static ^
    --public-ip-address-dns-name vmoracle19c

```

VM を作成すると、Azure CLI によって次の例のような情報が表示されます。 `publicIpAddress` の値をメモします。 このアドレスは、VM へのアクセスに使用します。

```output
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/rg-oracle/providers/Microsoft.Compute/virtualMachines/vmoracle19c",
  "location": "eastus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "rg-oracle"
}
```
## <a name="create-and-attach-a-new-disk-for-oracle-datafiles-and-fra"></a>Oracle のデータファイルと FRA に使用する新しいディスクを作成してアタッチする

```bash
az vm disk attach --name oradata01 --new --resource-group rg-oracle --size-gb 128 --sku StandardSSD_LRS --vm-name vmoracle19c
```

## <a name="open-ports-for-connectivity"></a>接続のポートを開く
このタスクでは、使用するデータベース リスナーと EM Express 用に、VM を保護する Azure Network Security Group を設定することによっていくつかの外部エンドポイントを構成する必要があります。 

1. リモートで Oracle データベースにアクセスするために使用するエンドポイントを開くには、次のように、ネットワーク セキュリティ グループ規則を作成します。
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle ^
       --protocol tcp ^
       --priority 1001 ^
       --destination-port-range 1521
   ```
2. リモートで Oracle EM Express にアクセスするために使用するエンドポイントを開くには、次のように az network nsg rule create を使用して、ネットワーク セキュリティ グループ規則を作成します。
   ```bash
   az network nsg rule create ^
       --resource-group rg-oracle ^
       --nsg-name vmoracle19cNSG ^
       --name allow-oracle-EM ^
       --protocol tcp ^
       --priority 1002 ^
       --destination-port-range 5502
   ```
3. 必要に応じて、次のように az network public-ip show を使用して、VM のパブリック IP アドレスを再度取得します。

   ```bash
   az network public-ip show ^
       --resource-group rg-oracle ^
       --name vmoracle19cPublicIP ^
       --query [ipAddress] ^
       --output tsv
   ```

## <a name="prepare-the-vm-environment"></a>VM 環境を準備する

1. VM に接続します

   VM との SSH セッションを作成するには、次のコマンドを使用します。 IP アドレスを、VM の `publicIpAddress` 値に置き換えます。

   ```bash
   ssh azureuser@<publicIpAddress>
   ```

2. root ユーザーに切り替えます

   ```bash
   sudo su -
   ```

3. 最後に作成されたディスク デバイスをチェックします。Oracle データファイルの保持用に、このディスク デバイスをフォーマットすることになります。

   ```bash
   ls -alt /dev/sd*|head -1
   ```

   出力は次のようになります。
   ```output
   brw-rw----. 1 root disk 8, 16 Dec  8 22:57 /dev/sdc
   ```

4. デバイスをフォーマットします。 
   そのデバイスに対し、root ユーザーとして parted を実行します 
   
   まず、ディスク ラベルを作成します。
   ```bash
   parted /dev/sdc mklabel gpt
   ```
   次に、ディスク全体にわたるプライマリ パーティションを作成します。
   ```bash
   parted -a optimal /dev/sdc mkpart primary 0GB 64GB   
   ```
   最後に、そのメタデータを出力してデバイスの詳細をチェックします。
   ```bash
   parted /dev/sdc print
   ```
   出力は次のようになります。
   ```bash
   # parted /dev/sdc print
   Model: Msft Virtual Disk (scsi)
   Disk /dev/sdc: 68.7GB
   Sector size (logical/physical): 512B/4096B
   Partition Table: gpt
   Disk Flags:
   Number  Start   End     Size    File system  Name     Flags
    1      1049kB  64.0GB  64.0GB  ext4         primary
   ```

5. デバイス パーティションにファイル システムを作成します

   ```bash
   mkfs -t ext4 /dev/sdc1
   ```

6. マウント ポイントの作成
   ```bash
   mkdir /u02
   ```

7. ディスクのマウント

   ```bash
   mount /dev/sdc1 /u02
   ```

8. マウント ポイントのアクセス許可を変更します

   ```bash
   chmod 777 /u02
   ```

9. そのマウントを /etc/fstab ファイルに追加します。 

   ```bash
   echo "/dev/sdc1               /u02                    ext4    defaults        0 0" >> /etc/fstab
   ```
   
10. パブリック IP とホスト名で ***/etc/hosts*** ファイルを更新します。

    ***<Public IP> と <VMname>*** は、実際の値に合わせて変更してください。
  
    ```bash
    echo "<Public IP> <VMname>.eastus.cloudapp.azure.com <VMname>" >> /etc/hosts
    ```
11. hostname ファイルを更新します
    
    次のコマンドを使用して、VM のドメイン名を **/etc/hostname** ファイルに追加します。 ここでは、リソース グループと VM が **eastus** リージョンに作成されていることを想定しています。
    
    ```bash
    sed -i 's/$/\.eastus\.cloudapp\.azure\.com &/' /etc/hostname
    ```

12. ファイアウォールのポートを開きます
    
    Marketplace イメージでは SELinux が既定で有効になっているため、データベースのリッスン ポート 1521 宛てのトラフィックと Enterprise Manager Express のポート 5502 宛てのトラフィックに対してファイアウォールを開放する必要があります。 root ユーザーとして、次のコマンドを実行します。

    ```bash
    firewall-cmd --zone=public --add-port=1521/tcp --permanent
    firewall-cmd --zone=public --add-port=5502/tcp --permanent
    firewall-cmd --reload
    ```
   

## <a name="create-the-database"></a>データベースの作成

Oracle ソフトウェアは、既に Marketplace イメージにインストールされています。 次のようにサンプル データベースを作成します。 

1.  **oracle** ユーザーに切り替えます。

    ```bash
    sudo su - oracle
    ```
2. データベースのリスナーを開始する

   ```bash
   lsnrctl start
   ```
   次のように出力されます。
  
   ```output
   LSNRCTL for Linux: Version 19.0.0.0.0 - Production on 20-OCT-2020 01:58:18

   Copyright (c) 1991, 2019, Oracle.  All rights reserved.

   Starting /u01/app/oracle/product/19.0.0/dbhome_1/bin/tnslsnr: please wait...

   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Log messages written to /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening on: (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))

   Connecting to (ADDRESS=(PROTOCOL=tcp)(HOST=)(PORT=1521))
   STATUS of the LISTENER
   ------------------------
   Alias                     LISTENER
   Version                   TNSLSNR for Linux: Version 19.0.0.0.0 - Production
   Start Date                20-OCT-2020 01:58:18
   Uptime                    0 days 0 hr. 0 min. 0 sec
   Trace Level               off
   Security                  ON: Local OS Authentication
   SNMP                      OFF
   Listener Log File         /u01/app/oracle/diag/tnslsnr/vmoracle19c/listener/alert/log.xml
   Listening Endpoints Summary...
     (DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=vmoracle19c.eastus.cloudapp.azure.com)(PORT=1521)))
   The listener supports no services
   The command completed successfully
   ```
3. Oracle データ ファイル用のデータ ディレクトリを作成します。

   ```bash
   mkdir /u02/oradata
   ```
    

3.  Database Creation Assistant を実行します。

    ```bash
    dbca -silent \
       -createDatabase \
       -templateName General_Purpose.dbc \
       -gdbname test \
       -sid test \
       -responseFile NO_VALUE \
       -characterSet AL32UTF8 \
       -sysPassword OraPasswd1 \
       -systemPassword OraPasswd1 \
       -createAsContainerDatabase false \
       -databaseType MULTIPURPOSE \
       -automaticMemoryManagement false \
       -storageType FS \
       -datafileDestination "/u02/oradata/" \
       -ignorePreReqs
    ```

    データベースの作成には数分かかります。

    次のような出力が表示されます。

    ```output
        Prepare for db operation
       10% complete
       Copying database files
       40% complete
       Creating and starting Oracle instance
       42% complete
       46% complete
       50% complete
       54% complete
       60% complete
       Completing Database Creation
       66% complete
       69% complete
       70% complete
       Executing Post Configuration Actions
       100% complete
       Database creation complete. For details check the logfiles at: /u01/app/oracle/cfgtoollogs/dbca/test.
       Database Information:
       Global Database Name:test
       System Identifier(SID):test
       Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/test/test.log" for further details.
    ```

4. Oracle 変数の設定

    接続する前に、環境変数 *ORACLE_SID* を設定する必要があります。

    ```bash
        export ORACLE_SID=test
    ```

    また、次のコマンドを使用して、今後のサインインのために `oracle` ユーザーの `.bashrc` ファイルに ORACLE_SID 変数を追加する必要があります。

    ```bash
    echo "export ORACLE_SID=test" >> ~oracle/.bashrc
    ```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express への接続

データベースを操作するために使用できる GUI 管理ツールのために、Oracle EM Express を設定します。 Oracle EM Express に接続するには、まず Oracle でポートを設定する必要があります。 

1. sqlplus を使用してデータベースに接続します。

    ```bash
    sqlplus sys as sysdba
    ```

2. 接続したら、EM Express 用にポート 5502 を設定します。

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3.  ブラウザーから EM Express に接続します。 ブラウザーが EM Express と互換性があることを確認してください (Flash のインストールが必要です)。 

    ```https
    https://<VM ip address or hostname>:5502/em
    ```

    **SYS** アカウントを使用してログインし、 **[as sysdba]\(sysdba として\)** チェック ボックスにオンにできます。 インストール時に設定したパスワード **OraPasswd1** を使用します。 

    ![Oracle OEM Express ログイン ページのスクリーンショット](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="automate-database-startup-and-shutdown"></a>データベースのスタートアップとシャットダウンの自動化

VM を再起動したとき、既定では、Oracle データベースは自動的には開始しません。 自動的に開始するように Oracle データベースを設定するには、まず root としてサインインします。 次に、いくつかのシステム ファイルを作成および更新します。

1. root としてサインオンします。

    ```bash
    sudo su -
    ```

2.  次のコマンドを実行して、`/etc/oratab` ファイルの自動起動フラグを `N` から `Y` に変更します。

    ```bash
    sed -i 's/:N/:Y/' /etc/oratab
    ```

3.  `/etc/init.d/dbora` というファイルを作成し、次の内容を貼り付けます。

    ```bash
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/19.0.0/dbhome_1
    ORA_OWNER=oracle

    case "$1" in
    'start')
        # Start the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        # Remove "&" if you don't want startup as a background process.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbstart $ORA_HOME" &
        touch /var/lock/subsys/dbora
        ;;

    'stop')
        # Stop the Oracle databases:
        # The following command assumes that the Oracle sign-in
        # will not prompt the user for any values.
        su - $ORA_OWNER -c "$ORA_HOME/bin/dbshut $ORA_HOME" &
        rm -f /var/lock/subsys/dbora
        ;;
    esac
    ```

4.  次のように *chmod* を使用してファイルのアクセス許可を変更します。

    ```bash
    chgrp dba /etc/init.d/dbora
    chmod 750 /etc/init.d/dbora
    ```

5.  次のようにスタートアップとシャットダウンのシンボリック リンクを作成します。

    ```bash
    ln -s /etc/init.d/dbora /etc/rc.d/rc0.d/K01dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc3.d/S99dbora
    ln -s /etc/init.d/dbora /etc/rc.d/rc5.d/S99dbora
    ```

6.  変更をテストするには、VM を再起動します。

    ```bash
    reboot
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

これで、Azure での初めての Oracle データベース探索が終了しました。VM は必要なくなりましたので、[az group delete](/cli/azure/group) コマンドを使用して、リソース グループ、VM、関連するすべてのリソースを削除することができます。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

[Oracle のバックアップ戦略](./oracle-database-backup-strategies.md)に関するページで、Azure 内のデータベースを保護する方法について学習します。

他の [Azure 上での Oracle ソリューション](./oracle-overview.md)について学習します。 

[Oracle Automated Storage Management のインストールと構成](configure-oracle-asm.md)チュートリアルに挑戦します。
