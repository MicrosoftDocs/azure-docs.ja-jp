---
title: アプリ ロールを追加してトークンから取得する | Azure
titleSuffix: Microsoft identity platform
description: Azure Active Directory に登録されたアプリケーションにアプリ ロールを追加し、それらのロールにユーザーとグループを割り当てて、トークンの 'roles' 要求で受け取る方法について説明します。
services: active-directory
author: kalyankrishna1
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 11/13/2020
ms.author: kkrishna
ms.reviewer: marsma, kkrishna, jmprieur
ms.custom: aaddev
ms.openlocfilehash: fce963bd9ffdc6f768d7b3de4a9e4870add06136
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100104248"
---
# <a name="how-to-add-app-roles-to-your-application-and-receive-them-in-the-token"></a>方法: アプリケーションにアプリ ロールを追加してトークンで受け取る

ロールベースのアクセス制御 (RBAC) は、アプリケーションにおいて承認を実施する一般的なメカニズムです。 RBAC を使用するとき、管理者は、個々のユーザーまたはグループではなく、ロールにアクセス許可を付与します。 その後、管理者はロールをさまざまなユーザーやグループに割り当てて、コンテンツや機能にだれがアクセスできるかを制御できます。

RBAC をアプリケーション ロールおよびロール要求と一緒に使用すると、開発者はあまり手間をかけずにアプリでの承認を確実に行うことができます。

