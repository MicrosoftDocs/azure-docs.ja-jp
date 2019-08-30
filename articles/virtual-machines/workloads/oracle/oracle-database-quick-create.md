---
title: Azure VM での Oracle データベースの作成 | Microsoft Docs
description: Azure 環境で Oracle Database 12c データベースをすばやく起動して実行します。
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 6d43fa2621aa95bdcf18d5c033d1347e13dc3f67
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70101486"
---
# <a name="create-an-oracle-database-in-an-azure-vm"></a>Azure VM での Oracle データベースの作成

このガイドでは、Oracle 12c データベースを作成するために、Azure CLI を使用して [Oracle Marketplace ギャラリー イメージ](https://azuremarketplace.microsoft.com/marketplace/apps/Oracle.OracleDatabase12102EnterpriseEdition?tab=Overview)から Azure 仮想マシンをデプロイする方法について説明します。 サーバーをデプロイすると、Oracle データベースを構成するために SSH 経由で接続します。 

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
## <a name="create-virtual-machine"></a>仮想マシンの作成

仮想マシン (VM) を作成するには、[az vm create](/cli/azure/vm) コマンドを使用します。 

次の例では、`myVM` という名前の VM を作成します。 また、既定のキーの場所にまだ SSH キーが存在しない場合は SSH キーも作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。  

```azurecli-interactive 
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --admin-username azureuser \
    --generate-ssh-keys
```

VM を作成すると、Azure CLI によって次の例のような情報が表示されます。 `publicIpAddress` の値をメモします。 このアドレスは、VM へのアクセスに使用します。

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/{snip}/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

## <a name="connect-to-the-vm"></a>VM に接続します

VM との SSH セッションを作成するには、次のコマンドを使用します。 IP アドレスを、VM の `publicIpAddress` 値に置き換えます。

```bash 
ssh azureuser@<publicIpAddress>
```

## <a name="create-the-database"></a>データベースの作成

Oracle ソフトウェアは、既に Marketplace イメージにインストールされています。 次のようにサンプル データベースを作成します。 

1.  *oracle* スーパーユーザーに切り替え、ログ用にリスナーを初期化します。

    ```bash
    $ sudo su - oracle
    $ lsnrctl start
    ```

    次のように出力されます。

    ```bash
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

2.  データベースを作成します。

    ```bash
    dbca -silent \
           -createDatabase \
           -templateName General_Purpose.dbc \
           -gdbname cdb1 \
           -sid cdb1 \
           -responseFile NO_VALUE \
           -characterSet AL32UTF8 \
           -sysPassword OraPasswd1 \
           -systemPassword OraPasswd1 \
           -createAsContainerDatabase true \
           -numberOfPDBs 1 \
           -pdbName pdb1 \
           -pdbAdminPassword OraPasswd1 \
           -databaseType MULTIPURPOSE \
           -automaticMemoryManagement false \
           -storageType FS \
           -ignorePreReqs
    ```

    データベースの作成には数分かかります。

3. Oracle 変数の設定

接続する前に、次の 2 つの環境変数を設定する必要があります:*ORACLE_HOME* と *ORACLE_SID*。

```bash
ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
ORACLE_SID=cdb1; export ORACLE_SID
```
また、.bashrc ファイルに ORACLE_HOME と ORACLE_SID 変数を追加することもできます。 これにより、これらの環境変数が将来のサインインのために保存されます。任意のエディターを使用して、次のステートメントが `~/.bashrc` ファイルに追加されたことを確認してください。

```bash
# Add ORACLE_HOME. 
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1 
# Add ORACLE_SID. 
export ORACLE_SID=cdb1 
```

## <a name="oracle-em-express-connectivity"></a>Oracle EM Express への接続

データベースを操作するために使用できる GUI 管理ツールのために、Oracle EM Express を設定します。 Oracle EM Express に接続するには、まず Oracle でポートを設定する必要があります。 

1. sqlplus を使用してデータベースに接続します。

    ```bash
    sqlplus / as sysdba
    ```

2. 接続したら、EM Express 用にポート 5502 を設定します。

    ```bash
    exec DBMS_XDB_CONFIG.SETHTTPSPORT(5502);
    ```

3. コンテナー PDB1 をまだ開いていない場合は開きますが、先に状態を確認します。

    ```bash
    select con_id, name, open_mode from v$pdbs;
    ```

    次のように出力されます。

    ```bash
      CON_ID NAME                           OPEN_MODE 
      ----------- ------------------------- ---------- 
      2           PDB$SEED                  READ ONLY 
      3           PDB1                      MOUNT
    ```

4. `PDB1` の OPEN_MODE が READ WRITE でない場合は、次のコマンドを実行して PDB1 を開きます。

   ```bash
    alter session set container=pdb1;
    alter database open;
   ```

「`quit`」と入力して sqlplus セッションを終了し、「`exit`」と入力して Oracle ユーザーからログアウトする必要があります。

## <a name="automate-database-startup-and-shutdown"></a>データベースのスタートアップとシャットダウンの自動化

VM を再起動したとき、既定では、Oracle データベースは自動的には開始しません。 自動的に開始するように Oracle データベースを設定するには、まず root としてサインインします。 次に、いくつかのシステム ファイルを作成および更新します。

1. root としてサインオンします。
    ```bash
    sudo su -
    ```

2.  任意のエディターを使用してファイル `/etc/oratab` を編集し、次のようにして既定値の `N` を `Y` に変更します。

    ```bash
    cdb1:/u01/app/oracle/product/12.1.0/dbhome_1:Y
    ```

3.  `/etc/init.d/dbora` というファイルを作成し、次の内容を貼り付けます。

    ```
    #!/bin/sh
    # chkconfig: 345 99 10
    # Description: Oracle auto start-stop script.
    #
    # Set ORA_HOME to be equivalent to $ORACLE_HOME.
    ORA_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
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

## <a name="open-ports-for-connectivity"></a>接続のポートを開く

最後のタスクとして、いくつかの外部エンドポイントを構成します。 VM を保護する Azure ネットワーク セキュリティ グループを設定するには、まず VM で SSH セッションを終了します (前の手順で再起動したときに SSH からログアウトしているはずです)。 

1.  リモートで Oracle データベースにアクセスするために使用するエンドポイントを開くには、次のように [az network nsg rule create](/cli/azure/network/nsg/rule) を使用して、ネットワーク セキュリティ グループ規則を作成します。 

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup\
        --nsg-name myVmNSG \
        --name allow-oracle \
        --protocol tcp \
        --priority 1001 \
        --destination-port-range 1521
    ```

2.  リモートで Oracle EM Express にアクセスするために使用するエンドポイントを開くには、次のように [az network nsg rule create](/cli/azure/network/nsg/rule) を使用して、ネットワーク セキュリティ グループ規則を作成します。

    ```azurecli-interactive
    az network nsg rule create \
        --resource-group myResourceGroup \
        --nsg-name myVmNSG \
        --name allow-oracle-EM \
        --protocol tcp \
        --priority 1002 \
        --destination-port-range 5502
    ```

3. 必要に応じて、次のように [az network public-ip show](/cli/azure/network/public-ip) を使用して、VM のパブリック IP アドレスを再度取得します。

    ```azurecli-interactive
    az network public-ip show \
        --resource-group myResourceGroup \
        --name myVMPublicIP \
        --query [ipAddress] \
        --output tsv
    ```

4.  ブラウザーから EM Express に接続します。 ブラウザーが EM Express と互換性があることを確認してください (Flash のインストールが必要です)。 

    ```
    https://<VM ip address or hostname>:5502/em
    ```

**SYS** アカウントを使用してログインし、 **[as sysdba]\(sysdba として\)** チェック ボックスにオンにできます。 インストール時に設定したパスワード **OraPasswd1** を使用します。 

![Oracle OEM Express ログイン ページのスクリーンショット](./media/oracle-quick-start/oracle_oem_express_login.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

これで、Azure での初めての Oracle データベース探索が終了しました。VM は必要なくなりましたので、[az group delete](/cli/azure/group) コマンドを使用して、リソース グループ、VM、関連するすべてのリソースを削除することができます。

```azurecli-interactive 
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

他の [Azure 上での Oracle ソリューション](oracle-considerations.md)について学習します。 

[Oracle Automated Storage Management のインストールと構成](configure-oracle-asm.md)チュートリアルに挑戦します。
