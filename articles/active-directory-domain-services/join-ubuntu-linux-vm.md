---
title: Azure Active Directory Domain Services:Ubuntu VM をマネージド ドメインに参加させる | Microsoft Docs
description: Ubuntu Linux 仮想マシンを Azure AD Domain Services に参加させる
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 29a6cb69a818ed11e5f20dddd7299c01fbefbf47
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234011"
---
# <a name="join-an-ubuntu-virtual-machine-in-azure-to-a-managed-domain"></a>Azure 内の Ubuntu 仮想マシンをマネージド ドメインに参加させる
この記事では、Ubuntu Linux 仮想マシンを Azure AD Domain Services のマネージド ドメインに参加させる方法について説明します。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>開始する前に
この記事に記載されているタスクを実行するには、次が必要です。  
1. 有効な **Azure サブスクリプション**。
2. オンプレミス ディレクトリまたはクラウド専用ディレクトリのいずれかと同期されている **Azure AD ディレクトリ** 。
3. **Azure AD ドメイン サービス** が Azure AD ディレクトリに対して有効である必要があります。 有効になっていない場合は、 [作業の開始に関するガイド](create-instance.md)に記載されているすべてのタスクを実行してください。
4. マネージド ドメインの IP アドレスを、必ず仮想ネットワークの DNS サーバーとして構成します。 詳しくは、[Azure 仮想ネットワークの DNS 設定を更新する方法](active-directory-ds-getting-started-dns.md)に関するページをご覧ください。
5. [Azure AD Domain Services のマネージド ドメインとのパスワードの同期](active-directory-ds-getting-started-password-sync.md)に必要な手順をすべて実行します。


## <a name="provision-an-ubuntu-linux-virtual-machine"></a>Ubuntu Linux 仮想マシンをプロビジョニングする
次のいずれかの方法を使用して、Azure で Ubuntu Linux 仮想マシンをプロビジョニングします。
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * **Azure AD Domain Services を有効にしたのと同じ仮想ネットワーク**に、仮想マシンをデプロイします。
> * Azure AD Domain Services を有効にしたサブネットとは**異なるサブネット**を選択します。
>


## <a name="connect-remotely-to-the-ubuntu-linux-virtual-machine"></a>Ubuntu Linux 仮想マシンにリモートで接続する
Ubuntu 仮想マシンが Azure でプロビジョニングされました。 次は、VM のプロビジョニング中に作成したローカル管理者アカウントを使用して、仮想マシンにリモートで接続します。

