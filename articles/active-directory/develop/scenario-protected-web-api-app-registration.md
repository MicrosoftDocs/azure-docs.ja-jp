---
title: 保護された Web API のアプリの登録 | Azure
titleSuffix: Microsoft identity platform
description: 保護されている Web API をビルドする方法と、アプリを登録するために必要な情報について学習します。
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/15/2020
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: dcfedf2cceddb59d456d421c4846f3cd252a65b3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "101651866"
---
# <a name="protected-web-api-app-registration"></a>保護された Web API: アプリの登録

この記事では、保護された Web API のアプリ登録の詳細について説明します。

アプリを登録するための一般的な手順については、「[クイックスタート: Microsoft ID プラットフォームにアプリケーションを登録する](quickstart-register-app.md)」を参照してください。

## <a name="accepted-token-version"></a>承認済みトークンのバージョン

Microsoft ID プラットフォームでは、v1.0 トークンと v2.0 トークンを発行できます。 これらのトークンの詳細については、[アクセス トークン](access-tokens.md)に関するページを参照してください。

API で承認できるトークンのバージョンは、Azure portal で Web API アプリケーションの登録を作成するときの **[サポートされているアカウントの種類]** の選択によって異なります。

- **[サポートされているアカウントの種類]** の値が **[任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント (Skype、Xbox、Outlook.com など)]** の場合は、承認済みトークンのバージョンを v2.0 にする必要があります。
- それ以外の場合は、承認済みトークンのバージョンを v1.0 にできます。

アプリケーションを作成した後、これらの手順に従って、承認済みトークンのバージョンを決定または変更できます。

1. Azure portal で、アプリを選択して、 **[マニフェスト]** を選択します。
1. マニフェストで **accessTokenAcceptedVersion** プロパティを見つけます。
1. その値により、Web API で受け入れられるトークンのバージョンが Azure Active Directory (Azure AD) に対して指定されます。
    - 値が 2 の場合、Web API では v2.0 トークンが受け入れられます。
    - 値が **null** の場合、Web API では v1.0 トークンが受け入れられます。
1. トークンのバージョンを変更した場合は、 **[保存]** を選択します。

> [!NOTE]
> Web API で受け入れられるトークンのバージョンを指定します。 クライアントで、Microsoft ID プラットフォームに Web API 用のトークンを要求すると、クライアントは Web API が受け入れるトークンのバージョンを示すトークンを受け取ります。

## <a name="no-redirect-uri"></a>リダイレクト URI なし

ユーザーが対話形式でサインインすることがないため、Web API ではリダイレクト URI を登録する必要がありません。

## <a name="exposed-api"></a>公開される API

Web API に固有の他の設定は、公開されている API と公開されているスコープまたはアプリのロールです。

### <a name="application-id-uri-and-scopes"></a>アプリケーション ID の URI とスコープ

通常、スコープの形式は `resourceURI/scopeName` です。 Microsoft Graph の場合、スコープにはショートカットがあります。 たとえば、`User.Read` は `https://graph.microsoft.com/user.read` のショートカットです。

アプリの登録時に、これらのパラメーターを定義します。

- リソース URI
- 1 つまたは複数のスコープ
- 1 つまたは複数のアプリ ロール

既定では、アプリケーションの登録ポータルでは、リソース URI `api://{clientId}` を使用することをお勧めします。 この URI は一意ですが、人間が判読できるものではありません。 URI を変更する場合は、新しい値が一意になるようにしてください。 アプリケーション登録ポータルにより、[構成された発行元ドメイン](howto-configure-publisher-domain.md)が確実に使用されるようになります。

クライアント アプリケーションに対して、スコープは Web API に対する "*委任されたアクセス許可*" として表示され、アプリ ロールは "*アプリケーション アクセス許可*" として表示されます。

スコープは、アプリのユーザーに提示される同意ウィンドウにも表示されます。 そのため、次の場合にスコープについて説明する、対応する文字列を指定します。

- ユーザーに表示される場合。
- 管理者の同意を許可できる、テナント管理者に表示される場合。

アプリのロールにはユーザーが同意することはできません (自身の代わりに Web API を呼び出すアプリケーションによって使用されるため)。 テナント管理者は、アプリのロールを公開する Web API のクライアント アプリケーションに同意する必要があります。 詳細については、[管理者の同意](v2-admin-consent.md)に関する記事をご覧ください

### <a name="exposing-delegated-permissions-scopes"></a>委任されたアクセス許可 (スコープ) の公開

1. アプリケーションの登録で **[API の公開]** を選択します。
1. **[Scope の追加]** を選択します。
1. メッセージが表示されたら、 **[保存して続行]** を選択し、提案されたアプリケーション ID URI (`api://{clientId}`) を受け入れます。
1. 次の値を指定します。
    - **[スコープ名]** を選択し、「**access_as_user**」と入力します。
    - **[同意できるユーザー]** を選択し、 **[管理者とユーザー]** が選択されていることを確認します。
    - **[管理者の同意の表示名]** を選択し、「**Access TodoListService as a user**」と入力します。
    - **[管理者の同意の説明]** を選択し、「**Accesses the TodoListService web API as a user**」と入力します。
    - **[ユーザーの同意の表示名]** を選択し、「**Access TodoListService as a user**」と入力します。
    - **[ユーザーの同意の説明]** を選択し、「**Accesses the TodoListService web API as a user**」と入力します。
    - **[状態]** の値は、 **[有効]** に設定されたままにします。
 1. **[スコープの追加]** を選択します。

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>デーモン アプリで Web API が呼び出される場合

