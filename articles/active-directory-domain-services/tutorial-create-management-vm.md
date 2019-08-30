---
title: チュートリアル - Azure Active Directory Domain Services の管理 VM を作成する | Microsoft Docs
description: このチュートリアルでは、Azure Active Directory Domain Services インスタンスの管理に使用する Windows 仮想マシンを作成して構成する方法について説明します。
author: iainfoulds
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 08/14/2019
ms.author: iainfou
ms.openlocfilehash: e7c3ccb553010b84a30ccdad875ea0362112d830
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2019
ms.locfileid: "69618792"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>チュートリアル:Azure Active Directory Domain Services のマネージド ドメインを構成および管理するための管理 VM を作成する

Azure Active Directory Domain Services (AD DS) は、Windows Server Active Directory と完全に互換性のあるマネージド ドメイン サービス (ドメイン参加、グループ ポリシー、LDAP、Kerberos 認証、NTLM 認証など) を提供します。 このマネージド ドメインは、オンプレミスの Active Directory Domain Services ドメインの場合と同じリモート サーバー管理ツール (RSAT) を使用して管理します。 Azure AD DS はマネージド サービスであるため、リモート デスクトップ プロトコル (RDP) を使用してドメイン コントローラーに接続するなど、ユーザーが実行できない管理タスクもいくつか存在します。

このチュートリアルでは、Windows Server VM を Azure に作成し、Azure AD DS のマネージド ドメインを管理するために必要なツールをインストールする方法を紹介します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure AD DS のマネージド ドメインで利用できる管理タスクを理解する
> * Windows Server VM に Active Directory 管理ツールをインストールする
> * Active Directory 管理センターを使用して一般的なタスクを実行する

