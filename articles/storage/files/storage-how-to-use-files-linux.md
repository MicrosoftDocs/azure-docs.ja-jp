---
title: Linux で Azure Files を使用する | Microsoft Docs
description: Linux で SMB 経由で Azure File 共有をマウントする方法について説明します。 前提条件の一覧を参照してください。 Linux クライアントでの SMB のセキュリティに関する考慮事項を確認します。
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d00b0558f85e18dfb53736d89fead953cc01ee60
ms.sourcegitcommit: 269da970ef8d6fab1e0a5c1a781e4e550ffd2c55
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/10/2020
ms.locfileid: "88053169"
---
# <a name="use-azure-files-with-linux"></a>Linux で Azure Files を使用する
[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 Azure ファイル共有は、[SMB カーネル クライアント](https://wiki.samba.org/index.php/LinuxCIFS)を使用して Linux ディストリビューションにマウントできます。 この記事では、Azure ファイル共有を `mount` コマンドを使用してオンデマンドでマウントするか、`/etc/fstab` にエントリを作成することで起動時にマウントするという 2 つの方法について説明します。

Linux で Azure ファイル共有をマウントするには、SMB 3.0 を使用することをお勧めします。 既定では、Azure Files は転送中の暗号化を必要としますが、これは SMB 3.0 でのみサポートされています。 Azure Files では SMB 2.1 (転送中の暗号化をサポートしていない) もサポートしていますが、セキュリティ上の理由から、別の Azure リージョンまたはオンプレミスから SMB 2.1 を使用して Azure ファイル共有をマウントすることはできません。 最近リリースされた Linux ディストリビューションでは SMB 3.0 がサポートされているため、アプリケーションで特別に必要とされるのでない限り、SMB 2.1 を使用する理由はほとんどありません。  

| Linux ディストリビューション | SMB 2.1 <br>(同じ Azure リージョン内の VM 上のマウント) | SMB 3.0 <br>(オンプレミスおよびクロスリージョンからのマウント) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| Red Hat Enterprise Linux (RHEL) | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | 10+ |
| openSUSE | 13.2+ | 42.3 以降 |
| SUSE Linux Enterprise Server | 12 以降 | 12 SP2+ |

上記の表に記載されていない Linux ディストリビューションを使用している場合は、Linux カーネル バージョンを確認することで、その Linux ディストリビューションで暗号化を使用した SMB 3.0 がサポートされているかどうかを確認できます。 暗号化を使用する SMB 3.0 が Linux カーネル バージョン 4.11 に追加されました。 `uname` コマンドにより、使用中の Linux カーネルのバージョンが返されます。

```bash
uname -r
```

## <a name="prerequisites"></a>前提条件
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**cifs-utils パッケージがインストールされていることを確認します。**  
    cifs-utils パッケージは、選択した Linux ディストリビューションのパッケージ マネージャーを使用してインストールできます。 

    **Ubuntu** と **Debian ベースの**ディストリビューションでは、`apt` パッケージ マネージャーを使用します。

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    **Fedora**、**Red Hat Enterprise Linux 8+**、および **CentOS 8+** では、`dnf` パッケージ マネージャーを使用します。

    ```bash
    sudo dnf install cifs-utils
    ```

    以前のバージョンの **Red Hat Enterprise Linux** と **CentOS** では、`yum` パッケージ マネージャーを使用します。

    ```bash
    sudo yum install cifs-utils 
    ```

    **openSUSE** では、`zypper` パッケージ マネージャーを使用します。

    ```bash
    sudo zypper install cifs-utils
    ```

    他のディストリビューションでは、適切なパッケージ マネージャーを使用するか、[ソースからコンパイル](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)します。

* **Azure コマンド ライン インターフェイス (CLI) の最新バージョン。** Azure CLI をインストールする方法の詳細については、[Azure CLI のインストール](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)に関するページを参照し、ご利用のオペレーティング システムを選択してください。 PowerShell 6 以降で Azure PowerShell モジュールを使用することは可能ですが、以下の手順は Azure CLI 用に提供されています。

* **ポート 445 が開いていることを確認する**: SMB は、TCP ポート 445 経由で通信します。ファイアウォールによってクライアント コンピューターの TCP ポート 445 がブロックされないことを確認してください。  **<your-resource-group>** と **<your-storage-account>** を置き換えます
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $smbPath | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    接続に成功した場合、次のような出力が表示されます。

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    企業ネットワーク上でポート 445 を開くことができない場合、または ISP によってブロックされている場合は、VPN 接続または ExpressRoute を使用してポート 445 を回避することができます。 詳細については、[直接の Azure ファイル共有アクセスのネットワークに関する考慮事項](storage-files-networking-overview.md)に関するページを参照してください。

## <a name="mounting-azure-file-share"></a>Azure ファイル共有のマウント
Linux ディストリビューションで Azure ファイル共有を使用するには、Azure ファイル共有のマウント ポイントとして機能するディレクトリを作成する必要があります。 マウント ポイントは Linux システム上のどこにでも作成できますが、一般的な規則では /mnt の下に作成します。 マウント ポイントの後、`mount` コマンドを使用して Azure ファイル共有にアクセスします。

必要に応じて、同じ Azure ファイル共有を複数のマウント ポイントにマウントできます。

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Azure ファイル共有を `mount` を使用してオンデマンドでマウントする
1. **マウント ポイントのフォルダーを作成します**。`<your-resource-group>`、`<your-storage-account>`、`<your-file-share>` をお使いの環境に適した情報に置き換えます。

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **mount コマンドを使用して Azure ファイル共有をマウントします**。 次の例では、ローカルの Linux ファイルとフォルダーのアクセス許可の既定値は 0755 です。これは、所有者 (ファイル/ディレクトリの Linux 所有者に基づく) の読み取り、書き込み、および実行、所有者グループのユーザーの読み取りおよび実行、システム上の他のユーザーの読み取りおよび実行を意味します。 `uid` および `gid` マウント オプションを使用して、マウントのユーザー ID とグループ ID を設定できます。 また、`dir_mode` および `file_mode` を使用して、必要に応じてカスタム アクセス許可を設定することもできます。 アクセス許可を設定する方法の詳細については、Wikipedia の「[UNIX numeric notation](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation)」 (UNIX の数値表記) を参照してください。 

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > 上記の mount コマンドは、SMB 3.0 でマウントされます。 Linux ディストリビューションで暗号化を使用した SMB 3.0 をサポートしていない場合、または SMB 2.1 のみをサポートしている場合は、ストレージ アカウントと同じリージョン内の Azure VM からのみマウントできます。 暗号化を使用した SMB 3.0 をサポートしていない Linux ディストリビューションで Azure ファイル共有をマウントするには、[ストレージ アカウントの転送中の暗号化を無効にする](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)必要があります。

Azure ファイル共有の使用を完了したら、`sudo umount $mntPath` を使用して共有を解除できます。

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>`/etc/fstab` を使って Azure ファイル共有の永続的なマウント ポイントを作成する
1. **マウント ポイントのフォルダーを作成します**。マウント ポイント用のフォルダーはファイル システム上のどこにでも作成できますが、一般的な規則では /mnt の下に作成します。 たとえば、次のコマンドは新しいディレクトリを作成し、`<your-resource-group>`、`<your-storage-account>`、`<your-file-share>` をお使いの環境に適した情報に置き換えます。

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **共有ファイル用のユーザー名 (ストレージ アカウント名) とパスワード (ストレージ アカウント キー) を格納する資格情報ファイルを作成します。** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **資格情報ファイルのアクセス許可を変更して、ルートのみがパスワード ファイルの読み取りと変更を実行できるようにします。** ストレージ アカウント キーは、本質的にはストレージ アカウント キーのスーパー管理者パスワードであるため、ルートのみがアクセスできるようにファイルのアクセス許可を設定して、特権の低いユーザーがストレージ アカウント キーを取得できないようにすることが重要です。   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **後続のコマンドを使用して、`/etc/fstab`** に下記の行を追加します。次の例では、ローカルの Linux ファイルとフォルダーのアクセス許可の既定値は 0755 です。これは、所有者 (ファイル/ディレクトリの Linux 所有者に基づく) の読み取り、書き込み、および実行、所有者グループのユーザーの読み取りおよび実行、システム上の他のユーザーの読み取りおよび実行を意味します。 `uid` および `gid` マウント オプションを使用して、マウントのユーザー ID とグループ ID を設定できます。 また、`dir_mode` および `file_mode` を使用して、必要に応じてカスタム アクセス許可を設定することもできます。 アクセス許可を設定する方法の詳細については、Wikipedia の「[UNIX numeric notation](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation)」 (UNIX の数値表記) を参照してください。

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > 上記の mount コマンドは、SMB 3.0 でマウントされます。 Linux ディストリビューションで暗号化を使用した SMB 3.0 をサポートしていない場合、または SMB 2.1 のみをサポートしている場合は、ストレージ アカウントと同じリージョン内の Azure VM からのみマウントできます。 暗号化を使用した SMB 3.0 をサポートしていない Linux ディストリビューションで Azure ファイル共有をマウントするには、[ストレージ アカウントの転送中の暗号化を無効にする](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)必要があります。

### <a name="using-autofs-to-automatically-mount-the-azure-file-shares"></a>autofs を使用した Azure ファイル共有の自動マウント

1. **autofs パッケージがインストールされていることを確認します。**  

    autofs パッケージは、任意の Linux ディストリビューションのパッケージ マネージャーを使用してインストールできます。 

    **Ubuntu** と **Debian ベースの**ディストリビューションでは、`apt` パッケージ マネージャーを使用します。
    ```bash
    sudo apt update
    sudo apt install autofs
    ```
    **Fedora**、**Red Hat Enterprise Linux 8+**、および **CentOS 8+** では、`dnf` パッケージ マネージャーを使用します。
    ```bash
    sudo dnf install autofs
    ```
    以前のバージョンの **Red Hat Enterprise Linux** と **CentOS** では、`yum` パッケージ マネージャーを使用します。
    ```bash
    sudo yum install autofs 
    ```
    **openSUSE** では、`zypper` パッケージ マネージャーを使用します。
    ```bash
    sudo zypper install autofs
    ```
2. **共有のマウント ポイントを作成します。**
   ```bash
    sudo mkdir /fileshares
    ```
3. **新しいカスタム autofs 構成ファイルを作成します。**
    ```bash
    sudo vi /etc/auto.fileshares
    ```
4. **/etc/auto.fileshares に次のエントリを追加します。**
   ```bash
   echo "$fileShareName -fstype=cifs,credentials=$smbCredentialFile :$smbPath"" > /etc/auto.fileshares
   ```
5. **/etc/auto.master に次のエントリを追加します。**
   ```bash
   /fileshares /etc/auto.fileshares --timeout=60
   ```
6. **autofs を再起動します。**
    ```bash
    sudo systemctl restart autofs
    ```
7.  **共有のために指定されたフォルダーにアクセスします。**
    ```bash
    cd /fileshares/$filesharename
    ```
## <a name="securing-linux"></a>Linux のセキュリティ保護
Linux で Azure ファイル共有をマウントするには、ポート 445 がアクセス可能な状態になっている必要があります。 ポート 445 は、SMB 1 に固有のセキュリティ リスクから、多くの組織でブロックされています。 SMB 1 は、数多くの Linux ディストリビューションに備わっているレガシ ファイル システム プロトコルで、CIFS (Common Internet File System) と呼ばれることもあります。 SMB 1 は、旧式で効率が悪く、また何よりもセキュリティに不安があるプロトコルです。 さいわいなことに、Azure Files は SMB 1 をサポートしておらず、Linux カーネル バージョン 4.18 以降では、Linux で SMB 1 を無効にすることができます。 運用環境で SMB ファイル共有を使用する前に、Linux クライアント上で SMB 1 を無効にすることを常に[強くお勧めします](https://aka.ms/stopusingsmb1)。

Linux カーネル 4.18 以降、レガシへの対応のために `cifs` と呼ばれる SMB カーネル モジュールは、`disable_legacy_dialects` と呼ばれる新しいモジュール パラメーター (多くの場合、各種の外部ドキュメントでは *parm* と呼ばれます) を公開しています。 Linux カーネル 4.18 で導入されましたが、一部のベンダーでは、サポート対象の旧カーネルにこの変更を移植しています。 便宜のため、次の表では、よく知られた Linux ディストリビューションでこのモジュール パラメーターを使用できるかどうかについて詳しく示しています。

| Distribution | SMB 1 を無効にできる |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | No |
| Ubuntu 18.04 | Yes |
| Ubuntu 19.04+ | Yes |
| Debian 8-9 | No |
| Debian 10+ | Yes |
| Fedora 29+ | Yes |
| CentOS 7 | No | 
| CentOS 8+ | Yes |
| Red Hat Enterprise Linux 6.x-7.x | No |
| Red Hat Enterprise Linux 8+ | Yes |
| openSUSE Leap 15.0 | No |
| openSUSE Leap 15.1+ | Yes |
| openSUSE Tumbleweed | Yes |
| SUSE Linux Enterprise 11.x-12.x | No |
| SUSE Linux Enterprise 15 | No |
| SUSE Linux Enterprise 15.1 | No |

次のコマンドを使用して、Linux ディストリビューションで `disable_legacy_dialects` モジュール パラメーターがサポートされているかどうかを確認できます。

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

このコマンドを実行すると、次のメッセージが出力されます。

```output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

SMB 1 を無効にする前に、SMB モジュールがご利用のシステムに現在読み込まれていないことを確認する必要があります (これは、SMB 共有をマウントしている場合に自動的に発生します)。 これを行うには、次のコマンドを使用します。SMB が読み込まれていない場合は、何も出力されません。

```bash
lsmod | grep cifs
```

モジュールをアンロードするには、最初にすべての SMB 共有のマウントを解除します (前述のように、`umount` コマンドを使用します)。 次のコマンドを使用して、システム上にマウントされているすべての SMB 共有を特定できます。

```bash
mount | grep cifs
```

すべての SMB ファイル共有のマウントを解除したら、モジュールをアンロードしても安全です。 そのためには、 `modprobe` コマンドを使用します。

```bash
sudo modprobe -r cifs
```

`modprobe` コマンドを使用して、SMB 1 をアンロードしてモジュールを手動で読み込むことができます。

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

最後に、`/sys/module/cifs/parameters` の読み込み済みパラメーターを参照すると、パラメーターを使用して SMB モジュールが読み込まれていることを確認できます。

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

Ubuntu および Debian ベースのディストリビューションで SMB 1 を永続的に無効にするには、設定を使用して `/etc/modprobe.d/local.conf` という新しいファイルを作成する必要があります (他のモジュールのカスタム オプションがまだない場合)。 これを行うには、次のコマンドを使います。

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

SMB モジュールを読み込むと、これが動作していることを確認できます。

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="next-steps"></a>次のステップ
Azure Files の詳細については、次のリンクをご覧ください。

* [Azure Files のデプロイの計画](storage-files-planning.md)
* [FAQ](../storage-files-faq.md)
* [トラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)
