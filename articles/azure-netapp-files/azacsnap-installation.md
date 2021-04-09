---
title: Azure NetApp Files 用の Azure アプリケーション整合性スナップショット ツールをインストールする | Microsoft Docs
description: Azure NetApp Files で使用できる Azure アプリケーション整合スナップショット ツールをインストールするためのガイドを提供します。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 458f4d3f29cb08a94095167ed45133f5cd70f5f4
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/23/2021
ms.locfileid: "104869193"
---
# <a name="install-azure-application-consistent-snapshot-tool-preview"></a>Azure アプリケーション整合性スナップショット ツールをインストールする (プレビュー)

この記事では、Azure NetApp Files で使用できる Azure アプリケーション整合スナップショット ツールをインストールするためのガイドを提供します。

## <a name="introduction"></a>はじめに

ダウンロード可能な自己インストーラーは、ルート以外のユーザー特権 (azacsnap など) を使用して、スナップショット ツールを簡単に設定して実行できるように設計されています。 インストーラーによってユーザーが設定され、スナップショット ツールがそのユーザーの `$HOME/bin` サブディレクトリ (既定では `/home/azacsnap/bin`) に配置されます。
自己インストーラーにより、インストールを行うユーザーの構成 (ルートなど) に基づいて、すべてのファイルの正しい設定とパスの判別が試みられます。 前提条件の手順 (ストレージと SAP HANA との通信を有効にする) がルートとして実行された場合は、インストール時に秘密キーと `hdbuserstore` がバックアップ ユーザーの場所にコピーされます。 しかし、ストレージ バックエンドと SAP HANA との通信を有効にする手順は、インストール後に知識のある管理者が手動で行うことができます。

## <a name="prerequisites-for-installation"></a>インストールの前提条件

ガイドラインに従って、スナップショットとディザスター リカバリー コマンドを設定して実行します。 スナップショット ツールをインストールして使用する前に、次の手順をルートとして行うことをお勧めします。

