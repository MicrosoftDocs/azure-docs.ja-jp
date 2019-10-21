---
title: CoreOS VM を Azure AD Domain Services に参加させる | Microsoft Docs
description: CoreOS 仮想マシンを構成して Azure AD Domain Services のマネージド ドメインに参加させる方法について説明します。
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 5db65f30-bf69-4ea3-9ea5-add1db83fdb8
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 09/14/2019
ms.author: iainfou
ms.openlocfilehash: c0c298a9aa0b9d46ec2c7510cdb5c3ba1c8c84af
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71075548"
---
# <a name="join-a-coreos-virtual-machine-to-an-azure-ad-domain-services-managed-domain"></a>CoreOS 仮想マシンを Azure AD Domain Services のマネージド ドメインに参加させる

ユーザーが 1 セットの資格情報を使用して Azure の仮想マシン (VM) にサインインできるようにするには、Azure Active Directory Domain Services (AD DS) のマネージド ドメインに VM を参加させます。 VM を Azure AD DS のマネージド ドメインに参加させると、ドメインのユーザー アカウントと資格情報を使用して、サーバーにサインインして管理することができます。 Azure AD DS マネージド ドメインのグループ メンバーシップも適用され、VM 上のファイルまたはサービスへのアクセスを制御できるようになります。

この記事では、CoreOS VM を Azure AD DS のマネージド ドメインに参加させる方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要であれば、1 つ目のチュートリアルで [Azure Active Directory Domain Services インスタンスを作成して構成][create-azure-ad-ds-instance]します。
* Azure AD テナントの *Azure AD DC administrators* グループのメンバーであるユーザー アカウント。

## <a name="create-and-connect-to-a-coreos-linux-vm"></a>CoreOS Linux VM を作成してそれに接続する

