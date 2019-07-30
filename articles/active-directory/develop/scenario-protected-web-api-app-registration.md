---
title: 保護されている Web API - アプリの登録 | Azure
description: 保護されている Web API をビルドする方法と、アプリを登録するために必要な情報について学習します。
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4622cffedc159ce85166eafe571ccb26c2c1b4d
ms.sourcegitcommit: 084630bb22ae4cf037794923a1ef602d84831c57
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/03/2019
ms.locfileid: "67536857"
---
# <a name="protected-web-api-app-registration"></a>保護された Web API:アプリの登録

この記事では、保護された Web API のアプリ登録の詳細について説明します。

「[クイック スタート:Microsoft ID プラットフォームにアプリケーションを登録する](quickstart-register-app.md)」で、アプリを登録する一般的な手順を参照してください。

## <a name="accepted-token-version"></a>承認済みトークンのバージョン

Microsoft ID プラットフォームのエンドポイントでは、2 種類のトークン (v1.0 トークンと v2.0 トークン) を発行できます。 これらのトークンの詳細については、[アクセス トークン](access-tokens.md)に関するページを参照してください。 承認済みトークンのバージョンは、アプリケーションの作成時に選択した **[サポートされているアカウントの種類]** によって異なります。

- **[サポートされているアカウントの種類]** の値が **[任意の組織のディレクトリ内のアカウントと、個人用の Microsoft アカウント (Skype、Xbox、Outlook.com など)]** の場合、承認済みトークンのバージョンは v2.0 にあります。
- それ以外の場合、承認済みトークンのバージョンは v1.0 になります。

アプリケーションを作成した後、これらの手順に従って、承認済みトークンのバージョンを決定または変更できます。

1. Azure portal では、自分のアプリを選択して、アプリ用の**マニフェスト**を選択します。
2. マニフェストで、 **"accessTokenAcceptedVersion"** を検索します。 その値が **2** であることに注意してください。 このプロパティにより、Web API で v2.0 トークンを受け入れることが Azure Active Directory (Azure AD) に示されます。 値が **null**  の場合、承認済みトークン バージョンは v1.0 です。
3. トークン バージョンを変更した場合は、 **[保存]** を選択します。

> [!NOTE]
> Web API によって、承認されるトークン バージョン (v1.0 または v2.0) が指定されます。 クライアントで、Microsoft ID プラットフォーム v2.0 エンドポイントから Web API 用のトークンが要求されると、Web API によって受け入れられるバージョンを示すトークンが取得されます。

## <a name="no-redirect-uri"></a>リダイレクト URI なし

ユーザーが対話形式でサインインすることがないため、Web API ではリダイレクト URI を登録する必要がありません。

## <a name="expose-an-api"></a>API の公開

Web API に固有の別の設定は、公開されている API と公開されているスコープです。

### <a name="resource-uri-and-scopes"></a>リソース URI とスコープ

通常、スコープは `resourceURI/scopeName` という形式になります。 Microsoft Graph の場合、スコープには `User.Read` などのショートカットがあります。 この文字列は、`https://graph.microsoft.com/user.read` のショートカットです。

アプリの登録時に、これらのパラメーターを定義する必要があります。

- リソース URI。 既定では、アプリケーションの登録ポータルで、`api://{clientId}` を使用することをお勧めします。 このリソース URI は一意ですが、人間が判読できるものではありません。 それを変更することはできますが、必ず、新しい値が一意になるようにしてください。
- 1 つまたは複数の*スコープ* (クライアント アプリケーションに対して、Web API の*委任されたアクセス許可* として表示されます)。
- 1 つまたは複数の*アプリ ロール* (クライアント アプリケーションに対して、Web API の*アプリケーションのアクセス許可* として表示されます)。

アプリのエンド ユーザーに示される同意画面には、スコープも表示されます。 そのため、スコープが説明される、対応する文字列を指定する必要があります。

- エンド ユーザーによって表示。
- 管理者の同意を許可できる、テナント管理者によって表示。

### <a name="exposing-delegated-permissions-scopes"></a>委任されたアクセス許可 (スコープ) の公開

1. アプリケーションの登録で **[API の公開]** セクションを選択します。
1. **[Scope の追加]** を選択します。
1. メッセージが表示されたら、 **[保存して続行]** を選択し、提案されたアプリケーション ID URI (`api://{clientId}`) を受け入れます。
1. これらのパラメーターを入力します。
      - **[スコープ名]** には、**access_as_user** を使用します。
      - **[同意できるユーザー]** では、 **[管理者とユーザー]** が選択されていることを確認します。
      - **[管理者の同意の表示名]** には、「**Access TodoListService as a user**」と入力します。
      - **[管理者の同意の説明]** には、「**Accesses the TodoListService Web API as a user**」と入力します。
      - **[ユーザーの同意の表示名]** には、「**Access TodoListService as a user**」と入力します。
      - **[ユーザーの同意の説明]** には、「**Accesses the TodoListService Web API as a user**」と入力します。
      - **[状態]** は **[有効]** に設定されたままにします。
      - **[スコープの追加]** を選択します。

