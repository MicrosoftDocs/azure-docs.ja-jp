---
title: "Azure Active Directory に新しいユーザーを追加する | Microsoft Docs"
description: "Azure Active Directory で新しいユーザーを追加する方法やユーザー情報を変更する方法を説明します。"
services: active-directory
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: e3673727-6bec-4fdc-87a4-d65b213c4c3c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/10/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 8f171456f74925eba16ebd6f56ced558a11f9d99
ms.openlocfilehash: 6c366a2b7e5017bbe67cf1dbefc0baec6cf1aed3


---
# <a name="add-new-users--or-users-with-microsoft-accounts-to-azure-active-directory"></a>新しいユーザーまたは Microsoft アカウントを持つユーザーを Azure Active Directory に追加する
ディレクトリに格納するユーザーを追加します。 この記事では、組織内の新しいユーザーを追加する方法と、Microsoft アカウントを持つユーザーを追加する方法について説明します。 Azure Active Directory の他のディレクトリからユーザーを追加する方法、またはパートナー会社からユーザーを追加する方法の詳細については、「 [Azure Active Directory で他のディレクトリまたはパートナー会社からユーザーを追加する](active-directory-create-users-external.md)」を参照してください。 既定では、追加されたユーザーに管理者のアクセス許可はありませんが、ロールはいつでも割り当てることができます。

## <a name="add-a-user"></a>ユーザーの追加
1. ディレクトリのグローバル管理者となっているアカウントで [Azure クラシック ポータル](https://manage.windowsazure.com) にサインインします。
2. **[Active Directory]**を選択し、組織のディレクトリの名前を選択します。
3. **[ユーザー]** タブを選択し、コマンド バーの **[ユーザーの追加]** をクリックします。
4. **[このユーザーに関する情報の入力]** ページの **[ユーザーの種類]** で次のいずれかを選択します。

   * **[組織内の新しいユーザー]** - ディレクトリに新しいユーザー アカウントを追加します。
   * **[既存の Microsoft アカウントを持つユーザー]** - ディレクトリに既存の Microsoft コンシューマー アカウント (Outlook アカウントなど) を追加します。
5. **[ユーザーの種類]**に応じて、ユーザー名 (新しいユーザーの場合) または電子メール アドレス (Microsoft アカウントを持つユーザーの場合) を入力します。
6. ユーザーの **[プロファイル]** ページで、姓と名およびユーザー フレンドリ名を入力し、**[ロール]** ボックスの一覧からユーザー ロールを選択します。 ユーザーおよび管理者のロールの詳細については、「 [Azure AD での管理者ロールの割り当て](active-directory-assign-admin-roles.md)」を参照してください。 ユーザーの **Multi-Factor Authentication を有効にする** かどうかを指定します。
7. **[一時パスワードの取得]** ページで、**[作成]** を選択します。

> [!IMPORTANT]
> 組織が複数のドメインを使用している場合は、ユーザー アカウントを追加するときに起きる、次の問題について理解しておく必要があります。
>
> * 複数のドメインで同じユーザー プリンシパル名 (UPN) を持つユーザー アカウントを追加するには、たとえば、**最初に** geoffgrisso@contoso.onmicrosoft.com, を追加し、**その後に** geoffgrisso@contoso.com を追加します。
> * geoffgrisso@contoso.onmicrosoft.com を追加する前に geoffgrisso@contoso.com を追加**しないでください**。 この順序は重要であり、なかなか元に戻すことができない場合があります。
>
>

## <a name="change-user-information"></a>ユーザー情報を変更する
ユーザーの属性は、オブジェクト ID を除いてすべて変更できます。

1. ディレクトリを開きます。
2. **[ユーザー]** タブをクリックし、変更するユーザーの表示名を選択します。
3. 変更が完了したら、 **[保存]**をクリックします。

編集しようとしているユーザーがオンプレミスの Active Directory サービスと同期している場合、この手順を使用してユーザー情報を編集することはできません。 このユーザーを編集するには、オンプレミスの Active Directory 管理ツールを使用します。

## <a name="guest-user-management-and-limitations"></a>ゲスト ユーザーの管理と制限事項
ゲスト アカウントは、SharePoint ドキュメントやアプリケーションなどの Azure リソースにアクセスするためにディレクトリに招待された、他のディレクトリのユーザーを表します。 ゲスト アカウントとは、その基になる UserType 属性が "Guest" に設定されている、ディレクトリ内のユーザー アカウントです。 通常のユーザー (具体的には、ディレクトリのメンバー) は、UserType 属性が "Member" となります。

ゲストは、ディレクトリ内で限定された権限のセットを持ちます。 これらの権限では、ディレクトリ内の他のユーザーに関する情報を検出するゲストの能力は制限されます。 ただし、作業するリソースに関連付けられているユーザーやグループと対話することはできます。 ゲスト ユーザーは次のことができます。

* ゲスト ユーザーが割り当てられている Azure サブスクリプションに関連付けられたその他のユーザーとグループを表示する
* 自分が所属しているグループのメンバーを表示する
* ユーザーの完全なメール アドレスを知っている場合にディレクトリ内の他のユーザーを検索する
* 検索したユーザーの限定された属性のみを表示する。表示名、電子メール アドレス、ユーザー プリンシパル名 (UPN)、およびサムネイル写真に限定されます
* ディレクトリの確認済みドメインの一覧を取得する
* アプリケーションに同意し、ディレクトリ内のメンバーと同一のアクセス権をゲストに付与する。

## <a name="set-guest-user-access-policies"></a>ゲスト ユーザー アクセス ポリシーの設定
ディレクトリの **[構成]** タブには、ゲスト ユーザーのアクセスを制御するオプションが含まれています。 これらのオプションの変更は、ディレクトリのグローバル管理者が Azure クラシック ポータルから行う必要があります。 現時点では、PowerShell や API では変更できません。

Azure クラシック ポータルで **[構成]** タブを開くには、**[Active Directory]** を選択し、ディレクトリの名前を選択します。

![Azure Active Directory の [構成] タブ][1]

その後、ゲスト ユーザーのアクセスを制御するオプションを編集できます。

![access control options for guest users][2]

## <a name="whats-next"></a>参照トピック
* [Azure Active Directory で他のディレクトリまたはパートナー会社からユーザーを追加する](active-directory-create-users-external.md)
* [Administer your Azure AD directory](active-directory-administer.md)
* [Manage passwords in Azure AD](active-directory-manage-passwords.md)
* [Manage groups in Azure AD](active-directory-manage-groups.md)

<!--Image references-->
[1]: ./media/active-directory-create-users/RBACDirConfigTab.png
[2]: ./media/active-directory-create-users/RBACGuestAccessControls.png



<!--HONumber=Feb17_HO2-->


