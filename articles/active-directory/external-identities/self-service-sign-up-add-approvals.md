---
title: セルフサービス サインアップ フローにカスタム承認を追加する - Azure AD
description: 外部 ID セルフサービス サインアップにカスタム承認ワークフローの API コネクタを追加する - Azure Active Directory (Azure AD)
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d41d7d45fd11f2dc26fc50182a7649b23cd21196
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/11/2021
ms.locfileid: "103008758"
---
# <a name="add-a-custom-approval-workflow-to-self-service-sign-up"></a>カスタム承認ワークフローをセルフサービス サインアップに追加する

[API コネクタ](api-connectors-overview.md)を使用すると、独自のカスタム承認ワークフローをセルフサービス サインアップと統合できます。これにより、テナントに作成されるゲスト ユーザー アカウントを管理できます。

この記事では、承認システムと統合する方法の例を示します。 この例では、セルフサービス サインアップ ユーザー フローがサインアップ プロセス中にユーザー データを収集し、データを承認システムに渡します。 承認システムでは、次のことが可能です。

- ユーザーを自動的に承認し、Azure AD にユーザー アカウントの作成を許可します。
- 手動レビューをトリガーします。 要求が承認された場合、承認システムは Microsoft Graph を使用してユーザー アカウントをプロビジョニングします。 承認システムは、アカウントが作成されたことをユーザーに通知することもできます。

