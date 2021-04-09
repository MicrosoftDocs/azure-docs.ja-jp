---
title: 動的グループと B2B コラボレーション - Azure Active Directory | Microsoft Docs
description: Azure Active Directory B2B コラボレーションを Azure AD の動的グループと共に使用する方法について説明します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 02/28/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b820b8b9606795709d03414fa14ec29a1b5c519
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92441557"
---
# <a name="dynamic-groups-and-azure-active-directory-b2b-collaboration"></a>動的グループと Azure Active Directory B2B コラボレーション

## <a name="what-are-dynamic-groups"></a>動的グループとは
Azure Active Directory (Azure AD) のセキュリティ グループ メンバーシップの動的な構成は、[Azure Portal](https://portal.azure.com) で利用できます。 管理者は、ユーザー属性 (userType、部門、国/地域など) に基づいて、Azure AD で作成されたグループのメンバーを設定するためのルールを指定できます。 メンバーを属性に基づいて自動的にセキュリティ グループに追加したり、セキュリティ グループから削除したりすることができます。 これらのグループを使用すると、アプリケーションやクラウド リソース (SharePoint サイト、ドキュメント) へのアクセスを付与したり、メンバーにライセンスを割り当てたりすることができます。 動的なグループの詳細については、「[Azure Active Directory の専用グループ](../fundamentals/active-directory-groups-create-azure-portal.md)」を参照してください。

動的グループを作成および使用するには、適切な [Azure AD Premium P1 または P2 ライセンス](https://azure.microsoft.com/pricing/details/active-directory/)が必要です。 詳細については「[Azure Active Directory で動的グループ メンバーシップの属性ベースのルールを作成する](../enterprise-users/groups-dynamic-membership.md)」の記事を参照してください。

## <a name="creating-an-all-users-dynamic-group"></a>"すべてのユーザー" の動的グループの作成
メンバーシップ ルールを使用し、テナント内のすべてのユーザーを含むグループを作成できます。 将来、ユーザーがテナントに追加されたり、テナントから削除されたりしたとき、メンバーシップは自動的に調整されます。

1. テナントのグローバル管理者またはユーザー管理者ロールが割り当てられたアカウントで [Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]** を選択します。
2. **[管理]** の下の **[グループ]** を選択し、 **[新しいグループ]** を選択します。
1. **[新しいグループ]** ページで、 **[グループの種類]** の下の **[セキュリティ]** を選択します。 新しいグループの **[グループ名]** と **[グループの説明]** を入力します。 
2. **[メンバーシップの種類]** で、 **[動的ユーザー]** 、 **[動的クエリの追加]** の順に選択します。 
4. **[ルール構文]** テキスト ボックスの上の **[編集]** を選択します。 **[ルール構文の編集]** ページで、テキスト ボックスに次の式を入力します。

   ```
   user.objectId -ne null
   ```
1. **[OK]** を選択します。 [ルール構文] ボックスにルールが表示されます。

   ![すべてのユーザーの動的グループに対するルール構文](media/use-dynamic-groups/all-user-rule-syntax.png)

1.  **[保存]** を選択します。 これで、新しい動的グループには、B2B ゲスト ユーザーとメンバー ユーザーが含まれるようになります。


1. **[新規グループ]** ページで **[作成]** をクリックして、グループを作成します。

## <a name="creating-a-group-of-members-only"></a>メンバーのみのグループの作成

グループでゲストユーザーを除外し、テナントのメンバーのみを含める場合は、前述のように動的グループを作成しますが、 **[ルール構文]** ボックスには次の式を入力します。

```
(user.objectId -ne null) and (user.userType -eq "Member")
```

次の図は、メンバーのみを含み、ゲストを除外するように変更された動的グループのルール構文を示しています。

![ユーザーの種類がメンバーと等しい場合のルールを示す](media/use-dynamic-groups/all-member-user-rule-syntax.png)

## <a name="creating-a-group-of-guests-only"></a>ゲストのみのグループの作成

ゲスト ユーザーにポリシー (Azure AD の条件付きアクセス ポリシーなど) を適用できるように、ゲスト ユーザーのみが含まれる新しい動的グループを作成すると便利な場合もあります。 前述のように動的グループを作成しますが、 **[ルール構文]** ボックスには次の式を入力します。

```
(user.objectId -ne null) and (user.userType -eq "Guest")
```

次の図は、ゲストのみを含み、メンバー ユーザーを除外するように変更された動的グループのルール構文を示しています。

![ユーザーの種類がゲストと等しい場合のルール](media/use-dynamic-groups/all-guest-user-rule-syntax.png)

## <a name="next-steps"></a>次のステップ

- [B2B コラボレーション ユーザーのプロパティ](user-properties.md)
- [B2B コラボレーション ユーザーのロールへの追加](add-guest-to-role.md)
- [B2B コラボレーション ユーザーの条件付きアクセス](conditional-access.md)