---
title: Ubuntu VM を Azure AD Domain Services に参加させる | Microsoft Docs
description: Ubuntu Linux 仮想マシンを構成して Azure AD Domain Services のマネージド ドメインに参加させる方法について説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 804438c4-51a1-497d-8ccc-5be775980203
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/13/2020
ms.author: justinha
ms.custom: fasttrack-edit
ms.openlocfilehash: 8b6d022b9a1f3be70f69943a53754c9dd909ca99
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619489"
---
# <a name="join-an-ubuntu-linux-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>Ubuntu Linux 仮想マシンを Azure Active Directory Domain Services のマネージド ドメインに参加させる

ユーザーが 1 セットの資格情報を使用して Azure の仮想マシン (VM) にサインインできるようにするには、Azure Active Directory Domain Services (Azure AD DS) のマネージド ドメインに VM を参加させます。 VM を Azure AD DS のマネージド ドメインに参加させると、ドメインのユーザー アカウントと資格情報を使用して、サーバーにサインインして管理することができます。 マネージド ドメインのグループ メンバーシップも適用され、VM 上のファイルまたはサービスへのアクセスを制御できるようになります。

この記事では、Ubuntu Linux VM をマネージド ドメインに参加させる方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要であれば、1 つ目のチュートリアルで [Azure Active Directory Domain Services のマネージド ドメインを作成して構成][create-azure-ad-ds-instance]します。
* マネージド ドメインの一部であるユーザー アカウント。

## <a name="create-and-connect-to-an-ubuntu-linux-vm"></a>Ubuntu Linux VM を作成してそれに接続する

