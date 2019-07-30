---
title: Azure Active Directory Domain Services:CoreOS Linux VM をマネージド ドメインに参加させる | Microsoft Docs
description: CoreOS Linux 仮想マシンを Azure AD Domain Services に参加させる
services: active-directory-ds
documentationcenter: ''
author: iainfoulds
manager: daveba
editor: curtand
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: iainfou
ms.openlocfilehash: 93d8279c07c936d7e5ce2c7e756baadfbe4a1b0a
ms.sourcegitcommit: f811238c0d732deb1f0892fe7a20a26c993bc4fc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/29/2019
ms.locfileid: "67473309"
---
# <a name="join-a-coreos-linux-virtual-machine-to-a-managed-domain"></a>CoreOS Linux 仮想マシンをマネージド ドメインに参加させる
この記事では、Azure 内の CoreOS Linux 仮想マシンを Azure AD Domain Services のマネージド ドメインに参加させる方法について説明します。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>開始する前に
この記事に記載されているタスクを実行するには、次が必要です。
1. 有効な **Azure サブスクリプション**。
2. オンプレミス ディレクトリまたはクラウド専用ディレクトリのいずれかと同期されている **Azure AD ディレクトリ** 。
3. **Azure AD ドメイン サービス** が Azure AD ディレクトリに対して有効である必要があります。 有効になっていない場合は、 [作業の開始に関するガイド](create-instance.md)に記載されているすべてのタスクを実行してください。
4. マネージド ドメインの IP アドレスを、必ず仮想ネットワークの DNS サーバーとして構成します。 詳しくは、[Azure 仮想ネットワークの DNS 設定を更新する方法](active-directory-ds-getting-started-dns.md)に関するページをご覧ください。
5. [Azure AD Domain Services のマネージド ドメインとのパスワードの同期](active-directory-ds-getting-started-password-sync.md)に必要な手順をすべて実行します。


## <a name="provision-a-coreos-linux-virtual-machine"></a>CoreOS Linux 仮想マシンをプロビジョニングする
次のいずれかの方法を使用して、Azure で CoreOS 仮想マシンをプロビジョニングします。
* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

この記事では、Azure で **CoreOS Linux (Stable)** 仮想マシンのイメージを使用します。

> [!IMPORTANT]
> * **Azure AD Domain Services を有効にしたのと同じ仮想ネットワーク**に、仮想マシンをデプロイします。
> * Azure AD Domain Services を有効にしたサブネットとは**異なるサブネット**を選択します。
>


## <a name="connect-remotely-to-the-newly-provisioned-linux-virtual-machine"></a>新しくプロビジョニングされた Linux 仮想マシンへのリモート接続
CoreOS 仮想マシンが Azure でプロビジョニングされました。 次は、VM のプロビジョニング中に作成したローカル管理者アカウントを使用して、仮想マシンにリモートで接続します。

[Linux が実行されている仮想マシンにログオンする方法](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関する記事の手順に従ってください。


## <a name="configure-the-hosts-file-on-the-linux-virtual-machine"></a>Linux 仮想マシン上の hosts ファイルを構成する
SSH ターミナルで /etc/hosts ファイルを編集し、ご自分のマシンの IP アドレスとホスト名を更新します。

```
sudo vi /etc/hosts
```

hosts ファイルに、次の値を入力します。

```
127.0.0.1 contoso-coreos.contoso100.com contoso-coreos
```
ここで、"contoso100.com" は、マネージド ドメインの DNS ドメイン名です。 "contoso-coreos" は、マネージド ドメインに参加させる CoreOS 仮想マシンのホスト名です。


## <a name="configure-the-sssd-service-on-the-linux-virtual-machine"></a>Linux 仮想マシン上の SSSD サービスを構成する
次に、('/etc/sssd/sssd.conf') 内の SSSD 構成ファイルを次のサンプルに一致するように更新します。

 ```
 [sssd]
 config_file_version = 2
 services = nss, pam
 domains = CONTOSO100.COM

 [domain/CONTOSO100.COM]
 id_provider = ad
 auth_provider = ad
 chpass_provider = ad

 ldap_uri = ldap://contoso100.com
 ldap_search_base = dc=contoso100,dc=com
 ldap_schema = rfc2307bis
 ldap_sasl_mech = GSSAPI
 ldap_user_object_class = user
 ldap_group_object_class = group
 ldap_user_home_directory = unixHomeDirectory
 ldap_user_principal = userPrincipalName
 ldap_account_expire_policy = ad
 ldap_force_upper_case_realm = true
 fallback_homedir = /home/%d/%u

 krb5_server = contoso100.com
 krb5_realm = CONTOSO100.COM
 ```
"CONTOSO100.COM" をマネージド ドメインの DNS ドメイン名に置き換えます。 構成ファイル内では、ドメイン名は必ず大文字で指定してください。


## <a name="join-the-linux-virtual-machine-to-the-managed-domain"></a>Linux 仮想マシンのマネージド ドメインへの参加
Linux 仮想マシンに必要なパッケージがインストールされたら、続いて仮想マシンをマネージド ドメインに参加させます。

```
sudo adcli join -D CONTOSO100.COM -U bob@CONTOSO100.COM -K /etc/krb5.keytab -H contoso-coreos.contoso100.com -N coreos
```


> [!NOTE]
> **トラブルシューティング:** *adcli* でマネージド ドメインが見つからない場合:
>   * ドメインに仮想マシンからアクセスできることを確認します (ping の試行)。
>   * 仮想マシンが、マネージド ドメインが利用可能な同じ仮想ネットワークにデプロイされていることを確認します。
>   * マネージド ドメインのドメイン コントローラーを指すように、仮想ネットワークの DNS サーバー設定を更新したかどうかを確認します。

SSSD サービスを起動します。 SSH ターミナルで、次のコマンドを入力します。
  ```
  sudo systemctl start sssd.service
  ```


## <a name="verify-domain-join"></a>ドメイン参加の確認
マシンがマネージド ドメインに正常に参加したかどうかを確認してみましょう。 別の SSH 接続を使用して、ドメインに参加した CoreOS VM に接続します。 ドメイン ユーザー アカウントを使用して、そのユーザー アカウントが正しく解決されているかどうかを確認します。

1. SSH ターミナルで次のコマンドを入力し、SSH を使用して、ドメインに参加した CoreOS 仮想マシンに接続します。 マネージド ドメインに属するドメイン アカウントを使用します (例: ここでは 'bob@CONTOSO100.COM')。
    ```
    ssh -l bob@CONTOSO100.COM contoso-coreos.contoso100.com
    ```

2. SSH ターミナルで次のコマンドを入力し、ホーム ディレクトリが正しく初期化されているかどうかを確認します。
    ```
    pwd
    ```

3. SSH ターミナルで次のコマンドを入力し、グループ メンバーシップが正しく解決されているかどうかを確認します。
    ```
    id
    ```


## <a name="troubleshooting-domain-join"></a>ドメイン参加のトラブルシューティング
「 [Troubleshooting domain join (ドメイン参加のトラブルシューティング)](join-windows-vm.md#troubleshoot-joining-a-domain) 」を参照してください。

## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービス - 作業開始ガイド](create-instance.md)
* [Azure AD Domain Services のマネージド ドメインに Windows Server 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)
* [Linux が実行されている仮想マシンにログオンする方法](../virtual-machines/linux/mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
