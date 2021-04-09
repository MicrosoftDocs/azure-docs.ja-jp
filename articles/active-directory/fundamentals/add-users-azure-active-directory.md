---
title: ユーザーの追加または削除 - Azure Active Directory | Microsoft Docs
description: Azure Active Directory を使用して新しいユーザーを追加する方法または既存のユーザーを削除する方法について説明します。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 03/05/2021
ms.author: ajburnle
ms.reviewer: krbain
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 36455b3f6395216bd1ed26c8bd193f0e3ca10b5b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104594599"
---
# <a name="add-or-delete-users-using-azure-active-directory"></a>Azure Active Directory を使用してユーザーを追加または削除する

Azure Active Directory (Azure AD) 組織に対して、新しいユーザーの追加または既存のユーザーの削除を行います。 ユーザーを追加または削除するには、ユーザー管理者またはグローバル管理者である必要があります。

## <a name="add-a-new-user"></a>新しいユーザーの追加

Azure Active Directory ポータルを使用して、新しいユーザーを作成できます。

新しいユーザーを追加するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com/) に組織のユーザー管理者としてサインインします。

1. 任意のページから *Azure Active Directory* を検索して選択します。

1. **[ユーザー]** を選択し、 **[新しいユーザー]** を選択します。

    ![[ユーザー] からユーザーを追加する - Azure AD の [すべてのユーザー]](media/add-users-azure-active-directory/add-user-in-users-all-users.png)

1. **[ユーザー]** ページで、このユーザーの情報を入力します。

   - **名前**。 必須。 新しいユーザーの氏名です。 たとえば、*Mary Parker* にします。

   - **ユーザー名**。 必須。 新しいユーザーのユーザー名です。 たとえば、「 `mary@contoso.com` 」のように入力します。

     ユーザー名のドメイン部分には、既定の初期ドメイン名の *\<yourdomainname>.onmicrosoft.com*、またはカスタム ドメイン名 (*contoso.com* など) のいずれかを使用する必要があります。 カスタム ドメイン名の作成方法の詳細については、「[Azure Active Directory ポータルを使用してカスタム ドメイン名を追加する](add-custom-domain.md)」を参照してください。

   - **グループ**。 オプションで、1 つまたは複数の既存のグループにユーザーを追加できます。 後でグループにユーザーを追加することもできます。 グループへのユーザーの追加方法の詳細については、「[Azure Active Directory を使用して基本グループを作成してメンバーを追加する](active-directory-groups-create-azure-portal.md)」を参照してください。

   - **ディレクトリ ロール**: ユーザーに Azure AD 管理アクセス許可が必要な場合は、Azure AD ロールに追加することができます。 ユーザーには、グローバル管理者を割り当てることも、Azure AD の限られた管理者ロールを 1 つ以上割り当てることもできます。 ロールの割り当て方法の詳細については、[ユーザーにロールを割り当てる方法](active-directory-users-assign-role-azure-portal.md)に関するページを参照してください。

   - **ジョブ情報**:こちらにはユーザーに関する詳細情報を追加できます。これは後で行うこともできます。 ユーザー情報の追加方法の詳細については、[ユーザー プロファイル情報を追加または変更する方法](active-directory-users-profile-azure-portal.md)に関するページを参照してください。

1. **[パスワード]** ボックスに表示されている自動生成されたパスワードをコピーします。 このパスワードは初回のサインインのためにユーザーに渡す必要があります。

1. **［作成］** を選択します

ユーザーが作成され、Azure AD 組織に追加されます。

## <a name="add-a-new-guest-user"></a>新しいゲスト ユーザーの追加

**[新しいユーザー]** ページから **[ユーザーの招待]** を選択して、お客様の組織とコラボレーションする新しいゲスト ユーザーを招待することもできます。 組織の外部コラボレーション設定で、ゲストを招待できるように構成されている場合、そのユーザーに招待メールが送信されます。共同作業を開始するには、ユーザーはこの招待を受け入れる必要があります。 B2B コラボレーション ユーザーの招待について詳しくは、[B2B ユーザーを Azure Active Directory に招待する](../external-identities/add-users-administrator.md)ことに関する記事をご覧ください。

