---
title: "Azure Active Directory Domain Services: 管理ガイド | Microsoft Docs"
description: "Azure AD ドメイン サービスの管理対象ドメインに組織単位 (OU) を作成する"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: 52602ad8-2b93-4082-8487-427bdcfa8126
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/06/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: 6e0da01df8ac5fd3cdb6b4e42dfbc08fab7d9615
ms.openlocfilehash: 5931d532a0790f1570d1d99687611231aafc7596
ms.lasthandoff: 02/08/2017


---
# <a name="create-an-organizational-unit-ou-on-an-azure-ad-domain-services-managed-domain"></a>Azure AD ドメイン サービスの管理対象ドメインに組織単位 (OU) を作成する
Azure AD ドメイン サービスの管理対象ドメインには&2; つの組み込みのコンテナーが含まれており、それぞれを "AADDC Computers"、"AADDC Users" と呼びます。 "AADDC Computers" コンテナーには、管理対象ドメインに参加しているすべてのコンピューターを対象としたコンピューター オブジェクトが含まれています。 "AADDC Users" コンテナーには、Azure AD テナント内のユーザーとグループが含まれています。 場合によっては、ワークロードをデプロイするために、管理対象ドメイン上にサービス アカウントを作成しなければならないことがあります。 その場合は、管理対象ドメイン上でカスタムの組織単位 (OU) を作成し、その OU 内でサービス アカウントを作成できます。 この記事では、管理対象ドメインに OU を作成する方法を説明します。

## <a name="before-you-begin"></a>開始する前に
この記事に記載されているタスクを実行するには、次が必要です。

1. 有効な **Azure サブスクリプション**。
2. オンプレミス ディレクトリまたはクラウド専用ディレクトリのいずれかと同期されている **Azure AD ディレクトリ** 。
3. **Azure AD ドメイン サービス** が Azure AD ディレクトリに対して有効である必要があります。 有効になっていない場合は、 [作業の開始に関するガイド](active-directory-ds-getting-started.md)に記載されているすべてのタスクを実行してください。
4. Azure AD Domain Services の管理対象ドメインの管理に使用する、ドメインに参加している仮想マシン。 そのような仮想マシンがない場合は、「[Windows Server 仮想マシンの管理対象ドメインへの参加](active-directory-ds-admin-guide-join-windows-vm.md)」で概要が示されているすべてのタスクに従います。
5. 管理対象ドメインでカスタム OU を作成するには、ディレクトリの **"AAD DC Administrators" グループに属するユーザー アカウント**の資格情報が必要です。

## <a name="install-ad-administration-tools-on-a-domain-joined-virtual-machine-for-remote-administration"></a>リモート管理のために、ドメインに参加している仮想マシンに AD 管理ツールをインストールする
Azure AD ドメイン サービスの管理対象ドメインは、Active Directory 管理センター (ADAC) や AD PowerShell などの使い慣れた Active Directory 管理ツールでリモート管理することができます。 テナント管理者には、管理対象ドメイン上のドメイン コントローラーにリモート デスクトップで接続する権限はありません。 管理対象ドメインを管理するには、管理対象ドメインに参加している仮想マシンに AD 管理ツール機能をインストールします。 手順については、「 [Azure AD ドメイン サービスで管理されているドメインの管理](active-directory-ds-admin-guide-administer-domain.md) 」をご覧ください。

## <a name="create-an-organizational-unit-on-the-managed-domain"></a>管理対象ドメイン上に組織単位を作成する
管理対象ドメインに参加している仮想マシンに AD 管理ツールをインストールしたら、そのツールを使って管理対象ドメインに組織単位を作成することができます。 次の手順に従います。

> [!NOTE]
> カスタム OU を作成するために必要な権限を持つのは、"AAD DC Administrators" グループのメンバーのみです。 次の手順を実施するときは、自分がこのグループに参加していることを確認してください。
>
>

