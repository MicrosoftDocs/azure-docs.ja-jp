---
title: Azure Active Directory Domain Services:マネージド ドメインのグループ ポリシーの管理 | Microsoft Docs
description: Azure Active Directory Domain Services のマネージド ドメインのグループ ポリシーを管理します
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 938a5fbc-2dd1-4759-bcce-628a6e19ab9d
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/22/2018
ms.author: ergreenl
ms.openlocfilehash: deaa6a9ff49fea2093c94a039aab0d7de0c95359
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177119"
---
# <a name="administer-group-policy-on-an-azure-ad-domain-services-managed-domain"></a>Azure AD Domain Services のマネージド ドメインのグループ ポリシーの管理
Azure Active Directory Domain Services には、"AADDC Users" コンテナーと "AADDC Computers" コンテナー用の組み込みのグループ ポリシー オブジェクト (GPO) が含まれています。 これらの組み込みの GPO を、マネージド ドメインのグループ ポリシーを構成するようにカスタマイズできます。 さらに、"AAD DC Administrators" グループのメンバーは、マネージド ドメイン内に、独自のカスタム OU を作成できます。 カスタム GPO を作成してカスタム OU にリンクすることもできます。 "AAD DC Administrators" グループに属するユーザーには、マネージド ドメインのグループ ポリシー管理者権限が付与されます。

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="before-you-begin"></a>開始する前に
この記事に記載されているタスクを実行するには、次が必要です。

1. 有効な **Azure サブスクリプション**。
2. オンプレミス ディレクトリまたはクラウド専用ディレクトリのいずれかと同期されている **Azure AD ディレクトリ** 。
3. **Azure AD ドメイン サービス** が Azure AD ディレクトリに対して有効である必要があります。 有効になっていない場合は、 [作業の開始に関するガイド](active-directory-ds-getting-started.md)に記載されているすべてのタスクを実行してください。
4. Azure AD Domain Services のマネージド ドメインの管理に使用する、 **ドメインに参加している仮想マシン** 。 そのような仮想マシンがない場合は、「[Windows Server 仮想マシンのマネージド ドメインへの参加](active-directory-ds-admin-guide-join-windows-vm.md)」で概要が示されているすべてのタスクに従います。
5. マネージド ドメインのグループ ポリシーを管理するには、ディレクトリに **"AAD DC Administrators' group" グループに属するユーザー アカウント** の資格情報が必要です。

<br>

## <a name="task-1---provision-a-domain-joined-virtual-machine-to-remotely-administer-group-policy-for-the-managed-domain"></a>タスク 1 - ドメインに参加している仮想マシンをプロビジョニングして、マネージド ドメインのグループ ポリシーをリモートで管理する
Azure AD Domain Services のマネージド ドメインは、Active Directory 管理センター (ADAC) や AD PowerShell などの使い慣れた Active Directory 管理ツールでリモート管理することができます。 同様に、マネージド ドメインのグループ ポリシーは、グループ ポリシー管理ツールを使用してリモートで管理できます。

Azure AD ディレクトリの管理者には、マネージド ドメイン上のドメイン コントローラーにリモート デスクトップで接続する権限はありません。 "AAD DC Administrators" グループのメンバーは、マネージド ドメインのグループ ポリシーをリモートで管理できます。 マネージド ドメインに参加している Windows サーバー/クライアント コンピューターでグループ ポリシー ツールを使用できます。 グループ ポリシー ツールは、マネージド ドメインに参加している Windows Server とクライアント コンピューターに、グループ ポリシー管理オプション機能の一部としてインストールできます。

最初のタスクでは、マネージド ドメインに参加している Windows Server 仮想マシンをプロビジョニングします。 手順については、「[Azure AD Domain Services のマネージド ドメインに Windows Server 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)」を参照してください。

## <a name="task-2---install-group-policy-tools-on-the-virtual-machine"></a>タスク 2 - 仮想マシンにグループ ポリシー ツールをインストールする
ドメインに参加している仮想マシンにグループ ポリシー管理ツールをインストールするには、次の手順を実行します。

1. Azure Portal に移動します。 左側のパネルの **[すべてのリソース]** をクリックします。 タスク 1 で作成した仮想マシンを見つけてクリックします。
2. [概要] タブの **[接続]** ボタンをクリックします。リモート デスクトップ プロトコル (.rdp) ファイルが作成されてダウンロードされます。

    ![Windows 仮想マシンに接続する](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)
3. VM に接続するには、ダウンロードした RDP ファイルを開きます。 メッセージが表示されたら、**[接続]** をクリックします。 ログイン プロンプトで、'AAD DC Administrators' グループに属しているユーザーの資格情報を使用します。 たとえば、ここでは 'bob@domainservicespreview.onmicrosoft.com' を使用します。 サインイン処理中に証明書の警告が表示される場合があります。 [はい] または [続行] をクリックして接続処理を続行します。
4. スタート画面で、 **[サーバー マネージャー]** を開きます。 [サーバー マネージャー] ウィンドウの中央ウィンドウで **[役割と機能の追加]** をクリックします。

    ![仮想マシンでのサーバー マネージャーの起動](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager.png)
5. **[役割と機能の追加]** ウィザードの **[開始する前に]** ページで **[次へ]** をクリックします。

    ![ページを開始する前に](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-begin.png)
6. **[インストールの種類]** ページで、**[役割ベースまたは機能ベースのインストール]** オプションが選択された状態にして **[次へ]** をクリックします。

    ![インストールの種類のページ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-type.png)