[Linux が実行されている仮想マシンにログオンする方法](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事の手順に従ってください。


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Linux 仮想マシン上の hosts ファイルを構成する
SSH ターミナルで /etc/hosts ファイルを編集し、ご自分のマシンの IP アドレスとホスト名を更新します。

```console
sudo vi /etc/hosts
```

hosts ファイルに、次の値を入力します。

```console
127.0.0.1 contoso-ubuntu.contoso100.com contoso-ubuntu
```

ここで、"contoso100.com" は、マネージド ドメインの DNS ドメイン名です。 "contoso-ubuntu" は、マネージド ドメインに参加させる Ubuntu 仮想マシンのホスト名です。


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Linux 仮想マシンに必要なパッケージのインストール
次は、仮想マシンでのドメイン参加に必要なパッケージをインストールします。 次の手順に従います。

1.  SSH ターミナルで、次のコマンドを入力して、リポジトリからパッケージ一覧をダウンロードします。 このコマンドはパッケージ一覧を更新して、最新バージョンのパッケージとその依存関係についての情報を取得します。

    ```console
    sudo apt-get update
    ```

2. 次のコマンドを入力して、必要なパッケージをインストールします。

    ```console
      sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
    ```

3. Kerberos のインストール中に、ピンク色の画面が表示されます。 "krb5-user" パッケージのインストールで、領域名 (すべて大文字) の入力を求めるメッセージが表示されます。 インストールによって、/etc/krb5.conf に [realm] セクションと [domain_realm] セクションが書き込まれます。

    > [!TIP]
    > マネージド ドメインの名前が contoso100.com の場合は、領域として「CONTOSO100.COM」を入力します。 領域名は大文字で指定する必要があることを忘れないでください。


## <a name="configure-the-ntp-network-time-protocol-settings-on-the-linux-virtual-machine"></a>Linux 仮想マシンで NTP (ネットワーク タイム プロトコル) 設定を構成する
Ubuntu VM の日付と時刻は、マネージド ドメインと同期させる必要があります。 /etc/ntp.conf ファイルに、マネージド ドメインの NTP ホスト名を追加します。

```console
sudo vi /etc/ntp.conf
```

ntp.conf ファイルに次の値を入力し、ファイルを保存します。

```console
server contoso100.com
```

ここで、"contoso100.com" は、マネージド ドメインの DNS ドメイン名です。

次は、Ubuntu VM の日付と時刻を NTP サーバーと同期させてから、NTP サービスを開始します。

```console
sudo systemctl stop ntp
sudo ntpdate contoso100.com
sudo systemctl start ntp
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Linux 仮想マシンのマネージド ドメインへの参加
Linux 仮想マシンに必要なパッケージがインストールされたら、続いて仮想マシンをマネージド ドメインに参加させます。

1. AAD ドメイン サービスのマネージド ドメインを探します。 SSH ターミナルで、次のコマンドを入力します。

    ```console
    sudo realm discover CONTOSO100.COM
    ```

   > [!NOTE]
   > **トラブルシューティング:** *realm discover* でマネージド ドメインが見つからない場合:
   >   * ドメインに仮想マシンからアクセスできることを確認します (ping の試行)。
   >   * 仮想マシンが、マネージド ドメインが利用可能な同じ仮想ネットワークにデプロイされていることを確認します。
   >   * マネージド ドメインのドメイン コントローラーを指すように、仮想ネットワークの DNS サーバー設定を更新したかどうかを確認します。

2. Kerberos を初期化します。 SSH ターミナルで、次のコマンドを入力します。

    > [!TIP]
    > * ”AAD DC 管理者” グループに所属するユーザーを指定します。
    > * kinit のエラーを防ぐため、ドメイン名は必ず大文字で指定します。
    >

    ```console
    kinit bob@CONTOSO100.COM
    ```

3. コンピューターをドメインに参加させます。 SSH ターミナルで、次のコマンドを入力します。

    > [!TIP]
    > 前の手順で指定したユーザー アカウントを使用します ("kinit")。

    ```console
    sudo realm join --verbose CONTOSO100.COM -U 'bob@CONTOSO100.COM' --install=/
    ```

コンピューターのマネージド ドメインへの参加が完了すると、「Successfully enrolled machine in realm (コンピューターは領域に正常に登録されました)」という旨のメッセージが表示されます。


## <a name="update-the-sssd-configuration-and-restart-the-service"></a>SSSD 構成を更新してサービスを再起動する
1. SSH ターミナルで、次のコマンドを入力します。 sssd.conf ファイルを開いて、次の変更を加えます。
    
    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

2. **use_fully_qualified_names = True** という行をコメント アウトし、ファイルを保存します。
    
    ```console
    # use_fully_qualified_names = True
    ```

3. SSSD サービスを再起動します。
    
    ```console
    sudo service sssd restart
    ```


## <a name="configure-automatic-home-directory-creation"></a>自動ホーム ディレクトリ作成を構成する
ユーザーにログインした後にホーム ディレクトリの自動作成を有効にするには、PuTTY ターミナルで次のコマンドを入力します。

```console
sudo vi /etc/pam.d/common-session
```

このファイルで、"session optional pam_sss.so" という行の下に次の行を追加し、保存します。

```console
session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
```


## <a name="verify-domain-join"></a>ドメイン参加の確認
マシンがマネージド ドメインに正常に参加したかどうかを確認してみましょう。 別の SSH 接続を使用して、ドメインに参加した Ubuntu VM に接続します。 ドメイン ユーザー アカウントを使用して、そのユーザー アカウントが正しく解決されているかどうかを確認します。

1. SSH ターミナルで次のコマンドを入力し、SSH を使用して、ドメインに参加した Ubuntu 仮想マシンに接続します。 マネージド ドメインに属するドメイン アカウントを使用します (例: ここでは 'bob@CONTOSO100.COM')。
    
    ```console
    ssh -l bob@CONTOSO100.COM contoso-ubuntu.contoso100.com
    ```

2. SSH ターミナルで次のコマンドを入力し、ホーム ディレクトリが正しく初期化されているかどうかを確認します。
    
    ```console
    pwd
    ```

3. SSH ターミナルで次のコマンドを入力し、グループ メンバーシップが正しく解決されているかどうかを確認します。
    
    ```console
    id
    ```


## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>"AAD DC Administrators" グループに sudo 特権を付与する
"AAD DC Administrators" グループのメンバーに Ubuntu VM での管理特権を付与できます。 sudo ファイルは、/etc/sudoers にあります。 sudoers に追加された AD グループのメンバーは、sudo を実行できます。

1. SSH ターミナルに、スーパーユーザー特権でログインしていることを確認します。 VM の作成中に指定した、ローカル管理者アカウントを使用することができます。 次のコマンドを実行します。
    
    ```console
    sudo vi /etc/sudoers
    ```

2. /etc/sudoers ファイルに次のエントリを追加し、保存します。
    
    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

3. これで、"AAD DC Administrators" グループのメンバーとしてログインできるようになりました。VM での管理特権が必要となります。


## <a name="troubleshooting-domain-join"></a>ドメイン参加のトラブルシューティング
「 [Troubleshooting domain join (ドメイン参加のトラブルシューティング)](join-windows-vm.md#troubleshoot-joining-a-domain) 」を参照してください。


## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービス - 作業開始ガイド](create-instance.md)
* [Azure AD Domain Services のマネージド ドメインに Windows Server 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)
* [Linux が実行されている仮想マシンにログオンする方法](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