1. スタート画面で **[管理ツール]**をクリックします。 仮想マシンにインストールされた AD 管理ツールを確認できます。

    ![Administrative Tools installed on server](./media/active-directory-domain-services-admin-guide/install-rsat-admin-tools-installed.png)
2. **[Active Directory 管理センター]**をクリックします。

    ![[Active Directory 管理センター]](./media/active-directory-domain-services-admin-guide/adac-overview.png)
3. ドメインを表示するには、左ウィンドウのドメイン名 ("contoso100.com" など) をクリックします。

    ![ADAC - view domain](./media/active-directory-domain-services-admin-guide/create-ou-adac-overview.png)
4. 右側にある **[タスク]** ウィンドウで、ドメイン名のノードの下に表示されている **[新規]** をクリックします。 今回の例では、右側の **[タスク]** ウィンドウで "contoso100(local)" ノードの下に表示されている **[新規]** をクリックします。

    ![ADAC - new OU](./media/active-directory-domain-services-admin-guide/create-ou-adac-new-ou.png)
5. 組織単位を作成するオプションが表示されます。 **[組織単位]** をクリックして **[Create Organizational Unit (組織単位の作成)]** ダイアログを開きます。
6. **[Create Organizational Unit (組織単位の作成)]** ダイアログで、新しい OU の **[名前]** を指定します。 作成する OU について、簡単な説明を入力します。 OU について、 **[Managed By]** (管理者) フィールドの設定をすることも可能です。 カスタム OU を作成するには、 **[OK]**をクリックします。

    ![ADAC - create OU dialog](./media/active-directory-domain-services-admin-guide/create-ou-dialog.png)
7. 新しく作成した OU が AD 管理センター (ADAC) に表示されます。

    ![ADAC - OU created](./media/active-directory-domain-services-admin-guide/create-ou-done.png)

## <a name="permissionssecurity-for-newly-created-ous"></a>新しく作成した OU へのアクセス許可とセキュリティ
既定では、カスタム OU を作成したユーザー ("AAD DC Administrators" グループのメンバー) に、その OU に対する管理特権 (フル コントロール) が付与されます。 管理特権が付与されたら、ユーザーは他のユーザーや "AAD DC Administrators" グループに、必要に応じて権限を付与できるようになります。 次のスクリーンショットでは、新しい "MyCustomOU" 組織単位を作成した 'bob@domainservicespreview.onmicrosoft.com' というユーザーに、OU に対するフル コントロールの権限が付与されています。

 ![ADAC - new OU security](./media/active-directory-domain-services-admin-guide/create-ou-permissions.png)

## <a name="notes-on-administering-custom-ous"></a>カスタム OU の管理に関する注意事項
カスタム OU を作成したら、OU 内にユーザー、グループ、コンピューター、サービス アカウントを作成できます。 "AAD DC Users" OU からカスタム OU にユーザーまたはグループを移動することはできません。

> [!WARNING]
> カスタム OU に作成したユーザー アカウント、グループ、サービス アカウント、コンピューターのオブジェクトは、お使いの Azure AD テナントでは利用できません。 言い換えると、Azure AD Graph API を使用している場合や、Azure AD の UI 上ではこれらのオブジェクトは表示されません。 これらのオブジェクトは Azure AD ドメイン サービスの管理対象ドメイン内でのみ利用できます。
>
>

## <a name="related-content"></a>関連コンテンツ
* [Azure AD ドメイン サービスで管理されているドメインの管理](active-directory-ds-admin-guide-administer-domain.md)
* [管理対象ドメインでグループ ポリシーを構成する](active-directory-ds-admin-guide-administer-group-policy.md)
* [Active Directory 管理センター: はじめに](https://technet.microsoft.com/library/dd560651.aspx)
* [サービス アカウントのステップ バイ ステップ ガイド](https://technet.microsoft.com/library/dd548356.aspx)

