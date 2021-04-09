---
title: Azure Active Directory B2C を使用して Azure API Management API をセキュリティで保護する
description: Azure Active Directory B2C によって発行されたアクセス トークンを使用して、Azure API Management API エンドポイントをセキュリティで保護する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 60b7bb33dfbf29b7e448887ce992d03009133b2e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94953493"
---
# <a name="secure-an-azure-api-management-api-with-azure-ad-b2c"></a>Azure AD B2C を使用して Azure API Management API をセキュリティで保護する

Azure Active Directory B2C (Azure AD B2C) で認証されたクライアントのみが、ご利用の Azure API Management (APIM) API にアクセスできるようにする方法について説明します。 この記事の手順に従うことで、Azure AD B2C で発行された有効なアクセス トークンを含む要求のみにアクセスを制限する受信ポリシーを APIM で作成してテストできます。

## <a name="prerequisites"></a>前提条件

この記事の手順を続ける前に、次のリソースを用意しておく必要があります。

* [Azure AD B2C テナント](tutorial-create-tenant.md)
* ご利用のテナントで[登録されているアプリケーション](tutorial-register-applications.md)
* ご利用のテナントで[作成したユーザー フロー](tutorial-create-user-flows.md)
* Azure API Management で[公開された API](../api-management/import-and-publish.md)
* セキュリティで保護されたアクセスをテストするための [Postman](https://www.getpostman.com/) (省略可能)

## <a name="get-azure-ad-b2c-application-id"></a>Azure AD B2C アプリケーション ID を取得する

Azure API Management において Azure AD B2C を使用して API のセキュリティを保護する場合、APIM で作成する[受信ポリシー](../api-management/api-management-howto-policies.md)には複数の値が必要です。 ご利用の Azure AD B2C テナントで以前に作成したアプリケーションのアプリ ID を記録します。 前提条件で作成したアプリケーションを使用する場合は、"*webbapp1*" のアプリケーション ID を使用します。

アプリケーションを Azure AD B2C テナントに登録するには、Microsoft の新しい統合 **アプリの登録** エクスペリエンスか以前の **アプリケーション (レガシ)** エクスペリエンスを使用できます。 [この新しいエクスペリエンスの詳細を参照してください](./app-registrations-training-guide.md)。

#### <a name="app-registrations"></a>[アプリの登録](#tab/app-reg-ga/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択してから、 **[Owned applications]\(所有しているアプリケーション\)** タブを選択します。
1. *webapp1* または以前に作成した別のアプリケーションの **[Application (client) ID]\(アプリケーション (クライアント) ID\)** 列の値を記録します。

#### <a name="applications-legacy"></a>[アプリケーション (レガシ)](#tab/applications-legacy/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. 上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. 左側のメニューで、 **[Azure AD B2C]** を選択します。 または、 **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[管理]** で、 **[アプリケーション (レガシ)]** を選択します。
1. *webapp1* または以前に作成した別のアプリケーションの **[アプリケーション ID]** 列の値を記録します。

* * *

## <a name="get-token-issuer-endpoint"></a>トークン発行者のエンドポイントを取得する

次に、ご利用の Azure AD B2C ユーザー フローのいずれかについて、既知の構成 URL を取得します。 また、Azure API Management でサポートするトークン発行者エンドポイント URI も必要です。

1. [Azure portal](https://portal.azure.com) で Azure AD B2C テナントを参照します。
1. **[ポリシー]** で **[ユーザー フロー]** を選択します。
1. 既存のポリシー ("*B2C_1_signupsignin1*" など) を選択して、 **[ユーザー フローを実行します]** を選択します。
1. ページの上部付近にある **[ユーザー フローを実行します]** 見出しの下に表示されるハイパーリンクの URL を記録します。 この URL は、ユーザー フロー用の OpenID Connect の既知の検出エンドポイントであり、次のセクションにおいて Azure API Management で受信ポリシーを構成するときに使用します。

    ![Azure portal の今すぐ実行ページの既知の URI ハイパーリンク](media/secure-apim-with-b2c-token/portal-01-policy-link.png)

1. ハイパーリンクを選択して、OpenID Connect の既知の構成ページを参照します。
1. ブラウザーに表示されるページで、`issuer` の値を記録します。次に例を示します。

    `https://<tenant-name>.b2clogin.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/v2.0/`

    この値は、次のセクションにおいて、Azure API Management で API を構成するときに使用します。

次のセクションで使用する 2 つの URL (OpenID Connect の既知の構成エンドポイント URL と発行者 URI) を記録しているはずです。 次に例を示します。

```
https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration
https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/
```

## <a name="configure-inbound-policy-in-azure-api-management"></a>Azure API Management で受信ポリシーを構成する

これで、API 呼び出しを検証する受信ポリシーを Azure API Management に追加する準備ができました。 アクセス トークン内の対象ユーザーと発行者を検証する [JWT 検証](../api-management/api-management-access-restriction-policies.md#ValidateJWT)ポリシーを追加して、有効なトークンを使用した API 呼び出しのみが確実に受け入れられるようにすることができます。

1. [Azure ポータル](https://portal.azure.com)で Azure API Management インスタンスに移動します。
1. **[API]** を選択します。
1. Azure AD B2C を使用してセキュリティ保護する API を選択します。
1. **[デザイン]** タブを選択します。
1. **[受信処理]** で、 **\</\>** を選択してポリシー コード エディターを開きます。
1. `<validate-jwt>` ポリシー内に次の `<inbound>` タグを配置します。

    1. `<openid-config>` 要素内の `url` 値を、使用するポリシーの既知の構成 URL で更新します。
    1. `<audience>` 要素を、ご利用の B2C テナントで以前に作成したアプリケーションのアプリケーション ID で更新します (たとえば、"*webapp1*")。
    1. `<issuer>` 要素を、先ほど記録したトークン発行者のエンドポイントで更新します。

    ```xml
    <policies>
        <inbound>
            <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
                <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
                <audiences>
                    <audience>44444444-0000-0000-0000-444444444444</audience>
                </audiences>
                <issuers>
                    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                </issuers>
            </validate-jwt>
            <base />
        </inbound>
        <backend> <base /> </backend>
        <outbound> <base /> </outbound>
        <on-error> <base /> </on-error>
    </policies>
    ```

## <a name="validate-secure-api-access"></a>セキュリティで保護された API アクセスを検証する

認証された呼び出し元だけが API にアクセスできるようにするには、[Postman](https://www.getpostman.com/) を使用して API を呼び出すると、使用する Azure API Management 構成を検証できます。

API を呼び出すには、Azure AD B2C によって発行されたアクセス トークンと APIM サブスクリプション キーの両方が必要です。

### <a name="get-an-access-token"></a>アクセス トークンを取得する

まず、Postman で、`Authorization` ヘッダーで使用するため、Azure AD B2C によって発行されるトークンが必要です。 これを取得するには、前提条件の 1 つとして作成したサインアップ/サインイン ユーザー フローの **[今すぐ実行]** 機能を使用します。

1. [Azure portal](https://portal.azure.com) で Azure AD B2C テナントを参照します。
1. **[ポリシー]** で **[ユーザー フロー]** を選択します。
1. 既存のサインアップ/サインイン ユーザーフロー (たとえば、"*B2C_1_signupsignin1*") を選択します。
1. **[アプリケーション]** で、 *[webapp1]* を選択します。
1. **[応答 URL]** で、`https://jwt.ms` を選択します。
1. **[ユーザー フローを実行します]** を選択します。

    ![Azure portal でのサインアップ サインイン ユーザーフローに対する [ユーザー フローを実行します] ページ](media/secure-apim-with-b2c-token/portal-03-user-flow.png)

1. サインイン プロセスを完了します。 `https://jwt.ms` にリダイレクトされるはずです。
1. ご利用のブラウザーに表示されたエンコード済みトークンの値を記録します。 このトークン値は、Postman で Authorization ヘッダーに使用します。

    ![jwt.ms に表示されたエンコード済みトークン値](media/secure-apim-with-b2c-token/jwt-ms-01-token.png)

### <a name="get-api-subscription-key"></a>API サブスクリプション キーを取得する

公開された API を呼び出すクライアントアプリケーション (この場合は Postman) では、有効な API Management サブスクリプション キーが、API に対する HTTP 要求に含まれる必要があります。 Postman HTTP 要求に含めるサブスクリプション キーを取得するには、次のことを行います。

1. [Azure portal](https://portal.azure.com) で、ご利用の Azure API Management サービス インスタンスに移動します。
1. **[サブスクリプション]** を選択します。
1. **[製品: 無制限]** の省略記号を選択して、 **[キーの表示/非表示]** を選択します。
1. 製品の **[主キー]** を記録します。 このキーは、Postman で、HTTP 要求の `Ocp-Apim-Subscription-Key` ヘッダーに使用します。

![Azure portal のサブスクリプション キー ページで [キーの表示/非表示] が選択されている](media/secure-apim-with-b2c-token/portal-04-api-subscription-key.png)

### <a name="test-a-secure-api-call"></a>セキュリティで保護された API 呼び出しをテストする

アクセス トークンと APIM サブスクリプション キーを記録したので、API に対するセキュリティで保護されたアクセスが正しく構成されているかどうかをテストできる準備ができました。

1. [Postman](https://www.getpostman.com/) で、新しい `GET` 要求を作成します。 [要求 URL] に、前提条件の 1 つとして発行した API のスピーカー リスト エンドポイントを指定します。 次に例を示します。

    `https://contosoapim.azure-api.net/conference/speakers`

1. さらに、次のヘッダーを追加します。

    | Key | 値 |
    | --- | ----- |
    | `Authorization` | 先ほど記録したエンコード済みトークン値。先頭に `Bearer ` を付けます ("Bearer" の後にスペースを含めます) |
    | `Ocp-Apim-Subscription-Key` | 先ほど記録した APIM サブスクリプション キー |

    **GET** 要求 URL と **ヘッダー** が次のように表示されるはずです。

    ![GET 要求 URL とヘッダーが表示されている Postman UI](media/secure-apim-with-b2c-token/postman-01-headers.png)

1. Postman で **[送信]** ボタンを選択して、要求を実行します。 すべてを正しく構成した場合は、次に示すように、カンファレンス講演者のコレクションを含む JSON 応答が表示されます (ここでは省略されています)。

    ```json
    {
      "collection": {
        "version": "1.0",
        "href": "https://conferenceapi.azurewebsites.net:443/speakers",
        "links": [],
        "items": [
          {
            "href": "https://conferenceapi.azurewebsites.net/speaker/1",
            "data": [
              {
                "name": "Name",
                "value": "Scott Guthrie"
              }
            ],
            "links": [
              {
                "rel": "http://tavis.net/rels/sessions",
                "href": "https://conferenceapi.azurewebsites.net/speaker/1/sessions"
              }
            ]
          },
    [...]
    ```

### <a name="test-an-insecure-api-call"></a>セキュリティで保護されていない API 呼び出しをテストする

要求が正常に完了したので、エラー ケースをテストして、"*無効な*" トークンによる API の呼び出しが想定どおりに拒否されることを確認します。 テストを実行する 1 つの方法として、トークン値にいくつかの文字を追加するか、変更してから、先ほどと同じ `GET` 要求を実行する方法があります。

1. トークン値に複数の文字を追加して、無効なトークンをシミュレートします。 たとえば、次のようにトークン値に "INVALID" を追加します。

    ![トークンに追加した "INVALID" が Postman UI のヘッダー セクションに示されている](media/secure-apim-with-b2c-token/postman-02-invalid-token.png)

1. **[送信]** ボタンを選択して、要求を実行します。 無効なトークンを使用した場合に想定される結果として、`401` (予期しない) 状態コードが返されます。

    ```json
    {
        "statusCode": 401,
        "message": "Unauthorized. Access token is missing or invalid."
    }
    ```

`401` 状態コードが表示された場合、Azure AD B2C によって発行された有効なアクセス トークンを持つ呼び出し元だけが、Azure API Management API への要求を成功させることができることを確認しました。

## <a name="support-multiple-applications-and-issuers"></a>複数のアプリケーションと発行者をサポートする

通常は、複数のアプリケーションが 1 つの REST API でやりとりします。 複数のアプリケーションを対象とするトークンを API で受け取れるようにするには、APIM 受信ポリシーの `<audiences>` 要素にアプリケーション ID を追加します。

```xml
<!-- Accept tokens intended for these recipient applications -->
<audiences>
    <audience>44444444-0000-0000-0000-444444444444</audience>
    <audience>66666666-0000-0000-0000-666666666666</audience>
</audiences>
```

同様に、複数のトークン発行者をサポートするには、APIM 受信ポリシーの `<issuers>` 要素にエンドポイント URI を追加します。

```xml
<!-- Accept tokens from multiple issuers -->
<issuers>
    <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
    <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
</issuers>
```

## <a name="migrate-to-b2clogincom"></a>b2clogin.com に移行する

レガシ `login.microsoftonline.com` エンドポイントによって発行されたトークンを検証する APIM API がある場合は、API とそれを呼び出すアプリケーションを移行して、[b2clogin.com](b2clogin.md) によって発行されたトークンを使用する必要があります。

次の一般的なプロセスに従うと、段階的な移行を実行できます。

1. b2clogin.com と login.microsoftonline.com の両方によって発行されたトークンに対するサポートを、ご利用の APIM 受信ポリシーに追加します。
1. アプリケーションを 1 つずつ更新して、b2clogin.com エンドポイントからトークンを取得します。
1. すべてのアプリケーションでトークンが b2clogin.com から正常に取得されたら、login.microsoftonline.com によって発行されたトークンに対するサポートを API から削除します。

次の APIM 受信ポリシーの例では、b2clogin.com と login.microsoftonline.com の両方によって発行されたトークンを受け入れる方法を示しています。 また、2 つのアプリケーションからの API 要求もサポートされています。

```xml
<policies>
    <inbound>
        <validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
            <openid-config url="https://<tenant-name>.b2clogin.com/<tenant-name>.onmicrosoft.com/B2C_1_signupsignin1/v2.0/.well-known/openid-configuration" />
            <audiences>
                <audience>44444444-0000-0000-0000-444444444444</audience>
                <audience>66666666-0000-0000-0000-666666666666</audience>
            </audiences>
            <issuers>
                <issuer>https://login.microsoftonline.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
                <issuer>https://<tenant-name>.b2clogin.com/99999999-0000-0000-0000-999999999999/v2.0/</issuer>
            </issuers>
        </validate-jwt>
        <base />
    </inbound>
    <backend> <base /> </backend>
    <outbound> <base /> </outbound>
    <on-error> <base /> </on-error>
</policies>
```

## <a name="next-steps"></a>次のステップ

Azure API Management ポリシーの詳細については、[APIM ポリシー リファレンスの索引](../api-management/api-management-policies.md)に関するページを参照してください。

OWIN ベースの Web API とそのアプリケーションを b2clogin.com に移行する方法については、「[OWIN ベースの Web API を b2clogin.com に移行する](multiple-token-endpoints.md)」を参照してください。