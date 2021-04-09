---
title: ロールベースのアクセスをユーザーに許可する
titleSuffix: Azure Maps
description: ロールベースのアクセス制御を使用して、ユーザーに Azure Maps への承認を与えます
author: anastasia-ms
ms.author: v-stharr
ms.date: 06/17/2020
ms.topic: include
ms.service: azure-maps
services: azure-maps
manager: timlt
ms.openlocfilehash: 234c3358b98b7af677793fba58c1602a8bd976f0
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "101102820"
---
## <a name="grant-role-based-access-for-users-to-azure-maps"></a>Azure Maps に対するロールベースのアクセスをユーザーに許可する

"*Azure ロールベースのアクセス制御*" (Azure RBAC) を付与するには、Azure AD グループまたはセキュリティ プリンシパルを、1 つまたは複数の Azure Maps ロールの定義に割り当てます。 Azure Maps に使用できる Azure ロールの定義を表示するには、 **[アクセス制御 (IAM)]** に移動します。 **[ロール]** を選択してから、「*Azure Maps*」で始まるロールを検索します。

* Azure Maps への大量のユーザーのアクセスを効率的に管理するには、[Azure AD グループ](../../active-directory/fundamentals/active-directory-manage-groups.md)に関する記事を参照してください。
* ユーザーがアプリケーションに対する認証を実行できるようにするには、ユーザーを Azure AD で作成する必要があります。 「[Azure Active Directory を使用してユーザーを追加または削除する](../../active-directory/fundamentals/add-users-azure-active-directory.md)」を参照してください。

ユーザーのディレクトリを効果的に管理する方法の詳細については、[Azure AD](../../active-directory/fundamentals/index.yml) に関する記事を参照してください。

1. **[Azure Maps アカウント]** にアクセスします。 **[アクセス制御 (IAM)]**  >  **[ロールの割り当て]** の順に選択します。

    ![Azure RBAC を使用してアクセス権を付与する](../media/how-to-manage-authentication/how-to-grant-rbac.png)

2. **[ロールの割り当て]** タブの **[ロール]** で、 **[Azure Maps データ閲覧者]** や **[Azure Maps データ共同作成者]** などの組み込みの Azure Maps ロールの定義を選択します。 **[アクセスの割り当て先]** で **[Azure AD user, group, or service principal]\(Azure AD のユーザー、グループ、またはサービス プリンシパル\)** を選択します。 プリンシパルを名前で選択します。 次に、 **[保存]** を選択します。

   * 詳細については、[Azure ロールの割り当て](../../role-based-access-control/role-assignments-portal.md)に関する記事をご覧ください。

> [!WARNING]
> Azure Maps の組み込みロール定義では、多くの Azure Maps REST API に対する非常に大きな承認アクセスが提供されます。 ユーザーに対する API を最小限に制限するには、[カスタム ロール定義を作成してそれにユーザーを割り当てる](../../role-based-access-control/custom-roles.md)方法に関する記事を参照してください。 これにより、ユーザーがアプリケーションに対して持つことのできる特権は必要最低限になります。