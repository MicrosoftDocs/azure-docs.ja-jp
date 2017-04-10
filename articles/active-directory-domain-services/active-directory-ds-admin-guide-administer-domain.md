---
title: "Azure Active Directory ドメイン サービス: 管理対象ドメインを管理する | Microsoft Docs"
description: "Azure Active Directory ドメイン サービスで管理されているドメインを管理する"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: d4fdbc75-3e6b-4e20-8494-5dcc3bf2220a
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: d215fd31ca4652437783ad630aee532a17cda611
ms.lasthandoff: 11/17/2016


---
# <a name="administer-an-azure-active-directory-domain-services-managed-domain"></a>Azure Active Directory ドメイン サービスで管理されているドメインを管理する
この記事では、Azure Active Directory (AD) ドメイン サービスで管理されているドメインを管理する方法について説明します。

## <a name="before-you-begin"></a>開始する前に
この記事に記載されているタスクを実行するには、次が必要です。

1. 有効な **Azure サブスクリプション**。
2. オンプレミス ディレクトリまたはクラウド専用ディレクトリのいずれかと同期されている **Azure AD ディレクトリ** 。
3. **Azure AD ドメイン サービス** が Azure AD ディレクトリに対して有効である必要があります。 有効になっていない場合は、 [作業の開始に関するガイド](active-directory-ds-getting-started.md)に記載されているすべてのタスクを実行してください。
4. Azure AD Domain Services の管理対象ドメインの管理に使用する、 **ドメインに参加している仮想マシン** 。 そのような仮想マシンがない場合は、「[Windows Server 仮想マシンの管理対象ドメインへの参加](active-directory-ds-admin-guide-join-windows-vm.md)」で概要が示されているすべてのタスクに従います。
5. 管理対象ドメインを管理するには、ディレクトリの **"AAD DC Administrators" グループに属するユーザー アカウント** の資格情報が必要です。

<br>

## <a name="administrative-tasks-you-can-perform-on-a-managed-domain"></a>管理対象ドメインで実行できる管理タスク
"AAD DC Administrators" グループのメンバーには、管理対象ドメインで以下のようなタスクを実行できる権限が付与されます。

* コンピューターを管理対象ドメインに参加させる。
* 管理対象ドメイン内の 'AADDC Computers' と 'AADDC Users' のコンテナーに対して組み込みの GPO を構成する。
* 管理対象ドメイン上で DNS を管理する。
* 管理対象ドメイン上でカスタム組織単位 (OU) を作成し、管理する。
* 管理対象ドメインに参加しているコンピューターへの管理アクセスを取得する。

## <a name="administrative-privileges-you-do-not-have-on-a-managed-domain"></a>管理対象ドメインに対して付与されていない管理者特権
ドメインは、修正プログラムの適用、監視、バックアップの実行などのアクティビティを含め、Microsoft によって管理されます。 そのため、ドメインはロックされており、ユーザーには特定の管理タスクをドメインで実行する権限がありません。 例として、以下のようなタスクを実行できません。

* 管理対象ドメインのドメイン管理者特権またはエンタープライズ管理者権限は付与されません。
* 管理対象ドメインのスキーマを拡張することはできません。
* リモート デスクトップを使用して管理対象ドメインのドメイン コントローラーに接続することはできません。
* 管理対象ドメインにドメイン コントローラーを追加することはできません。

## <a name="task-1---provision-a-domain-joined-windows-server-virtual-machine-to-remotely-administer-the-managed-domain"></a>タスク 1 - ドメインに参加している Windows Server 仮想マシンをプロビジョニングして、管理対象ドメインをリモートで管理する
Azure AD ドメイン サービスの管理対象ドメインは、Active Directory 管理センター (ADAC) や AD PowerShell などの使い慣れた Active Directory 管理ツールで管理することができます。 テナント管理者には、管理対象ドメイン上のドメイン コントローラーにリモート デスクトップで接続する権限はありません。 そのため、"AAD DC Administrators" グループのメンバーは、管理対象ドメインに参加している Windows Server とクライアント コンピューターから、AD 管理ツールを使用して、リモートで管理対象ドメインを管理できます。 AD 管理ツールは、管理対象ドメインに参加している Windows Server とクライアント コンピューターで、リモート サーバー管理ツール (RSAT) のオプション機能の一部としてインストールできます。

最初の手順では、管理対象ドメインに参加している Windows Server 仮想マシンを設定します。 手順については、 [Azure AD Domain Services の管理対象ドメインへの Windows Server 仮想マシンの参加](active-directory-ds-admin-guide-join-windows-vm.md)に関するページを参照してください。

### <a name="remotely-administer-the-managed-domain-from-a-client-computer-for-example-windows-10"></a>クライアント コンピューター (Windows 10 など) から管理対象ドメインをリモートで管理する
この記事の手順では、AAD DS の管理対象ドメインを管理するために、Windows Server 仮想マシンを使用します。 ただし、Windows クライアント (Windows 10 など) の仮想マシンを使用することもできます。

