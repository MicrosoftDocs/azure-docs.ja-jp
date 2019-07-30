---
title: Linux で Azure Files を使用する | Microsoft Docs
description: Linux で SMB 経由で Azure File 共有をマウントする方法について説明します。
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 03/29/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 06df5d403ba10489ea9a36a79a94f4b94782e4ef
ms.sourcegitcommit: a0b37e18b8823025e64427c26fae9fb7a3fe355a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/25/2019
ms.locfileid: "68501327"
---
# <a name="use-azure-files-with-linux"></a>Linux で Azure Files を使用する

[Azure Files](storage-files-introduction.md) は、Microsoft の使いやすいクラウド ファイル システムです。 Azure ファイル共有は、[SMB カーネル クライアント](https://wiki.samba.org/index.php/LinuxCIFS)を使用して Linux ディストリビューションにマウントできます。 この記事では、Azure ファイル共有を `mount` コマンドを使用してオンデマンドでマウントするか、`/etc/fstab` にエントリを作成することで起動時にマウントするという 2 つの方法について説明します。

> [!NOTE]  
> Azure ファイル共有がホストされている Azure リージョン以外の場所 (オンプレミスや他の Azure リージョンなど) に Azure File 共有をマウントするには、OS が SMB 3.0 の暗号化機能をサポートしている必要があります。

## <a name="prerequisites-for-mounting-an-azure-file-share-with-linux-and-the-cifs-utils-package"></a>Linux で cifs-utils パッケージを使用してAzure ファイル共有をマウントするための前提条件
<a id="smb-client-reqs"></a>

* **既存の Azure ストレージ アカウントおよびファイル共有**:この記事で説明している操作を完了するには、ストレージ アカウントとファイル共有を用意する必要があります。 まだ作成していない場合は、当該のクイックスタートを参照してください:[ファイル共有の作成 - CLI](storage-how-to-use-files-cli.md)。

* **ストレージ アカウント名とキー** この記事で説明している操作を完了するには、ストレージ アカウント名とキーが必要です。 CLI のクイック スタートを使用して既に作成している場合は、それらを使用できます。それ以外の場合は、上記のリンクに示した CLI クイック スタートを参照して、ストレージ アカウント キーを取得する方法について学習してください。

* **マウントのニーズに合わせて Linux ディストリビューションを選択する。**  
      Azure Files は、SMB 2.1 および SMB 3.0 経由のどちらかでマウントできます。 オンプレミスのクライアントから受信する接続や他の Azure リージョンでの接続については、SMB 3.0 が必要です。Azure Files は SMB 2.1 (または、暗号化を使用しない SMB 3.0) を拒否します。 同じ Azure リージョン内の VM から Azure ファイル共有にアクセスしようとしている場合は、Azure ファイル共有をホストしているストレージ アカウントで *[安全な転送が必須]* が無効にされている場合に限って、SMB 2.1 を使用してファイル共有にアクセスできます。 Microsoft では常に、安全な転送を必須として、暗号化した SMB 3.0 のみを使用することをお勧めしています。

    SMB 3.0 暗号化サポートは、Linux カーネル バージョン 4.11 で導入され、普及している Linux ディストリビューションのより古いカーネル バージョンにバックポートされました。 このドキュメントの公開時点では、Azure ギャラリーの以下のディストリビューションでは、テーブル ヘッダーで指定されたマウント オプションをサポートしています。 

### <a name="minimum-recommended-versions-with-corresponding-mount-capabilities-smb-version-21-vs-smb-version-30"></a>該当のマウント機能で推奨される最低限のバージョン (SMB バージョン 2.1 と SMB バージョン 3.0)

|   | SMB 2.1 <br>(同じ Azure リージョン内の VM 上のマウント) | SMB 3.0 <br>(オンプレミスおよびクロスリージョンからのマウント) |
| --- | :---: | :---: |
| Ubuntu Server | 14.04+ | 16.04+ |
| RHEL | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ |   |
| openSUSE | 13.2+ | 42.3 以降 |
| SUSE Linux Enterprise Server | 12 | 12 SP3+ |

お使いの Linux ディストリビューションがこの一覧にない場合は、次のコマンドを使用して Linux カーネル バージョンを参照し、チェックできます。

```bash
uname -r
```

* <a id="install-cifs-utils"></a>**cifs-utils パッケージがインストールされている。**  
    cifs-utils パッケージは、選択した Linux ディストリビューションのパッケージ マネージャーを使用してインストールできます。 

    **Ubuntu** と **Debian ベースの**ディストリビューションでは、`apt-get` パッケージ マネージャーを使用します。

    ```bash
    sudo apt-get update
    sudo apt-get install cifs-utils
    ```

    **RHEL** と **CentOS** では、`yum` パッケージ マネージャーを使用します。

    ```bash
    sudo yum install cifs-utils
    ```

    **openSUSE** では、`zypper` パッケージ マネージャーを使用します。

    ```bash
    sudo zypper install cifs-utils
    ```

    他のディストリビューションでは、適切なパッケージ マネージャーを使用するか、[ソースからコンパイル](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download)します。

* **マウントされた共有のディレクトリ/ファイルのアクセス許可について決定する**: 下記の例では、アクセス許可 `0777` を使用して、すべてのユーザーに読み取り、書き込み、および実行のアクセス許可を与えています。 必要に応じて、他の [chmod アクセス許可](https://en.wikipedia.org/wiki/Chmod)に置き換えることができますが、それは潜在的にアクセスを制限することを意味します。 他のアクセス許可を使用する場合は、選択したローカルのユーザーとグループへのアクセスを保持するために、uid と gid の使用も検討する必要があります。

> [!NOTE]
> dir_mode と file_mode を使用してディレクトリとファイルのアクセス許可を明示的に割り当てない場合、それらは既定値の 0755 になります。

* **ポート 445 が開いていることを確認する**: SMB は、TCP ポート 445 経由で通信します。ファイアウォールがクライアント マシンの TCP ポート 445 をブロックしていないことを確認してください。

## <a name="mount-the-azure-file-share-on-demand-with-mount"></a>Azure ファイル共有を `mount` を使用してオンデマンドでマウントする

1. **[使用する Linux ディストリビューション用の cifs-utils パッケージをインストールします](#install-cifs-utils)** 。

1. **マウント ポイントのフォルダーを作成します**。マウント ポイント用のフォルダーはファイル システム上の任意の場所に作成できますが、一般的な方法ではこれを新しいフォルダーの下に作成します。 たとえば、次のコマンドは新しいディレクトリを作成し、 **<storage_account_name>** と **<file_share_name>** を環境の適切な情報に置き換えます。

    ```bash
    mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **mount コマンドを使用して Azure ファイル共有をマウントします**。 **<storage_account_name>** 、 **<share_name>** 、 **<smb_version>** 、 **<storage_account_key>** 、および **<mount_point>** を環境の適切な情報に置き換えることを忘れないでください。 Linux ディストリビューションで暗号化付き SMB 3.0 がサポートされている場合は (詳細については [SMB クライアントの要件の概要](#smb-client-reqs)を参照)、 **<smb_version>** として **3.0** を使用します。 暗号化付き SMB 3.0 をサポートしていない Linux ディストリビューションの場合は、 **<smb_version>** として **2.1** を使用します。 Azure ファイル共有は、SMB 3.0 がある Azure リージョン (オンプレミスを含むか、または別の Azure リージョン内にある) の外部でしかマウントできません。 必要な場合は、マウントされた共有のディレクトリとファイルのアクセス許可を変更できますが、それはアクセスを制限することを意味します。

    ```bash
    sudo mount -t cifs //<storage_account_name>.file.core.windows.net/<share_name> <mount_point> -o vers=<smb_version>,username=<storage_account_name>,password=<storage_account_key>,dir_mode=0777,file_mode=0777,serverino
    ```

> [!Note]  
> Azure ファイル共有の使用を完了したら、`sudo umount <mount_point>` を使用して共有を解除できます。

## <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>`/etc/fstab` を使って Azure ファイル共有の永続的なマウント ポイントを作成する

1. **[使用する Linux ディストリビューション用の cifs-utils パッケージをインストールします](#install-cifs-utils)** 。

1. **マウント ポイントのフォルダーを作成します**。マウント ポイント用のフォルダーはファイル システム上の任意の場所に作成できますが、一般的な方法ではこれを新しいフォルダーの下に作成します。 これをどこに作成する場合でも、フォルダーの絶対パスをメモしてください。 たとえば、次のコマンドは新しいディレクトリを作成し、 **<storage_account_name>** と **<file_share_name>** を環境の適切な情報に置き換えます。

    ```bash
    sudo mkdir -p <storage_account_name>/<file_share_name>
    ```

1. **共有ファイル用のユーザー名 (ストレージ アカウント名) とパスワード (ストレージ アカウント キー) を格納する資格情報ファイルを作成します。** **<storage_account_name>** と **<storage_account_key>** を環境の適切な情報に置き換えます。

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
    fi
    if [ ! -f "/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred" ]; then
    sudo bash -c 'echo "username=<STORAGE ACCOUNT NAME>" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    sudo bash -c 'echo "password=7wRbLU5ea4mgc<DRIVE LETTER>PIpUCNcuG9gk2W4S2tv7p0cTm62wXTK<DRIVE LETTER>CgJlBJPKYc4VMnwhyQd<DRIVE LETTER>UT<DRIVE LETTER>yR5/RtEHyT/EHtg2Q==" >> /etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred'
    fi
    ```

1. **資格情報ファイルのアクセス許可を変更して、ルートのみがパスワード ファイルの読み取りと変更を実行できるようにします。** ストレージ アカウント キーは、本質的にはストレージ アカウント キーのスーパー管理者パスワードであるため、ルートのみがアクセスできるようにファイルのアクセス許可を設定して、特権の低いユーザーがストレージ アカウント キーを取得できないようにすることが重要です。   

    ```bash
    sudo chmod 600 /etc/smbcredentials/<storage_account_name>.cred
    ```

1. **後続のコマンドを使用して、`/etc/fstab`** に下記の行を追加します。 **<storage_account_name>** 、 **<share_name>** 、 **<smb_version>** 、および **<mount_point>** を環境の適切な情報に置き換えることを忘れないでください。 Linux ディストリビューションで暗号化付き SMB 3.0 がサポートされている場合は (詳細については [SMB クライアントの要件の概要](#smb-client-reqs)を参照)、 **<smb_version>** として **3.0** を使用します。 暗号化付き SMB 3.0 をサポートしていない Linux ディストリビューションの場合は、 **<smb_version>** として **2.1** を使用します。 Azure ファイル共有は、SMB 3.0 がある Azure リージョン (オンプレミスを含むか、または別の Azure リージョン内にある) の外部でしかマウントできません。

    ```bash
    sudo bash -c 'echo "//<STORAGE ACCOUNT NAME>.file.core.windows.net/<FILE SHARE NAME> /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME> cifs _netdev,nofail,vers=3.0,credentials=/etc/smbcredentials/<STORAGE ACCOUNT NAME>.cred,dir_mode=0777,file_mode=0777,serverino" >> /etc/fstab'

    sudo mount /mount/<STORAGE ACCOUNT NAME>/<FILE SHARE NAME>
    ```

> [!Note]  
> `/etc/fstab` を編集した後、再起動する代わりに、`sudo mount -a` を使用して、Azure ファイル共有をマウントできます。

## <a name="feedback"></a>フィードバック

Linux ユーザーからのご意見をお待ちしています。

Azure Files for Linux ユーザーのグループによって、File Storage を Linux で評価および導入するときにフィードバックを共有できるフォーラムが提供されています。 [Azure Files Linux Users](mailto:azurefileslinuxusers@microsoft.com) にメールを送信して、ユーザー グループに参加してください。

## <a name="next-steps"></a>次の手順

Azure Files の詳細については、次のリンクをご覧ください。

* [Azure Files のデプロイの計画](storage-files-planning.md)
* [FAQ](../storage-files-faq.md)
* [トラブルシューティング](storage-troubleshoot-linux-file-connection-problems.md)
