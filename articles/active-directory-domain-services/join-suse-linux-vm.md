---
title: SLE VM を Azure AD Domain Services に参加させる | Microsoft Docs
description: SUSE Linux Enterprise 仮想マシンを構成して Azure AD Domain Services のマネージド ドメインに参加させる方法について説明します。
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 08/12/2020
ms.author: justinha
ms.openlocfilehash: f2f421d95dfc376aed373c718198db33a870d9dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96619608"
---
# <a name="join-a-suse-linux-enterprise-virtual-machine-to-an-azure-active-directory-domain-services-managed-domain"></a>SUSE Linux Enterprise 仮想マシンを Azure Active Directory Domain Services のマネージド ドメインに参加させる

ユーザーが 1 セットの資格情報を使用して Azure の仮想マシン (VM) にサインインできるようにするには、Azure Active Directory Domain Services (Azure AD DS) マネージド ドメインに VM を参加させます。 VM を Azure AD DS のマネージド ドメインに参加させると、ドメインのユーザー アカウントと資格情報を使用して、サーバーにサインインして管理することができます。 マネージド ドメインのグループ メンバーシップも適用され、VM 上のファイルまたはサービスへのアクセスを制御できるようになります。

この記事では、SUSE Linux Enterprise (SLE) VM をマネージド ドメインに参加させる方法について説明します。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要であれば、1 つ目のチュートリアルで [Azure Active Directory Domain Services のマネージド ドメインを作成して構成][create-azure-ad-ds-instance]します。
* マネージド ドメインの一部であるユーザー アカウント。

## <a name="create-and-connect-to-a-sle-linux-vm"></a>SLE Linux VM を作成してそれに接続する

