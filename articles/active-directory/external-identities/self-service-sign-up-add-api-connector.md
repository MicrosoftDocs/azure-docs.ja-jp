---
title: セルフサービスのサインアップ フローに API コネクタを追加する - Azure AD
description: ユーザー フローで使用するように Web API を構成します。
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 06/16/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f241fd038d0d7309d8e1e5578dd77f950261b68
ms.sourcegitcommit: c28fc1ec7d90f7e8b2e8775f5a250dd14a1622a6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/13/2020
ms.locfileid: "88165177"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>API コネクタをユーザー フローに追加する

[API コネクタ](api-connectors-overview.md)を使用するには、まず API コネクタを作成してから、ユーザー フローで有効にします。

## <a name="create-an-api-connector"></a>API コネクタを作成する

1. [Azure Portal](https://portal.azure.com/) に Azure AD 管理者としてサインインします。
2. **[Azure サービス]** で **[Azure Active Directory]** を選択します。
3. 左側のメニューで、 **[External Identities]** を選択します。
4. **[All API connectors (Preview)]\(すべての API コネクタ (プレビュー)\)** を選択し、 **[New API connector]\(新しい API コネクタ\)** を選択します。

   ![新しい API コネクタを追加する](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. 呼び出しの表示名を指定します。 たとえば、「**承認状態の確認**」などです。
6. API 呼び出しの **[エンドポイント URL]** を指定します。
7. API の認証情報を指定します。

   - 現在サポートされているのは [基本認証] のみです。 開発目的で基本認証を使用せずに API を使用する場合は、API で無視できるダミーの **[ユーザー名]** と **[パスワード]** を入力するだけです。 Azure 関数で API キーを使用するには、コードをクエリ パラメーターとして **[エンドポイント URL]** に含めることができます (例: https[]()://contoso.azurewebsites.net/api/endpoint<b>?code=0123456789</b>)。

   ![新しい API コネクタを追加する](./media/self-service-sign-up-add-api-connector/api-connector-config.png)
8. **[保存]** を選択します。

> [!IMPORTANT]
> 以前は、API に送信するユーザー属性 ("送信する要求") と、API から受け入れるユーザー属性 ("受信する要求") を構成する必要がありました。 現在は、既定ですべてのユーザー属性 (値を含む場合) が送信されるようになり、API ではすべてのユーザー属性を "継続" 応答で返すことができます。

## <a name="the-request-sent-to-your-api"></a>API に送信される要求
API コネクタによってユーザー属性 ("要求") が **HTTP POST** 要求として具体化され、JSON 本文のキーと値のペアとして送信されます。 属性は [Microsoft Graph](https://docs.microsoft.com/graph/api/resources/user?view=graph-rest-1.0#properties) ユーザー プロパティと同様にシリアル化されます。 

**要求の例**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
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

**[Azure Active Directory]**  >  **[外部 ID]**  >  **[カスタムのユーザー属性]** のエクスペリエンスで一覧表示されるユーザー プロパティとカスタム属性だけが、要求で送信できます。

カスタム属性は、ディレクトリ内に **extension_\<extensions-app-id>_AttributeName** の形式で存在しています。 API では、これと同じシリアル化された形式で要求を受け取ることを想定しています。 カスタム属性の詳細については、[セルフサービス サインアップ フローのカスタム属性の定義](user-flow-add-custom-attributes.md)に関するページを参照してください。

また、**UI ロケール ("ui_locales")** 要求は、すべての要求で既定で送信されます。 これによって、デバイスで構成されているユーザーのロケールがわかります。API ではこれを使用して、国際化応答を返すことができます。

> [!IMPORTANT]
> API エンドポイントが呼び出されたときに送信する要求に値がない場合、要求は API に送信されません。 API は、それが予期する値を明示的に確認するように設計する必要があります。

> [!TIP] 
> [**identities ("identities")** ](https://docs.microsoft.com/graph/api/resources/objectidentity?view=graph-rest-1.0) および **Email Address ("email")** の各要求は、テナントにアカウントを作成する前にユーザーを識別するために API によって使用できます。 "identities" 要求は、ユーザーが Google または Facebook などの ID プロバイダーで認証されるときに送信されます。 "email" は常に送信されます。

## <a name="enable-the-api-connector-in-a-user-flow"></a>ユーザー フローで API コネクタを有効にする

セルフサービス サインアップ ユーザー フローに API コネクタを追加するには、次の手順を実行します。

1. [Azure Portal](https://portal.azure.com/) に Azure AD 管理者としてサインインします。
2. **[Azure サービス]** で **[Azure Active Directory]** を選択します。
3. 左側のメニューで、 **[External Identities]** を選択します。
4. **[User flows (Preview)]\(ユーザー フロー (プレビュー)\)** を選択し、API コネクタを追加するユーザー フローを選択します。
5. **[API connectors]\(API コネクタ\)** を選択し、ユーザー フローの次の手順で呼び出す API エンドポイントを選択します。

   - **ID プロバイダーを使用してサインインした後**
   - **ユーザーを作成する前**

   ![API をユーザー フローに追加する](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. **[保存]** を選択します。

## <a name="after-signing-in-with-an-identity-provider"></a>ID プロバイダーを使用してサインインした後

サインアップ プロセスのこのステップでの API コネクタは、ID プロバイダー (Google、Facebook、Azure AD) でユーザーが認証された直後に呼び出されます。 このステップは、***属性コレクション ページ*** (ユーザーに提示される、ユーザー属性を収集するためのフォーム) の前にあります。 

<!-- The following are examples of API connector scenarios you may enable at this step:
- Use the email or federated identity that the user provided to look up claims in an existing system. Return these claims from the existing system, pre-fill the attribute collection page, and make them available to return in the token.
- Validate whether the user is included in an allow or deny list, and control whether they can continue with the sign-up flow. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>この手順で API に送信される要求の例
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
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

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>この手順での Web API からの予期される応答の種類

ユーザー フロー中に Web API で Azure AD から HTTP 要求を受信すると、次の応答が返されることがあります。

- 継続応答
- ブロック応答

#### <a name="continuation-response"></a>継続応答

継続応答は、ユーザー フローが次の手順 (属性収集ページ) に進む必要があることを示します。

継続応答では、API から要求が返されることがあります。 要求が API によって返されると、その要求によって次のことが行われます。

- 属性収集ページの入力フィールドに事前入力する。

[継続応答](#example-of-a-continuation-response)の例を参照してください。

#### <a name="blocking-response"></a>ブロック応答

ブロック応答によって、ユーザー フローは終了します。 API を使用してこれを意図的に発行して、ユーザーにブロック ページを表示することにより、ユーザー フローの継続を停止することができます。 ブロック ページには、API によって提供された `userMessage` が表示されます。

[ブロック応答](#example-of-a-blocking-response)の例を参照してください。

## <a name="before-creating-the-user"></a>ユーザーを作成する前

サインアップ プロセスのこのステップでの API コネクタは、属性コレクション ページが含まれている場合、その後に呼び出されます。 このステップは、Azure AD でユーザー アカウントが作成される前に必ず呼び出されます。 

<!-- The following are examples of scenarios you might enable at this point during sign-up: -->
<!-- 
- Validate user input data and ask a user to resubmit data.
- Block a user sign-up based on data entered by the user.
- Perform identity verification.
- Query external systems for existing data about the user and overwrite the user-provided value. -->

### <a name="example-request-sent-to-the-api-at-this-step"></a>この手順で API に送信される要求の例

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ //Sent for Google and Facebook identity providers
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

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>この手順での Web API からの予期される応答の種類

ユーザー フロー中に Web API で Azure AD から HTTP 要求を受信すると、次の応答が返されることがあります。

- 継続応答
- ブロック応答
- 検証応答

#### <a name="continuation-response"></a>継続応答

継続応答は、ユーザー フローが次の手順 (ディレクトリでのユーザーの作成) に進む必要があることを示します。

継続応答では、API から要求が返されることがあります。 要求が API によって返されると、その要求によって次のことが行われます。

- 属性収集ページから要求に既に割り当てられている値をオーバーライドする。

[継続応答](#example-of-a-continuation-response)の例を参照してください。

#### <a name="blocking-response"></a>ブロック応答
ブロック応答によって、ユーザー フローは終了します。 API を使用してこれを意図的に発行して、ユーザーにブロック ページを表示することにより、ユーザー フローの継続を停止することができます。 ブロック ページには、API によって提供された `userMessage` が表示されます。

[ブロック応答](#example-of-a-blocking-response)の例を参照してください。

### <a name="validation-error-response"></a>検証エラー応答
 API が検証エラー応答で応答すると、ユーザー フローは属性収集ページにとどまり、`userMessage` がユーザーに表示されます。 これで、ユーザーはフォームを編集して再送信することができます。 この種類の応答は、入力の検証に使用できます。

[検証エラー応答](#example-of-a-validation-error-response)の例を参照してください。

## <a name="example-responses"></a>応答の例

### <a name="example-of-a-continuation-response"></a>継続応答の例

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| パラメーター                                          | Type              | 必須 | 説明                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| version                                            | String            | はい      | API のバージョン。                                                                                                                                                                                                                                                                |
| action                                             | String            | はい      | 値は `Continue` とする必要があります。                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | いいえ       | 値は、API コネクタの構成の **[Claim to receive]\(受信する要求\)** とユーザー フローの **[ユーザー属性]** として選択した場合にディレクトリに格納できます。 **[アプリケーション要求]** として選択されている場合、値をトークンで返すことができます。                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | いいえ       | 返される要求に `_<extensions-app-id>_` が含まれている必要はありません。 値は、API コネクタの構成の **[Claim to receive]\(受信する要求\)** とユーザー フローの **[ユーザー属性]** として選択した場合にディレクトリに格納されます。 トークンではカスタム属性を返信できません。 |

### <a name="example-of-a-blocking-response"></a>ブロック応答の例

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
    "code": "CONTOSO-BLOCK-00"
}

```

| パラメーター   | Type   | 必須 | 説明                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| version     | String | はい      | API のバージョン。                                                    |
| action      | String | はい      | 値は `ShowBlockPage` とする必要があります                                              |
| userMessage | String | はい      | ユーザーに表示するメッセージ。                                            |
| code        | String | いいえ       | エラー コード。 デバッグの目的で使用できます。 ユーザーには表示されません。 |

**ブロック応答を使用したエンド ユーザー エクスペリエンス**

![ブロック ページの例](./media/api-connectors-overview/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>検証エラー応答の例

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
    "code": "CONTOSO-VALIDATION-00"
}
```

| パラメーター   | Type    | 必須 | 説明                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| version     | String  | はい      | API のバージョン。                                                    |
| action      | String  | はい      | 値は `ValidationError` とする必要があります。                                           |
| status      | Integer | はい      | ValidationError 応答の値 `400` である必要があります。                        |
| userMessage | String  | はい      | ユーザーに表示するメッセージ。                                            |
| code        | String  | いいえ       | エラー コード。 デバッグの目的で使用できます。 ユーザーには表示されません。 |

**検証エラー応答を使用したエンド ユーザー エクスペリエンス**

![検証ページの例](./media/api-connectors-overview/validation-error-postal-code.png)

## <a name="using-azure-functions"></a>Azure Functions の使用
API コネクタで使用する API エンドポイントを作成する簡単な方法として、Azure Functions で HTTP トリガーを使用できます。 Azure Functions を使用して、[たとえば](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)、検証ロジックを実行し、サインアップを特定のドメインに制限します。 大規模なシナリオでは、Azure Functions から他の Web API、ユーザー ストア、他のクラウド サービスを呼び出して起動することもできます。

## <a name="next-steps"></a>次のステップ

<!-- - Learn [where you can enable an API connector](api-connectors-overview.md#where-you-can-enable-an-api-connector-in-a-user-flow) -->
- [カスタム承認ワークフローをセルフサービス サインアップに追加する](self-service-sign-up-add-approvals.md)方法を確認する
- [Azure Function クイックスタート サンプル](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)を使用する。
<!-- - Learn how to [use API connectors to verify a user identity](code-samples-self-service-sign-up.md#identity-verification) -->
