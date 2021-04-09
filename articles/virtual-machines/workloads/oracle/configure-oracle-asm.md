---
title: Azure Linux 仮想マシンで Oracle ASM をセットアップする | Microsoft Docs
description: Azure 環境で Oracle ASM をすばやく稼動させます。
author: dbakevlar
ms.service: virtual-machines
ms.subservice: oracle
ms.collection: linux
ms.topic: article
ms.date: 08/02/2018
ms.author: kegorman
ms.openlocfilehash: b180a0f148db5222a288958724ad248acd18b565
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101673855"
---
# <a name="set-up-oracle-asm-on-an-azure-linux-virtual-machine"></a>Azure Linux 仮想マシンで Oracle ASM をセットアップする  

Azure 仮想マシンは、完全に構成可能で柔軟なコンピューティング環境を提供します。 このチュートリアルでは、Azure 仮想マシンの基本的な展開と Oracle Automated Storage Management (Oracle自動ストレージ管理) (ASM) のインストールと構成について扱います。  学習内容は次のとおりです。

> [!div class="checklist"]
> * Oracle データベース VM を作成し、それに接続する
> * Oracle Automated Storage Management (Oracle自動ストレージ管理) をインストールし、構成する
> * Oracle Grid Infrastructure をインストールし、構成する
> * Oracle ASM インストールを初期化する
> * ASM により管理される Oracle DB を作成する


CLI をローカルにインストールして使用する場合、このチュートリアルでは、Azure CLI バージョン 2.0.4 以降を実行していることが要件です。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール]( /cli/azure/install-azure-cli)に関するページを参照してください。 

## <a name="prepare-the-environment"></a>環境の準備

### <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループを作成するには、[az group create](/cli/azure/group) コマンドを使用します。 Azure リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 この例では、*myResourceGroup* という名前のリソース グループが *eastus* リージョンに作成されます。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-vm"></a>VM の作成

Oracle データベース イメージに基づいて仮想マシンを作成した上で、Oracle ASM を使用できるように構成するには、[az vm create](/cli/azure/vm) コマンドを使用します。 

次の例では、myVM という名前の VM が作成されます。サイズは Standard_DS2_v2 で、50 GB のデータ ディスクが 4 つ装着されます。 これらがキーの既定の場所にまだ存在しない場合、SSH キーも作成されます。  特定のキーのセットを使用するには、`--ssh-key-value` オプションを使用します。  

   ```azurecli-interactive
   az vm create --resource-group myResourceGroup \
    --name myVM \
    --image Oracle:Oracle-Database-Ee:12.1.0.2:latest \
    --size Standard_DS2_v2 \
    --generate-ssh-keys \
    --data-disk-sizes-gb 50 50 50 50
   ```

VM を作成すると、Azure CLI によって次の例のような情報が表示されます。 `publicIpAddress` の値をメモします。 このアドレスは、VM へのアクセスに使用します。

   ```output
   {
     "fqdns": "",
     "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVM",
     "location": "eastus",
     "macAddress": "00-0D-3A-36-2F-56",
     "powerState": "VM running",
     "privateIpAddress": "10.0.0.4",
     "publicIpAddress": "13.64.104.241",
     "resourceGroup": "myResourceGroup"
   }
   ```

### <a name="connect-to-the-vm"></a>VM に接続します

VM との SSH セッションを作成し、追加の設定を構成するには、次のコマンドを使用します。 IP アドレスを、VM の `publicIpAddress` 値に置き換えます。

```bash
ssh <publicIpAddress>
```

## <a name="install-oracle-asm"></a>Oracle ASM のインストール

Oracle ASM をインストールするには、次の手順を実行します。 