Azure に CoreOS Linux VM が既にある場合は、SSH を使用してそれに接続した後、次の手順に進んで [VM の構成を開始](#configure-the-hosts-file)します。

CoreOS Linux VM を作成する必要がある場合、またはこの記事で使用するためのテスト VM を作成する場合は、次のいずれかの方法を使用できます。

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

VM を作成するときは、VM が Azure AD DS マネージド ドメインと通信できるように、仮想ネットワークの設定に注意してください。

* Azure AD Domain Services を有効にしたのと同じ仮想ネットワーク、またはピアリングされた仮想ネットワークに、VM をデプロイします。
* Azure AD Domain Services インスタンスとは別のサブネットに VM をデプロイします。

VM をデプロイした後、SSH を使用して VM に接続する手順に従います。

## <a name="configure-the-hosts-file"></a>hosts ファイルを構成する

マネージド ドメインに対して VM ホスト名が正しく構成されていることを確認するには、 */etc/hosts* ファイルを編集して、ホスト名を設定します。

```console
sudo vi /etc/hosts
```

*hosts* ファイルで、*localhost* アドレスを更新します。 次の例では

* *contoso.com* は、Azure AD DS マネージド ドメインの DNS ドメイン名です。
* *coreos* は、マネージド ドメインに参加させる CoreOS VM のホスト名です。

これらの名前を実際の値に更新します。

```console
127.0.0.1 coreos coreos.contoso.com
```

終わったら、エディターの `:wq` コマンドを使用して、*hosts* ファイルを保存して終了します。

## <a name="configure-the-sssd-service"></a>SSSD サービスを構成する

*/etc/sssd/sssd.conf* SSSD 構成を更新します。

```console
sudo vi /etc/sssd/sssd.conf
```

次のパラメーターに対し、独自の Azure AD DS マネージド ドメインの名前を指定します。

* *domains* (すべて大文字)
* *[domain/CONTOSO]* (CONTOSO はすべて大文字)
* *ldap_uri*
* *ldap_search_base*
* *krb5_server*
* *krb5_realm* (すべて大文字)

```console
[sssd]
config_file_version = 2
services = nss, pam
domains = CONTOSO.COM

[domain/CONTOSO.COM]
id_provider = ad
auth_provider = ad
chpass_provider = ad

ldap_uri = ldap://contoso.com
ldap_search_base = dc=contoso,dc=com
ldap_schema = rfc2307bis
ldap_sasl_mech = GSSAPI
ldap_user_object_class = user
ldap_group_object_class = group
ldap_user_home_directory = unixHomeDirectory
ldap_user_principal = userPrincipalName
ldap_account_expire_policy = ad
ldap_force_upper_case_realm = true
fallback_homedir = /home/%d/%u

krb5_server = contoso.com
krb5_realm = CONTOSO.COM
```

## <a name="join-the-vm-to-the-managed-domain"></a>VM をマネージド ドメインに参加させる

SSSD 構成ファイルを更新したので、仮想マシンをマネージド ドメインに参加させます。

1. 最初に、`adcli info` コマンドを使用して、Azure AD DS マネージド ドメインに関する情報を表示できることを確認します。 次の例では、ドメイン *CONTOSO.COM* の情報を取得します。 独自の Azure AD DS マネージド ドメイン名を、すべて大文字で指定します。

    ```console
    sudo adcli info CONTOSO.COM
    ```

   `adcli info` コマンドで Azure AD DS マネージド ドメインが見つからない場合は、次のトラブルシューティング手順を確認してください。

    * ドメインに VM からアクセスできることを確認します。 `ping contoso.com` を試し、肯定応答が返されるかどうかを確認します。
    * VM が、Azure AD DS マネージド ドメインを利用可能な仮想ネットワークと同じ仮想ネットワーク、またはそれとピアリングされた仮想ネットワークに、デプロイされていることを確認します。
    * 仮想ネットワークに対する DNS サーバーの設定が、Azure AD DS マネージド ドメインのドメイン コントローラーを指すように更新されていることを確認します。

1. 次に、`adcli join` コマンドを使用して、VM を Azure AD DS マネージド ドメインに参加させます。 *AAD DC Administrators* グループに属しているユーザーを指定します。 必要に応じて、[Azure AD のグループにユーザー アカウントを追加します](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    やはり、Azure AD DS マネージド ドメインの名前をすべて大文字で入力する必要があります。 次の例では、`contosoadmin@contoso.com` という名前のアカウントを使用して Kerberos を初期化しています。 *AAD DC Administrators* グループのメンバーである独自のユーザー アカウントを入力してください。

    ```console
    sudo adcli join -D CONTOSO.COM -U contosoadmin@CONTOSO.COM -K /etc/krb5.keytab -H coreos.contoso.com -N coreos
    ```

    VM が Azure AD DS マネージド ドメインに正常に参加している場合、`adcli join` コマンドから情報は返されません。

1. ドメイン参加構成を適用するには、SSSD サービスを開始します。
  
    ```console
    sudo systemctl start sssd.service
    ```

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>ドメイン アカウントを使用して VM にサインインする

VM が Azure AD DS マネージド ドメインに正常に参加したことを確認するには、ドメイン ユーザー アカウントを使用して新しい SSH 接続を開始します。 ホーム ディレクトリが作成されていること、およびドメインのグループ メンバーシップが適用されていることを確認します。

1. コンソールから新しい SSH 接続を作成します。 `ssh -l` コマンドを使用して、マネージド ドメインに属しているドメイン アカウントを使用し (`contosoadmin@contoso.com` など)、VM のアドレス (*coreos.contoso.com* など) を入力します。 Azure Cloud Shell を使用する場合は、内部 DNS 名ではなく、VM のパブリック IP アドレスを使用します。

    ```console
    ssh -l contosoadmin@CONTOSO.com coreos.contoso.com
    ```

1. 次に、グループ メンバーシップが正しく解決されていることを確認します。

    ```console
    id
    ```

    Azure AD DS マネージド ドメインからのグループ メンバーシップが表示される必要があります。

## <a name="next-steps"></a>次の手順

Azure AD DS マネージド ドメインへの VM の接続、またはドメイン アカウントでのサインインに関して問題がある場合は、「[ドメイン参加の問題のトラブルシューティング](join-windows-vm.md#troubleshoot-domain-join-issues)」を参照してください。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
