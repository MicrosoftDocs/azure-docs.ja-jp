---
title: Azure Active Directory 登録アプリケーションにアプリ ロールを追加してトークンで受け取る方法
description: Azure Active Directory に登録されたアプリケーションにアプリ ロールを追加し、それらのロールにユーザーとグループを割り当てて、トークンの `roles` 要求で受け取る方法について説明します。
services: active-directory
documentationcenter: ''
author: kkrishna
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: kkrishna
ms.reviewer: ''
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 427e293c28f634df9f66a7210d79e0df0d4d063c
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/13/2019
ms.locfileid: "56164041"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>方法:アプリケーションにアプリ ロールを追加してトークンで受け取る

ロールベースのアクセス制御 (RBAC) は、アプリケーションにおいて承認を実施する一般的なメカニズムです。 RBAC を使用するとき、管理者は、個々のユーザーまたはグループではなく、ロールにアクセス許可を付与します。 その後、管理者はロールをさまざまなユーザーやグループに割り当てて、コンテンツや機能にだれがアクセスできるかを制御できます。

RBAC をアプリケーション ロールおよびロール要求と一緒に使用すると、開発者はほとんど手間をかけずにアプリでの承認を確実に行うことができます。

もう 1 つの方法として、[WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) で説明するように Azure AD グループおよびグループ要求を使用することもできます。 Azure AD グループとアプリケーション ロールは決して相互排他ではありません。一緒に使用することで、さらにきめ細かいアクセス制御を提供することができます。

## <a name="declare-roles-for-an-application"></a>アプリケーションのロールを宣言する

これらのアプリケーション ロールは [Azure portal](https://portal.azure.com) でアプリケーションの登録マニフェストに定義されます。  ユーザーがアプリケーションにサインインすると、Azure AD は、各ロール (ユーザーに個別に付与されたロール、およびグループ メンバーシップを通じて付与されているロール) の `roles` 要求を生成します。  ユーザーとグループのロールへの割り当ては、ポータルの UI を介して行うか、[Microsoft Graph](https://developer.microsoft.com/graph/docs/concepts/azuread-identity-access-management-concept-overview) を使用してプログラミングによって行うことができます。

### <a name="declare-app-roles-using-azure-portal"></a>Azure portal を使用してアプリ ロールを宣言する

1. [Azure Portal](https://portal.azure.com) にサインインします。
1. 一番上のバーで、自分のアカウントを選択し、**[ディレクトリの切り替え]** を選択します。
1. **[ディレクトリ + サブスクリプション]** ウィンドウが開いたら、**[お気に入り]** または **[すべてのディレクトリ]** リストで、アプリケーションを登録する Active Directory テナントを選択します。
1. 左側のナビゲーション バーで **[すべてのサービス]** を選択し、**[Azure Active Directory]** を選択します。
1. **[Azure Active Directory]** ウィンドウで、**[アプリの登録]** を選択してすべてのアプリケーションを一覧表示します。

     必要なアプリケーションが表示されていない場合は、**[アプリの登録]** リストの一番上にあるさまざまなフィルターを使用して表示内容を制限するか、一覧表示の下までスクロールしてアプリケーションを探します。

1. アプリ ロールを定義するアプリケーションを選択します。
1. アプリケーションのブレードで **[マニフェスト]** を選択します。
1. `appRoles` 設定を見つけたら、すべてのアプリケーション ロールを追加して、アプリケーション マニフェストを編集します。

     > [!NOTE]
     > このマニフェストの各ロール定義は、"Id" プロパティに対して有効な異なる **Guid** を含む必要があります。 各ロールの `"value"` プロパティは、アプリケーションのコードで使用されている文字列と正確に一致する必要があります。
     
1. マニフェストを保存します。

### <a name="examples"></a>例

次の例では、`users` に割り当てることができる `appRoles` を示します。

> [!NOTE]
>  `id` は一意の GUID であることが必要です。

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-98f8-45fd-aa4a-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

`users` または `applications` (あるいは両方) を対象とするアプリ ロールを定義できます。 `applications` で使用可能な場合、アプリ ロールはアプリケーションのアクセス許可として **[必要なアクセス許可]** ブレードに表示されます。 次の例では、`Application` を対象とするアプリ ロールを示します。

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "Consumer Apps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

### <a name="assign-users-and-groups-to-roles"></a>ロールにユーザーとグループを割り当てる

アプリケーションにアプリ ロールを追加したら、それらのロールにユーザーとグループを割り当てることができます。

1. **[Azure Active Directory]** ウィンドウで、**[Azure Active Directory]** の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。
1. **[すべてのアプリケーション]** を選択して、すべてのアプリケーションの一覧を表示します。

     必要なアプリケーションが表示されていない場合は、**[すべてのアプリケーション]** リストの一番上にあるさまざまなフィルターを使用して表示内容を制限するか、一覧表示の下までスクロールしてアプリケーションを探します。

1. ユーザーまたはグループをロールに割り当てるアプリケーションを選択します。
1. アプリケーションの左側のナビゲーション メニューで、**[ユーザーとグループ]** ウィンドウを選択します。
1. **[ユーザーとグループ]** 一覧の上部にある **[ユーザーの追加]** ボタンをクリックして、**[割り当ての追加]** ウィンドウを開きます。
1. **[割り当ての追加]** ウィンドウから **[ユーザーとグループ]** セレクターを選択します。

     ユーザーとセキュリティ グループの一覧と一緒に、特定のユーザーまたはグループを検索して探すためのテキスト ボックスが表示されます。 この画面では、一度に複数のユーザーとグループを選択できます。

1. ユーザーとグループの選択が終了したら、一番下の **[選択]** ボタンをクリックして次の段階に進みます。
1. **[割り当ての追加]** ウィンドウで **[ロールの選択]** セレクターを選択します。 アプリケーション マニフェストで前に宣言したすべてのロールが表示されます。
1. ロールを選択して、**[選択]** ボタンをクリックします。
1. 一番下の **[割り当て]** ボタンをクリックすると、ユーザーとグループのアプリへの割り当てが完了します。
1. 追加したユーザーとグループが、更新された **[ユーザーとグループ]** のリストに表示されていることを確認します。

## <a name="more-information"></a>詳細情報

- [Azure AD アプリケーション ロールとロール要求を使用した Web アプリでの承認 (サンプル)](https://azure.microsoft.com/resources/samples/active-directory-dotnet-webapp-roleclaims/)
- [アプリでのセキュリティ グループとアプリケーション ロールの使用 (ビデオ)](https://www.youtube.com/watch?v=V8VUPixLSiM)
- [グループ要求とアプリケーション ロールが追加された Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2014/12/18/azure-active-directory-now-with-group-claims-and-application-roles)
- [Azure Active Directory のアプリ マニフェスト](https://docs.microsoft.com/azure/active-directory/develop/reference-app-manifest)
- [AAD アクセス トークン](access-tokens.md)
- [AAD `id_tokens`](id-tokens.md)