## <a name="add-a-consumer-user"></a>コンシューマー ユーザーの追加

Azure Active Directory B2C (Azure AD B2C) ディレクトリにコンシューマー アカウントを手動で作成することが必要になるシナリオも考えられます。 コンシューマー アカウントの作成について詳しくは、[Azure AD B2C でコンシューマー ユーザーを作成および削除する](../../active-directory-b2c/manage-users-portal.md)ことに関する記事をご覧ください。

## <a name="add-a-new-user-within-a-hybrid-environment"></a>ハイブリッド環境内での新しいユーザーの追加

Azure Active Directory (クラウド) と Windows Server Active Directory (オンプレミス) の両方の環境がある場合は、既存のユーザー アカウントのデータを同期することによって新しいユーザーを追加できます。 ハイブリッド環境とユーザーの詳細については、[オンプレミスのディレクトリと Azure Active Directory の統合](../hybrid/whatis-hybrid-identity.md)に関するページを参照してください。

## <a name="delete-a-user"></a>ユーザーの削除

Azure Active Directory ポータルを使用して、既存のユーザーを削除できます。

>[!Note]
>組織内のユーザーを削除するには、グローバル管理者またはユーザー管理者のロールが割り当てられている必要があります。 グローバル管理者は、他の管理者を含むすべてのユーザーを削除できます。 ユーザー管理者は、管理者以外のユーザー、ヘルプデスク管理者、およびその他のユーザー管理者を削除できます。 詳細については、[Azure AD での管理者ロールのアクセス許可](../roles/permissions-reference.md)に関する記事を参照してください。

ユーザーを削除するには、次の手順に従います。

1. 組織のユーザー管理者アカウントを使用して [Azure portal](https://portal.azure.com/) にサインインします。

1. 任意のページから *Azure Active Directory* を検索して選択します。

1. Azure AD テナントから削除するユーザーを検索して選択します。 たとえば、_Mary Parker_ にします。

1. **[ユーザーの削除]** を選択します。

    ![[ユーザー] - [すべてのユーザー] ページ ([ユーザーの削除] が強調表示されている)](media/add-users-azure-active-directory/delete-user-all-users-blade.png)

ユーザーが削除され、 **[ユーザー] - [すべてのユーザー]** ページに表示されなくなります。 ユーザーは、削除後 30 日間は **[削除済みのユーザー]** ページに表示され、その期間内であれば復元できます。 ユーザーの復元の詳細については、「[Azure Active Directory を使用して最近削除されたユーザーを復元または削除する](active-directory-users-restore.md)」を参照してください。

ユーザーが削除されると、そのユーザーによって使用されていたライセンスは、他のユーザーが使用できるようになります。

>[!Note]
>権限ソースが Windows Server Active Directory であるユーザーの ID、連絡先情報、または仕事情報を更新するには、Windows Server Active Directory を使用する必要があります。 更新を完了した後、次の同期サイクルが完了するのを待ってから変更を確認する必要があります。

## <a name="next-steps"></a>次のステップ

ユーザーの追加が完了すると、次の基本的なプロセスを実行できます。

- [プロファイル情報を追加または変更する](active-directory-users-profile-azure-portal.md)

- [ユーザーにロールを割り当てる](active-directory-users-assign-role-azure-portal.md)

- [基本的なグループを作成し、メンバーを追加する](active-directory-groups-create-azure-portal.md)

- [動的グループとユーザーを操作する](../enterprise-users/groups-create-rule.md)

または、[別のディレクトリからのゲスト ユーザーの追加](../external-identities/what-is-b2b.md)や、[削除されたユーザーの復元](active-directory-users-restore.md)など、他のユーザー管理タスクを実行することもできます。 他の実行可能なアクションの詳細については、「[Azure Active Directory のユーザー管理のドキュメント](../enterprise-users/index.yml)」を参照してください。