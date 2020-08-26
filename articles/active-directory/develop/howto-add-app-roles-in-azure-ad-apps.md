---
title: アプリ ロールを追加してトークンから取得する | Azure
titleSuffix: Microsoft identity platform
description: Azure Active Directory に登録されたアプリケーションにアプリ ロールを追加し、それらのロールにユーザーとグループを割り当てて、トークンの `roles` 要求で受け取る方法について説明します。
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 07/15/2020
ms.author: kkrishna
ms.reviewer: kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: 5a2acb08971bc0878c943047c42c9dc2a9525794
ms.sourcegitcommit: a2a7746c858eec0f7e93b50a1758a6278504977e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/12/2020
ms.locfileid: "88141433"
---
# <a name="how-to-add-app-roles-in-your-application-and-receive-them-in-the-token"></a>方法:アプリケーションにアプリ ロールを追加してトークンで受け取る

ロールベースのアクセス制御 (RBAC) は、アプリケーションにおいて承認を実施する一般的なメカニズムです。 RBAC を使用するとき、管理者は、個々のユーザーまたはグループではなく、ロールにアクセス許可を付与します。 その後、管理者はロールをさまざまなユーザーやグループに割り当てて、コンテンツや機能にだれがアクセスできるかを制御できます。

RBAC をアプリケーション ロールおよびロール要求と一緒に使用すると、開発者はほとんど手間をかけずにアプリでの承認を確実に行うことができます。

