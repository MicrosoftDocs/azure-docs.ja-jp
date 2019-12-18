---
title: Azure Linux VM での Oracle Golden Gate の実装 | Microsoft Docs
description: Oracle Golden Gate をすばやく起動し、Azure 環境で実行します。
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
ms.openlocfilehash: 31137bba8c9b6b88c6a8b9569c02ae887e73e8d0
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309598"
---
# <a name="implement-oracle-golden-gate-on-an-azure-linux-vm"></a>Azure Linux VM での Oracle Golden Gate の実装 

Azure CLI は、コマンドラインやスクリプトで Azure リソースを作成および管理するために使用します。 このガイドでは、Azure CLI を使用して Azure Marketplace ギャラリー イメージから Oracle 12c データベースをデプロイする方法について説明します。 

このドキュメントでは、Azure VM で Oracle Golden Gate を作成、インストール、および構成する方法を、順を追って説明します。 このチュートリアルでは、1 つのリージョンの可用性セットに 2 つの仮想マシンを設定します。 同じチュートリアルを使用して、1 つの Azure リージョン内の異なる複数の Availability Zones の VM、または 2 つの異なるリージョン内の VM セットアップに、OracleGolden Gate をセットアップすることもできます。

開始する前に、Azure CLI がインストールされていることを確認してください。 詳細については、[Azure CLI インストール ガイド](https://docs.microsoft.com/cli/azure/install-azure-cli)を参照してください。

## <a name="prepare-the-environment"></a>環境の準備

Oracle Golden Gate のインストールを実行するには、同じ可用性セットに 2 つの Azure VM を作成する必要があります。 VM の作成に使用する Marketplace イメージは **Oracle:Oracle-Database-Ee:12.1.0.2:latest** です。

また、Unix vi エディターを使い慣れていて、X11 (X Windows) の基本を理解している必要があります。

環境構成の概要を次に示します。
> 
> |  | **プライマリ サイト** | **レプリケート サイト** |
> | --- | --- | --- |
> | **Oracle リリース** |Oracle 12c リリース 2 – (12.1.0.2) |Oracle 12c リリース 2 – (12.1.0.2)|
> | **マシン名** |myVM1 |myVM2 |
> | **オペレーティング システム** |Oracle Linux 6.x |Oracle Linux 6.x |
> | **Oracle SID** |CDB1 |CDB1 |
> | **レプリケーション スキーマ** |TEST|TEST |
> | **Golden Gate の所有者/レプリケート** |C##GGADMIN |REPUSER |
> | **Golden Gate のプロセス** |EXTORA |REPORA|


### <a name="sign-in-to-azure"></a>Azure へのサインイン 

[az login](/cli/azure/reference-index) コマンドを使用して、Azure サブスクリプションにサインインします。 その後、画面の指示に従います。

```azurecli
az login
```

### <a name="create-a-resource-group"></a>リソース グループの作成

[az group create](/cli/azure/group) コマンドでリソース グループを作成します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 

次の例では、`myResourceGroup` という名前のリソース グループを `westus` の場所に作成します。

```azurecli
az group create --name myResourceGroup --location westus
```

### <a name="create-an-availability-set"></a>可用性セットの作成

次の手順は省略可能ですが、実施することをお勧めします。 詳細については、[Azure 可用性セットのガイド](https://docs.microsoft.com/azure/virtual-machines/windows/infrastructure-availability-sets-guidelines)に関する記事をご覧ください。

```azurecli
az vm availability-set create \
    --resource-group myResourceGroup \
    --name myAvailabilitySet \
    --platform-fault-domain-count 2 \
    --platform-update-domain-count 2
```

### <a name="create-a-virtual-machine"></a>仮想マシンの作成

[az vm create](/cli/azure/vm) コマンドで VM を作成します。 

次の例では、`myVM1` と `myVM2` という名前の 2 つの VM を作成します。 既定のキーの場所にまだ SSH キーが存在しない場合は SSH キーを作成します。 特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。

#### <a name="create-myvm1-primary"></a>myVM1 (プライマリ) を作成します。
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM1 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

VM が作成されると、Azure CLI によって次の例のような情報が表示されます。 (`publicIpAddress` を書き留めておきます。 このアドレスは、VM へのアクセスに使用されます。)

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
  "location": "westus",
  "macAddress": "00-0D-3A-36-2F-56",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.4",
  "publicIpAddress": "13.64.104.241",
  "resourceGroup": "myResourceGroup"
}
```

#### <a name="create-myvm2-replicate"></a>myVM2 (レプリケート) を作成します。
```azurecli
az vm create \
     --resource-group myResourceGroup \
     --name myVM2 \
     --availability-set myAvailabilitySet \
     --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
     --size Standard_DS1_v2  \
     --generate-ssh-keys \
```

作成されたら、同様に `publicIpAddress` を書き留めておきます。

### <a name="open-the-tcp-port-for-connectivity"></a>接続用の TCP ポートを開く

次の手順では、Oracle データベースにリモート アクセスするための外部エンドポイントを構成します。 外部エンドポイントを構成するには、次のコマンドを実行します。

#### <a name="open-the-port-for-myvm1"></a>myVM1 のポートを開きます。

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm1NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

結果は、次の応答のようになります。

```bash
{
  "access": "Allow",
  "description": null,
  "destinationAddressPrefix": "*",
  "destinationPortRange": "1521",
  "direction": "Inbound",
  "etag": "W/\"bd77dcae-e5fd-4bd6-a632-26045b646414\"",
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup/providers/Microsoft.Network/networkSecurityGroups/myVmNSG/securityRules/allow-oracle",
  "name": "allow-oracle",
  "priority": 999,
  "protocol": "Tcp",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sourceAddressPrefix": "*",
  "sourcePortRange": "*"
}
```

#### <a name="open-the-port-for-myvm2"></a>myVM2 のポートを開きます。

```azurecli
az network nsg rule create --resource-group myResourceGroup\
    --nsg-name myVm2NSG --name allow-oracle\
    --protocol tcp --direction inbound --priority 999 \
    --source-address-prefix '*' --source-port-range '*' \
    --destination-address-prefix '*' --destination-port-range 1521 --access allow
```

### <a name="connect-to-the-virtual-machine"></a>仮想マシンへの接続

次のコマンドを使用して、仮想マシンとの SSH セッションを作成します。 IP アドレスを仮想マシンの `publicIpAddress` に置き換えます。

```bash 
ssh <publicIpAddress>
```

### <a name="create-the-database-on-myvm1-primary"></a>myVM1 (プライマリ) でのデータベースの作成

Marketplace イメージには Oracle ソフトウェアが既にインストールされているので、次の手順ではデータベースをインストールします。 

"oracle" スーパーユーザーとしてソフトウェアを実行します。

```bash
sudo su - oracle
```

データベースを作成します。

```bash
$ dbca -silent \
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
出力は次のようになります。

```bash
Copying database files
1% complete
2% complete
8% complete
13% complete
19% complete
27% complete
Creating and starting Oracle instance
29% complete
32% complete
33% complete
34% complete
38% complete
42% complete
43% complete
45% complete
Completing Database Creation
48% complete
51% complete
53% complete
62% complete
70% complete
72% complete
Creating Pluggable Databases
78% complete
100% complete
Look at the log file "/u01/app/oracle/cfgtoollogs/dbca/cdb1/cdb1.log" for more details.
```

変数 ORACLE_SID および ORACLE_HOME を設定します。

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

必要に応じて、ORACLE_HOME と ORACLE_SID を .bashrc ファイルに追加できます。これにより、今後のサインインで使用できるように設定が保存されます。

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Oracle リスナーの起動
```bash
$ lsnrctl start
```

### <a name="create-the-database-on-myvm2-replicate"></a>myVM2 (レプリケート) でのデータベースの作成

```bash
sudo su - oracle
```
データベースを作成します。

```bash
$ dbca -silent \
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
変数 ORACLE_SID および ORACLE_HOME を設定します。

```bash
$ ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1; export ORACLE_HOME
$ ORACLE_SID=cdb1; export ORACLE_SID
$ LD_LIBRARY_PATH=ORACLE_HOME/lib; export LD_LIBRARY_PATH
```

必要に応じて、ORACLE_HOME と ORACLE_SID を .bashrc ファイルに追加できます。これにより、今後のサインインで使用できるように設定が保存されます。

```bash
# add oracle home
export ORACLE_HOME=/u01/app/oracle/product/12.1.0/dbhome_1
# add oracle sid
export ORACLE_SID=cdb1
# add Oracle library path
export LD_LIBRARY_PATH=$ORACLE_HOME/lib
```

### <a name="start-oracle-listener"></a>Oracle リスナーの起動
```bash
$ sudo su - oracle
$ lsnrctl start
```

## <a name="configure-golden-gate"></a>Golden Gate の構成 
Golden Gate を構成するには、このセクションの手順を実行します。

### <a name="enable-archive-log-mode-on-myvm1-primary"></a>myVM1 (プライマリ) でのアーカイブ ログ モードの有効化

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
```
強制ログを有効にし、少なくとも 1 つのログ ファイルが存在するようにします。

```bash
SQL> ALTER DATABASE FORCE LOGGING;
SQL> ALTER SYSTEM SWITCH LOGFILE;
SQL> ALTER SYSTEM set enable_goldengate_replication=true;
SQL> ALTER PLUGGABLE DATABASE PDB1 OPEN;
SQL> ALTER SESSION SET CONTAINER=PDB1;
SQL> ALTER DATABASE ADD SUPPLEMENTAL LOG DATA;
SQL> EXIT;
```

### <a name="download-golden-gate-software"></a>Golden Gate ソフトウェアのダウンロード
Oracle Golden Gate ソフトウェアをダウンロードして準備するには、次の手順を実行します。

1. [Oracle Golden Gate ダウンロード ページ](https://www.oracle.com/technetwork/middleware/goldengate/downloads/index.html)から **fbo_ggs_Linux_x64_shiphome.zip** のファイルをダウンロードします。 ダウンロード タイトル **Oracle GoldenGate 12.x.x.x for Oracle Linux x86-64** の下に、ダウンロードする一連の .zip ファイルがあります。

2. これらの .zip ファイルをクライアント コンピューターにダウンロードしたら、Secure Copy Protocol (SCP) を使用してそのファイルを VM にコピーします。

   ```bash
   $ scp fbo_ggs_Linux_x64_shiphome.zip <publicIpAddress>:<folder>
   ```

3. .zip ファイルを **/opt** フォルダーに移動します。 その後、次のようにファイルの所有者を変更します。

   ```bash
   $ sudo su -
   # mv <folder>/*.zip /opt
   ```

4. ファイルを解凍します (Linux のファイル解凍ユーティリティがまだインストールされていない場合はインストールします)。

   ```bash
   # yum install unzip
   # cd /opt
   # unzip fbo_ggs_Linux_x64_shiphome.zip
   ```

5. アクセス許可を変更します。

   ```bash
   # chown -R oracle:oinstall /opt/fbo_ggs_Linux_x64_shiphome
   ```

### <a name="prepare-the-client-and-vm-to-run-x11-for-windows-clients-only"></a>X11 を実行するクライアントと VM の準備 (Windows クライアントの場合のみ)
これは省略可能な手順です。 Linux クライアントを使用している場合、または X11 を既にセットアップしている場合はこの手順をスキップできます。

1. PuTTY と Xming を Windows コンピューターにダウンロードします。

   * [PuTTY のダウンロード](https://www.putty.org/)
   * [Xming のダウンロード](https://xming.en.softonic.com/)

2. PuTTY をインストールしたら、PuTTY フォルダー (C:\Program Files\PuTTY など) にある puttygen.exe (PuTTY Key Generator) を実行します。

3. PuTTY Key Generator で、次の手順を実行します。

   - キーを生成するために、 **[Generate]\(生成\)** ボタンを選択します。
   - キーの内容をコピーします (**Ctrl + C キー**)。
   - **[Save private key]\(秘密キーの保存\)** ボタンを選択します。
   - 表示される警告を無視し、 **[OK]** を選択します。

   ![PuTTY Key Generator ページのスクリーンショット](./media/oracle-golden-gate/puttykeygen.png)

4. VM で、次のコマンドを実行します。

   ```bash
   # sudo su - oracle
   $ mkdir .ssh (if not already created)
   $ cd .ssh
   ```

5. **authorized_keys** という名前のファイルを作成します。 このファイルにキーの内容を貼り付けて、ファイルを保存します。

   > [!NOTE]
   > キーには、`ssh-rsa` という文字列を含める必要があります。 また、キーの内容は 1 行のテキストである必要があります。
   >  

6. PuTTY を起動します。 **[Category]\(カテゴリ\)** ウィンドウで、 **[Connection]\(接続\)**  >  **[SSH]**  >  **[Auth]\(認証\)** の順に選択します。 **[Private key file for authentication]\(認証のための秘密キー ファイル\)** ボックスに、先ほど生成したキーを参照します。

   ![秘密キーを設定するページのスクリーンショット](./media/oracle-golden-gate/setprivatekey.png)

7. **[Category]\(カテゴリ\)** ウィンドウで、 **[Connection]\(接続\)**  >  **[SSH]**  >  **[X11]** の順に選択します。 **[Enable X11 forwarding]\(X11 フォワーディングを有効にする\)** チェック ボックスをオンにします。

   ![X11 を有効にするページのスクリーンショット](./media/oracle-golden-gate/enablex11.png)

8. **[Category]\(カテゴリ\)** ウィンドウで、 **[Session]\(セッション\)** に移動します。 ホスト情報を入力し、 **[Open]\(開く\)** を選択します。

   ![セッション ページのスクリーンショット](./media/oracle-golden-gate/puttysession.png)

### <a name="install-golden-gate-software"></a>Golden Gate ソフトウェアのインストール

Oracle Golden Gate をインストールするには、次の手順を実行します。

1. oracle としてサインインします。 (パスワードの入力を要求されることなくサインインできるはずです)。インストールを開始する前に、Xming が実行されていることを確認してください。
 
   ```bash
   $ cd /opt/fbo_ggs_Linux_x64_shiphome/Disk1
   $ ./runInstaller
   ```
2. [Oracle GoldenGate for Oracle Database 12c] を選択します。 **[Next]\(次へ\)** をクリックして続行します。

   ![インストーラーの [Select Installation]\(インストールの選択\) ページのスクリーンショット](./media/oracle-golden-gate/golden_gate_install_01.png)

3. ソフトウェアの場所を変更します。 **[Start Manager]\(Manager の起動\)** チェックボックスをオンにし、データベースの場所を入力します。 **[次へ]** をクリックして続行します。

   ![[Select Installation]\(インストールの選択\) ページのスクリーンショット](./media/oracle-golden-gate/golden_gate_install_02.png)

4. インベントリ ディレクトリを変更し、 **[Next]\(次へ\)** を選択して続行します。

   ![[Select Installation]\(インストールの選択\) ページのスクリーンショット](./media/oracle-golden-gate/golden_gate_install_03.png)

5. **[Summary]\(概要\)** 画面で、 **[Install]\(インストール\)** を選択して続行します。

   ![インストーラーの [Select Installation]\(インストールの選択\) ページのスクリーンショット](./media/oracle-golden-gate/golden_gate_install_04.png)

6. スクリプトを "ルート" として実行するよう求められる場合があります。 その場合は別のセッションを開き (VM には ssh、root には sudo)、スクリプトを実行します。 **[OK]** を選択して続行します。

   ![[Select Installation]\(インストールの選択\) ページのスクリーンショット](./media/oracle-golden-gate/golden_gate_install_05.png)

7. インストールが完了したら、 **[Close]\(閉じる\)** を選択してプロセスを完了します。

   ![[Select Installation]\(インストールの選択\) ページのスクリーンショット](./media/oracle-golden-gate/golden_gate_install_06.png)

### <a name="set-up-service-on-myvm1-primary"></a>myVM1 (プライマリ) でのサービスのセットアップ

1. tnsnames.ora ファイルを作成または更新します。

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. Golden Gate の所有者とユーザー アカウントを作成します。

   > [!NOTE]
   > 所有者のアカウントには、C## プレフィックスが必要です。
   >

    ```bash
    $ sqlplus / as sysdba
    SQL> CREATE USER C##GGADMIN identified by ggadmin;
    SQL> EXEC dbms_goldengate_auth.grant_admin_privilege('C##GGADMIN',container=>'ALL');
    SQL> GRANT DBA to C##GGADMIN container=all;
    SQL> connect C##GGADMIN/ggadmin
    SQL> ALTER SESSION SET CONTAINER=PDB1;
    SQL> EXIT;
    ```

3. Golden Gate のテスト ユーザー アカウントを作成します。

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> @demo_ora_insert
   SQL> EXIT;
   ```

4. Extract パラメーター ファイルを構成します。

   Golden Gate コマンドライン インターフェイス (ggsci) を起動します。

   ```bash
   $ sudo su - oracle
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> DBLOGIN USERID test@pdb1, PASSWORD test
   Successfully logged into database  pdb1
   GGSCI>  ADD SCHEMATRANDATA pdb1.test
   2017-05-23 15:44:25  INFO    OGG-01788  SCHEMATRANDATA has been added on schema test.
   2017-05-23 15:44:25  INFO    OGG-01976  SCHEMATRANDATA for scheduling columns has been added on schema test.

   GGSCI> EDIT PARAMS EXTORA
   ```
5. vi コマンドを使用して、Extract パラメーター ファイルに次を追加します。 Esc キーを押すか、":wq!" で ファイルを保存します。 

   ```bash
   EXTRACT EXTORA
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTRAIL ./dirdat/rt  
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT 
   LOGALLSUPCOLS
   UPDATERECORDFORMAT COMPACT
   TABLE pdb1.test.TCUSTMER;
   TABLE pdb1.test.TCUSTORD;
   ```
6. Extract (統合された Extract) を登録します。

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci

   GGSCI> dblogin userid C##GGADMIN, password ggadmin
   Successfully logged into database CDB$ROOT.

   GGSCI> REGISTER EXTRACT EXTORA DATABASE CONTAINER(pdb1)

   2017-05-23 15:58:34  INFO    OGG-02003  Extract EXTORA successfully registered with database at SCN 1821260.

   GGSCI> exit
   ```
7. Extract チェックポイントを設定し、リアルタイムの Extract を開始します。

   ```bash
   $ ./ggsci
   GGSCI>  ADD EXTRACT EXTORA, INTEGRATED TRANLOG, BEGIN NOW
   EXTRACT (Integrated) added.

   GGSCI>  ADD RMTTRAIL ./dirdat/rt, EXTRACT EXTORA, MEGABYTES 10
   RMTTRAIL added.

   GGSCI>  START EXTRACT EXTORA

   Sending START request to MANAGER ...
   EXTRACT EXTORA starting

   GGSCI > info all

   Program     Status      Group       Lag at Chkpt  Time Since Chkpt

   MANAGER     RUNNING
   EXTRACT     RUNNING     EXTORA      00:00:11      00:00:04
   ```
   この手順では起点の SCN を検索します。これは後ほど別のセクションで使用します。

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> SELECT current_scn from v$database;
   CURRENT_SCN
   -----------
      1857887
   SQL> EXIT;
   ```

   ```bash
   $ ./ggsci
   GGSCI> EDIT PARAMS INITEXT
   ```

   ```bash
   EXTRACT INITEXT
   USERID C##GGADMIN, PASSWORD ggadmin
   RMTHOST 10.0.0.5, MGRPORT 7809
   RMTTASK REPLICAT, GROUP INITREP
   TABLE pdb1.test.*, SQLPREDICATE 'AS OF SCN 1857887'; 
   ```

   ```bash
   GGSCI> ADD EXTRACT INITEXT, SOURCEISTABLE
   ```

### <a name="set-up-service-on-myvm2-replicate"></a>myVM2 (レプリケート) でのサービスのセットアップ


1. tnsnames.ora ファイルを作成または更新します。

   ```bash
   $ cd $ORACLE_HOME/network/admin
   $ vi tnsnames.ora

   cdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=cdb1)
      )
    )

   pdb1=
    (DESCRIPTION=
      (ADDRESS=
        (PROTOCOL=TCP)
        (HOST=localhost)
        (PORT=1521)
      )
      (CONNECT_DATA=
        (SERVER=dedicated)
        (SERVICE_NAME=pdb1)
      )
    )
   ```

2. レプリケート アカウントを作成します。

   ```bash
   $ sqlplus / as sysdba
   SQL> alter session set container = pdb1;
   SQL> create user repuser identified by rep_pass container=current;
   SQL> grant dba to repuser;
   SQL> exec dbms_goldengate_auth.grant_admin_privilege('REPUSER',container=>'PDB1');
   SQL> connect repuser/rep_pass@pdb1 
   SQL> EXIT;
   ```

3. Golden Gate のテスト ユーザー アカウントを作成します。

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ sqlplus system/OraPasswd1@pdb1
   SQL> CREATE USER test identified by test DEFAULT TABLESPACE USERS TEMPORARY TABLESPACE TEMP;
   SQL> GRANT connect, resource, dba TO test;
   SQL> ALTER USER test QUOTA 100M on USERS;
   SQL> connect test/test@pdb1
   SQL> @demo_ora_create
   SQL> EXIT;
   ```

4. 変更をレプリケートする REPLICAT パラメーター ファイル: 

   ```bash
   $ cd /u01/app/oracle/product/12.1.0/oggcore_1
   $ ./ggsci
   GGSCI> EDIT PARAMS REPORA  
   ```
   REPORA パラメーター ファイルの内容:

   ```bash
   REPLICAT REPORA
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/repora.dsc, PURGE, MEGABYTES 100
   DDL INCLUDE MAPPED
   DDLOPTIONS REPORT
   DBOPTIONS INTEGRATEDPARAMS(parallelism 6)
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;
   ```

5. レプリケート チェックポイントを設定します。

   ```bash
   GGSCI> ADD REPLICAT REPORA, INTEGRATED, EXTTRAIL ./dirdat/rt
   GGSCI> EDIT PARAMS INITREP

   ```

   ```bash
   REPLICAT INITREP
   ASSUMETARGETDEFS
   DISCARDFILE ./dirrpt/tcustmer.dsc, APPEND
   USERID repuser@pdb1, PASSWORD rep_pass
   MAP pdb1.test.*, TARGET pdb1.test.*;   
   ```

   ```bash
   GGSCI> ADD REPLICAT INITREP, SPECIALRUN
   ```

### <a name="set-up-the-replication-myvm1-and-myvm2"></a>レプリケーション (myVM1 と myVM2) のセットアップ

#### <a name="1-set-up-the-replication-on-myvm2-replicate"></a>1.myVM2 (レプリケート) でのレプリケーションのセットアップ

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  GGSCI> EDIT PARAMS MGR
  ```
次のようにファイルを更新します。

  ```bash
  PORT 7809
  ACCESSRULE, PROG *, IPADDR *, ALLOW
  ```
続いて、Manager サービスを再起動します。

  ```bash
  GGSCI> STOP MGR
  GGSCI> START MGR
  GGSCI> EXIT
  ```

#### <a name="2-set-up-the-replication-on-myvm1-primary"></a>2.myVM1 (プライマリ) でのレプリケーションのセットアップ

初期読み込みとエラーのチェックを開始します。

```bash
$ cd /u01/app/oracle/product/12.1.0/oggcore_1
$ ./ggsci
GGSCI> START EXTRACT INITEXT
GGSCI> VIEW REPORT INITEXT
```
#### <a name="3-set-up-the-replication-on-myvm2-replicate"></a>3.myVM2 (レプリケート) でのレプリケーションのセットアップ

前に取得した番号で、SCN 番号を変更します。

  ```bash
  $ cd /u01/app/oracle/product/12.1.0/oggcore_1
  $ ./ggsci
  START REPLICAT REPORA, AFTERCSN 1857887
  ```
レプリケーションが開始されたら、テスト テーブルに新しいレコードを挿入してテストすることができます。


### <a name="view-job-status-and-troubleshooting"></a>ジョブの状態とトラブルシューティングの表示

#### <a name="view-reports"></a>レポートを表示する
myVM1 でレポートを表示するには、次のコマンドを実行します。

  ```bash
  GGSCI> VIEW REPORT EXTORA 
  ```
 
myVM2 でレポートを表示するには、次のコマンドを実行します。

  ```bash
  GGSCI> VIEW REPORT REPORA
  ```

#### <a name="view-status-and-history"></a>状態と履歴の表示
myVM1 で状態と履歴を表示するには、次のコマンドを実行します。

  ```bash
  GGSCI> dblogin userid c##ggadmin, password ggadmin 
  GGSCI> INFO EXTRACT EXTORA, DETAIL
  ```

myVM2 で状態と履歴を表示するには、次のコマンドを実行します。

  ```bash
  GGSCI> dblogin userid repuser@pdb1 password rep_pass 
  GGSCI> INFO REP REPORA, DETAIL
  ```
これで、Oracle linux 上の Golden Gate のインストールと構成が完了しました。


## <a name="delete-the-virtual-machine"></a>仮想マシンの削除

必要がなくなったら、次のコマンドを使用して、リソース グループ、VM、およびすべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

[可用性が高い仮想マシンの作成のチュートリアル](../../linux/create-cli-complete.md)

[VM デプロイ CLI サンプルを探索する](../../linux/cli-samples.md)