このセクションでは、デーモン アプリで安全に呼び出せるように保護された Web API を登録する方法を説明します。

- デーモン アプリはユーザーと対話しないため、"*アプリケーションのアクセス許可*" のみを宣言して公開します。 委任されたアクセス許可は意味がありません。
- テナント管理者は、API のアプリケーションのアクセス許可のいずれかにアクセスするために登録されているアプリケーションに対してのみ Web API トークンを発行するように、Azure AD に要求できます。

#### <a name="exposing-application-permissions-app-roles"></a>アプリケーションのアクセス許可 (アプリ ロール) の公開

アプリケーションのアクセス許可を公開するには、マニフェストを編集します。

1. ご利用のアプリケーションのアプリケーション登録の場合は、 **[マニフェスト]** を選択します。
1. マニフェストを編集するには、`appRoles` の設定を見つけて、アプリケーション ロールを追加します。 ロールの定義は、次のサンプル JSON ブロックで提供されています。
1. `allowedMemberTypes` は `"Application"` のみが設定されたままにします。
1. `id` が一意の GUID であることを確認します。
1. `displayName` と `value` にスペースが含まれていないことを確認します。
1. マニフェストを保存します。

次の例では、`appRoles` の内容を示します。`id` の値には、任意の一意の GUID を指定できます。

```json
"appRoles": [
  {
    "allowedMemberTypes": [ "Application" ],
    "description": "Accesses the TodoListService-Cert as an application.",
    "displayName": "access_as_application",
    "id": "ccf784a6-fd0c-45f2-9c08-2f9d162a0628",
    "isEnabled": true,
    "lang": null,
    "origin": "Application",
    "value": "access_as_application"
  }
],
```

#### <a name="ensuring-that-azure-ad-issues-tokens-for-your-web-api-to-only-allowed-clients"></a>Azure AD で、許可されたクライアントのみに Web API のトークンが発行されることを確認する

Web API によってアプリ ロールが確認されます ソフトウェア開発者は、このロールにより、アプリケーションのアクセス許可を公開します。 また、テナント管理者が API アクセスを承認するアプリに対してのみ API トークンを発行するように、Azure AD を構成することもできます。

この強化されたセキュリティを追加するには、次のようにします。

1. アプリの登録でアプリの **[概要]** ページに移動します。
1. **[ローカル ディレクトリでのマネージド アプリケーション]** で、アプリの名前のリンクを選択します。 この選択項目のラベルは、切り詰められている可能性があります。 たとえば、 **[ローカル ディレクトリでの...]** と表示されている場合があります。

   > [!NOTE]
   >
   > このリンクを選択すると、 **[Enterprise Application Overview]\(エンタープライズ アプリケーションの概要\)** ページに移動します。 このページには、アプリケーションを作成したテナントでのアプリケーションのサービス プリンシパルが関連付けられています。 ブラウザーの [戻る] ボタンを使用して、アプリの登録ページに移動することができます。

1. エンタープライズ アプリケーション ページの **[管理]** セクションで、 **[プロパティ]** ページを選択します。
1. Azure AD で特定のクライアントのみからの Web API へのアクセスを許可する場合は、 **[ユーザーの割り当てが必要ですか?]** を **[はい]** に設定します。

   > [!IMPORTANT]
   >
   > **[ユーザーの割り当てが必要ですか?]** を **[はい]** に設定すると、クライアントが Web API のアクセス トークンを要求したときに、Azure AD によって、クライアントのアプリ ロールの割り当てが確認されます。 クライアントがどのアプリ ロールにも割り当てられていない場合、Azure AD によって次のエラー メッセージが返されます: "invalid_client: AADSTS501051: アプリケーション \<application name\> は \<web API\> のロールに割り当てられていません。
   >
   > **[ユーザーの割り当てが必要ですか?]** を **[いいえ]** のままにした場合、クライアントが Web API のアクセス トークンを要求したときに、Azure AD ではアプリ ロールの割り当てが確認されません。 すべてのデーモン クライアント (つまり、クライアントの資格情報フローを使用するすべてのクライアント) では、その対象ユーザーを指定するだけで、API のアクセス トークンを取得できます。 すべてのアプリケーションで、そのアクセス許可を要求しなくても、API にアクセスできます。
   >
   > ただし、前のセクションで説明したように、Web API では常に、アプリケーションに (テナント管理者によって承認される) 適切なロールがあることを検証できます。API では、アクセス トークンにロール要求があり、この要求の値が正しいことを確認することで、この検証を行います 前の JSON サンプルでは、値は `access_as_application` です。

1. **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

このシナリオの次の記事である[アプリ コードの構成](scenario-protected-web-api-app-configuration.md)に関する記事に進みます。
