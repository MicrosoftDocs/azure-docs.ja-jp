---
title: Azure Active Directory Domain Services:RHEL VM をマネージド ドメインに参加させる | Microsoft Docs
description: Red Hat Enterprise Linux 仮想マシンのAzure AD ドメイン サービスへの参加
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: d76ae997-2279-46dd-bfc5-c0ee29718096
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: b59bd7c7196ceb87da087967498eca6dda7c212b
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/23/2019
ms.locfileid: "69990599"
---
# <a name="join-a-red-hat-enterprise-linux-7-virtual-machine-to-a-managed-domain"></a>Red Hat Enterprise Linux 7 仮想マシンのマネージド ドメインへの参加
この記事では、Red Hat Enterprise Linux (RHEL) 7 仮想マシンを Azure AD Domain Services のマネージド ドメインに参加させる方法について説明します。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>開始する前に
この記事に記載されているタスクを実行するには、次が必要です。  
1. 有効な **Azure サブスクリプション**。
2. オンプレミス ディレクトリまたはクラウド専用ディレクトリのいずれかと同期されている **Azure AD ディレクトリ** 。
3. **Azure AD ドメイン サービス** が Azure AD ディレクトリに対して有効である必要があります。 有効になっていない場合は、 [作業の開始に関するガイド](tutorial-create-instance.md)に記載されているすべてのタスクを実行してください。
4. マネージド ドメインの IP アドレスを、必ず仮想ネットワークの DNS サーバーとして構成します。 詳しくは、[Azure 仮想ネットワークの DNS 設定を更新する方法](tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network)に関するページをご覧ください。
5. [Azure AD Domain Services のマネージド ドメインとのパスワードの同期](tutorial-create-instance.md#enable-user-accounts-for-azure-ad-ds)に必要な手順をすべて実行します。


## <a name="provision-a-red-hat-enterprise-linux-virtual-machine"></a>Red Hat Enterprise Linux 仮想マシンのプロビジョニング
次のいずれかの方法を使用して、Azure で RHEL 7 仮想マシンをプロビジョニングします。
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

> [!IMPORTANT]
> * **Azure AD Domain Services を有効にしたのと同じ仮想ネットワーク**に、仮想マシンをデプロイします。
> * Azure AD Domain Services を有効にしたサブネットとは**異なるサブネット**を選択します。
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>新しくプロビジョニングされた Linux 仮想マシンへのリモート接続
RHEL 7.2 仮想マシンの Azure でのプロビジョニングが完了しました。 次は、VM のプロビジョニング中に作成したローカル管理者アカウントを使用して、仮想マシンにリモートで接続します。

[Linux が実行されている仮想マシンにサインインする方法](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事の手順に従ってください。


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Linux 仮想マシン上の hosts ファイルを構成する
SSH ターミナルで /etc/hosts ファイルを編集し、ご自分のマシンの IP アドレスとホスト名を更新します。

```console
sudo vi /etc/hosts
```

hosts ファイルに、次の値を入力します。

```console
127.0.0.1 contoso-rhel.contoso.com contoso-rhel
```

ここで、"contoso.com" は、マネージド ドメインの DNS ドメイン名です。 "contoso rhel" は、マネージド ドメインに参加させる RHEL 仮想マシンのホスト名です。


## <a name="install-required-packages-on-the-linux-virtual-machine"></a>Linux 仮想マシンに必要なパッケージのインストール
次は、仮想マシンでのドメイン参加に必要なパッケージをインストールします。 SSH ターミナルで、次のコマンドを入力して、必要なパッケージをインストールします。

```console
sudo yum install realmd sssd krb5-workstation krb5-libs samba-common-tools
```


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Linux 仮想マシンのマネージド ドメインへの参加
Linux 仮想マシンに必要なパッケージがインストールされたら、続いて仮想マシンをマネージド ドメインに参加させます。

1. AAD ドメイン サービスのマネージド ドメインを探します。 SSH ターミナルで、次のコマンドを入力します。

    ```console
    sudo realm discover CONTOSO.COM
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

    ```console
    kinit bob@CONTOSO.COM
    ```

3. コンピューターをドメインに参加させます。 SSH ターミナルで、次のコマンドを入力します。

    > [!TIP]
    > 前の手順で指定したユーザー アカウントを使用します ("kinit")。
    >
    > VM がドメインに参加できない場合は、VM のネットワーク セキュリティ グループで、Azure AD DS マネージド ドメインの仮想ネットワーク サブネットに対して、TCP + UDP ポート 464 の送信 Kerberos トラフィックが許可されていることを確認します。

    ```console
    sudo realm join --verbose CONTOSO.COM -U 'bob@CONTOSO.COM'
    ```

コンピューターのマネージド ドメインへの参加が完了すると、「Successfully enrolled machine in realm (コンピューターは領域に正常に登録されました)」という旨のメッセージが表示されます。


## <a name="verify-domain-join"></a>ドメイン参加の確認
マシンがマネージド ドメインに正常に参加したかどうかを確認してみましょう。 異なる SSH 接続を使用して、ドメイン参加 RHEL VM に接続してください。 ドメイン ユーザー アカウントを使用して、ユーザー アカウントが正しく解決されているかどうかを確認します。

1. SSH ターミナルで次のコマンドを入力し、ドメインに参加した RHEL 仮想マシンに、SSH を使用して接続します。 マネージド ドメインに属するドメイン アカウントを使用します (例: ここでは 'bob@CONTOSO.COM')。
    
    ```console
    ssh -l bob@CONTOSO.COM contoso-rhel.contoso.com
    ```

2. SSH ターミナルで次のコマンドを入力し、ホーム ディレクトリが正しく初期化されているかどうかを確認します。
    
    ```console
    pwd
    ```

3. SSH ターミナルで次のコマンドを入力し、グループ メンバーシップが正しく解決されているかどうかを確認します。
    
    ```console
    id
    ```


## <a name="troubleshooting-domain-join"></a>ドメイン参加のトラブルシューティング
「 [Troubleshooting domain join (ドメイン参加のトラブルシューティング)](join-windows-vm.md#troubleshoot-domain-join-issues) 」を参照してください。

## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービス - 作業開始ガイド](tutorial-create-instance.md)
* [Azure AD Domain Services のマネージド ドメインに Windows Server 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)
* [Linux が実行されている仮想マシンにサインインする方法](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
* [Installing Kerberos (Kerberos のインストール)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Managing_Smart_Cards/installing-kerberos.html)
* [Red Hat Enterprise Linux 7 - Windows Integration Guide (Red Hat Enterprise Linux 7 - Windows 統合ガイド)](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/7/html/Windows_Integration_Guide/index.html)
