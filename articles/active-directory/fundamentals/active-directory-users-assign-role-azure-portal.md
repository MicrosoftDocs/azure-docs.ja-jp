---
title: ユーザーにディレクトリ ロールを割り当てる - Azure Active Directory | Microsoft Docs
description: Azure Active Directory を使って、ユーザーに管理者と管理者以外のロールを割り当てる方法について説明します。
services: active-directory
author: eross-msft
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 09/06/2018
ms.author: lizross
ms.reviewer: jeffsta
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: bd26fa53c91c53893c7f326afda5158fa430be1e
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56188139"
---
# <a name="assign-administrator-and-non-administrator-roles-to-users-with-azure-active-directory"></a>Azure Active Directory を使ってユーザーに管理者と管理者以外のロールを割り当てる
組織内のユーザーが Azure Active Directory (Azure AD) のリソースを管理するためのアクセス許可を必要としている場合は、ユーザーが実行するアクションに基づいて、Azure AD で適切なロールをユーザーに割り当てる必要があります。

使用可能なロールについて詳しくは、[Azure Active Directory での管理者ロールの割り当て](../users-groups-roles/directory-assign-admin-roles.md)に関するページを参照してください。 ユーザーの追加について詳しくは、「[Azure Active Directory に新しいユーザーを追加する](add-users-azure-active-directory.md)」をご覧ください。

## <a name="assign-roles"></a>ロールを割り当てる
ユーザーに Azure AD ロールを割り当てる一般的な方法は、ユーザーの **[ディレクトリ ロール]** ページです。

Privileged Identity Management (PIM) を使用してロールを割り当てることもできます。 PIM の使用方法について詳しくは、「[Privileged Identity Management のドキュメント](https://docs.microsoft.com/azure/active-directory/privileged-identity-management)」をご覧ください。

### <a name="to-assign-a-role-to-a-user"></a>ユーザーにロールを割り当てるには
1. ディレクトリの全体管理者アカウントを使用して、[Azure portal](https://portal.azure.com/) にサインインします。

2. **[Azure Active Directory]**、**[ユーザー]** の順に選択し、ロールを割り当てるユーザーを検索して選択します。 たとえば、_Alain Charon_ にします。

3. **[Alain Charon - プロファイル]** ページで、**[ディレクトリ ロール]** を選択します。

    **[Alain Charon - ディレクトリ ロール]** ページが表示されます。

4. **[ロールの追加]** を選択し、Alain に割り当てるロールを選択して (たとえば _[アプリケーション管理者]_)、**[選択]** を選択します。

    ![選択されたロールが示されているディレクトリ ロール ページ](media/active-directory-users-assign-role-azure-portal/directory-role-select-role.png)

    アプリケーション管理者ロールが Alain Charon に割り当てられて、**[Alain Charon - ディレクトリ ロール]** ページに表示されます。

## <a name="remove-a-role-assignment"></a>ロールの割り当てを削除する
ユーザーからロールの割り当てを削除する必要がある場合も、**[Alain Charon - ディレクトリ ロール]** ページから行うことができます。

### <a name="to-remove-a-role-assignment-from-a-user"></a>ユーザーからロールの割り当てを削除するには

1. **[Azure Active Directory]**、**[ユーザー]** の順に選択し、ロールの割り当てを削除するユーザーを検索して選択します。 たとえば、_Alain Charon_ を選びます。

2. **[ディレクトリ ロール]**、**[アプリケーション管理者]**、**[ロールを削除します]** の順に選択します。

    ![選択されたロールと削除オプションが示されているディレクトリ ロール ページ](media/active-directory-users-assign-role-azure-portal/directory-role-remove-role.png)

    アプリケーション管理者ロールが Alain Charon から削除されて、**[Alain Charon - ディレクトリ ロール]** ページに表示されなくなります。

## <a name="next-steps"></a>次の手順
- [ユーザーを追加または削除する](add-users-azure-active-directory.md)

- [プロファイル情報を追加または変更する](active-directory-users-profile-azure-portal.md)

- [別のディレクトリからのゲスト ユーザーの追加](../b2b/what-is-b2b.md)

または、委任の割り当て、ポリシーの使用、ユーザー アカウントの共有など、他のユーザー管理タスクを実行することもできます。 他の実行可能なアクションについては、[Azure Active Directory のユーザー管理のドキュメント](../users-groups-roles/index.yml)に関するページを参照してください。