> [!IMPORTANT]
>**2021 年 1 月 4 日以降**、Google は [WebView サインインのサポートを廃止](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html)します。 Gmail で Google フェデレーションまたはセルフサービス サインアップを使用している場合は、[基幹業務ネイティブ アプリケーションの互換性をテストする](google-federation.md#deprecation-of-webview-sign-in-support)必要があります。

## <a name="register-an-application-for-your-approval-system"></a>承認システム用のアプリケーションを登録する

承認システムをアプリケーションとして Azure AD テナントに登録することで、承認システムを Azure AD で認証し、承認システムがユーザーを作成するアクセス許可を持つようにすることができます。 詳しくは、[Microsoft Graph 用の認証および承認の基本](/graph/auth/auth-concepts)に関する記事をご覧ください。

1. [Azure Portal](https://portal.azure.com) に Azure AD 管理者としてサインインします。
2. **[Azure サービス]** で **[Azure Active Directory]** を選択します。
3. 左のメニューで、 **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
4. アプリケーションの **名前** を入力します (_Sign-up Approvals_ など)。

   <!-- ![Register an application for the approval system](./self-service-sign-up-add-approvals/approvals/register-an-approvals-application.png) -->

5. **[登録]** を選択します。 他のすべてのフィールドは既定値のままにすることができます。

   ![[登録] ボタンが強調表示されているスクリーンショット。](media/self-service-sign-up-add-approvals/register-approvals-app.png)

6. 左のメニューの **[管理]** で、 **[API のアクセス許可]** を選択してから、 **[アクセス許可の追加]** を選択します。
7. **[API アクセス許可の要求]** ページで、 **[Microsoft Graph]** を選択し、 **[アプリケーションのアクセス許可]** を選択します。
8. **[アクセス許可の選択]** で、 **[ユーザー]** を展開して、 **[User.ReadWrite.All]** チェック ボックスをオンにします。 このアクセス許可は、承認システムが承認時にユーザーを作成することを許可します。 **[アクセス許可の追加]** を選択します。

   ![[アプリケーションの登録] ページ](media/self-service-sign-up-add-approvals/request-api-permissions.png)

9. **[API のアクセス許可]** ページで、 **[(テナント名) に管理者の同意を与えます]** を選択し、 **[はい]** を選択します。
10. 左のメニューの **[管理]** で **[証明書とシークレット]** を選択してから、 **[新しいクライアント シークレット]** を選択します。
11. シークレットの **[説明]** を入力し (たとえば、_Approvals client secret (承認クライアント シークレット)_ )、クライアント シークレットが **期限切れになる** までの期間を選択します。 その後、 **[追加]** を選択します。
12. クライアント シークレットの値をコピーします。

    ![承認システムで使用するクライアント シークレットをコピーする](media/self-service-sign-up-add-approvals/client-secret-value-copy.png)

13. クライアント ID としての **アプリケーション ID** と、Azure AD で認証するために生成した **クライアント シークレット** を使用するように承認システムを構成します。

## <a name="create-the-api-connectors"></a>API コネクタを作成する

次に、セルフサービス サインアップ ユーザー フロー用の [API コネクタを作成します](self-service-sign-up-add-api-connector.md#create-an-api-connector)。 承認システム API には、次に示す例のように、2 つのコネクタとそれに対応するエンドポイントが必要です。 これらの API コネクタは次の処理を実行します。

- **承認状態の確認** - ユーザーが既存の承認要求を持っているか、既に拒否されているかどうかを確認するために、ユーザーが ID プロバイダーでサインインした直後に承認システムに呼び出しを送信します。 承認システムが自動承認の決定のみを行う場合、この API コネクタは必要ないことがあります。 "承認状態の確認" API コネクタの例。

  ![承認状態の確認 API コネクタの構成](./media/self-service-sign-up-add-approvals/check-approval-status-api-connector-config-alt.png)

- **[要求の承認]** - ユーザーが [属性のコレクション] ページに入力した後、ユーザー アカウントが作成される前に、承認を要求するために承認システムに呼び出しを送信します。 承認要求は、自動的に許可したり手動で確認したりすることができます。 "要求の承認" API コネクタの例。 

  ![要求の承認 API コネクタの構成](./media/self-service-sign-up-add-approvals/create-approval-request-api-connector-config-alt.png)

これらのコネクタを作成するには、「[API コネクタを作成する](self-service-sign-up-add-api-connector.md#create-an-api-connector)」の手順に従います。

## <a name="enable-the-api-connectors-in-a-user-flow"></a>ユーザー フローで API コネクタを有効にする

ここで、次の手順を使用して、セルフサービス サインアップ ユーザー フローに API コネクタを追加します。

1. [Azure Portal](https://portal.azure.com/) に Azure AD 管理者としてサインインします。
2. **[Azure サービス]** で **[Azure Active Directory]** を選択します。
3. 左側のメニューで、 **[External Identities]** を選択します。
4. **[ユーザー フロー]** を選択し、API コネクタを有効にするユーザー フローを選択します。
5. **[API connectors]\(API コネクタ\)** を選択し、ユーザー フローの次の手順で呼び出す API エンドポイントを選択します。

   - **ID プロバイダーを使用してサインインした後**:承認状態 API コネクタを選択します。たとえば、 _[Check approval status]\(承認状態の確認\)_ を選択します。
   - **ユーザーを作成する前**:承認要求 API コネクタを選択します。たとえば、 _[要求の承認]_ を選択します。

   ![API をユーザー フローに追加する](./media/self-service-sign-up-add-approvals/api-connectors-user-flow-api.png)

6. **[保存]** を選択します。

## <a name="control-the-sign-up-flow-with-api-responses"></a>API 応答を使用してサインアップ フローを制御する

承認システムは、呼び出し時にその応答を使用して、サインアップ フローを制御できます。 

### <a name="request-and-responses-for-the-check-approval-status-api-connector"></a>"承認状態の確認" API コネクタの要求と応答

API によって "承認状態の確認" API コネクタから受信した要求の例:

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

API に送信される正確な要求は、ID プロバイダーによって提供される情報によって異なります。 "email" は常に送信されます。

#### <a name="continuation-response-for-check-approval-status"></a>"承認状態の確認" の継続応答

**承認状態の確認** API エンドポイントは、次の場合に継続応答を返す必要があります。

- ユーザーが以前承認を要求していない。

継続応答の例:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

#### <a name="blocking-response-for-check-approval-status"></a>"承認状態の確認" のブロック応答

**承認状態の確認** API エンドポイントは、次の場合にブロック応答を返す必要があります。

- ユーザー承認が保留されている。
- ユーザーが拒否され、再度承認を要求することができない。

ブロック応答の例は次のとおりです。

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your access request is already processing. You'll be notified when your request has been approved.",
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
}
```

### <a name="request-and-responses-for-the-request-approval-api-connector"></a>"要求の承認" API コネクタの要求と応答

API によって受信された "要求の承認" API コネクタからの HTTP 要求の例:

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

API に送信される正確な要求は、ユーザーから収集される情報または ID プロバイダーによって提供される情報によって異なります。

#### <a name="continuation-response-for-request-approval"></a>"要求の承認" の継続応答

**要求の承認** API エンドポイントは、次の場合に継続応答を返す必要があります。

- ユーザーを **_自動的に承認_** できる。

継続応答の例:

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue"
}
```

> [!IMPORTANT]
> 継続応答を受信すると、Azure AD によってユーザー アカウントが作成され、そのユーザーがアプリケーションに送られます。

#### <a name="blocking-response-for-request-approval"></a>"要求の承認" のブロック応答

**要求の承認** API エンドポイントは、次の場合にブロック応答を返す必要があります。

- ユーザー承認要求が作成されて現在保留になっている。
- ユーザー承認要求が自動的に拒否された。

ブロック応答の例は次のとおりです。

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your account is now waiting for approval. You'll be notified when your request has been approved.",
}
```

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "Your sign up request has been denied. Please contact an administrator if you believe this is an error",
}
```

応答内の `userMessage` がユーザーに表示されます。次に例を示します。

![保留中の承認ページの例](./media/self-service-sign-up-add-approvals/approval-pending.png)

## <a name="user-account-creation-after-manual-approval"></a>手動承認後のユーザー アカウントの作成

手動による承認を取得した後、カスタム承認システムでは [Microsoft Graph](/graph/use-the-api) を使用して[ユーザー](/graph/azuread-users-concept-overview) アカウントを作成します。 承認システムがユーザー アカウントをプロビジョニングする方法は、ユーザーによって使用された ID プロバイダーによって異なります。

### <a name="for-a-federated-google-or-facebook-user-and-email-one-time-passcode"></a>Google または Facebook のフェデレーション ユーザーおよび電子メール ワンタイム パスコードの場合

> [!IMPORTANT]
> この方法を使用するには、承認システムは、`identities`、`identities[0]`、および `identities[0].issuer` が存在し、`identities[0].issuer` が 'facebook'、'google'、または 'mail' に一致することを明示的に確認する必要があります。

ユーザーが Google アカウント、Facebook アカウント、または電子メール ワンタイム パスコードを使用してサインインした場合は、[ユーザー作成 API](/graph/api/user-post-users?tabs=http) を使用できます。

1. 承認システムはユーザー フローから HTTP 要求を受信します。

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@outlook.com",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. 承認システムは、Microsoft Graph を使用してユーザー アカウントを作成します。

```http
POST https://graph.microsoft.com/v1.0/users
Content-type: application/json

{
 "userPrincipalName": "johnsmith_outlook.com#EXT@contoso.onmicrosoft.com",
 "accountEnabled": true,
 "mail": "johnsmith@outlook.com",
 "userType": "Guest",
 "identities": [
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value"
}
```

| パラメーター                                           | 必須 | 説明                                                                                                                                                            |
| --------------------------------------------------- | -------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| userPrincipalName                                   | はい      | API に送信された `email` 要求を取得し、`@` 文字を `_` に置き換え、これを `#EXT@<tenant-name>.onmicrosoft.com` の先頭に追加することによって生成できます。 |
| accountEnabled                                      | はい      | `true` に設定する必要があります。                                                                                                                                                 |
| mail                                                | はい      | API に送信される `email` 要求と同じです。                                                                                                               |
| userType                                            | はい      | `Guest`である必要があります。 このユーザーをゲスト ユーザーとして指定します。                                                                                                                 |
| ID                                          | はい      | フェデレーション ID 情報。                                                                                                                                    |
| \<otherBuiltInAttribute>                            | いいえ       | `displayName`、`city` などの他の組み込み属性。 パラメーター名は、API コネクタによって送信されるパラメーターと同じです。                            |
| \<extension\_\{extensions-app-id}\_CustomAttribute> | いいえ       | ユーザーに関するカスタム属性。 パラメーター名は、API コネクタによって送信されるパラメーターと同じです。                                                            |

### <a name="for-a-federated-azure-active-directory-user-or-microsoft-account-user"></a>Azure Active Directory のフェデレーション ユーザーまたは Microsoft アカウント ユーザーの場合

ユーザーがフェデレーション Azure Active Directory アカウントまたは Microsoft アカウントを使用してサインインする場合は、[招待 API](/graph/api/invitation-post) を使用してユーザーを作成し、必要に応じて[ユーザー更新 API](/graph/api/user-update) を使用してユーザーに追加の属性を割り当てる必要があります。

1. 承認システムはユーザー フローから HTTP 要求を受信します。

```http
POST <Approvals-API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "displayName": "John Smith",
 "city": "Redmond",
 "extension_<extensions-app-id>_CustomAttribute": "custom attribute value",
 "ui_locales":"en-US"
}
```

2. 承認システムは、API コネクタによって提供される `email` を使用して招待を作成します。

```http
POST https://graph.microsoft.com/v1.0/invitations
Content-type: application/json

{
    "invitedUserEmailAddress": "johnsmith@fabrikam.onmicrosoft.com",
    "inviteRedirectUrl" : "https://myapp.com"
}
```

応答の例:

```http
HTTP/1.1 201 OK
Content-type: application/json

{
    ...
    "invitedUser": {
        "id": "<generated-user-guid>"
    }
}
```

3. 承認システムは、招待されたユーザーの ID を使用して、収集されたユーザー属性でユーザーのアカウントを更新します (省略可能)。

```http
PATCH https://graph.microsoft.com/v1.0/users/<generated-user-guid>
Content-type: application/json

{
    "displayName": "John Smith",
    "city": "Redmond",
    "extension_<extensions-app-id>_AttributeName": "custom attribute value"
}
```

## <a name="next-steps"></a>次のステップ

- [Azure Function クイックスタート サンプル](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)を使用します。
- [手動承認を使用したゲスト ユーザーのセルフサービス サインアップのサンプル](code-samples-self-service-sign-up.md#custom-approval-workflows)をチェックアウトします。