Azure サブスクリプションをお持ちでない場合は、始める前に[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下のリソースと特権が必要です。

* 有効な Azure サブスクリプション
    * Azure サブスクリプションをお持ちでない場合は、[アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)してください。
* ご利用のサブスクリプションに関連付けられた Azure Active Directory テナント (オンプレミス ディレクトリまたはクラウド専用ディレクトリと同期されていること)。
    * 必要に応じて、[Azure Active Directory テナントを作成][create-azure-ad-tenant]するか、[ご利用のアカウントに Azure サブスクリプションを関連付け][associate-azure-ad-tenant]ます。
* Azure AD テナントで有効化され、構成された Azure Active Directory Domain Services のマネージド ドメイン。
    * 必要であれば、1 つ目のチュートリアルで [Azure Active Directory Domain Services インスタンスを作成して構成][create-azure-ad-ds-instance]します。
* Azure AD DS のマネージド ドメインに参加している Windows Server VM。
    * 必要であれば、前のチュートリアルで [Windows Server VM を作成し、マネージド ドメインに参加][create-join-windows-vm]させます。
* Azure AD テナントの *Azure AD DC administrators* グループのメンバーであるユーザー アカウント。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

このチュートリアルでは、Azure portal を使用して管理 VM の作成と構成を行います。 最初に、[Azure portal](https://portal.azure.com) にサインインしてください。

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Azure AD DS で利用できる管理タスク

Azure AD DS では、ユーザー、アプリケーション、サービスで使用するためのマネージド ドメインが提供されます。 このようなアプローチにより、実行できる管理タスクや、マネージド ドメイン内で与えられる特権が一部変更されています。 これらのタスクと権限は、通常のオンプレミス Active Directory Domain Services 環境で利用できるものとは異なる場合があります。 また、Azure AD DS のマネージド ドメイン上のドメイン コントローラーには、リモート デスクトップを使って接続することはできません。

### <a name="administrative-tasks-you-can-perform-on-an-azure-ad-ds-managed-domain"></a>Azure AD DS のマネージド ドメインで実行できる管理タスク

*AAD DC Administrators* グループのメンバーには、Azure AD DS のマネージド ドメインで以下のようなタスクを実行できる特権が付与されます。

* コンピューターをマネージド ドメインに参加させる。
* マネージド ドメイン内の *AADDC Computers* と *AADDC Users* のコンテナーに対して組み込みのグループ ポリシー オブジェクト (GPO) を構成する。
* マネージド ドメイン上で DNS を管理する。
* マネージド ドメイン上でカスタム組織単位 (OU) を作成し、管理する。
* マネージド ドメインに参加しているコンピューターへの管理アクセスを取得する。

### <a name="administrative-privileges-you-dont-have-on-an-azure-ad-ds-managed-domain"></a>Azure AD DS のマネージド ドメインに対して持っていない管理特権

Azure AD DS のマネージド ドメインはロックダウンされており、ユーザーには特定の管理タスクをそのドメインで実行する特権がありません。 ユーザーが実行できないタスクの例を次に示します。

* マネージド ドメインのスキーマを拡張する。
* リモート デスクトップを使用してマネージド ドメインのドメイン コントローラーに接続する。
* マネージド ドメインにドメイン コントローラーを追加する。
* マネージド ドメインに対する "*ドメイン管理者*" 特権や "*エンタープライズ管理者*" 特権はありません。

## <a name="sign-in-to-the-windows-server-vm"></a>Windows Server VM にサインインする

前のチュートリアルでは、Windows Server VM を作成して Azure AD DS のマネージド ドメインに参加させました。 その VM を使用して管理ツールをインストールしましょう。 必要であれば、[チュートリアルの手順に従って Windows Server VM を作成し、マネージド ドメインに参加][create-join-windows-vm]させてください。

> [!NOTE]
> このチュートリアルでは、Azure AD DS のマネージド ドメインに参加している Azure の Windows Server VM を使用します。 マネージド ドメインに参加している Windows クライアント (Windows 10 など) を使用することもできます。
>
> Windows クライアントに管理ツールをインストールする方法の詳細については、[リモート サーバー管理ツール (RSAT) のインストール](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)に関するページを参照してください。

まず、次の手順に従って Windows Server VM に接続します。

1. Azure portal で、左側にある **[リソース グループ]** を選択します。 VM の作成先となったリソース グループ (例: *myResourceGroup*) を選択し、VM (例: *myVM*) を選択します。
1. VM の **[概要]** ウィンドウで **[接続]** を選択します。

    ![Azure portal で Windows 仮想マシンに接続する](./media/tutorial-create-management-vm/connect-vm.png)

    [Azure Bastion ホスト (現在プレビュー段階) を作成、使用][azure-bastion]して、Azure portal から SSL 経由のアクセスのみを許可することもできます。

1. *[RDP ファイルのダウンロード]* オプションを選択します。 この RDP ファイルを Web ブラウザーで保存します。
1. VM に接続するには、ダウンロードした RDP ファイルを開きます。 メッセージが表示されたら、 **[Connect]** を選択します。
1. *Azure AD DC administrators* グループに属しているユーザーの資格情報を入力します (例: *contoso\dee*)。
1. サインイン プロセス中に証明書警告が表示された場合は、 **[はい]** または **[続行]** を選択して接続します。

## <a name="install-active-directory-administrative-tools"></a>Active Directory 管理ツールをインストールする

Azure AD DS のマネージド ドメインは、Active Directory 管理センター (ADAC) や AD PowerShell など、オンプレミス AD DS 環境と同じ管理ツールを使用して管理します。 Windows Server コンピューターとクライアント コンピューターには、リモート サーバー管理ツール (RSAT) 機能の一部としてこれらのツールをインストールできます。 その後、*AAD DC Administrators* グループのメンバーは、マネージド ドメインに参加しているコンピューターから、それらの AD 管理ツールを使用して Azure AD DS のマネージド ドメインをリモートから管理することができます。

ドメイン参加済み VM に Active Directory 管理ツールをインストールするには、次の手順を実行します。

1. VM にサインインすると、既定で**サーバー マネージャー**が表示されるはずです。 そうならない場合は、 **[スタート]** メニューの **[サーバー マネージャー]** を選択します。
1. **[サーバー マネージャー]** ウィンドウの *[ダッシュボード]* ウィンドウで **[役割と機能の追加]** を選択します。
1. *[役割と機能の追加]* ウィザードの **[開始する前に]** ページで **[次へ]** を選択します。
1. *[インストールの種類]* で、 **[役割ベースまたは機能ベースのインストール]** オプションが選択された状態にして **[次へ]** を選択します。
1. **[サーバーの選択]** ページで、サーバー プールから現在の VM (例: *myvm.contoso.com*) を選択し、 **[次へ]** を選択します。
1. **[サーバーの役割]** ページで、 **[次へ]** をクリックします。
1. **[機能]** ページで、 **[リモート サーバー管理ツール]** ノードを展開し、次に **[役割管理ツール]** ノードを展開します。

    役割管理ツールの一覧から **[AD DS および AD LDS ツール]** 機能を選択し、 **[次へ]** を選択します。

    ![[機能] ページから "AD DS および AD LDS ツール" をインストールする](./media/tutorial-create-management-vm/install-features.png)

1. **[確認]** ページで **[インストール]** を選択します。 管理ツールのインストールには 1 分から 2 分かかる場合があります。
1. 機能のインストールが完了したら、 **[閉じる]** を選択して **[役割と機能の追加]** ウィザードを終了します。

## <a name="use-active-directory-administrative-tools"></a>Active Directory 管理ツールを使用する

管理ツールがインストールされている状態で、それらを使用して Azure AD DS のマネージド ドメインを管理する方法を見ていきましょう。 *AAD DC Administrators* グループのメンバーであるユーザー アカウントで VM にサインインしていることを確認してください。

1. **[スタート]** メニューから **[Windows 管理ツール]** を選択します。 前の手順でインストールした AD 管理ツールが一覧表示されます。

    ![サーバーにインストールされている管理ツール](./media/tutorial-create-management-vm/list-admin-tools.png)

1. **[Active Directory 管理センター]** を選択します。
1. Azure AD DS のマネージド ドメインの詳細を確認するために、左側のウィンドウでドメイン名を選択します (例: *contoso.com*)。 一覧の先頭に *AADDC Computers* および *AADDC Users* という名前の 2 つのコンテナーがあります。

    ![Azure AD DS のマネージド ドメインで使用できるコンテナーの一覧](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. Azure AD DS のマネージド ドメインに属しているユーザーとグループを表示するには、 **[AADDC Users]** コンテナーを選択します。 このコンテナーには、Azure AD テナントのユーザー アカウントとグループが表示されています。

    次の出力例では、*contosoadmin* という名前のユーザー アカウントと *AAD DC Administrators* のグループがこのコンテナーに表示されています。

    ![Active Directory 管理センターで Azure AD DS ドメイン ユーザーの一覧を表示する](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. Azure AD DS のマネージド ドメインに参加しているコンピューターを表示するには、 **[AADDC Computers]** コンテナーを選択します。 現在の仮想マシンのエントリ (例: *myVM*) が表示されます。 Azure AD DS のマネージド ドメインに参加しているすべてのコンピューターのコンピューター アカウントが、この *AADDC Computers* コンテナーに格納されます。

Active Directory 管理センターの一般的な操作 (ユーザー アカウント パスワードのリセット、グループ メンバーシップの管理など) が利用できます。 Azure AD DS のマネージド ドメインにおける一般的な操作については、管理ツールの一部としてインストールされる "*Windows PowerShell 用 Active Directory モジュール*" を使用して管理することもできます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * Azure AD DS のマネージド ドメインで利用できる管理タスクを理解する
> * Windows Server VM に Active Directory 管理ツールをインストールする
> * Active Directory 管理センターを使用して一般的なタスクを実行する

Azure AD DS のマネージド ドメインを安全に操作するには、セキュリティで保護されたライトウェイト ディレクトリ アクセス プロトコル (LDAPS) を有効にしてください。

> [!div class="nextstepaction"]
> [マネージド ドメイン用に Secure LDAP を構成する](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/bastion-create-host-portal.md