もう 1 つの方法として、[WebApp-GroupClaims-DotNet](https://github.com/Azure-Samples/WebApp-GroupClaims-DotNet) で説明するように Azure AD グループおよびグループ要求を使用することもできます。 Azure AD グループとアプリケーション ロールは決して相互排他ではありません。一緒に使用することで、さらにきめ細かいアクセス制御を提供することができます。

## <a name="declare-roles-for-an-application"></a>アプリケーションのロールを宣言する

これらのアプリケーション ロールは [Azure portal](https://portal.azure.com) でアプリケーションの登録マニフェストに定義されます。  ユーザーがアプリケーションにサインインすると、Azure AD は、各ロール (ユーザーに個別に付与されたロール、およびグループ メンバーシップを通じて付与されているロール) の `roles` 要求を生成します。  ユーザーとグループのロールへの割り当ては、ポータルの UI を介して行うか、[Microsoft Graph](/graph/azuread-identity-access-management-concept-overview) を使用してプログラミングによって行うことができます。

### <a name="declare-app-roles-using-azure-portal"></a>Azure portal を使用してアプリ ロールを宣言する

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータルツールバーの **[ディレクトリ + サブスクリプション]** アイコンを選択します。
1. **[お気に入り]** または **[すべてのディレクトリ]** リストで、アプリケーションを登録する Active Directory テナントを選択します。
1. Azure portal で、 **[Azure Active Directory]** を検索して選択します。
1. **[Azure Active Directory]** ウィンドウで、 **[アプリの登録]** を選択してすべてのアプリケーションを一覧表示します。
1. アプリ ロールを定義するアプリケーションを選択します。 次に、 **[マニフェスト]** を選択します。
1. `appRoles` 設定を見つけたら、すべてのアプリケーション ロールを追加して、アプリケーション マニフェストを編集します。

     > [!NOTE]
     > このマニフェストの各アプリ ロール定義には、`id` プロパティに対するマニフェストのコンテキスト内に有効な異なる GUID が含まれている必要があります。
     >
     > 各アプリ ロール定義の `value` プロパティは、アプリケーションのコードで使用されている文字列と正確に一致する必要があります。 `value` プロパティにスペースを含めることはできません。 スペースが含まれていると、マニフェストの保存時にエラーが発生します。

1. マニフェストを保存します。

### <a name="examples"></a>例

次の例では、`users` に割り当てることができる `appRoles` を示します。

> [!NOTE]
>`id` は一意の GUID であることが必要です。

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

> [!NOTE]
>`displayName` にはスペースを含めることができます。

`users` または `applications` (あるいは両方) を対象とするアプリ ロールを定義できます。 `applications` から使用できる場合、アプリのロールは、 **[管理]** セクション > **[API のアクセス許可] > [アクセス許可の追加] > [自分の API] > [API を選択する] > [アプリケーションの許可]** の下にアプリケーションのアクセス許可として表示されます。 次の例では、`Application` を対象とするアプリ ロールを示します。

```Json
"appId": "8763f1c4-f988-489c-a51e-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-859a-4941-89c9-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

定義するロールの数は、アプリケーション マニフェストの制限に影響します。 詳しい説明については、[マニフェストの制限](./reference-app-manifest.md#manifest-limits)に関するページを参照してください。

### <a name="assign-users-and-groups-to-roles"></a>ロールにユーザーとグループを割り当てる

アプリケーションにアプリ ロールを追加したら、それらのロールにユーザーとグループを割り当てることができます。

1. **[Azure Active Directory]** ウィンドウで、 **[Azure Active Directory]** の左側のナビゲーション メニューから **[エンタープライズ アプリケーション]** をクリックします。
1. **[すべてのアプリケーション]** を選択して、すべてのアプリケーションの一覧を表示します。

     必要なアプリケーションが表示されていない場合は、 **[すべてのアプリケーション]** リストの一番上にあるさまざまなフィルターを使用して表示内容を制限するか、一覧表示の下までスクロールしてアプリケーションを探します。

1. ユーザーまたはグループをロールに割り当てるアプリケーションを選択します。
1. アプリケーションの左側のナビゲーション メニューで、 **[ユーザーとグループ]** ペインを選択します。
1. **[ユーザーとグループ]** 一覧の上部にある **[ユーザーの追加]** ボタンをクリックして、 **[割り当ての追加]** ウィンドウを開きます。
1. **[割り当ての追加]** ウィンドウから **[ユーザーとグループ]** セレクターを選択します。

     ユーザーとセキュリティ グループの一覧と一緒に、特定のユーザーまたはグループを検索して探すためのテキスト ボックスが表示されます。 この画面では、一度に複数のユーザーとグループを選択できます。

1. ユーザーとグループの選択が終了したら、一番下の **[選択]** ボタンをクリックして次の段階に進みます。
1. **[割り当ての追加]** ウィンドウで **[ロールの選択]** セレクターを選択します。 アプリケーション マニフェストで前に宣言したすべてのロールが表示されます。
1. ロールを選択して、 **[選択]** ボタンをクリックします。
1. 一番下の **[割り当て]** ボタンをクリックすると、ユーザーとグループのアプリへの割り当てが完了します。
1. 追加したユーザーとグループが、更新された **[ユーザーとグループ]** のリストに表示されていることを確認します。

### <a name="receive-roles-in-tokens"></a>トークンでロールを受信する

さまざまなアプリのロールに割り当てられたユーザーがアプリケーションにサインインすると、`roles` 要求で割り当てられたロールがトークンに付与されます。

## <a name="more-information"></a>詳細情報

- [アプリ ロールおよびロール要求を使用して ASP.NET Core Web アプリに承認を追加する](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/5-WebApp-AuthZ/5-1-Roles)
- [Implement authorization in your applications with Microsoft identity platform (Microsoft ID プラットフォームを使用してアプリケーションで承認を実装する) (ビデオ)](https://www.youtube.com/watch?v=LRoc-na27l0)
- [グループ要求とアプリケーション ロールが追加された Azure Active Directory](https://techcommunity.microsoft.com/t5/Azure-Active-Directory-Identity/Azure-Active-Directory-now-with-Group-Claims-and-Application/ba-p/243862)
- [Azure Active Directory のアプリ マニフェスト](./reference-app-manifest.md)
- [AAD アクセス トークン](access-tokens.md)
- [AAD `id_tokens`](id-tokens.md)