### <a name="if-your-web-api-is-called-by-a-daemon-app"></a>デーモン アプリで Web API が呼び出される場合

このセクションでは、保護された Web API を登録し、デーモン アプリで安全に呼び出せるようにする方法を学習します。

- *アプリケーションのアクセス許可* を公開する必要があります。 デーモン アプリではユーザーとやり取りしないため、アプリケーションのアクセス許可のみを宣言します。したがって、委任されたアクセス許可は意味がありません。
- テナント管理者は、Web API のアプリケーションのアクセス許可のいずれかにアクセスするために登録されているアプリケーションにのみ、Web API のトークンを発行するように Azure Active Directory (Azure AD) に要求できます。

#### <a name="exposing-application-permissions-app-roles"></a>アプリケーションのアクセス許可 (アプリ ロール) の公開

アプリケーションのアクセス許可を公開するには、マニフェストを編集する必要があります。

1. ご利用のアプリケーションのアプリケーション登録の場合は、 **[マニフェスト]** を選択します。
1. `appRoles` 設定を見つけ、1 つまたは複数のアプリケーション ロールを追加して、マニフェストを編集します。 ロールの定義は、次のサンプル JSON ブロックで提供されています。 `allowedMemberTypes` は `"Application"` のみに設定したままにします。 `id` が一意の GUID であり、`displayName` と `value` にスペースが含まれていないことを確認します。
1. マニフェストを保存します。

次のサンプルでは、`appRoles` の内容が示されています (`id` は任意の一意の GUID にすることができます)。

```JSon
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

Web API によってアプリ ロールが確認されます (これが、開発者がアプリケーションのアクセス許可を公開する手段です)。しかし、API にアクセスするためにテナント管理者によって承認されたアプリにのみ、Web API のトークンを発行するように Azure AD を構成することもできます。 この強化されたセキュリティを追加するには、次のようにします。

1. アプリ登録用のアプリの **[概要]** ページの **[ローカル ディレクトリで管理されているアプリケーション]** で、ご利用のアプリの名前を含むリンクを選択します。 このフィールドのタイトルは切り捨てられている場合があります。 たとえば、 **[...で管理されているアプリケーション]** と表示されている場合があります。

   > [!NOTE]
   >
   > このリンクを選択したら、それを作成したテナントのアプリケーションに対するサービス プリンシパルに関連付けられている **[Enterprise Application Overview]\(エンタープライズ アプリケーションの概要\)** ページに移動します。 ブラウザーの [戻る] ボタンを使用して、アプリの登録ページに戻ることができます。

1. エンタープライズ アプリケーション ページの **[管理]** セクションで、 **[プロパティ]** ページを選択します。
1. Azure AD で特定のクライアントのみからの Web API へのアクセスを許可する場合は、 **[ユーザーの割り当てが必要ですか?]** を **[はい]** に設定します。

   > [!IMPORTANT]
   >
   > **[ユーザーの割り当てが必要ですか?]** を **[はい]** に設定すると、クライアントで Web API のアクセス トークンが要求されたときに、Azure AD によって、クライアントのアプリ ロールの割り当てが確認されます。 クライアントがどのアプリ ロールにも割り当てられていない場合、Azure AD によって `invalid_client: AADSTS501051: Application <application name> is not assigned to a role for the <web API>` というエラーが返されます。
   >
   > **[ユーザーの割り当てが必要ですか?]** を **[いいえ]** のままにした場合、*クライアントで Web API のアクセス トークンが要求されたときに、Azure AD によってアプリ ロールの割り当てが確認されません*。 すべてのデーモン クライアント (つまり、クライアント資格情報フローを使用するすべてのクライアント) では、その対象ユーザーを指定するだけで、API のアクセス トークンを取得できるようになります。 すべてのアプリケーションで、そのアクセス許可を要求しなくても、API にアクセスできるようになります。 しかし、前のセクションで説明したとおり、Web API では常に、アプリケーションに (テナント管理者によって承認される) 適切なロールがあることを検証できます。 API では、アクセス トークンにロール要求があり、この要求の値が正しいことを確認することで、この検証を行います (ここでは、値は `access_as_application` です)。

1. **[保存]** を選択します。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリのコード構成](scenario-protected-web-api-app-configuration.md)