Azure に SLE Linux VM が既にある場合は、SSH を使用してそれに接続した後、次の手順に進んで [VM の構成を開始](#configure-the-hosts-file)します。

SLE Linux VM を作成する必要がある場合、またはこの記事で使用するためのテスト VM を作成する場合は、次のいずれかの方法を使用できます。

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
* *linux-q2gr* は、マネージド ドメインに参加している SLE VM のホスト名です。

これらの名前を実際の値に更新します。

```console
127.0.0.1 linux-q2gr linux-q2gr.aaddscontoso.com
```

終わったら、エディターの `:wq` コマンドを使用して、*hosts* ファイルを保存して終了します。

## <a name="join-vm-to-the-managed-domain-using-sssd"></a>SSSD を使用して VM をマネージド ドメインに参加させる

**SSSD** と YaST の *User Logon Management* モジュールを使用してマネージド ドメインに参加するには、次の手順を行います。

1. YaST の *User Logon Management* モジュールをインストールします。

    ```bash
    sudo zypper install yast2-auth-client
    ```

1. YaST を開きます。

1. 後で DNS 自動検出を正常に実行するには、マネージド ドメインの IP アドレス ("*Active Directory サーバー*") をクライアントのネーム サーバーとして構成します。

    YaST で、 **[システム] > [ネットワーク設定]** の順に選択します。

1. *[ホスト名/DNS]* タブを選択し、テキスト ボックス *[ネーム サーバー 1]* にマネージド ドメインの IP アドレスを入力します。 これらの IP アドレスは、ご利用のマネージド ドメインの Azure portal で *[プロパティ]* ウィンドウに表示されます (例: *10.0.2.4*、*10.0.2.5*)。

    ご自分のマネージド ドメインの IP アドレスを追加し、 **[OK]** を選択します。

1. YaST のメイン ウィンドウで、 *[ネットワーク サービス]*  >  *[User Logon Management]* の順に選択します。

    次のスクリーンショット例で示すように、このモジュールでは、コンピューターのさまざまなネットワーク プロパティと現在使用されている認証方法を示す概要が表示されます。

    ![YaST の [User Login Management] ウィンドウのスクリーンショット例](./media/join-suse-linux-vm/overview-window.png)

    編集を開始するには、 **[設定の変更]** を選択します。

VM をマネージド ドメインに参加させるには、次の手順を行います。

1. ダイアログ ボックスで、 **[ドメインの追加]** を選択します。

1. 正しい "*ドメイン名*" (例: *aaddscontoso.com*) を指定し、データの識別と認証に使用するサービスを指定します。 両方について、 *[Microsoft Active Directory]* を選択します。

    *[ドメインを有効にする]* オプションが選択されていることを確認します。

1. 準備ができたら **[OK]** を選択します。

1. 次のダイアログで既定の設定を受け入れ、 **[OK]** を選択します。

1. VM では、必要に応じて追加のソフトウェアがインストールされ、マネージド ドメインが使用可能かどうかが確認されます。

    すべてが正しい場合、次の例のダイアログが表示され、VM によってマネージド ドメインが検出されたが、"*まだ登録されていない*" ことを示します。

    ![YaST の Active Directory 登録ウィンドウのスクリーンショット例](./media/join-suse-linux-vm/enroll-window.png)

1. ダイアログで、マネージド ドメインに参加するユーザーの *[ユーザー名]* と "*パスワード*" を指定します。 必要に応じて、[Azure AD のグループにユーザー アカウントを追加します](../active-directory/fundamentals/active-directory-groups-members-azure-portal.md)。

    現在のドメインが Samba に対して有効であることを確認するには、 *[Overwrite Samba configuration to work with this AD]\(この AD で動作するように Samba の構成を上書きする\)* をアクティブ化します。

1. 登録するには、 **[OK]** を選択します。

1. 正常に登録されたことを確認するメッセージが表示されます。 完了するには、 **[OK]** を選択します。

VM をマネージド ドメインに登録した後、次のスクリーンショット例で示すように、 *[ドメイン ユーザー ログオンの管理]* を使用してクライアントを構成します。

![YaST の [ドメイン ユーザー ログオンの管理] ウィンドウのスクリーンショット](./media/join-suse-linux-vm/manage-domain-user-logon-window.png)

1. マネージド ドメインによって提供されたデータを使用したサインインを許可するには、 *[ドメイン ユーザー ログオンを許可する]* をオンにします。

1. 必要に応じて、 *[ドメイン データ ソースを有効にする]* で、ご利用の環境に必要な追加のデータ ソースをオンにします。 これらのオプションとしては、**sudo** の使用を許可するユーザーや使用可能なネットワーク ドライブなどがあります。

1. マネージド ドメイン内のユーザーが VM でホーム ディレクトリを持つことを許可するには、 *[ホーム ディレクトリを作成する]* ボックスをオンにします。

1. サイド バーで、 **[サービス オプション]、[名前] スイッチ**、 *[拡張オプション]* の順に選択します。 そのウィンドウで、 *[fallback_homedir]* または *[override_homedir]* を選択し、 **[追加]** を選択します。

1. ホーム ディレクトリの場所の値を指定します。 ホーム ディレクトリを */home/USER_NAME* の形式に従って作成するには、 */home/%u* を使用します。 使用可能な変数の詳細については、*override_homedir* セクションの sssd.conf man ページ (`man 5 sssd.conf`) を参照してください。

1. **[OK]** を選択します。

1. 変更を保存するには、 **[OK]** を選択します。 その後、ここで表示される値が正しいことを確認します。 ダイアログを終了するには、 **[キャンセル]** を選択します。

1. SSSD と Winbind を同時に実行する予定があれば (SSSD を使用して参加するが、Samba ファイル サーバーを実行する場合など)、smb.conf で Samba オプションの *kerberos method* を *secrets and keytab* に設定する必要があります。 さらに、sssd.conf で SSSD オプション *ad_update_samba_machine_account_password* を *true* に設定する必要もあります。 これらのオプションは、システムのキータブが同期しなくなるのを防ぎます。

## <a name="join-vm-to-the-managed-domain-using-winbind"></a>Winbind を使用して VM をマネージド ドメインに参加させる

**winbind** と YaST の *Windows Domain Membership* モジュールを使用してマネージド ドメインに参加するには、次の手順を行います。

1. YaST で、 **[ネットワーク サービス] > [Windows Domain Membership]** の順に選択します。

1. *[Windows Domain Membership]* 画面の *[ドメインまたはワークグループ]* で、参加するドメインを入力します。 マネージド ドメイン名 (例: *aaddscontoso.com*) を入力します。

    ![YaST の [Windows Domain Membership] ウィンドウのスクリーンショット例](./media/join-suse-linux-vm/samba-client-window.png)

1. Linux 認証に SMB ソースを使用するには、 *[Linux 認証に SMB 情報を使用する]* オプションをオンにします。

1. VM 上にマネージド ドメイン ユーザーのローカル ホーム ディレクトリを自動的に作成するには、 *[ログイン時にホーム ディレクトリを作成する]* オプションをオンにします。

1. マネージド ドメインが一時的に使用できない場合でもドメイン ユーザーがサインインするのを許可するには、 *[オフライン認証]* オプションをオンにします。

1. Samba ユーザーおよびグループの UID と GID の範囲を変更するには、 *[エキスパート設定]* を選択します。

1. *[NTP 構成]* を選択して、マネージド ドメインのネットワーク タイム プロトコル (NTP) 時刻の同期を構成します。 マネージド ドメインの IP アドレスを入力します。 これらの IP アドレスは、ご利用のマネージド ドメインの Azure portal で *[プロパティ]* ウィンドウに表示されます (例: *10.0.2.4*、*10.0.2.5*)。

1. **[OK]** を選択し、入力を要求されたら、ドメイン参加を確認します。

1. マネージド ドメインの管理者パスワードを入力し、 **[OK]** を選択します。

    ![SLE VM をマネージド ドメインに参加させる場合の認証ダイアログのスクリーンショット例](./media/join-suse-linux-vm/domain-join-authentication-prompt.png)

マネージド ドメインに参加すると、デスクトップまたはコンソールのディスプレイ マネージャーを使用してワークステーションからサインインできます。

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-yast-command-line-interface"></a>YaST コマンド ライン インターフェイスから Winbind を使用して VM をマネージド ドメインに参加させる

**winbind** と *YaST コマンド ライン インターフェイス* を使用してマネージド ドメインに参加させるには:

* ドメインに参加する:

  ```console
  sudo yast samba-client joindomain domain=aaddscontoso.com user=<admin> password=<admin password> machine=<(optional) machine account>
  ```

## <a name="join-vm-to-the-managed-domain-using-winbind-from-the-terminal"></a>ターミナルから Winbind を使用して VM をマネージド ドメインに参加させる

**winbind** と *`samba net` コマンド* を使用してマネージド ドメインに参加させるには:

1. kerberos クライアントと samba-winbind をインストールする:

   ```console
   sudo zypper in krb5-client samba-winbind
   ```

2. 構成ファイルを編集する:

   * /etc/samba/smb.conf
   
     ```ini
     [global]
         workgroup = AADDSCONTOSO
         usershare allow guests = NO #disallow guests from sharing
         idmap config * : backend = tdb
         idmap config * : range = 1000000-1999999
         idmap config AADDSCONTOSO : backend = rid
         idmap config AADDSCONTOSO : range = 5000000-5999999
         kerberos method = secrets and keytab
         realm = AADDSCONTOSO.COM
         security = ADS
         template homedir = /home/%D/%U
         template shell = /bin/bash
         winbind offline logon = yes
         winbind refresh tickets = yes
     ```

   * /etc/krb5.conf
   
     ```ini
     [libdefaults]
         default_realm = AADDSCONTOSO.COM
         clockskew = 300
     [realms]
         AADDSCONTOSO.COM = {
             kdc = PDC.AADDSCONTOSO.COM
             default_domain = AADDSCONTOSO.COM
             admin_server = PDC.AADDSCONTOSO.COM
         }
     [domain_realm]
         .aaddscontoso.com = AADDSCONTOSO.COM
     [appdefaults]
         pam = {
             ticket_lifetime = 1d
             renew_lifetime = 1d
             forwardable = true
             proxiable = false
             minimum_uid = 1
         }
     ```

   * /etc/security/pam_winbind.conf
   
     ```ini
     [global]
         cached_login = yes
         krb5_auth = yes
         krb5_ccache_type = FILE
         warn_pwd_expire = 14
     ```

   * /etc/nsswitch.conf
   
     ```ini
     passwd: compat winbind
     group: compat winbind
     ```

3. Azure AD および Linux の日付と時刻が同期されていることを確認してください。これを行うには、NTP サービスに Azure AD サーバーを追加します。
   
   1. /etc/ntp.conf ファイルに次の行を追加します。
     
      ```console
      server aaddscontoso.com
      ```

   1. NTP サービスを再起動する:
     
      ```console
      sudo systemctl restart ntpd
      ```

4. ドメインに参加する:

   ```console
   sudo net ads join -U Administrator%Mypassword
   ```

5. Linux のプラグ可能な認証モジュール (PAM) でログイン ソースとして winbind を有効にします。

   ```console
   pam-config --add --winbind
   ```

6. ユーザーがログインできるように、ホーム ディレクトリの自動作成を有効にします。

   ```console
   pam-config -a --mkhomedir
   ```

7. winbind サービスを開始し、有効にします。

   ```console
   sudo systemctl enable winbind
   sudo systemctl start winbind
   ```

## <a name="allow-password-authentication-for-ssh"></a>SSH のパスワード認証を許可する

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
    sudo systemctl restart sshd
    ```

## <a name="grant-the-aad-dc-administrators-group-sudo-privileges"></a>"AAD DC Administrators" グループに sudo 特権を付与する

*AAD DC Administrators* グループのメンバーに SLE VM での管理特権を付与するには、 */etc/sudoers* にエントリを追加します。 追加した後、*AAD DC Administrators* グループのメンバーは、SLE VM で `sudo` コマンドを使用できるようになります。

1. *sudoers* ファイルを編集用に開きます。

    ```console
    sudo visudo
    ```

1. */etc/sudoers* ファイルの最後に、次のエントリを追加します。 *AAD DC Administrators* グループの名前に空白が含まれているため、グループ名に円記号のエスケープ文字を含めます。 独自のドメイン名 (*aaddscontoso.com* など) を追加します。

    ```console
    # Add 'AAD DC Administrators' group members as admins.
    %AAD\ DC\ Administrators@aaddscontoso.com ALL=(ALL) NOPASSWD:ALL
    ```

    終わったら、エディターの `:wq` コマンドを使用してエディターを保存して終了します。

## <a name="sign-in-to-the-vm-using-a-domain-account"></a>ドメイン アカウントを使用して VM にサインインする

VM がマネージド ドメインに正常に参加したことを確認するには、ドメイン ユーザー アカウントを使用して新しい SSH 接続を開始します。 ホーム ディレクトリが作成されていること、およびドメインのグループ メンバーシップが適用されていることを確認します。

1. コンソールから新しい SSH 接続を作成します。 `ssh -l` コマンドを使用して、マネージド ドメインに属しているドメイン アカウントを使用し (例: `contosoadmin@aaddscontoso.com`)、VM のアドレス (例: *linux-q2gr.aaddscontoso.com*) を入力します。 Azure Cloud Shell を使用する場合は、内部 DNS 名ではなく、VM のパブリック IP アドレスを使用します。

    ```console
    ssh -l contosoadmin@AADDSCONTOSO.com linux-q2gr.aaddscontoso.com
    ```

2. VM に正常に接続したら、ホーム ディレクトリが正しく初期化されていることを確認します。

    ```console
    pwd
    ```

    ユーザー アカウントと一致する独自のディレクトリの */home* ディレクトリにいる必要があります。

3. 次に、グループ メンバーシップが正しく解決されていることを確認します。

    ```console
    id
    ```

    マネージド ドメインからのグループ メンバーシップが表示される必要があります。

4. *AAD DC Administrators* グループのメンバーとして VM にサインインした場合は、`sudo` コマンドを正しく使用できることを確認します。

    ```console
    sudo zypper update
    ```

## <a name="next-steps"></a>次のステップ

マネージド ドメインへの VM の接続、またはドメイン アカウントでのサインインに関して問題がある場合は、「[ドメイン参加の問題のトラブルシューティング](join-windows-vm.md#troubleshoot-domain-join-issues)」を参照してください。

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