もう 1 つの方法として、GitHub 上の [active-directory-aspnetcore-webapp-openidconnect-v2](https://aka.ms/groupssample) コード サンプルで示されているように、Azure AD グループおよびグループ要求を使用することもできます。 Azure AD グループとアプリケーション ロールは相互排他ではありません。一緒に使用することで、さらにきめ細かいアクセス制御を提供することができます。

## <a name="declare-roles-for-an-application"></a>アプリケーションのロールを宣言する

アプリ ロールを定義するには、[Azure portal](https://portal.azure.com) を使用します。 アプリ ロールは通常、サービス、アプリ、または API を表すアプリケーション登録で定義されます。 ユーザーがアプリケーションにサインインすると、Azure AD によって、各ロール (ユーザーまたはサービス プリンシパルに個別に付与されたロール、およびグループ メンバーシップを通じて付与されているロール) の `roles` 要求が生成されます。 これは要求ベースの承認を実装するために使用できます。 アプリ ロールは[ユーザーまたはユーザー グループ](../manage-apps/add-application-portal-assign-users.md#assign-users-to-an-app)に割り当てることができます。 アプリ ロールは、別のアプリケーションのサービス プリンシパルに割り当てたり、[マネージド ID のサービス プリンシパルに](../managed-identities-azure-resources/how-to-assign-app-role-managed-identity-powershell.md)割り当てたりすることもできます。

> [!IMPORTANT]
> 現在のところ、サービス プリンシパルをグループに追加し、その後、アプリ ロールを割り当てる場合、Azure AD では、それが発行するトークンに `roles` 要求が追加されません。

Azure portal を使用してアプリ ロールを宣言するには、2 つの方法があります。

* [アプリ ロール UI](#app-roles-ui--preview) | プレビュー
* [アプリ マニフェスト エディター](#app-manifest-editor)

追加するロールの数は、Azure Active Directory によって適用されるアプリケーション マニフェストの制限に照らしてカウントされます。 このような制限については、[Azure Active Directory のアプリ マニフェスト リファレンス](reference-app-manifest.md)に関するページの「[マニフェストの制限](./reference-app-manifest.md#manifest-limits)」セクションを参照してください。

### <a name="app-roles-ui--preview"></a>アプリ ロール UI | プレビュー

> [!IMPORTANT]
> アプリ ロール ポータルの UI 機能 [!INCLUDE [PREVIEW BOILERPLATE](../../../includes/active-directory-develop-preview.md)]

Azure portal のユーザー インターフェイスを使用してアプリ ロールを作成するには:

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 上部のメニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、アプリのロールを追加するアプリ登録を含む Azure Active Directory テナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** で **[アプリの登録]** を選択し、アプリ ロールを定義するアプリケーションを選択します。
1. **[App roles | Preview]\(アプリ ロール | プレビュー\)** を選択し、 **[Create app role]\(アプリ ロールの作成\)** を選択します。

   :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-overview-pane.png" alt-text="Azure portal の [アプリの登録] の [アプリのロール] ペイン":::
1. **[Create app role]\(アプリのロールの作成\)** ペインで、ロールの設定を入力します。 図の下の表では、各設定とそのパラメーターについて説明します。

    :::image type="content" source="media/howto-add-app-roles-in-azure-ad-apps/app-roles-create-context-pane.png" alt-text="Azure portal の [アプリの登録] のアプリ ロールによって作成されるコンテキスト ペイン":::

    | フィールド | 説明 | 例 |
    |-------|-------------|---------|
    | **表示名** | 管理者の同意やアプリの割り当て時に表示されるアプリのロールの表示名です。 この値にはスペースを含めることができます。 | `Survey Writer` |
    | **Allowed member types (許可されるメンバーの種類)** | このアプリのロールをユーザー、アプリケーション、またはその両方に割り当てることができるかどうかを指定します。<br/><br/>`applications` から使用できる場合、アプリ ロールは、アプリの登録の **[管理]** セクション > **[API のアクセス許可] > [アクセス許可の追加] > [自分の API] > [API を選択する] > [アプリケーションの許可]** の下にアプリケーションのアクセス許可として表示されます。 | `Users/Groups` |
    | **Value** | アプリケーション側でトークンに想定するロール要求の値を指定します。 この値は、アプリケーションのコードで参照される文字列と正確に一致する必要があります。 値にスペースを含めることはできません。 | `Survey.Create` |
    | **説明** | 管理者のアプリの割り当てと同意エクスペリエンスの間に表示されるアプリのロールの詳細な説明。 | `Writers can create surveys.` |
    | **Do you want to enable this app role? (このアプリのロールを有効にしますか?)** | アプリ ロールを有効にするかどうかを指定します。 アプリのロールを削除するには、このチェックボックスをオフにして、変更を適用してから削除操作を試行してください。 | *オン* |

1. **[適用]** を選択して変更を保存します。

### <a name="app-manifest-editor"></a>アプリ マニフェスト エディター

マニフェストを直接編集してロールを追加するには:

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 上部のメニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、アプリのロールを追加するアプリ登録を含む Azure Active Directory テナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** で **[アプリの登録]** を選択し、アプリ ロールを定義するアプリケーションを選択します。
1. 再び **[管理]** で、 **[マニフェスト]** を選択します。
1. `appRoles` 設定を見つけ、アプリケーション ロールを追加して、アプリ マニフェストを編集します。 `users` または `applications` (あるいは両方) を対象とするアプリ ロールを定義できます。 次の JSON スニペットでは、両方の例を示します。
1. マニフェストを保存します。

マニフェスト内の各アプリ ロールの定義には、`id` 値として一意の GUID が必要です。

各アプリ ロール定義の `value` プロパティは、アプリケーションのコードで使用されている文字列と正確に一致する必要があります。 `value` プロパティにスペースを含めることはできません。 スペースが含まれていると、マニフェストの保存時にエラーが発生します。

#### <a name="example-user-app-role"></a>例:ユーザーのアプリ ロール

この例では、`User` に割り当てることができる `Writer` という名前のアプリ ロールを定義します。

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "User"
      ],
      "displayName": "Writer",
      "id": "d1c2ade8-0000-0000-0000-6d06b947c66f",
      "isEnabled": true,
      "description": "Writers Have the ability to create tasks.",
      "value": "Writer"
    }
  ],
"availableToOtherTenants": false,
```

#### <a name="example-application-app-role"></a>例:アプリケーションのアプリ ロール

`applications` から使用できる場合、アプリ ロールは、アプリの登録の **[管理]** セクション > **[API のアクセス許可] > [アクセス許可の追加] > [自分の API] > [API を選択する] > [アプリケーションの許可]** の下にアプリケーションのアクセス許可として表示されます。

この例では、`Application` を対象とするアプリ ロールを示します。

```json
"appId": "8763f1c4-0000-0000-0000-158e9ef97d6a",
"appRoles": [
    {
      "allowedMemberTypes": [
        "Application"
      ],
      "displayName": "ConsumerApps",
      "id": "47fbb575-0000-0000-0000-0f7a6c30beac",
      "isEnabled": true,
      "description": "Consumer apps have access to the consumer data.",
      "value": "Consumer"
    }
  ],
"availableToOtherTenants": false,
```

## <a name="assign-users-and-groups-to-roles"></a>ロールにユーザーとグループを割り当てる

アプリケーションにアプリ ロールを追加したら、それらのロールにユーザーとグループを割り当てることができます。 ユーザーとグループのロールへの割り当ては、ポータルの UI を介して行うか、[Microsoft Graph](/graph/api/user-post-approleassignments) を使用してプログラミングによって行うことができます。 さまざまなアプリのロールに割り当てられたユーザーがアプリケーションにサインインすると、`roles` 要求で割り当てられたロールがトークンに付与されます。

Azure portal を使用してユーザーとグループをロールに割り当てるには:

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. **Azure Active Directory** の左側のナビゲーション メニューで **[エンタープライズ アプリケーション]** を選択します。
1. **[すべてのアプリケーション]** を選択して、すべてのアプリケーションの一覧を表示します。 アプリケーションが一覧に表示されない場合は、 **[すべてのアプリケーション]** 一覧の上部にあるフィルターを使用して一覧を制限するか、一覧を下にスクロールしてアプリケーションを見つけます。
1. ユーザーまたはグループをロールに割り当てるアプリケーションを選択します。
1. **[管理]** で **[ユーザーとグループ]** を選択します。
1. **[ユーザーの追加]** を選択し、 **[割り当ての追加]** ペインを開きます。
1. **[割り当ての追加]** ウィンドウから **[ユーザーとグループ]** セレクターを選択します。 ユーザーとセキュリティ グループの一覧が表示されます。 特定のユーザーまたはグループを検索することや、一覧に表示される複数のユーザーやグループを選択することができます。
1. ユーザーとグループを選択したら、 **[選択]** ボタンを選択して続行します。
1. **[割り当ての追加]** ペインで **[ロールの選択]** を選択します。 アプリケーションに対して定義されているすべてのロールが表示されます。
1. ロールを選択し、 **[選択]** ボタンを選択します。
1. **[割り当て]** ボタンを選択して、アプリへのユーザーとグループの割り当てを完了します。

追加したユーザーとグループが **[ユーザーとグループ]** の一覧に表示されることを確認します。

## <a name="assign-app-roles-to-applications"></a>アプリケーションへのアプリ ロールの割り当て

アプリケーションにアプリ ロールを追加したら、Azure portal を使用するか、[Microsoft Graph](/graph/api/user-post-approleassignments) を使用してプログラムでアプリ ロールをクライアント アプリに割り当てることができます。

アプリケーションにアプリ ロールを割り当てるときは、"*アプリケーションのアクセス許可*" を作成します。 アプリケーションのアクセス許可は、通常、認証と承認された API 呼び出しをユーザーによる操作なしで行う必要があるデーモン アプリまたはバックエンド サービスによって使用されます。

Azure portal を使用してアプリケーションにアプリ ロールを割り当てるには:

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. **Azure Active Directory** の左側のナビゲーション メニューで **[アプリの登録]** を選択します。
1. **[すべてのアプリケーション]** を選択して、すべてのアプリケーションの一覧を表示します。 アプリケーションが一覧に表示されない場合は、 **[すべてのアプリケーション]** 一覧の上部にあるフィルターを使用して一覧を制限するか、一覧を下にスクロールしてアプリケーションを見つけます。
1. アプリ ロールを割り当てるアプリケーションを選択します。
1. **[API のアクセス許可]** 、 **[アクセス許可の追加]** の順に選択します。
1. **[マイ API]** タブを選択し、アプリ ロールを定義したアプリを選択します。
1. **[アプリケーションのアクセス許可]** を選択します。
1. 割り当てるロールを選択します。
1. ロールの追加を完了するために、 **[アクセス許可の追加]** ボタンを選択します。

新しく追加されたロールは、アプリの登録の **[API のアクセス許可]** ペインに表示されます。

#### <a name="grant-admin-consent"></a>管理者の同意の付与

これらは委任されたアクセス許可ではなく、"*アプリケーションのアクセス許可*" であるため、アプリケーションに割り当てられたアプリ ロールの使用に管理者が同意を付与する必要があります。

1. アプリの登録の **[API のアクセス許可]** ペインで、 **[\<tenant name\> に管理者の同意を与えます]** を選択します。
1. 要求されたアクセス許可への同意の付与を求めるメッセージが表示されたら、 **[はい]** を選択します。

**[状態]** 列には、同意が **\<tenant name\> に付与された** ことが反映されているはずです。

## <a name="use-app-roles-in-your-web-api"></a>Web API でアプリ ロールを使用する

アプリ ロールを定義してユーザー、グループ、またはアプリケーションに割り当てたら、次の手順は、API が呼び出されたときにこれらのロールの有無を確認するコードを Web API に追加することです。 つまり、承認が必要と決めた API 操作がクライアント アプリから要求された場合、クライアント アプリの呼び出しに示されたアクセス トークン内にスコープがあることを API のコードで確認する必要があります。

Web API に承認を追加する方法については、「[保護された Web API: スコープとアプリのロールを検証する](scenario-protected-web-api-verification-scope-app-roles.md)」を参照してください。

## <a name="app-roles-vs-groups"></a>アプリ ロールとグループ

承認にはアプリ ロールまたはグループを使用できますが、両者の間の重要な違いは、実際のシナリオでどちらを使用するかの決定に影響する可能性があります。

| アプリ ロール                                                                          | グループ                                                      |
|------------------------------------------------------------------------------------|-------------------------------------------------------------|
| アプリケーションに固有のものであり、アプリの登録で定義されます。 これらはアプリケーションと共に移動します。 | アプリではなく Azure AD テナントに固有のものです。 |
| アプリ ロールは、アプリの登録が削除されると削除されます。                      | アプリが削除されても、グループはそのまま残ります。            |
| `roles` 要求で提供されます。                                                     | `groups` 要求で提供されます。                                 |

開発者はアプリ ロールを使用して、ユーザーがアプリにサインインできるか、Web API のアクセス トークンをアプリで取得できるかを制御できます。 このセキュリティ制御をグループにまで拡張するために、開発者と管理者は、セキュリティ グループをアプリ ロールに割り当てることもできます。

開発者がアプリ自体に承認のパラメーターを記述して制御する必要がある場合は、アプリ ロールをお勧めします。 たとえば、承認にグループを使用するアプリは、グループ ID と名前の両方が異なる可能性があるため、次のテナントで中断されます。 アプリ ロールを使用するアプリは安全なままです。 実際、アプリ ロールにグループを割り当てることは、同じ理由で SaaS アプリでも一般的です。

## <a name="next-steps"></a>次のステップ

アプリ ロールの詳細については、次のリソースを参照してください。

* コード サンプル (GitHub)
  * [グループおよびグループ要求を使用して ASP.NET Core Web アプリに承認を追加する](https://aka.ms/groupssample)
  * [.NET Core Web API を呼び出してアプリ ロールとセキュリティ グループを使用する、Angular の単一ページ アプリケーション (SPA)](https://github.com/Azure-Samples/ms-identity-javascript-angular-spa-dotnetcore-webapi-roles-groups/blob/master/README.md)
* リファレンス ドキュメント
  * [Azure AD のアプリ マニフェスト](./reference-app-manifest.md)
  * [Azure AD のアクセス トークン](access-tokens.md)
  * [Azure AD の ID トークン](id-tokens.md)
  * [アプリに省略可能な要求を提供する](active-directory-optional-claims.md)
* ビデオ:[Microsoft ID プラットフォームを使用してアプリケーションで承認を実装する](https://www.youtube.com/watch?v=LRoc-na27l0) (1:01:15)