Oracle ASM のインストールの詳細については、「[Oracle ASMLib Downloads for Oracle Linux 6 (Oracle Linux 6 用の Oracle ASMLib のダウンロード)](https://www.oracle.com/technetwork/server-storage/linux/asmlib/ol6-1709075.html)」を参照してください。  

1. ASM インストールを続行するには、root としてログインする必要があります。

   ```bash
   sudo su -
   ```
   
2. Oracle ASM コンポーネントをインストールするには、追加コマンドを実行します。

   ```bash
    yum list | grep oracleasm 
    yum -y install kmod-oracleasm.x86_64 
    yum -y install oracleasm-support.x86_64 
    wget https://download.oracle.com/otn_software/asmlib/oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    yum -y install oracleasmlib-2.0.12-1.el6.x86_64.rpm 
    rm -f oracleasmlib-2.0.12-1.el6.x86_64.rpm
   ```

3. Oracle ASM がインストールされていることを確認します。

   ```bash
   rpm -qa |grep oracleasm
   ```

    このコマンドの出力には、次のコンポーネントが一覧表示されます。

    ```bash
   oracleasm-support-2.1.10-4.el6.x86_64
   kmod-oracleasm-2.0.8-15.el6_9.x86_64
   oracleasmlib-2.0.12-1.el6.x86_64
    ```

4. ASM は正しく機能するために、特定のユーザーとロールを必要とします。 次のコマンドにより、前提条件のユーザー アカウントとグループが作成されます。 

   ```bash
    groupadd -g 54345 asmadmin 
    groupadd -g 54346 asmdba 
    groupadd -g 54347 asmoper 
    useradd -u 3000 -g oinstall -G dba,asmadmin,asmdba,asmoper grid 
    usermod -g oinstall -G dba,asmdba,asmadmin oracle
   ```

5. ユーザーとグループが正しく作成されたことを確認します。

   ```bash
   id grid
   ```

    このコマンドの出力には、次のユーザーとグループが一覧表示されます。

    ```bash
    uid=3000(grid) gid=54321(oinstall) groups=54321(oinstall),54322(dba),54345(asmadmin),54346(asmdba),54347(asmoper)
    ```
 
6. ユーザー *grid* のフォルダーを作成し、所有者を変更します。

   ```bash
   mkdir /u01/app/grid 
   chown grid:oinstall /u01/app/grid
   ```

## <a name="set-up-oracle-asm"></a>Oracle ASM のセットアップ

このチュートリアルでは、既定のユーザーは *grid*、既定のグループは *asmadmin* です。 *oracle* ユーザーが asmadmin グループに属していることを確認します。 Oracle ASM インストールをセットアップするには、次の手順を実行します。

1. Oracle ASM ライブラリ ドライバーの設定には、既定のユーザー (grid) と既定のグループ (asmadmin) を定義し、ブート時に起動し (y を選択)、ディスクをスキャンする (y を選択) ようにドライブを構成するという作業が含まれます。 次のコマンドからのプロンプトに答える必要があります。

   ```bash
   /usr/sbin/oracleasm configure -i
   ```

   このコマンドの出力は次のようになります。回答するべきプロンプトで停止します。

    ```output
   Configuring the Oracle ASM library driver.

   This will configure the on-boot properties of the Oracle ASM library
   driver. The following questions will determine whether the driver is
   loaded on boot and what permissions it will have. The current values
   will be shown in brackets ('[]'). Hitting <ENTER> without typing an
   answer will keep that current value. Ctrl-C will abort.

   Default user to own the driver interface []: grid
   Default group to own the driver interface []: asmadmin
   Start Oracle ASM library driver on boot (y/n) [n]: y
   Scan for Oracle ASM disks on boot (y/n) [y]: y
   Writing Oracle ASM library driver configuration: done
   ```

2. ディスク構成を表示します。

   ```bash
   cat /proc/partitions
   ```

   このコマンドの出力は、次のようなディスクの一覧になります。

   ```output
   8       16   14680064 sdb
   8       17   14678976 sdb1
   8        0   52428800 sda
   8        1     512000 sda1
   8        2   51915776 sda2
   8       48   52428800 sdd
   8       64   52428800 sde
   8       80   52428800 sdf
   8       32   52428800 sdc
   11       0       1152 sr0
   ```

3. ディスク */dev/sdc* をフォーマットします。次のコマンドを実行し、プロンプトに次のように回答します。
   - 新しいパーティションには *n*
   - プライマリ パーティションには *p*
   - 最初のパーティションを選択するには *1*
   - 既定の最初のシリンダーには `enter` を押します
   - 既定の最後のシリンダーには `enter` を押します
   - 変更をパーティション テーブルに書き込むには *w* を押します  

   ```bash
   fdisk /dev/sdc
   ```
   
   上の回答を利用した場合、`fdisk` コマンドの出力は次のようになります。

   ```output
   Device contains not a valid DOS partition table, or Sun, SGI or OSF disklabel
   Building a new DOS disklabel with disk identifier 0xf865c6ca.
   Changes will remain in memory only, until you decide to write them.
   After that, of course, the previous content won't be recoverable.

   Warning: invalid flag 0x0000 of partition table 4 will be corrected by w(rite)

   The device presents a logical sector size that is smaller than
   the physical sector size. Aligning to a physical sector (or optimal
   I/O) size boundary is recommended, or performance may be impacted.

   WARNING: DOS-compatible mode is deprecated. It's strongly recommended to
           switch off the mode (command 'c') and change display units to
           sectors (command 'u').

   Command (m for help): n
   Command action
     e   extended
     p   primary partition (1-4)
   p
   Partition number (1-4): 1
   First cylinder (1-6527, default 1):
   Using default value 1
   Last cylinder, +cylinders or +size{K,M,G} (1-6527, default 6527):
   Using default value 6527

   Command (m for help): w
   The partition table has been altered!

   Calling ioctl() to re-read partition table.
   Syncing disks.
   ```

4. 前述の `fdisk` コマンドを `/dev/sdd`、`/dev/sde`、`/dev/sdf` に対して繰り返します。

5. ディスク構成を確認します。

   ```bash
   cat /proc/partitions
   ```

   コマンドの出力は次のようになります。

   ```output
   major minor  #blocks  name

     8       16   14680064 sdb
     8       17   14678976 sdb1
     8       32   52428800 sdc
     8       33   52428096 sdc1
     8       48   52428800 sdd
     8       49   52428096 sdd1
     8       64   52428800 sde
     8       65   52428096 sde1
     8       80   52428800 sdf
     8       81   52428096 sdf1
     8        0   52428800 sda
     8        1     512000 sda1
     8        2   51915776 sda2
     11       0    1048575 sr0
   ```

6. Oracle ASM サービスの状態を確認し、Oracle ASM サービスを開始します。

   ```bash
   service oracleasm status 
   service oracleasm start
   ```

   コマンドの出力は次のようになります。

   ```output
   Checking if ASM is loaded: no
   Checking if /dev/oracleasm is mounted: no
   Initializing the Oracle ASMLib driver:                     [  OK  ]
   Scanning the system for Oracle ASMLib disks:               [  OK  ]
   ```

7. Oracle ASM ディスクを作成します。

   ```bash
   service oracleasm createdisk ASMSP /dev/sdc1 
   service oracleasm createdisk DATA /dev/sdd1 
   service oracleasm createdisk DATA1 /dev/sde1 
   service oracleasm createdisk FRA /dev/sdf1
   ```

   コマンドの出力は次のようになります。

   ```output
   Marking disk "ASMSP" as an ASM disk:                       [  OK  ]
   Marking disk "DATA" as an ASM disk:                        [  OK  ]
   Marking disk "DATA1" as an ASM disk:                       [  OK  ]
   Marking disk "FRA" as an ASM disk:                         [  OK  ]
   ```

8. Oracle ASM ディスクを一覧表示します。

   ```bash
   service oracleasm listdisks
   ```

   コマンドの出力には、次のような Oracle ASM ディスクが一覧表示されます。

   ```output
    ASMSP
    DATA
    DATA1
    FRA
   ```

9. ルート、Oracle、および grid ユーザーのパスワードを変更します インストールの後の段階で使用するため、**新しいパスワードをメモしておきます**。

   ```bash
   passwd oracle 
   passwd grid 
   passwd root
   ```

10. フォルダーのアクセス許可を変更します。

    ```bash
    chmod -R 775 /opt 
    chown grid:oinstall /opt 
    chown oracle:oinstall /dev/sdc1 
    chown oracle:oinstall /dev/sdd1 
    chown oracle:oinstall /dev/sde1 
    chown oracle:oinstall /dev/sdf1 
    chmod 600 /dev/sdc1 
    chmod 600 /dev/sdd1 
    chmod 600 /dev/sde1 
    chmod 600 /dev/sdf1
    ```

## <a name="download-and-prepare-oracle-grid-infrastructure"></a>Oracle Grid Infrastructure のダウンロードと準備

Oracle Grid Infrastructure ソフトウェアをダウンロードして準備するには、次の手順を実行します。

1. [Oracle ASM のダウンロード ページ](https://www.oracle.com/database/technologies/oracle19c-linux-downloads.html)から Oracle Grid Infrastructure をダウンロードします。 

   "**Oracle Database 12c Release 1 Grid Infrastructure (12.1.0.2.0) for Linux x86-64**" というダウンロード タイトルの下で、2 つの .zip ファイルをダウンロードします。

2. これらの .zip ファイルをクライアント コンピューターにダウンロードしたら、Secure Copy Protocol (SCP) を使用してそのファイルを VM にコピーすることができます。

   ```bash
   scp *.zip <publicIpAddress>:.
   ```

3. .zip ファイルを /opt フォルダーに移動する目的で、Azure の Oracle VM に SSH で戻ります。 その後、ファイルの所有者を変更します。

   ```bash
   ssh <publicIPAddress>
   sudo mv ./*.zip /opt
   cd /opt
   sudo chown grid:oinstall linuxamd64_12102_grid_1of2.zip
   sudo chown grid:oinstall linuxamd64_12102_grid_2of2.zip
   ```

4. .zip ファイルを解凍します (Linux のファイル解凍ツールがまだインストールされていない場合はインストールします)。

   ```bash
   sudo yum install unzip
   sudo unzip linuxamd64_12102_grid_1of2.zip
   sudo unzip linuxamd64_12102_grid_2of2.zip
   ```

5. アクセス許可を変更します。

   ```bash
   sudo chown -R grid:oinstall /opt/grid
   ```

6. 構成済みのスワップ領域を更新します。 Oracle Grid コンポーネントは、Grid をインストールするにために 6.8 GB 以上のスワップ領域を必要とします。 Azure の Oracle Linux イメージの既定のスワップ ファイル サイズはわずか 2048 MB です。 更新した設定を適用するには、`/etc/waagent.conf` ファイルの `ResourceDisk.SwapSizeMB` を増やし、WALinuxAgent サービスを再起動する必要があります。 読み取り専用ファイルであるため、書き込みアクセスを有効にするにはファイルのアクセス許可を変更する必要があります。

   ```bash
   sudo chmod 777 /etc/waagent.conf  
   vi /etc/waagent.conf
   ```

   `ResourceDisk.SwapSizeMB` を検索し、値を **8192** に変更します。 `insert` を押して挿入モードに入り、値として **8192** を入力し、`esc` を押してコマンド モードに戻ります。 変更を書き込み、ファイルを終了するには、`:wq` を入力し、`enter` を押します。
   
   > [!NOTE]
   > 常にローカル短期ディスク (一時ディスク) に作成され、最適なパフォーマンスが得られるように、常に `WALinuxAgent` を使用してスワップ領域を構成することが推奨されます。 詳細については、「[Linux Azure の仮想マシンのスワップ ファイルを追加する方法](https://support.microsoft.com/en-us/help/4010058/how-to-add-a-swap-file-in-linux-azure-virtual-machines)」を参照してください。

## <a name="prepare-your-local-client-and-vm-to-run-x11"></a>x11 を実行するようにローカル クライアントと VM を用意する
Oracle ASM を構成するとき、インストールと構成を完了するためにグラフィカル インターフェイスが必要になります。 x11 プロトコルを利用し、このインストールを簡単にします。 X11 機能が既に有効で構成されているクライアント システム (Mac または Linux) を使用している場合、Windows マシン固有のこの構成と設定を省略できます。 

1. [PuTTY](https://www.putty.org/) および [Xming](https://xming.en.softonic.com/) を Windows コンピューターにダウンロードします。 続行する前に、両方のアプリケーションのインストールを既定値で完了する必要があります。

2. PuTTY をインストールしたら、コマンド プロンプトを開き、PuTTY フォルダー (例: C:\Program Files\PuTTY) に移動し、`puttygen.exe` を実行してキーを生成します。

3. PuTTY Key Generator で、次の手順を実行します。
   
   1. `Generate` ボタンを選択し、キーを生成します。
   2. キーの内容をコピーします (Ctrl + C キー)。
   3. `Save private key` ボタンを選択します。
   4. パスフレーズによるキーのセキュリティ強化に関する警告を無視し、`OK` を選択します。

   ![PuTTY Key Generator のスクリーンショット](./media/oracle-asm/puttykeygen.png)

4. VM で、次のコマンドを実行します。

   ```bash
   sudo su - grid
   mkdir .ssh 
   cd .ssh
   ```

5. `authorized_keys` という名前でファイルを作成します。 このファイルにキーの内容を貼り付けて、ファイルを保存します。

   > [!NOTE]
   > キーには、`ssh-rsa` という文字列を含める必要があります。 また、キーの内容は 1 行のテキストである必要があります。
   >  

6. クライアント システムで、PuTTY を起動します。 **[Category]\(カテゴリ\)** ウィンドウで、 **[Connection]\(接続\)**  >  **[SSH]**  >  **[Auth]\(認証\)** の順に移動します。 **[Private key file for authentication]\(認証のための秘密キー ファイル\)** ボックスに、先ほど生成したキーを参照します。

   ![SSH 認証オプションのスクリーン ショット](./media/oracle-asm/setprivatekey.png)

7. **[Category]\(カテゴリ\)** ウィンドウで、 **[Connection]\(接続\)**  >  **[SSH]**  >  **[X11]** の順に移動します。 **[Enable X11 forwarding]\(X11 転送を有効にする\)** チェック ボックスをオンにします。

   ![SSH X11 転送オプションのスクリーンショット](./media/oracle-asm/enablex11.png)

8. **[Category]\(カテゴリ\)** ウィンドウで、 **[Session]\(セッション\)** に移動します。 ホスト名ダイアログ ボックスに Oracle ASM VM `<publicIPaddress>` を入力し、新しい `Saved Session` 名を入力し、`Save` をクリックします。  保存後、`open` をクリックし、Oracle ASM VM に接続します。  初めて接続するとき、リモート システムはレジストリにキャッシュされないと警告されます。 `yes` をクリックして追加し、続行します。

   ![PuTTY セッション オプションのスクリーン ショット](./media/oracle-asm/puttysession.png)

## <a name="install-oracle-grid-infrastructure"></a>Oracle Grid Infrastructure のインストール

Oracle Grid Infrastructure をインストールするには、次の手順を実行します。

1. **grid** としてサインインします。 (パスワードの入力を要求されることなくサインインできるはずです)。 

   > [!NOTE]
   > Windows を実行している場合、インストールを始める前に Xming を起動しておきます。

   ```bash
   cd /opt/grid
   ./runInstaller
   ```

   Oracle Grid Infrastructure 12c Release 1 のインストーラーが開きます (インストーラーが起動するまでに数分かかる場合があります)。

2. **[インストール オプションの選択]** ページで、 **[スタンドアロン サーバー用に Oracle Grid Infrastructure をインストールおよび構成]** を選択します。

   ![インストーラーの [インストール オプションの選択] ページのスクリーンショット](./media/oracle-asm/install01.png)

3. **[製品言語の選択]** ページで、 **[英語]** や他の使用する言語が選択されていることを確認します。  [`next`] をクリックします。

4. **[ASM ディスク グループの作成]** ページで、次の手順を実行します。
   - ディスク グループの名前を入力します。
   - **[冗長性]** で、 **[外部]** を選択します。
   - **[割当て単位サイズ]** で、 **[4]** を選択します。
   - **[ディスクの追加]** で、**ORCLASMSP** を選択します。
   - [`next`] をクリックします。

5. **[ASM パスワードの指定]** ページで、 **[これらのアカウントごとに、同じパスワードを使用]** を選択し、パスワードを入力します。

   ![インストーラーの [ASM パスワードの指定] ページのスクリーンショット](./media/oracle-asm/install04.png)

6. **[Specify Management Options]\(管理オプションの指定\)** ページには、EM クラウド コントロールを構成するためのオプションがあります。 このオプションは省略し、`next` をクリックして続行します。 

7. **[権限付きオペレーティング システム グループ]** ページで、既定の設定を使用します。 `next` をクリックして続行します。

8. **[インストール場所の指定]** ページで、既定の設定を使用します。 `next` をクリックして続行します。

9. **[インベントリの作成]** ページで、インベントリ ディレクトリを `/u01/app/grid/oraInventory` に変更します。 `next` をクリックして続行します。

   ![インストーラーの [インベントリの作成] ページのスクリーンショット](./media/oracle-asm/install08.png)

10. **[root スクリプトの実行構成]** ページで、 **[構成スクリプトを自動的に実行]** チェック ボックスをオンにします。 次に、 **[root ユーザーの資格証明を使用]** オプションを選択し、root ユーザーのパスワードを入力します。

    ![インストーラーの [root スクリプトの実行構成] ページのスクリーンショット](./media/oracle-asm/install09.png)

11. 現在の設定が失敗し、 **[Perform Prerequisite Checks]\(前提条件チェックの実行\)** ページにエラーが表示されます。 これは予想される現象です。 [`Fix & Check Again`] を選択します。

12. **[Fixup Script]\(修正スクリプト\)** ダイアログ ボックスで、`OK` をクリックします。

13. **[サマリー]** ページで、選択した設定を確認し、`Install` をクリックします。

    ![インストーラーの [サマリー] ページのスクリーンショット](./media/oracle-asm/install12.png)

14. 警告ダイアログ ボックスが表示され、構成スクリプトを特権ユーザーとして実行する必要があると表示されます。 `Yes` をクリックして続行します。

15. **[終了]** ページで、`Close` をクリックしてインストールを終了します。

## <a name="set-up-your-oracle-asm-installation"></a>Oracle ASM インストールのセットアップ

Oracle ASM インストールをセットアップするには、次の手順を実行します。

1. X11 セッションから、まだ **grid** としてサインインしていることを確認します。 場合によっては、`enter` を押し、ターミナルを復帰させる必要があります。 Oracle Automated Storage Management Configuration Assistant (Oracle自動ストレージ管理コンフィギュレーション・アシスタント) を起動します。

   ```bash
   cd /u01/app/grid/product/12.1.0/grid/bin
   ./asmca
   ```

   Oracle ASM コンフィギュレーション アシスタントが開きます。

2. **[ASM の構成: ディスク グループ]** ダイアログ ボックスで、`Create` ボタンをクリックし、`Show Advanced Options` をクリックします。

3. **[ディスク グループの作成]** ダイアログ ボックスで、次の作業を行います。

   - ディスク グループ名として「**DATA**」を入力します。
   - **[メンバー ディスクの選択]** で、**ORCL_DATA** と **ORCL_DATA1** を選択します。
   - **[割当て単位サイズ]** で、 **[4]** を選択します。
   - `ok` をクリックし、ディスク グループを作成します。
   - `ok` をクリックし、確認ウィンドウを閉じます。

   ![[ディスク グループの作成] ダイアログ ボックスのスクリーンショット](./media/oracle-asm/asm02.png)

4. **[ASM の構成: ディスク グループ]** ダイアログ ボックスで、`Create` ボタンをクリックし、`Show Advanced Options` をクリックします。

5. **[ディスク グループの作成]** ダイアログ ボックスで、次の作業を行います。

   - ディスク グループ名として「**FRA**」を入力します。
   - **[冗長性]** で、 **[External (none)]\(外部 (なし)\)** を選択します。
   - **[メンバー ディスクの選択]** で、**ORCL_FRA** を選択します。
   - **[割当て単位サイズ]** で、 **[4]** を選択します。
   - `ok` をクリックし、ディスク グループを作成します。
   - `ok` をクリックし、確認ウィンドウを閉じます。

   ![[ディスクグループの作成] ダイアログ ボックスのスクリーンショットで、[外部 (なし)] オプションが強調表示されています。](./media/oracle-asm/asm04.png)

6. **[終了]** を選択して ASM コンフィギュレーション アシスタントを閉じます。

   ![[終了] ボタンがある [ASM の構成: ディスク グループ] ダイアログ ボックスのスクリーンショット](./media/oracle-asm/asm05.png)

## <a name="create-the-database"></a>データベースの作成

Oracle データベース ソフトウェアは既に Azure Marketplace イメージにインストールされています。 データベースを作成するには、次の手順を実行します。

1. ユーザーを Oracle スーパー ユーザーに切り替え、ログ用にリスナーを初期化します。

   ```bash
   su - oracle
   cd /u01/app/oracle/product/12.1.0/dbhome_1/bin
   ./dbca
   ```

   Database Configuration Assistant が開きます。

2. **[データベース操作]** ページで、`Create Database` をクリックします。

3. **[作成モード]** ページで、次の手順を実行します。

   - データベースの名前を入力します。
   - **[記憶域のタイプ]** に、 **[Automatic Storage Management (ASM)]\(自動ストレージ管理 (ASM)\)** が選択されていることを確認します。
   - **[データベース ファイルの場所]** には、ASM により提案された既定の場所を使用します。
   - **[高速リカバリ領域]** には、ASM により提案された既定の場所を使用します。
   - **[管理パスワード]** と **[パスワードの確認]** に入力します。
   - `create as container database` が選択されていることを確認します。
   - `pluggable database name` 値を入力します。

4. **[サマリー]** ページで、選択した設定を確認し、`Finish` をクリックしてデータベースを作成します。

   ![[サマリー] ページのスクリーンショット](./media/oracle-asm/createdb03.png)

5. データベースが作成されました。 **[完了]** ページには、追加アカウントのロックを解除し、このデータベースを使用したり、パスワードを変更したりするためのオプションがあります。 オプションを利用する場合、 **[パスワード管理]** を選択します。利用しない場合、`close` をクリックします。

## <a name="delete-the-vm"></a>VM の削除

Azure Marketplace の Oracle DB イメージ上でOracle Automated Storage Management (Oracle自動ストレージ管理) を正常に構成できました。  この VM が不要になったら、次のコマンドを使用し、リソース グループ、VM、すべての関連リソースを削除できます。

```azurecli
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次のステップ

[チュートリアル:Oracle DataGuard の構成](configure-oracle-dataguard.md)

[チュートリアル:Oracle GoldenGate の構成](Configure-oracle-golden-gate.md)

[Oracle DB の構築](oracle-design.md)を確認する
