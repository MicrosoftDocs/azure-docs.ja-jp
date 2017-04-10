---
title: "Azure AD ドメイン サービス: AAD DC 管理者グループの作成 | Microsoft Docs"
description: "Azure Active Directory ドメイン サービスの概要"
services: active-directory-ds
documentationcenter: 
author: mahesh-unnikrishnan
manager: stevenpo
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory-ds
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/15/2017
ms.author: maheshu
translationtype: Human Translation
ms.sourcegitcommit: afe143848fae473d08dd33a3df4ab4ed92b731fa
ms.openlocfilehash: 5a7603148bd90ab164dbe34cd8770bc6484e6d69
ms.lasthandoff: 03/17/2017


---
# <a name="get-started-with-azure-ad-domain-services"></a>Azure AD ドメイン サービスの使用開始
この記事では、Azure AD テナントの Azure AD ドメイン サービスを有効にするために必要な構成タスクについて説明します。

## <a name="task-1-create-the-aad-dc-administrators-group"></a>タスク 1: "AAD DC 管理者" グループの作成
最初のタスクは、Azure Active Directory テナントでの管理グループの作成です。 この特別な管理グループは、 **AAD DC 管理者**と呼ばれます。 このグループのメンバーには、Azure AD ドメイン サービス管理対象ドメインにドメイン参加するコンピューターへの管理特権が付与されます。 ドメインに参加しているコンピューターでは、このグループは、’Administrators’ グループに追加されます。 さらに、このグループのメンバーは、リモート デスクトップを使用して、ドメインに参加しているコンピューターにリモート接続できます。  

> [!NOTE]
> Azure AD ドメイン サービスを使用して作成された管理対象ドメインに、ドメイン管理者またはエンタープライズ管理者の特権がありません。 管理対象ドメインでは、これらの権限はサービスによって予約されており、テナント内でユーザーが使用することはできません。 ただし、この構成タスクで作成した特殊な管理者グループを使用して、特権操作の一部を行うことはできます。 これらの操作には、ドメインへのコンピューターの参加、ドメインに参加しているコンピューターでの管理者グループへの所属、グループ ポリシーの構成などが含まれます。
>
>

この構成タスクでは、管理グループを作成し、そのグループにディレクトリ内の&1; 人以上のユーザーを追加します。 Azure AD ドメイン サービスの管理グループを作成するには、次の手順を実行します。

1. **Azure クラシック ポータル** ([https://manage.windowsazure.com](https://manage.windowsazure.com)) に移動します。
2. 左ウィンドウで、 **[Active Directory]** を選択します。
3. Azure AD Domain Services を有効にする Azure AD テナント (ディレクトリ) を選択します。 Azure AD ディレクトリごとに作成できるドメインは&1; つだけです。

    ![Select Azure AD Directory](./media/active-directory-domain-services-getting-started/select-aad-directory.png)
4. **[グループ]** タブをクリックします。
5. Azure AD テナントにグループを追加するには、ページの下部にある作業ウィンドウの **[グループの追加]** をクリックします。

    ![[グループの追加] ボタン](./media/active-directory-domain-services-getting-started/add-group-button.png)
6. **AAD DC Administrators**という名前のグループを作成します。 **[グループの種類]** を **[セキュリティ]** に設定します。

   > [!WARNING]
   > Azure AD Domain Services 管理対象ドメイン内でアクセスを有効にするには、この名前をそのまま使用してグループを作成します。
   >
   >

    ![Create administrator group](./media/active-directory-domain-services-getting-started/create-admin-group.png)
7. Azure AD ドメイン サービス内で管理者特権を付与するためにこのグループが使用されることを他のユーザーが理解できるように、このグループの説明を追加します。
8. グループが作成されたら、グループの名前をクリックして、このグループのプロパティを表示します。 ユーザーをこのグループのメンバーとして追加するには、下部のパネルの **[メンバーの追加]** をクリックします。

    ![[グループ メンバーの追加] ボタン](./media/active-directory-domain-services-getting-started/add-group-members-button.png)
9. **[メンバーの追加]** ダイアログ ボックスで、このグループのメンバーにするユーザーを選択し、チェック ボックスをオンします。

    ![管理者グループへのユーザーの追加](./media/active-directory-domain-services-getting-started/add-group-members.png)

<br>

## <a name="task-2-create-or-select-an-azure-virtual-network"></a>タスク 2: Azure 仮想ネットワークの作成または選択
次の構成タスクでは、 [Azure 仮想ネットワークを作成または選択します](active-directory-ds-getting-started-vnet.md)。

