---
title: ユーザー プロファイル情報を追加または更新する - Azure AD
description: Azure Active Directory でのユーザーのプロファイルに画像やジョブの詳細などの情報を追加する手順。
services: active-directory
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: how-to
ms.date: 04/11/2019
ms.author: ajburnle
ms.reviewer: jeffsta
ms.collection: M365-identity-device-management
ms.openlocfilehash: 27896bac6f5436fb6717e4bc1ab0a1f9381904ad
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92370918"
---
# <a name="add-or-update-a-users-profile-information-using-azure-active-directory"></a>Azure Active Directory を使用してユーザーのプロファイル情報を追加または更新する
Azure Active Directory (Azure AD) を使用して、プロファイル画像、職種別情報、および一部の設定などのユーザー プロファイル情報を追加します。 新しいユーザーの追加方法については、[Azure Active Directory でユーザーを追加または削除する方法](add-users-azure-active-directory.md)に関するページをご覧ください。

## <a name="add-or-change-profile-information"></a>ユーザー プロファイル情報の追加または変更
ご覧のとおり、ユーザーを作成するときに追加する情報より、ユーザー プロファイルの方が情報が多いです。 この追加情報は、すべて組織が必要に応じて追加することができるオプションです。

## <a name="to-add-or-change-profile-information"></a>プロファイル情報を追加または変更する
1. [Azure portal](https://portal.azure.com/) に組織のユーザー管理者としてサインインします。

2. **[Azure Active Directory]**、**[ユーザー]** の順に選択し、ユーザーを選択します。 たとえば、_Alain Charon_ を選びます。

    **[Alain Charon - Profile]** \(Alain Charon - プロファイル\) ページが表示されます。

    ![編集可能な情報を含むユーザー プロファイル ページ](media/active-directory-users-profile-azure-portal/user-profile-all-blade.png)

3. **[編集]** を選択し、利用可能な各セクションに含まれる情報をオプションで追加するか、更新します。

    ![編集可能な領域が表示されたユーザー プロファイル ページ](media/active-directory-users-profile-azure-portal/user-profile-edit.png)

    - **プロファイルの画像。** ユーザー アカウントのサムネイル画像を選択します。 この画像は、Azure Active Directory と myapps.microsoft.com ページなどのユーザーの個人ページに表示されます。

    - **ID。** 結婚後の姓など、ユーザーの ID 値を追加または追加の ID 値を更新します。 名と姓の値とは別に、この名前を設定することができます。 たとえば、イニシャル、会社名を含める場合や、表示される名前の順序を変更する場合に使用できます。 別の例として、名前が ‘Chris Green’ である 2 人のユーザーが存在する場合、ID 文字列を使用して、それらの名前を 'Chris B. Green' 'Chris R. Green (Contoso)' に設定することができます。

    - **ジョブ情報。** ユーザーの役職、部署またはマネージャーなど、仕事に関係する任意の情報を追加します。

    - **設定。** ユーザーが Azure Active Directory テナントにサインインできるかどうかを決定します。 ユーザーのグローバルな場所を指定することも可能です。

    - **連絡先情報。** ユーザーの電話または携帯電話の連絡先情報を除き、ユーザーの関連する連絡先情報を追加します (管理者のロールのユーザーに対しては、全体管理者のみが更新できます)。

    - **認証の連絡先情報。** この情報を確認し、ユーザーの有効な電話番号と電子メール アドレスがあることを確認します。 この情報は、サインインしようとしているユーザーが本当にそのユーザーであるかどうかを Azure Active Directory が確認するために使用されます。 認証の連絡先情報は全体管理者のみが更新できます。

4. **[保存]** を選択します。

    行った変更はそのユーザー用に保存されます。

    >[!Note]
    >権限ソースが Windows Server Active Directory であるユーザーの ID、連絡先情報、仕事情報を更新するには、Windows Server Active Directory を使用する必要があります。 次の同期のサイクルの完了を待機すると、更新の完了による変更が反映されています。

## <a name="next-steps"></a>次のステップ
ユーザーのプロファイルを更新したら、次の基本手順を実行できます。

- [ユーザーを追加または削除する](add-users-azure-active-directory.md)

- [ユーザーにロールを割り当てる](active-directory-users-assign-role-azure-portal.md)

- [基本的なグループを作成し、メンバーを追加する](active-directory-groups-create-azure-portal.md)

または、委任の割り当て、ポリシーの使用、ユーザー アカウントの共有など、他のユーザー管理タスクを実行することもできます。 他の実行可能なアクションの詳細については、「[Azure Active Directory のユーザー管理のドキュメント](../enterprise-users/index.yml)」を参照してください。