1. **OS に修正プログラムを適用する**: 修正プログラムの適用と SMT の設定については、「[SAP HANA on Azure (L インスタンス) のインストールと構成の方法](../virtual-machines/workloads/sap/hana-installation.md#operating-system)」を参照してください。
1. **時間同期を設定する**。 お客様は NTP と互換性のあるタイム サーバーを指定し、それに応じて OS を構成する必要があります。
1. **HANA をインストールする**: HANA のインストール手順については、「[HANA データベースへの SAP NetWeaver のインストール](/archive/blogs/saponsqlserver/sap-netweaver-installation-on-hana-database)」を参照してください。
1. **[ストレージとの通信を有効にする](#enable-communication-with-storage)** (詳細については、別のセクションを参照): お客様は秘密および公開キーのペアを使用して SSH を設定し、ストレージ バックエンドでの設定のために Microsoft Operations に対してスナップショット ツールが実行される予定の各ノードの公開キーを提供する必要があります。
   1. **Azure NetApp Files の場合 (詳細については別のセクションを参照)** : お客様はサービス プリンシパル認証ファイルを生成する必要があります。
   1. **Azure L インスタンスの場合 (詳細については別のセクションを参照)** : お客様は秘密および公開キーのペアを使用して SSH を設定し、ストレージ バックエンドでの設定のために Microsoft Operations に対してスナップショット ツールが実行される予定の各ノードの公開キーを提供する必要があります。

      SSH を使用してノードのいずれかに接続し、これをテストします (例: `ssh -l <Storage UserName> <Storage IP Address>`)。
      ストレージ プロンプトからログアウトするには、「`exit`」と入力します。

      Microsoft Operations により、プロビジョニング時にストレージ ユーザーおよびストレージ IP が提供されます。
  
1. **[SAP HANA との通信を有効にする](#enable-communication-with-sap-hana)** (詳細については、別のセクションを参照): お客様は、スナップショットを実行するために必要な権限を持つ適切な SAP HANA ユーザーを設定する必要があります。
   1. この設定は、コマンド ラインから次のように灰色のテキストを使用してテストできます`grey`
      1. HANAv1

            `hdbsql -n <HANA IP address> -i <HANA instance> -U <HANA user> "\s"`

      1. HANAv2

            `hdbsql -n <HANA IP address> -i <HANA instance> -d SYSTEMDB -U <HANA user> "\s"`

      - 上記の例は、SAP HANA との非 SSL 通信用です。

## <a name="enable-communication-with-storage"></a>ストレージとの通信を有効にする

このセクションでは、ストレージとの通信を有効にする方法について説明します。

### <a name="azure-netapp-files"></a>Azure NetApp Files

RBAC サービス プリンシパルを作成します

1. Azure Cloud Shell セッション内で、既定でサービス プリンシパルに関連付けるサブスクリプションでログオンしていることを確認します。

    ```azurecli-interactive
    az account show
    ```

1. サブスクリプションが正しくない場合は、以下を使用します

    ```azurecli-interactive
    az account set -s <subscription name or id>
    ```

1. 次の例に従って、Azure CLI を使用してサービス プリンシパルを作成します

    ```azurecli-interactive
    az ad sp create-for-rbac --sdk-auth
    ```

    1. これにより、次の例のような出力が生成されるはずです。

        ```output
        {
          "clientId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "clientSecret": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "subscriptionId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "tenantId": "00aa000a-aaaa-0000-00a0-00aa000aaa0a",
          "activeDirectoryEndpointUrl": "https://login.microsoftonline.com",
          "resourceManagerEndpointUrl": "https://management.azure.com/",
          "activeDirectoryGraphResourceId": "https://graph.windows.net/",
          "sqlManagementEndpointUrl": "https://management.core.windows.net:8443/",
          "galleryEndpointUrl": "https://gallery.azure.com/",
          "managementEndpointUrl": "https://management.core.windows.net/"
        }
        ```

    > このコマンドにより、サブスクリプション レベルで RBAC 共同作成者ロールがサービス プリンシパルに自動的に割り当てられます。スコープは、テスト時にリソースが作成される特定のリソース グループに絞り込むことができます。

1. 出力内容を切り取り、`azacsnap` コマンドと同じシステム上に格納されている `azureauth.json` というファイルに貼り付け、適切なシステム アクセス許可でファイルを保護します。

### <a name="azure-large-instance"></a>Azure Large Instances

ストレージ バックエンドとの通信は、暗号化された SSH チャネルを介して実行されます。 次の手順の例では、この通信用の SSH の設定に関するガイダンスを提供します。

1. `/etc/ssh/ssh_config` ファイルを変更します

    `MACs hmac-sha1` 行が追加された次の出力を参照してください。

    ```output
    # RhostsRSAAuthentication no
    # RSAAuthentication yes
    # PasswordAuthentication yes
    # HostbasedAuthentication no
    # GSSAPIAuthentication no
    # GSSAPIDelegateCredentials no
    # GSSAPIKeyExchange no
    # GSSAPITrustDNS no
    # BatchMode no
    # CheckHostIP yes
    # AddressFamily any
    # ConnectTimeout 0
    # StrictHostKeyChecking ask
    # IdentityFile ~/.ssh/identity
    # IdentityFile ~/.ssh/id_rsa
    # IdentityFile ~/.ssh/id_dsa
    # Port 22
    Protocol 2
    # Cipher 3des
    # Ciphers aes128-ctr,aes192-ctr,aes256-ctr,arcfour256,arcfour128,aes128-cbc,3des-
    cbc
    # MACs hmac-md5,hmac-sha1,umac-64@openssh.com,hmac-ripemd
    MACs hmac-sha
    # EscapeChar ~
    # Tunnel no
    # TunnelDevice any:any
    # PermitLocalCommand no
    # VisualHostKey no
    # ProxyCommand ssh -q -W %h:%p gateway.example.com
    ```

1. 秘密および公開キーのペアを作成します

    次のコマンド例を使用してキーのペアを生成する場合は、キーの生成時にパスワードを入力しないでください。

    ```bash
    ssh-keygen -t rsa –b 5120 -C ""
    ```

1. Microsoft Operations に公開キーを送信します

    `cat /root/.ssh/id_rsa.pub` コマンドの出力 (下記の例) を Microsoft Operations に送信し、スナップショット ツールがストレージ サブシステムと通信できるようにします。

    ```bash
    cat /root/.ssh/id_rsa.pub
    ```

    ```output
    ssh-rsa
    AAAAB3NzaC1yc2EAAAADAQABAAABAQDoaRCgwn1Ll31NyDZy0UsOCKcc9nu2qdAPHdCzleiTWISvPW
    FzIFxz8iOaxpeTshH7GRonGs9HNtRkkz6mpK7pCGNJdxS4wJC9MZdXNt+JhuT23NajrTEnt1jXiVFH
    bh3jD7LjJGMb4GNvqeiBExyBDA2pXdlednOaE4dtiZ1N03Bc/J4TNuNhhQbdsIWZsqKt9OPUuTfD
    j0XvwUTLQbR4peGNfN1/cefcLxDlAgI+TmKdfgnLXIsSfbacXoTbqyBRwCi7p+bJnJD07zSc9YCZJa
    wKGAIilSg7s6Bq/2lAPDN1TqwIF8wQhAg2C7yeZHyE/ckaw/eQYuJtN+RNBD
    ```

## <a name="enable-communication-with-sap-hana"></a>SAP HANA との通信を有効にする

スナップショット ツールでは SAP HANA と通信を行います。データベースのセーブポイントを開始および解放するための適切な権限を持つユーザーが必要になります。 次の例は、SAP HANA v2 ユーザーの設定と、SAP HANA データベースとの通信のための `hdbuserstore` を示しています。

次のコマンド例では、SAP HANA 2 データベースの SYSTEMDB でユーザー (AZACSNAP) を設定し、
IP アドレス、ユーザー名、およびパスワード (必要に応じて) を変更します。

1. SYSTEMDB に接続してユーザーを作成します

    ```bash
    hdbsql -n <IP_address_of_host>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD>
    ```

    ```output
    Welcome to the SAP HANA Database interactive terminal.

    Type: \h for help with commands
    \q to quit

    hdbsql SYSTEMDB=>
    ```

1. ユーザーを作成します

    この例では、SYSTEMDB に AZACSNAP ユーザーを作成します。

    ```sql
    hdbsql SYSTEMDB=> CREATE USER AZACSNAP PASSWORD <AZACSNAP_PASSWORD_CHANGE_ME> NO FORCE_FIRST_PASSWORD_CHANGE;
    ```

1. ユーザーにアクセス許可を付与します

    この例では、データベース整合性ストレージ スナップショットを実行できるようにするために、AZACSNAP ユーザーに対してアクセス許可を設定します。

    ```sql
    hdbsql SYSTEMDB=> GRANT BACKUP ADMIN, CATALOG READ, MONITORING TO AZACSNAP;
    ```

1. "*省略可能*" - ユーザーのパスワードの有効期限が切れないようにします

    > [!NOTE]
    > この変更を行う前に、企業のポリシーをご確認ください。

   この例では、AZACSNAP ユーザーのパスワードの有効期限を無効にします。この変更を行わないと、ユーザーのパスワードの有効期限が切れ、スナップショットが正しく取得されなくなります。  

   ```sql
   hdbsql SYSTEMDB=> ALTER USER AZACSNAP DISABLE PASSWORD LIFETIME;
   ```

1. SAP HANA Secure User Store を設定します (パスワードを変更する)  この例では、Linux シェルから `hdbuserstore` コマンドを使用して、SAP HANA Secure User Store を設定します。

    ```bash
    hdbuserstore Set AZACSNAP <IP_address_of_host>:30013 AZACSNAP <AZACSNAP_PASSWORD_CHANGE_ME>
    ```

1. SAP HANA Secure User Store を確認します Secure User Store が正しく設定されているかどうかを確認するには、`hdbuserstore` コマンドを使用して、次の例のような出力を一覧表示します。 `hdbuserstore` の使用について詳しくは、SAP の Web サイトを参照してください。

    ```bash
    hdbuserstore List
    ```

    ```output
    DATA FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.DAT
    KEY FILE : /home/azacsnap/.hdb/sapprdhdb80/SSFS_HDB.KEY

    KEY AZACSNAP
    ENV : <IP_address_of_host>:
    USER: AZACSNAP
    ```

### <a name="using-ssl-for-communication-with-sap-hana"></a>SAP HANA との通信での SSL の使用

`azacsnap` ツールでは、SAP HANA の `hdbsql` コマンドを利用して SAP HANA と通信を行います。 これには、SAP HANA との通信を暗号化するときの SSL オプションの使用が含まれます。 `azacsnap` では、次のように `hdbsql` コマンドの SSL オプションを使用します。

`azacsnap --ssl` オプションを使用する場合は、常に次のものが使用されます。

- `-e` -TLS encryptionTLS/SSL 暗号化を有効にします。 サーバーにより、使用可能な最も高いレベルのものが選択されます。
- `-ssltrustcert` - サーバーの証明書を確認するかどうかを指定します。
- `-sslhostnameincert "*"` - サーバーの ID を検証するために使用するホスト名を指定します。 ホスト名として `"*"` を指定すると、サーバーのホスト名は確認されません。

SSL 通信には、キー ストアおよびトラスト ストア ファイルも必要です。  これらのファイルは Linux インストールの既定の場所に格納できますが、さまざまな SAP HANA システムで確実に正しいキー マテリアルが使用されるようにするには (つまり、各 SAP HANA システムに異なるキーストアおよびトラストストア ファイルが使用されている場合)、`azacsnap` では、`azacsnap` 構成ファイルで指定されている `securityPath` の場所にキーストアおよびトラストストア ファイルを格納する必要があります。

#### <a name="key-store-files"></a>キー ストア ファイル

- キー マテリアルが同じである複数の SID を使用する場合は、`azacsnap` 構成ファイルで定義されている securityPath の場所へのリンクをより簡単に作成できます。  SSL を使用する SID ごとにこれらの値が確実に存在するようにします。
  - openssl の場合:
    - `ln $HOME/.ssl/key.pem <securityPath>/<SID>_keystore`
  - commoncrypto の場合:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_keystore`
- SID ごとにキー マテリアルが異なる複数の SID を使用する場合は、SID の `azacsnap` 構成ファイルで定義されている securityPath の場所にファイルをコピー (または移動して名前を変更) します。
  - openssl の場合:
    - `mv key.pem <securityPath>/<SID>_keystore`
  - commoncrypto の場合:
    - `mv sapcli.pse <securityPath>/<SID>_keystore`

`azacsnap` により `hdbsql` が呼び出されると、`-sslkeystore=<securityPath>/<SID>_keystore` がコマンド ラインに追加されます。

#### <a name="trust-store-files"></a>トラスト ストア ファイル

- キー マテリアルが同じである複数の SID を使用する場合は、`azacsnap` 構成ファイルで定義されている securityPath の場所へのハードリンクを作成します。  SSL を使用する SID ごとにこれらの値が確実に存在するようにします。
  - openssl の場合:
    - `ln $HOME/.ssl/trust.pem <securityPath>/<SID>_truststore`
  - commoncrypto の場合:
    - `ln $SECUDIR/sapcli.pse <securityPath>/<SID>_truststore`
- SID ごとにキー マテリアルが異なる複数の SID を使用する場合は、SID の `azacsnap` 構成ファイルで定義されている securityPath の場所にファイルをコピー (または移動して名前を変更) します。
  - openssl の場合:
    - `mv trust.pem <securityPath>/<SID>_truststore`
  - commoncrypto の場合:
    - `mv sapcli.pse <securityPath>/<SID>_truststore`

> [!NOTE]
> ファイル名の `<SID>` コンポーネントは、すべて大文字の SAP HANA システム識別子である必要があります (たとえば、`H80`、`PR1` など)

`azacsnap` により `hdbsql` が呼び出されると、`-ssltruststore=<securityPath>/<SID>_truststore` がコマンド ラインに追加されます。

そのため、構成ファイルで `SID` が `H80` である場合に `azacsnap -c test --test hana --ssl openssl` を実行すると、次のように `hdbsql` 接続が実行されます。

```bash
hdbsql \
    -e \
    -ssltrustcert \
    -sslhostnameincert "*" \
    -sslprovider openssl \
    -sslkeystore ./security/H80_keystore \
    -ssltruststore ./security/H80_truststore
    "sql statement"
```

> [!NOTE]
> `\` 文字は、コマンド ラインで渡される複数のパラメーターをよりわかりやすくするためのコマンド ラインの行の折り返しを表します。

## <a name="installing-the-snapshot-tools"></a>スナップショット ツールのインストール

ダウンロード可能な自己インストーラーは、ルート以外のユーザー特権 (azacsnap など) を使用して、スナップショット ツールを簡単に設定して実行できるように設計されています。 インストーラーによってユーザーが設定され、スナップショット ツールがそのユーザーの `$HOME/bin` サブディレクトリ (既定では `/home/azacsnap/bin`) に配置されます。

自己インストーラーにより、インストールを行うユーザーの構成 (ルートなど) に基づいて、すべてのファイルの正しい設定とパスの判別が試みられます。 先ほどの設定手順 (ストレージと SAP HANA との通信を有効にする) がルートとして実行された場合は、インストール時に秘密キーと `hdbuserstore` がバックアップ ユーザーの場所にコピーされます。 しかし、ストレージ バックエンドと SAP HANA との通信を有効にする手順は、インストール後に知識のある管理者が手動で行うことができます。

> [!NOTE]
> 先ほどの SAP HANA on Azure (L インスタンス) のインストールでは、インストール済みのスナップショット ツールのディレクトリは `/hana/shared/<SID>/exe/linuxx86_64/hdb` でした。

[前提条件の手順](#prerequisites-for-installation)が完了したので、次のように自己インストーラーを使用してスナップショット ツールをインストールできるようになりました。

1. ダウンロードした自己インストーラーをターゲット システムにコピーします。
1. 自己インストーラーを `root` ユーザーとして実行します (以下の例を参照)。 必要に応じて、`chmod +x *.run` コマンドを使用してファイルを実行可能にします。

引数を指定せずに自己インストーラー コマンドを実行すると、次のように、インストーラーを使用してスナップショット ツールをインストールする方法についてのヘルプが表示されます。

```bash
chmod +x azacsnap_installer_v5.0.run
./azacsnap_installer_v5.0.run
```

```output
Usage: ./azacsnap_installer_v5.0.run [-v] -I [-u <HLI Snapshot Command user>]
./azacsnap_installer_v5.0.run [-v] -X [-d <directory>]
./azacsnap_installer_v5.0.run [-h]

Switches enclosed in [] are optional for each command line.
- h prints out this usage.
- v turns on verbose output.
- I starts the installation.
- u is the Linux user to install the scripts into, by default this is
'azacsnap'.
- X will only extract the commands.
- d is the target directory to extract into, by default this is
'./snapshot_cmds'.
Examples of a target directory are ./tmp or /usr/local/bin
```

> [!NOTE]
> 自己インストーラーには、ユーザーを作成して設定せずにバンドルからスナップショット ツールを抽出する (-X) オプションが用意されています。 これにより、経験豊富な管理者は設定手順を手動で行うか、既存のインストールをアップグレードするコマンドをコピーすることができます。

### <a name="easy-installation-of-snapshot-tools-default"></a>スナップショット ツールの簡単なインストール (既定)

インストーラーは、SAP HANA on Azure 用のスナップショット ツールをすばやくインストールするように設計されています。 既定では、-I オプションのみを指定してインストーラーが実行された場合、次の手順が行われます。

1. スナップショット ユーザー 'azacsnap' のホーム ディレクトリを作成し、グループ メンバーシップを設定します。
1. azacsnap ユーザーのログイン `~/.profile` を構成します。
1. azacsnap の `$PATH` に追加するディレクトリのファイルシステムを検索します。通常、これらは `hdbsql` や `hdbuserstore` など、SAP HANA ツールへのパスです。
1. azacsnap の `$LD_LIBRARY_PATH` に追加するディレクトリのファイルシステムを検索します。 多くのコマンドでは、正常に実行されるようにライブラリ パスを設定する必要があります。これにより、インストール済みのユーザー用に構成されます。
1. "ルート" ユーザー (インストールを実行するユーザー) から azacsnap 用のバックエンド ストレージの SSH キーをコピーします。 この場合、"ルート" ユーザーが既にストレージへの接続を構成していることが前提となります
    - 「[ストレージとの通信を有効にする](#enable-communication-with-storage)」セクションを参照してください。
1. ターゲット ユーザー azacsnap 用の SAP HANA Connection Secure User Store をコピーします。 この場合、"ルート" ユーザーが Secure User Store を既に構成していることが前提となります。「SAP HANA との通信を有効にする」セクションを参照してください。
1. スナップショット ツールは `/home/azacsnap/bin/` に抽出されます。
1. `/home/azacsnap/bin/` のコマンドにはアクセス許可 (所有権や実行可能ビットなど) が設定されています。

次の例は、既定のインストール オプションを使用して実行した場合のインストーラーの正しい出力を示しています。

```bash
./azacsnap_installer_v5.0.run -I
```

```output
+-----------------------------------------------------------+
| Azure Application Consistent Snapshot tool Installer      |
+-----------------------------------------------------------+
|-> Installer version '5.0'
|-> Create Snapshot user 'azacsnap', home directory, and set group membership.
|-> Configure azacsnap .profile
|-> Search filesystem for directories to add to azacsnap's $PATH
|-> Search filesystem for directories to add to azacsnap's $LD_LIBRARY_PATH
|-> Copying SSH keys for back-end storage for azacsnap.
|-> Copying HANA connection keystore for azacsnap.
|-> Extracting commands into /home/azacsnap/bin/.
|-> Making commands in /home/azacsnap/bin/ executable.
|-> Creating symlink for hdbsql command in /home/azacsnap/bin/.
+-----------------------------------------------------------+
| Install complete! Follow the steps below to configure.    |
+-----------------------------------------------------------+
+-----------------------------------------------------------+
|  Install complete!  Follow the steps below to configure.  |
+-----------------------------------------------------------+

1. Change into the snapshot user account.....
     su - azacsnap
2. Set up the HANA Secure User Store..... (command format below)
     hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>
3. Change to location of commands.....
     cd /home/azacsnap/bin/
4. Configure the customer details file.....
     azacsnap -c configure --configuration new
5. Test the connection to storage.....
     azacsnap -c test --test storage
6. Test the connection to HANA.....
   a. without SSL
     azacsnap -c test --test hana
   b. with SSL,  you will need to choose the correct SSL option
     azacsnap -c test --test hana --ssl=<commoncrypto|openssl>
7. Run your first snapshot backup..... (example below)
     azacsnap -c backup --volume=data --prefix=hana_test --frequency=15min --retention=1
```

### <a name="uninstall-the-snapshot-tools"></a>スナップショット ツールをアンインストールする

既定の設定を使用してスナップショット ツールがインストールされている場合、アンインストールするのに必要なのは、コマンドがインストールされているユーザー (既定値は azacsnap) の削除のみです。

```bash
userdel -f -r azacsnap
```

### <a name="manual-installation-of-the-snapshot-tools"></a>スナップショット ツールの手動インストール

場合によっては、手動でツールをインストールする必要がありますが、このプロセスを簡単にするためにインストーラーの既定のオプションを使用することをお勧めします。

`#` 文字で始まる各行は、この文字の後に続くコマンド例がルート ユーザーによって実行されることを示しています。 行の末尾にある `\` は、シェル コマンドの標準の行連結文字です。

ルート スーパーユーザーとして、次のように手動でインストールすることができます。

1. "sapsys" グループ ID を取得します。この例では、グループ ID は 1010 です

    ```bash
    grep sapsys /etc/group
    ```

    ```output
    sapsys:x:1010:
    ```

1. スナップショット ユーザー 'azacsnap' のホーム ディレクトリを作成し、手順 1 のグループ ID を使用してグループ メンバーシップを設定します。

    ```bash
    useradd -m -g 1010 -c "Azure SAP HANA Snapshots User" azacsnap
    ```

1. ユーザー azacsnap のログイン `.profile` が存在していることを確認します。

    ```bash
    echo "" >> /home/azacsnap/.profile
    ```

1. azacsnap の $PATH に追加するディレクトリのファイルシステムを検索します。通常、これらは `hdbsql` や `hdbuserstore` など、SAP HANA ツールへのパスです。

    ```bash
    HDBSQL_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name hdbsql -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. 更新されたパスをユーザーのプロファイルに追加します

    ```bash
    echo "export PATH=\"\$PATH:$HDBSQL_PATH\"" >> /home/azacsnap/.profile
    ```

1. azacsnap の $LD_LIBRARY_PATH に追加するディレクトリのファイルシステムを検索します。

    ```bash
    NEW_LIB_PATH=`find -L /hana/shared/[A-z0-9][A-z0-9][A-z0-9]/HDB*/exe /usr/sap/hdbclient -name "*.so" -exec dirname {} + 2> /dev/null | sort | uniq | tr '\n' ':'`
    ```

1. 更新されたライブラリ パスをユーザーのプロファイルに追加します

    ```bash
    echo "export LD_LIBRARY_PATH=\"\$LD_LIBRARY_PATH:$NEW_LIB_PATH\"" >> /home/azacsnap/.profile
    ```

1. Azure L インスタンスの場合
    1. "ルート" ユーザー (インストールを実行するユーザー) から azacsnap 用のバックエンド ストレージの SSH キーをコピーします。 この場合、"ルート" ユーザーが既にストレージへの接続を構成していることが前提となります
       > 「[ストレージとの通信を有効にする](#enable-communication-with-storage)」セクションを参照してください。

        ```bash
        cp -pr ~/.ssh /home/azacsnap/.
        ```

    1. SSH ファイルに対してユーザー アクセス許可を正しく設定します

        ```bash
        chown -R azacsnap.sapsys /home/azacsnap/.ssh
        ```

1. Azure NetApp Files の場合
    1. .NET Core の単一ファイル抽出ガイダンスに従って、ユーザーの `DOTNET_BUNDLE_EXTRACT_BASE_DIR` パスを構成します。
        1. SUSE Linux

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.profile
            ```

        1. RHEL

            ```bash
            echo "export DOTNET_BUNDLE_EXTRACT_BASE_DIR=\$HOME/.net" >> /home/azacsnap/.bash_profile
            echo "[ -d $DOTNET_BUNDLE_EXTRACT_BASE_DIR] && chmod 700 $DOTNET_BUNDLE_EXTRACT_BASE_DIR" >> /home/azacsnap/.bash_profile
            ```

1. ターゲット ユーザー azacsnap 用の SAP HANA Connection Secure User Store をコピーします。 この場合、"ルート" ユーザーが既に Secure User Store を構成していることが前提となります。
    > 「[SAP HANA との通信を有効にする](#enable-communication-with-sap-hana)」セクションを参照してください。

    ```bash
    cp -pr ~/.hdb /home/azacsnap/.
    ```

1. `hdbuserstore` ファイルに対してユーザー アクセス許可を正しく設定します

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/.hdb
    ```

1. スナップショット ツールを /home/azacsnap/bin/ に抽出します。

    ```bash
    ./azacsnap_installer_v5.0.run -X -d /home/azacsnap/bin
    ```

1. コマンドを実行可能にします

    ```bash
    chmod 700 /home/azacsnap/bin/*
    ```

1. ユーザーのホーム ディレクトリに適切な所有権のアクセス許可が確実に設定されるようにします

    ```bash
    chown -R azacsnap.sapsys /home/azacsnap/*
    ```

### <a name="complete-the-setup-of-snapshot-tools"></a>スナップショット ツールの設定を完了する

インストーラーにより、スナップショット ツールのインストールが完了した後に行う手順が示されます。
スナップショット ツールを構成してテストするには、これらの手順に従います。  テストが正常に行われた後に、最初のデータベース整合性ストレージ スナップショットを実行します。

次の出力は、既定のインストール オプションを使用してインストーラーを実行した後に行う手順を示しています。

1. スナップショット ユーザー アカウントに変更する
    1. `su - azacsnap`
1. HANA Secure User Store を設定する
   1. `hdbuserstore Set <ADMIN_USER> <HOSTNAME>:<PORT> <admin_user> <password>`
1. コマンドの場所に変更する
   1. `cd /home/azacsnap/bin/`
1. 顧客の詳細ファイルを構成する
   1. `azacsnap -c configure –-configuration new`
1. ストレージへの接続をテストする....
   1. `azacsnap -c test –-test storage`
1. HANA への接続をテストする....
    1. SSL を使用しない場合
       1. `azacsnap -c test –-test hana`
    1. SSL を使用する場合は、適切な SSL オプションを選択する必要があります
       1. `azacsnap -c test –-test hana --ssl=<commoncrypto|openssl>`
1. 最初のスナップショット バックアップを実行する
    1. `azacsnap -c backup –-volume data--prefix=hana_test --retention=1`

手順 2 は、インストール前に [SAP HANA との通信を有効にする](#enable-communication-with-sap-hana)作業が行われなかった場合に必要になります。

> [!NOTE]
> テスト コマンドが正しく実行される必要があります。 それ以外の場合、コマンドが失敗するおそれがあります。

## <a name="configuring-the-database"></a>データベースの構成

このセクションでは、データベースの構成方法について説明します。

### <a name="sap-hana-configuration"></a>SAP HANA の構成

SAP HANA には、ログ バックアップとカタログを確実に保護するために推奨される変更がいくつか適用されます。 既定では、`basepath_logbackup` および `basepath_catalogbackup` によりそれぞれのファイルが `$(DIR_INSTANCE)/backup/log` ディレクトリに出力されます。このパスは、`azacsnap` がスナップショットを作成するように構成されているボリューム上にある可能性はなく、これらのファイルはストレージ スナップショットでは保護されません。

次の `hdbsql` コマンドの例は、`azacsnap` によってスナップショットを作成できるストレージ ボリューム上の場所にログおよびカタログのパスを設定する方法を示すためのものです。 必ず、コマンド ラインの値がローカルの SAP HANA の構成と一致していることをご確認ください。

### <a name="configure-log-backup-location"></a>ログ バックアップの場所を構成する

この例では、`basepath_logbackup` パラメーターに変更が加えられています。

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_logbackup') = '/hana/logbackups/H80' WITH RECONFIGURE"
```

### <a name="configure-catalog-backup-location"></a>カタログ バックアップの場所を構成する

この例では、`basepath_catalogbackup` パラメーターに変更が加えられています。 最初に、ファイルシステムに `basepath_catalogbackup` パスが確実に存在していることを確認します。存在しない場合は、ディレクトリと同じ所有権でパスを作成します。

```bash
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

パスを作成する必要がある場合、次の例ではパスを作成して適切な所有権とアクセス許可を設定します。 これらのコマンドはルートとして実行する必要があります。

```bash
mkdir /hana/logbackups/H80/catalog
chown --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
chmod --reference=/hana/shared/H80/HDB00 /hana/logbackups/H80/catalog
ls -ld /hana/logbackups/H80/catalog
```

```output
drwxr-x--- 4 h80adm sapsys 4096 Jan 17 06:55 /hana/logbackups/H80/catalog
```

次の例では、SAP HANA の設定を変更します。

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'basepath_catalogbackup') = '/hana/logbackups/H80/catalog' WITH RECONFIGURE"
```

### <a name="check-log-and-catalog-backup-locations"></a>ログおよびカタログのバックアップの場所を確認する

上記の変更を行った後、設定が正しいことを次のコマンドで確認します。
この例では、上記のガイダンスに従って行われた設定が SYSTEM 設定として表示されます。

> このクエリの場合、比較のために DEFAULT 設定も返されます。

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where (key = 'basepath_databackup' or key ='basepath_datavolumes' or key = 'basepath_logbackup' or key = 'basepath_logvolumes' or key = 'basepath_catalogbackup')"
```

```output
global.ini,DEFAULT,,,persistence,basepath_catalogbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_databackup,$(DIR_INSTANCE)/backup/data
global.ini,DEFAULT,,,persistence,basepath_datavolumes,$(DIR_GLOBAL)/hdb/data
global.ini,DEFAULT,,,persistence,basepath_logbackup,$(DIR_INSTANCE)/backup/log
global.ini,DEFAULT,,,persistence,basepath_logvolumes,$(DIR_GLOBAL)/hdb/log
global.ini,SYSTEM,,,persistence,basepath_catalogbackup,/hana/logbackups/H80/catalog
global.ini,SYSTEM,,,persistence,basepath_datavolumes,/hana/data/H80
global.ini,SYSTEM,,,persistence,basepath_logbackup,/hana/logbackups/H80
global.ini,SYSTEM,,,persistence,basepath_logvolumes,/hana/log/H80
```

### <a name="configure-log-backup-timeout"></a>ログ バックアップのタイムアウトを構成する

ログ バックアップを実行する SAP HANA の既定の設定は、900 秒 (15 分) です。 この値を 300 秒 (つまり、5 分) に減らすことをお勧めします。  その後、構成ファイルのその他のボリューム セクションに log_backups ボリュームを追加して、定期的なバックアップを (たとえば、10 分ごとに) 実行することができます。

```bash
hdbsql -jaxC -n <HANA_ip_address>:30013 -i 00 -u SYSTEM -p <SYSTEM_USER_PASSWORD> "ALTER SYSTEM ALTER CONFIGURATION ('global.ini', 'SYSTEM') SET ('persistence', 'log_backup_timeout_s') = '300' WITH RECONFIGURE"
```

#### <a name="check-log-backup-timeout"></a>ログ バックアップのタイムアウトを確認する

ログ バックアップのタイムアウトを変更した後、確実にこれが次のように設定されていることを確認します。
この例では、行われた設定が SYSTEM 設定として表示されますが、このクエリの場合も比較のために DEFAULT 設定が返されます。

```bash
hdbsql -jaxC -n <HANA_ip_address> - i 00 -U AZACSNAP "select * from sys.m_inifile_contents where key like '%log_backup_timeout%' "
```

```output
global.ini,DEFAULT,,,persistence,log_backup_timeout_s,900
global.ini,SYSTEM,,,persistence,log_backup_timeout_s,300
```

## <a name="next-steps"></a>次のステップ

- [Azure アプリケーション整合性スナップショット ツールを構成する](azacsnap-cmd-ref-configure.md)