Azure に Ubuntu Linux VM が既にある場合は、SSH を使用してそれに接続した後、次の手順に進んで [VM の構成を開始](#configure-the-hosts-file)します。

Ubuntu Linux VM を作成する必要がある場合、またはこの記事で使用するためのテスト VM を作成する場合は、次のいずれかの方法を使用できます。

* [Azure Portal](../virtual-machines/linux/quick-create-portal.md)
* [Azure CLI](../virtual-machines/linux/quick-create-cli.md)
* [Azure PowerShell](../virtual-machines/linux/quick-create-powershell.md)

VM を作成するときは、VM がマネージド ドメインと通信できるように、仮想ネットワークの設定に注意してください。

* Azure AD Domain Services を有効にしたのと同じ仮想ネットワーク、またはピアリングされた仮想ネットワークに、VM をデプロイします。
* Azure AD Domain Services マネージド ドメインとは別のサブネットに VM をデプロイします。

VM をデプロイした後、SSH を使用して VM に接続する手順に従います。

## <a name="configure-the-hosts-file"></a>hosts ファイルを構成する

マネージド ドメインに対して VM ホスト名が正しく構成されていることを確認するには、 */etc/hosts* ファイルを編集して、ホスト名を設定します。

```console
sudo vi /etc/hosts
```

*hosts* ファイルで、*localhost* アドレスを更新します。 次の例では

* *aaddscontoso.com* は、マネージド ドメインの DNS ドメイン名です。
* *ubuntu* は、マネージド ドメインに参加させる Ubuntu VM のホスト名です。

これらの名前を実際の値に更新します。

```console
127.0.0.1 ubuntu.aaddscontoso.com ubuntu
```

終わったら、エディターの `:wq` コマンドを使用して、*hosts* ファイルを保存して終了します。

## <a name="install-required-packages"></a>必要なパッケージをインストールする

VM をマネージド ドメインに参加させるには、VM にいくつかの追加パッケージが必要です。 これらのパッケージをインストールして構成するには、`apt-get` を使用してドメイン参加ツールを更新およびインストールします

Kerberos のインストールの間に、*krb5-user* パッケージでは、領域名をすべて大文字で入力するように求められます。 たとえば、マネージド ドメインの名前が *aaddscontoso.com* の場合、「*AADDSCONTOSO.COM*」を領域として入力します。 インストールによって、`[realm]` セクションと `[domain_realm]` セクションが */etc/krb5.conf* 構成ファイルに書き込まれます。 領域はすべて大文字で指定します。

```console
sudo apt-get update
sudo apt-get install krb5-user samba sssd sssd-tools libnss-sss libpam-sss ntp ntpdate realmd adcli
```

## <a name="configure-network-time-protocol-ntp"></a>ネットワーク タイム プロトコル (NTP) を構成する

ドメインの通信が正常に機能するためには、Ubuntu VM の日付と時刻を、マネージド ドメインと同期させる必要があります。 マネージド ドメインの NTP ホスト名を、 */etc/ntp.conf* ファイルに追加します。

1. エディターで *ntp.conf* ファイルを開きます。

    ```console
    sudo vi /etc/ntp.conf
    ```

1. *ntp.conf* ファイルに、マネージド ドメインの DNS 名を追加する行を作成します。 次の例では、*aaddscontoso.com* のエントリが追加されます。 独自の DNS 名を使用してください。

    ```console
    server aaddscontoso.com
    ```

    終わったら、エディターの `:wq` コマンドを使用して、*ntp.conf* ファイルを保存して終了します。

1. VM をマネージド ドメインと同期させるには、次の手順を行う必要があります。

    * NTP サーバーを停止します
    * マネージド ドメインから日付と時刻を更新します
    * NTP サービスを開始します

    次のコマンドを実行して、これらの手順を完了します。 `ntpdate` コマンドでは、独自の DNS 名を使用してください。

    ```console
    sudo systemctl stop ntp
    sudo ntpdate aaddscontoso.com
    sudo systemctl start ntp
    ```

## <a name="join-vm-to-the-managed-domain"></a>VM をマネージド ドメインに参加させる

必要なパッケージが VM にインストールされ、NTP が構成されたので、VM をマネージド ドメインに参加させます。

1. `realm discover` コマンドを使用して、マネージド ドメインを検出します。 次の例では、領域 *AADDSCONTOSO.COM* が検出されます。 独自のマネージド ドメイン名を、すべて大文字で指定します。

    ```console
    sudo realm discover AADDSCONTOSO.COM
    ```

   `realm discover` コマンドでマネージド ドメインが見つからない場合は、次のトラブルシューティング手順を確認してください。

    * ドメインに VM からアクセスできることを確認します。 `ping aaddscontoso.com` を試し、肯定応答が返されるかどうかを確認します。
    * VM が、マネージド ドメインを利用可能な仮想ネットワークと同じ仮想ネットワーク、またはそれとピアリングされた仮想ネットワークに、デプロイされていることを確認します。
    * 仮想ネットワークに対する DNS サーバーの設定が、マネージド ドメインのドメイン コントローラーを指すように更新されていることを確認します。

1. 次に、`kinit` コマンドを使用して Kerberos を初期化します。 マネージド ドメインの一部であるユーザーを指定します。 必要に応じて、[Azure AD のグループにユーザー アカウントを追加します](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    やはり、マネージド ドメインの名前をすべて大文字で入力する必要があります。 次の例では、`contosoadmin@aaddscontoso.com` という名前のアカウントを使用して Kerberos を初期化しています。 マネージド ドメインの一部である独自のユーザー アカウントを入力します。

    ```console
    kinit -V contosoadmin@AADDSCONTOSO.COM
    ```

1. 最後に、`realm join` コマンドを使用して、VM をマネージド ドメインに参加させます。 前の `kinit` コマンドで指定した、マネージド ドメインの一部である同じユーザー アカウントを使用します (`contosoadmin@AADDSCONTOSO.COM` など)。

    ```console
    sudo realm join --verbose AADDSCONTOSO.COM -U 'contosoadmin@AADDSCONTOSO.COM' --install=/
    ```

VM をマネージド ドメインに参加させるにはしばらくかかります。 次の出力例では、VM がマネージド ドメインに正常に参加したことが示されています。

```output
Successfully enrolled machine in realm
```

VM のドメイン参加プロセスを正常に完了できない場合は、VM のネットワーク セキュリティ グループで、マネージド ドメインの仮想ネットワーク サブネットに対する TCP + UDP ポート 464 での送信 Kerberos トラフィックが許可されていることを確認します。

"*Unspecified GSS failure.Unspecified GSS failure. Minor code may provide more information (Server not found in Kerberos database) (未指定の GSS の障害。マイナー コードで詳細情報が提供されている可能性があります (Kerberos データベースでサーバーが見つかりません))* " というエラーを受け取ったら、 */etc/krb5.conf* ファイルを開き、`[libdefaults]` セクションに次のコードを追加して、もう一度実行してください。

```console
rdns=false
```

## <a name="update-the-sssd-configuration"></a>SSSD の構成を更新する

前のステップでインストールされたパッケージの 1 つは、システム セキュリティ サービス デーモン (SSSD) に対するものでした。 ユーザーがドメインの資格情報を使用して VM にサインインしようとすると、SSSD によって要求が認証プロバイダーに中継されます。 このシナリオでは、要求の認証を行うために SSSD によって Azure AD DS が使用されます。

1. エディターで *sssd.conf* ファイルを開きます。

    ```console
    sudo vi /etc/sssd/sssd.conf
    ```

1. 次のように、*use_fully_qualified_names* という行をコメントアウトします。

    ```console
    # use_fully_qualified_names = True
    ```

    終わったら、エディターの `:wq` コマンドを使用して、*sssd.conf* ファイルを保存して終了します。

1. 変更を適用するには、SSSD サービスを再起動します。

    ```console
    sudo systemctl restart sssd
    ```

## <a name="configure-user-account-and-group-settings"></a>ユーザー アカウントとグループの設定を構成する

VM をマネージド ドメインに参加させ、認証用に構成したので、いくつかのユーザー構成オプションを完了する必要があります。 これらの構成の変更には、パスワードベースの認証の許可と、ドメイン ユーザーが初めてサインインしたときのローカル VM へのホーム ディレクトリの自動作成が含まれます。

### <a name="allow-password-authentication-for-ssh"></a>SSH のパスワード認証を許可する

既定では、ユーザーは SSH 公開キーベースの認証を使用することによってのみ、VM にサインインできます。 パスワードベースの認証は失敗します。 VM をマネージド ドメインに参加させるときは、これらのドメイン アカウントでパスワードベースの認証を使用する必要があります。 次のようにして、パスワードベースの認証を許可するように SSH の構成を更新します。

1. エディターで *sshd_conf* ファイルを開きます。

    ```console
    sudo vi /etc/ssh/sshd_config
    ```

1. *PasswordAuthentication* の行を *yes* に更新します。

    ```console
    PasswordAuthentication yes
    ```

    終わったら、エディターの `:wq` コマンドを使用して、*sshd_conf* ファイルを保存して終了します。

1. 変更を適用し、ユーザーがパスワードを使用してサインインできるようにするには、SSH サービスを再起動します。

    ```console
    sudo systemctl restart ssh
    ```

### <a name="configure-automatic-home-directory-creation"></a>自動ホーム ディレクトリ作成を構成する

ユーザーが初めてサインインしたときにホーム ディレクトリが自動的に作成されるようにするには、次の手順を実行します。

1. エディターで */etc/pam.d/common-session* ファイルを開きます。

    ```console
    sudo vi /etc/pam.d/common-session
    ```

1. このファイルの行 `session optional pam_sss.so` の下に、次の行を追加します。

    ```console
    session required pam_mkhomedir.so skel=/etc/skel/ umask=0077
    ```

    終わったら、エディターの `:wq` コマンドを使用して、*common-session* ファイルを保存して終了します。

### <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>"AAD DC Administrators" グループに sudo 特権を付与する

*AAD DC Administrators* グループのメンバーに Ubuntu VM での管理特権を付与するには、 */etc/sudoers* にエントリを追加します。 追加した後、*AAD DC Administrators* グループのメンバーは、Ubuntu VM で `sudo` コマンドを使用できるようになります。

1. *sudoers* ファイルを編集用に開きます。

    ```console
    sudo visudo
    ```

1. */etc/sudoers* ファイルの最後に、次のエントリを追加します。

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators ALL=(ALL) NOPASSWD:ALL
    ```

    終わったら、`Ctrl-X` コマンドを使用してエディターを保存して終了します。

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>ドメイン アカウントを使用して VM にサインインする

VM がマネージド ドメインに正常に参加したことを確認するには、ドメイン ユーザー アカウントを使用して新しい SSH 接続を開始します。 ホーム ディレクトリが作成されていること、およびドメインのグループ メンバーシップが適用されていることを確認します。

1. コンソールから新しい SSH 接続を作成します。 `ssh -l` コマンドを使用して、マネージド ドメインに属しているドメイン アカウントを使用して (例: `contosoadmin@aaddscontoso.com`)、VM のアドレス (*例: ubuntu.aadds.contoso.com*) を入力します。 Azure Cloud Shell を使用する場合は、内部 DNS 名ではなく、VM のパブリック IP アドレスを使用します。

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com ubuntu.aaddscontoso.com
    ```

1. VM に正常に接続したら、ホーム ディレクトリが正しく初期化されていることを確認します。

    ```console
    pwd
    ```

    ユーザー アカウントと一致する独自のディレクトリの */home* ディレクトリにいる必要があります。

1. 次に、グループ メンバーシップが正しく解決されていることを確認します。

    ```console
    id
    ```

    マネージド ドメインからのグループ メンバーシップが表示される必要があります。

1. *AAD DC Administrators* グループのメンバーとして VM にサインインした場合は、`sudo` コマンドを正しく使用できることを確認します。

    ```console
    sudo apt-get update
    ```

## <a name="next-steps"></a>次のステップ

マネージド ドメインへの VM の接続、またはドメイン アカウントでのサインインに関して問題がある場合は、「[ドメイン参加の問題のトラブルシューティング](join-windows-vm.md#troubleshoot-domain-join-issues)」を参照してください。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
