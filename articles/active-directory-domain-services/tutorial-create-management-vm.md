---
title: チュートリアル - Azure Active Directory Domain Services の管理 VM を作成する | Microsoft Docs
description: このチュートリアルでは、Azure Active Directory Domain Services のマネージド ドメインの管理に使用する Windows 仮想マシンを作成して構成する方法について説明します。
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: tutorial
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: d017fc3fc53c9f89978893670df10ecd731da904
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96618180"
---
# <a name="tutorial-create-a-management-vm-to-configure-and-administer-an-azure-active-directory-domain-services-managed-domain"></a>チュートリアル:Azure Active Directory Domain Services のマネージド ドメインを構成および管理するための管理 VM を作成する

Azure Active Directory Domain Services (Azure AD DS) では、Windows Server Active Directory と完全に互換性のあるマネージド ドメイン サービス (ドメイン参加、グループ ポリシー、LDAP、Kerberos 認証、NTLM 認証など) が提供されます。 このマネージド ドメインは、オンプレミスの Active Directory Domain Services ドメインの場合と同じリモート サーバー管理ツール (RSAT) を使用して管理します。 Azure AD DS はマネージド サービスであるため、リモート デスクトップ プロトコル (RDP) を使用してドメイン コントローラーに接続するなど、ユーザーが実行できない管理タスクもいくつか存在します。

このチュートリアルでは、Windows Server VM を Azure に構成し、Azure AD DS のマネージド ドメインを管理するために必要なツールをインストールする方法を紹介します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * マネージド ドメインで利用できる管理タスクを理解する
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
    * 必要に応じて、1 つ目のチュートリアルを参照し、[Azure Active Directory Domain Services のマネージド ドメインの作成と構成][create-azure-ad-ds-instance]を行ってください。
* マネージド ドメインに参加している Windows Server VM。
    * 必要に応じて、前のチュートリアルを参照し、[Windows Server VM を作成してマネージド ドメインに参加][create-join-windows-vm]させてください。
* Azure AD テナントの *Azure AD DC administrators* グループのメンバーであるユーザー アカウント。
* Azure AD DS 仮想ネットワークにデプロイされた Azure Bastion ホスト。
    * 必要に応じて [Azure Bastion ホストを作成][azure-bastion]してください。

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインする

このチュートリアルでは、Azure portal を使用して管理 VM の作成と構成を行います。 最初に、[Azure portal](https://portal.azure.com) にサインインします。

## <a name="available-administrative-tasks-in-azure-ad-ds"></a>Azure AD DS で利用できる管理タスク

Azure AD DS では、ユーザー、アプリケーション、サービスで使用するためのマネージド ドメインが提供されます。 このようなアプローチにより、実行できる管理タスクや、マネージド ドメイン内で与えられる特権が一部変更されています。 これらのタスクと権限は、通常のオンプレミス Active Directory Domain Services 環境で利用できるものとは異なる場合があります。 また、マネージド ドメイン上のドメイン コントローラーには、リモート デスクトップを使って接続することはできません。

### <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>マネージド ドメインで実行できる管理タスク

*AAD DC Administrators* グループのメンバーには、マネージド ドメインで以下のようなタスクを実行できる権限が付与されます。

* マネージド ドメイン内の *AADDC Computers* と *AADDC Users* のコンテナーに対して組み込みのグループ ポリシー オブジェクト (GPO) を構成する。
* マネージド ドメイン上で DNS を管理する。
* マネージド ドメイン上でカスタム組織単位 (OU) を作成し、管理する。
* マネージド ドメインに参加しているコンピューターへの管理アクセスを取得する。

### <a name="administrative-privileges-you-dont-have-on-a-managed-domain"></a>マネージド ドメインに対して付与されていない管理者特権

マネージド ドメインはロックダウンされており、ユーザーには特定の管理タスクをそのドメインで実行する特権がありません。 ユーザーが実行できないタスクの例を次に示します。

* マネージド ドメインのスキーマを拡張する。
* リモート デスクトップを使用してマネージド ドメインのドメイン コントローラーに接続する。
* マネージド ドメインにドメイン コントローラーを追加する。
* マネージド ドメインに対する "*ドメイン管理者*" 特権や "*エンタープライズ管理者*" 特権はありません。

## <a name="sign-in-to-the-windows-server-vm"></a>Windows Server VM にサインインする

前のチュートリアルでは、Windows Server VM を作成してマネージド ドメインに参加させました。 その VM を使用して管理ツールをインストールします。 必要であれば、[チュートリアルの手順に従って Windows Server VM を作成し、マネージド ドメインに参加][create-join-windows-vm]させてください。

> [!NOTE]
> このチュートリアルでは、マネージド ドメインに参加している Azure の Windows Server VM を使用します。 マネージド ドメインに参加している Windows クライアント (Windows 10 など) を使用することもできます。
>
> Windows クライアントに管理ツールをインストールする方法の詳細については、[リモート サーバー管理ツール (RSAT) のインストール](https://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx)に関するページを参照してください。

まず、次の手順に従って Windows Server VM に接続します。

1. Azure portal で、左側にある **[リソース グループ]** を選択します。 VM の作成先となったリソース グループ (例: *myResourceGroup*) を選択し、VM (例: *myVM*) を選択します。
1. VM の **[概要]** ペインで **[接続]** を選択し、 **[Bastion]** を選択します。

    ![Azure portal から Bastion を使用して Windows 仮想マシンに接続する](./media/join-windows-vm/connect-to-vm.png)

1. VM の資格情報を入力し、 **[接続]** を選択します。

   ![Azure portal から Bastion ホストを使用して接続する](./media/join-windows-vm/connect-to-bastion.png)

必要に応じて、ポップアップの表示を Web ブラウザーに許可して、Bastion 接続を表示します。 VM への接続には数秒かかります。

## <a name="install-active-directory-administrative-tools"></a>Active Directory 管理ツールをインストールする

Active Directory 管理センター (ADAC) や AD PowerShell など、オンプレミス AD DS 環境と同じ管理ツールをマネージド ドメイン内で使用して管理します。 Windows Server コンピューターとクライアント コンピューターには、リモート サーバー管理ツール (RSAT) 機能の一部としてこれらのツールをインストールできます。 その後、*AAD DC Administrators* グループのメンバーは、マネージド ドメインに参加しているコンピューターから、それらの AD 管理ツールを使用してマネージド ドメインをリモートから管理することができます。

ドメイン参加済み VM に Active Directory 管理ツールをインストールするには、次の手順を実行します。

1. VM にサインインしたときに **サーバー マネージャー** が既定で開かない場合は、 **[スタート]** メニューを選択し、 **[サーバー マネージャー]** を選択します。
1. **[サーバー マネージャー]** ウィンドウの *[ダッシュボード]* ウィンドウで **[役割と機能の追加]** を選択します。
1. *[役割と機能の追加]* ウィザードの **[開始する前に]** ページで **[次へ]** を選択します。
1. *[インストールの種類]* で、 **[役割ベースまたは機能ベースのインストール]** オプションが選択された状態にして **[次へ]** を選択します。
1. **[サーバーの選択]** ページで、サーバー プールから現在の VM (例: *myvm.aaddscontoso.com*) を選択し、 **[次へ]** を選択します。
1. **[サーバーの役割]** ページで、 **[次へ]** をクリックします。
1. **[機能]** ページで、 **[リモート サーバー管理ツール]** ノードを展開し、次に **[役割管理ツール]** ノードを展開します。

    役割管理ツールの一覧から **[AD DS および AD LDS ツール]** 機能を選択し、 **[次へ]** を選択します。

    ![[機能] ページから "AD DS および AD LDS ツール" をインストールする](./media/tutorial-create-management-vm/install-features.png)

1. **[確認]** ページで **[インストール]** を選択します。 管理ツールのインストールには 1 分から 2 分かかる場合があります。
1. 機能のインストールが完了したら、 **[閉じる]** を選択して **[役割と機能の追加]** ウィザードを終了します。

## <a name="use-active-directory-administrative-tools"></a>Active Directory 管理ツールを使用する

管理ツールがインストールされている状態で、それらを使用してマネージド ドメインを管理する方法を見ていきましょう。 *AAD DC Administrators* グループのメンバーであるユーザー アカウントで VM にサインインしていることを確認してください。

1. **[スタート]** メニューから **[Windows 管理ツール]** を選択します。 前の手順でインストールした AD 管理ツールが一覧表示されます。

    ![サーバーにインストールされている管理ツール](./media/tutorial-create-management-vm/list-admin-tools.png)

1. **[Active Directory 管理センター]** を選択します。
1. マネージド ドメインの詳細を確認するために、左ペインでドメイン名を選択します (例: *aaddscontoso*)。 一覧の先頭に *AADDC Computers* および *AADDC Users* という名前の 2 つのコンテナーがあります。

    ![マネージド ドメインで使用できるコンテナーの一覧](./media/tutorial-create-management-vm/active-directory-administrative-center.png)

1. マネージド ドメインに属しているユーザーとグループを表示するには、 **[AADDC Users]** コンテナーを選択します。 このコンテナーには、Azure AD テナントのユーザー アカウントとグループが表示されています。

    次の出力例では、*Contoso Admin* という名前のユーザー アカウントと *AAD DC Administrators* のグループがこのコンテナーに表示されています。

    ![Active Directory 管理センターで Azure AD DS ドメイン ユーザーの一覧を表示する](./media/tutorial-create-management-vm/list-azure-ad-users.png)

1. マネージド ドメインに参加しているコンピューターを表示するには、 **[AADDC Computers]** コンテナーを選択します。 現在の仮想マシンのエントリ (例: *myVM*) が表示されます。 マネージド ドメインに参加しているすべてのデバイスのコンピューター アカウントが、この *AADDC Computers* コンテナーに格納されます。

Active Directory 管理センターの一般的な操作 (ユーザー アカウント パスワードのリセット、グループ メンバーシップの管理など) が利用できます。 これらの操作は、マネージド ドメインに直接作成されたユーザーとグループに対してのみ機能します。 ID 情報は、Azure AD "*から*" Azure AD DS へのみ同期されます。 Azure AD DS から Azure AD への書き戻しはありません。 Azure AD から同期されたユーザーのパスワードまたは管理対象グループ メンバーシップを変更し、それらの変更を同期して戻すことはできません。

マネージド ドメインにおける一般的な操作については、管理ツールの一部としてインストールされる "*Windows PowerShell 用 Active Directory モジュール*" を使用して管理することもできます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * マネージド ドメインで利用できる管理タスクを理解する
> * Windows Server VM に Active Directory 管理ツールをインストールする
> * Active Directory 管理センターを使用して一般的なタスクを実行する

他のアプリケーションからマネージド ドメインを安全に操作するには、セキュリティで保護されたライトウェイト ディレクトリ アクセス プロトコル (LDAPS) を有効にしてください。

> [!div class="nextstepaction"]
> [マネージド ドメイン用に Secure LDAP を構成する](tutorial-configure-ldaps.md)

<!-- INTERNAL LINKS -->
[create-azure-ad-tenant]: ../active-directory/fundamentals/sign-up-organization.md
[associate-azure-ad-tenant]: ../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md
[create-azure-ad-ds-instance]: tutorial-create-instance.md
[create-join-windows-vm]: join-windows-vm.md
[azure-bastion]: ../bastion/tutorial-create-host-portal.md