7. **[サーバーの選択]** ページで、サーバー プールから現在の仮想マシンを選択して **[次へ]** をクリックします。

    ![サーバー選択ページ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-server.png)
8. **[サーバーの役割]** ページで、**[次へ]** をクリックします。 サーバーに役割をインストールしないため、このページはスキップします。
9. **[機能]** ページで、**[グループ ポリシー管理]** 機能を選択します。

    ![機能ページ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management.png)
10. **[確認]** ページで、**[インストール]** をクリックして仮想マシンに グループ ポリシー管理機能をインストールします。 機能のインストールが正常に完了したら、**[閉じる]** をクリックして **[役割と機能の追加]** ウィザードを終了します。

    ![確認ページ](./media/active-directory-domain-services-admin-guide/install-rsat-server-manager-add-roles-gp-management-confirmation.png)

## <a name="task-3---launch-the-group-policy-management-console-to-administer-group-policy"></a>タスク 3 - グループ ポリシー管理コンソールを起動してグループ ポリシーを管理する
ドメインに参加している仮想マシンでグループ ポリシー管理コンソールを使用して、マネージド ドメインのグループ ポリシーを管理できます。

> [!NOTE]
> マネージド ドメインのグループ ポリシーを管理するには、"AAD DC Administrators" グループのメンバーである必要があります。
>
>

1. スタート画面で **[管理ツール]** をクリックします。 仮想マシンにインストールされた **グループ ポリシー管理**コンソールが表示されます。

    ![グループ ポリシー管理の起動](./media/active-directory-domain-services-admin-guide/gp-management-installed.png)
2. **[グループ ポリシー管理]** をクリックして、グループ ポリシー管理コンソールを起動します。

    ![グループ ポリシー コンソール](./media/active-directory-domain-services-admin-guide/gp-management-console.png)

## <a name="task-4---customize-built-in-group-policy-objects"></a>タスク 4 - 組み込みのグループ ポリシー オブジェクトをカスタマイズする
マネージド ドメインには、"AADDC Computers" コンテナーと "AADDC Users" コンテナーという 2 つの組み込みのグループ ポリシー オブジェクト (GPO) があります。 これらの GPO をカスタマイズして、マネージド ドメインのグループ ポリシーを構成できます。

1. **グループ ポリシー管理**コンソールで、**[フォレスト: contoso100.com]** ノードと **[ドメイン]** ノードをクリックして展開し、マネージド ドメインのグループ ポリシーを表示します。

    ![組み込みの GPO](./media/active-directory-domain-services-admin-guide/builtin-gpos.png)
2. これらの組み込みの GPO を、マネージド ドメインのグループ ポリシーを構成するようにカスタマイズできます。 組み込みの GPO をカスタマイズするには、GPO を右クリックし、**[編集...]** をクリックします。 グループ ポリシー構成エディター ツールで GPO をカスタマイズできます。

    ![組み込みの GPO の編集](./media/active-directory-domain-services-admin-guide/edit-builtin-gpo.png)
3. これで、**グループ ポリシー管理エディター** コンソールを使用して、組み込みの GPO を編集できます。 たとえば、次のスクリーン ショットは、組み込みの "AADDC Computers" GPO をカスタマイズする方法を示しています。

    ![GPO のカスタマイズ](./media/active-directory-domain-services-admin-guide/gp-editor.png)

## <a name="task-5---create-a-custom-group-policy-object-gpo"></a>タスク 5 - カスタム グループ ポリシー オブジェクト (GPO) を作成する
独自のカスタム グループ ポリシー オブジェクトを作成またはインポートできます。 また、マネージド ドメインで作成したカスタム OU にカスタム GPO をリンクすることもできます。 カスタム組織単位の作成方法の詳細については、[マネージド ドメインでのカスタム OU の作成](active-directory-ds-admin-guide-create-ou.md)に関する記事をご覧ください。

> [!NOTE]
> マネージド ドメインのグループ ポリシーを管理するには、"AAD DC Administrators" グループのメンバーである必要があります。
>
>

1. **グループ ポリシー管理**コンソールで、カスタム組織単位 (OU) をクリックして選択します。 OU を右クリックし、**[このドメインに GPO を作成し、このコンテナーにリンクする...]** をクリックします。

    ![カスタム GPO の作成](./media/active-directory-domain-services-admin-guide/gp-create-gpo.png)
2. 新しい GPO の名前を指定し、**[OK]** をクリックします。

    ![GPO の名前の指定](./media/active-directory-domain-services-admin-guide/gp-specify-gpo-name.png)
3. 新しい GPO が作成され、カスタム OU にリンクされます。 GPO を右クリックし、メニューの **[編集...]** をクリックします。

    ![新しく作成された GPO](./media/active-directory-domain-services-admin-guide/gp-gpo-created.png)
4. **グループ ポリシー管理エディター**を使用して、新しく作成された GPO をカスタマイズできます。

    ![新しい GPO のカスタマイズ](./media/active-directory-domain-services-admin-guide/gp-customize-gpo.png)


[グループ ポリシー管理コンソール](https://technet.microsoft.com/library/cc753298.aspx)の使用方法の詳細については、Technet を参照してください。

## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービス - 作業開始ガイド](active-directory-ds-getting-started.md)
* [Azure AD Domain Services のマネージド ドメインに Windows Server 仮想マシンを参加させる](active-directory-ds-admin-guide-join-windows-vm.md)
* [Azure AD Domain Services のマネージド ドメインの管理](active-directory-ds-admin-guide-administer-domain.md)
* [グループ ポリシー管理コンソール](https://technet.microsoft.com/library/cc753298.aspx)