TechNet の 手順に従って、Windows クライアントの仮想マシンで [リモート サーバー管理ツール (RSAT) をインストール](http://social.technet.microsoft.com/wiki/contents/articles/2202.remote-server-administration-tools-rsat-for-windows-client-and-windows-server-dsforum2wiki.aspx) できます。

## <a name="task-2---install-active-directory-administration-tools-on-the-virtual-machine"></a>タスク 2 - 仮想マシンに Active Directory 管理ツールをインストールする
ドメインに参加している仮想マシンに Active Directory 管理ツールをインストールするには、次の手順を実行します。 [リモート サーバー管理ツールのインストールおよび使用](https://technet.microsoft.com/library/hh831501.aspx)の詳細については、TechNet を参照してください。

1. Azure クラシック ポータルの **[Virtual Machines]** ノードに移動します。 タスク 1 で作成した仮想マシンを選択し、ウィンドウ下部にあるコマンド バーで **[接続]** をクリックします。

    ![Windows 仮想マシンに接続する](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
2. クラシック ポータルでは、仮想マシンへの接続に使用される ".rdp" という拡張子のファイルを開くか保存するように求められます。 ダウンロードが完了したら、ファイルをクリックして開きます。
3. ログイン プロンプトで、'AAD DC Administrators' グループに属しているユーザーの資格情報を使用します。 たとえば、ここでは 'bob@domainservicespreview.onmicrosoft.com' を使用します。
4. スタート画面で、 **[サーバー マネージャー]**を開きます。 [サーバー マネージャー] ウィンドウの中央ウィンドウで **[役割と機能の追加]** をクリックします。

    ![仮想マシンでのサーバー マネージャーの起動](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. **[役割と機能の追加]** ウィザードの **[開始する前に]** ページで **[次へ]** をクリックします。

    ![ページを開始する前に](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. **[インストールの種類]** ページで、**[役割ベースまたは機能ベースのインストール]** オプションが選択された状態にして **[次へ]** をクリックします。

    ![インストールの種類のページ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. **[サーバーの選択]** ページで、サーバー プールから現在の仮想マシンを選択して **[次へ]** をクリックします。

    ![サーバー選択ページ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. **[サーバーの役割]** ページで、**[次へ]** をクリックします。 サーバーに役割をインストールしないため、このページはスキップします。
9. **[機能]** ページで、**[リモート サーバー管理ツール]** ノードをクリックして展開し、次に **[役割管理ツール]** ノードをクリックして展開します。 役割管理ツールの一覧から、 **[AD DS および AD LDS ツール]** 機能を選択します。

    ![機能ページ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-ad-tools.png)
10. **[確認]** ページで、**[インストール]** をクリックして仮想マシンに AD および AD LDS ツールの機能をインストールします。 機能のインストールが正常に完了したら、**[閉じる]** をクリックして **[役割と機能の追加]** ウィザードを終了します。

    ![確認ページ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-confirmation.png)

## <a name="task-3---connect-to-and-explore-the-managed-domain"></a>タスク 3 - 管理対象ドメインへの接続と確認
ドメインに参加している仮想マシンに AD 管理ツールがインストールされたら、その管理ツールを使って管理対象ドメインを確認し、管理することができます。

> [!NOTE]
> 管理対象ドメインを管理するには、"AAD DC Administrators" グループのメンバーである必要があります。
>
>

1. スタート画面で **[管理ツール]**をクリックします。 仮想マシンにインストールされた AD 管理ツールを確認できます。

    ![Administrative Tools installed on server](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. **[Active Directory 管理センター]**をクリックします。

    ![[Active Directory 管理センター]](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. ドメインを確認するには、左ウィンドウのドメイン名 ("contoso100.com" など) をクリックします。 'AADDC Computers' と 'AADDC Users' という名前の 2 つのコンテナーがあります。

    ![ADAC: ドメインの表示](./media/active-directory-domain-services-admin-guide/adac-domain-view.png)
4. **AADDC Users** というコンテナーをクリックすると、管理対象ドメインに属しているすべてのユーザーとグループが表示されます。 ユーザー アカウントと Azure AD テナントからのグループがこのコンテナー内にあります。 この例では、"bob" という名前のユーザーのユーザー アカウントおよび "AAD DC Administrators" という名前のグループがこのコンテナーで利用できます。

    ![ADAC: ドメイン ユーザー](./media/active-directory-domain-services-admin-guide/adac-aaddc-users.png)
5. **AADDC Computers** という名前のコンテナーをクリックすると、この管理対象ドメインに参加しているコンピューターが表示されます。 ドメインに参加している現在の仮想マシンのエントリが表示されます。 Azure AD Domain Services の管理対象ドメインに参加しているすべてのコンピューターのコンピューター アカウントが、この "AADDC Computers" コンテナーに格納されます。

    ![ADAC: ドメインに参加しているコンピューター](./media/active-directory-domain-services-admin-guide/adac-aaddc-computers.png)

<br>

## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービス - 作業開始ガイド](active-directory-ds-getting-started.md)
* [Azure AD ドメイン サービスで管理されているドメインに Windows Server 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)
* [リモート サーバー管理ツールのデプロイ](https://technet.microsoft.com/library/hh831501.aspx)

