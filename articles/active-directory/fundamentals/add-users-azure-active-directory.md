---
title: ユーザーの追加または削除 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory を使用して新しいユーザーを追加する方法または既存のユーザーを削除する方法について説明します。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9cda9f976a7680a1338584e4308426683de82a79
ms.sourcegitcommit: 3341598aebf02bf45a2393c06b136f8627c2a7b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58802120"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Azure Active Directory を使用してユーザーを追加または削除する
Azure Active Directory (Azure AD) 組織に対して、新しいユーザーの追加または既存のユーザーの削除を行います。

## <a name="add-a-new-user"></a>新しいユーザーの追加
Azure Active Directory ポータルを使用して、新しいユーザーを作成できます。

### <a name="to-add-a-new-user"></a>新しいユーザーを追加するには
1. [Azure portal](https://portal.azure.com/) に組織のユーザー管理者としてサインインします。

2. **[Azure Active Directory]**、**[ユーザー]** の順に選択し、**[新しいユーザー]** を選択します。

    ![[ユーザー] - [すべてのユーザー] ページ ([新しいユーザー] が強調表示されている)](media/add-users-azure-active-directory/new-user-all-users-blade.png)

3. **[ユーザー]** ページで、必要な情報を入力します。

    ![新しいユーザーの追加 (ユーザー情報が入力された [ユーザー] ページ)](media/add-users-azure-active-directory/new-user-user-blade.png)

   - **[名前] (必須)。** 新しいユーザーの氏名です。 たとえば、Mary Parker などです。

   - **[ユーザー名] (必須)。** 新しいユーザーのユーザー名です。 たとえば、「 mary@contoso.com 」のように入力します。
    
       ユーザー名のドメイン部分には、既定の初期ドメイン名の <_yourdomainname_>.onmicrosoft.com、またはカスタム ドメイン名 (contoso.com など) のいずれかを使用する必要があります。 カスタム ドメイン名の作成方法の詳細については、[Azure Active Directory にカスタム ドメイン名を追加する方法](add-custom-domain.md)に関するページを参照してください。

   - **[プロファイル]。** オプションで、ユーザーに関する詳細情報を追加することができます。 後でユーザー情報を追加することもできます。 ユーザー情報の追加方法の詳細については、[ユーザー プロファイル情報を追加または変更する方法](active-directory-users-profile-azure-portal.md)に関するページを参照してください。

   - **[グループ]。** オプションで、1 つまたは複数の既存のグループにユーザーを追加できます。 後でグループにユーザーを追加することもできます。 グループへのユーザーの追加方法の詳細については、[基本的なグループを作成し、メンバーを追加する方法](active-directory-groups-create-azure-portal.md)に関するページを参照してください。

   - **[ディレクトリ ロール]。** 必要に応じて、Azure AD 管理者ロールにユーザーを追加することもできます。 ユーザーには、グローバル管理者を割り当てることも、Azure AD の限られた管理者ロールを 1 つ以上割り当てることもできます。 ロールの割り当て方法の詳細については、[ユーザーにロールを割り当てる方法](active-directory-users-assign-role-azure-portal.md)に関するページを参照してください。

4. **[パスワード]** ボックスに入力されている自動生成されたパスワードをコピーします。 このパスワードは、最初のサインイン プロセスのためにユーザーに渡す必要があります。

5. **作成**を選択します。

    ユーザーが作成され、Azure AD テナントに追加されます。

## <a name="add-a-new-user-within-a-hybrid-environment"></a>ハイブリッド環境内での新しいユーザーの追加
Azure Active Directory (クラウド) と Windows Server Active Directory (オンプレミス) の両方の環境がある場合は、既存のユーザー アカウントのデータを同期することによって新しいユーザーを追加できます。 ハイブリッド環境とユーザーの詳細については、[オンプレミスのディレクトリと Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)に関するページを参照してください。

## <a name="delete-a-user"></a>ユーザーの削除
Azure Active Directory ポータルを使用して、既存のユーザーを削除できます。

### <a name="to-delete-a-user"></a>ユーザーの削除
1. 組織のユーザー管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。

2. **[Azure Active Directory]**、**[ユーザー]** の順に選択し、Azure AD テナントから削除するユーザーを検索して選択します。 たとえば、_Mary Parker_ を選択します。

3. **[ユーザーの削除]** を選択します。

    ![[ユーザー] - [すべてのユーザー] ページ ([ユーザーの削除] が強調表示されている)](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

    ユーザーが削除され、**[ユーザー] - [すべてのユーザー]** ページに表示されなくなります。 ユーザーは、削除後 30 日間は **[削除済みのユーザー]** ページに表示され、その期間内であれば復元できます。 ユーザーの復元の詳細については、[最近削除されたユーザーを復元するか完全に削除する方法](active-directory-users-restore.md)に関するページを参照してください。 ユーザーが削除されると、そのユーザーによって使用されていたライセンスは、別のユーザーが使用できるようになります。

    >[!Note]
    >権限ソースが Windows Server Active Directory であるユーザーの ID、連絡先情報、仕事情報を更新するには、Windows Server Active Directory を使用する必要があります。 次の同期のサイクルの完了を待機すると、更新の完了による変更が反映されています。

## <a name="next-steps"></a>次の手順

ユーザーを追加したら、次の基本的なプロセスを実行できます。

- [プロファイル情報を追加または変更する](active-directory-users-profile-azure-portal.md)

- [ユーザーにロールを割り当てる](active-directory-users-assign-role-azure-portal.md)

- [基本的なグループを作成し、メンバーを追加する](active-directory-groups-create-azure-portal.md)

- [動的グループとユーザーを操作する](../users-groups-roles/groups-create-rule.md)

または、[別のディレクトリからのゲスト ユーザーの追加](../b2b/what-is-b2b.md)や、[削除されたユーザーの復元](active-directory-users-restore.md)など、他のユーザー管理タスクを実行することもできます。 他の実行可能なアクションの詳細については、「[Azure Active Directory のユーザー管理のドキュメント](../users-groups-roles/index.yml)」を参照してください。
