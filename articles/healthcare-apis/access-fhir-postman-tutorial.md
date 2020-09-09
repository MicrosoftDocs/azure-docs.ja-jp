---
title: Azure 内の Postman FHIR サーバー - Azure API for FHIR
description: このチュートリアルでは、Postman を使用して FHIR サーバーにアクセスするために必要な手順について説明します。 Postman は、API にアクセスするアプリケーションのデバッグに役立ちます。
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: dseven
ms.author: matjazl
author: matjazl
ms.date: 02/07/2019
ms.openlocfilehash: f8b5e344fc963d466571e75ff16f17367dc32971
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/06/2020
ms.locfileid: "87844849"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Postman を使用して Azure API for FHIR にアクセスする

クライアント アプリケーションは、[REST API](https://www.hl7.org/fhir/http.html) を介して FHIR API にアクセスします。 アプリケーションの作成時に、デバッグ目的などで、FHIR サーバーを直接操作することが必要になる場合もあります。 このチュートリアルでは、[Postman](https://www.getpostman.com/) を使用して FHIR サーバーにアクセスするために必要な手順について説明します。 Postman は、API にアクセスするアプリケーションを作成する際によくデバッグに使用されるツールです。

## <a name="prerequisites"></a>前提条件

- Azure 内の FHIR エンドポイント。 これは、マネージド Azure API for FHIR またはオープンソースの FHIR Server for Azure を使用して設定できます。 [Azure portal](fhir-paas-portal-quickstart.md)、[PowerShell](fhir-paas-powershell-quickstart.md)、または [Azure CLI](fhir-paas-cli-quickstart.md) を使用して、マネージド Azure API for FHIR を設定します。
- FHIR サービスへのアクセスに使用する[クライアント アプリケーション](register-confidential-azure-ad-client-app.md)
- Postman がインストールされている。 これは [https://www.getpostman.com](https://www.getpostman.com) から入手できます

## <a name="fhir-server-and-authentication-details"></a>FHIR サーバーと認証の詳細

Postman を使用するには、次の詳細情報が必要です。

- ご使用の FHIR サーバーの URL (例: `https://MYACCOUNT.azurehealthcareapis.com`)
- ご使用の FHIR サーバーの ID プロバイダー `Authority` (例: `https://login.microsoftonline.com/{TENANT-ID}`)
- 構成済みの `audience`。 通常、これは FHIR サーバーの URL です (例: `https://MYACCOUNT.azurehealthcareapis.com` や `https://azurehealthcareapis.com` のみ)。
- FHIR サービスへのアクセスに使用する[クライアント アプリケーション](register-confidential-azure-ad-client-app.md)の `client_id` (またはアプリケーション ID)。
- クライアント アプリケーションの `client_secret` (またはアプリケーション シークレット)。

最後に、`https://www.getpostman.com/oauth2/callback` が実際のクライアント アプリケーションに対して登録されている応答 URL であることを確認する必要があります。

## <a name="connect-to-fhir-server"></a>FHIR サーバーに接続する

Postman を使用して、`https://fhir-server-url/metadata` に `GET` 要求を実行します。

![Postman のメタデータ機能ステートメント](media/tutorial-postman/postman-metadata.png)

Azure API for FHIR のメタデータ URL は `https://MYACCOUNT.azurehealthcareapis.com/metadata` です。 この例では、FHIR サーバーの URL は `https://fhirdocsmsft.azurewebsites.net` であり、サーバーの機能ステートメントは `https://fhirdocsmsft.azurewebsites.net/metadata` から入手できます。 このエンドポイントには、認証なしでアクセスできる必要があります。

制限されたリソースにアクセスしようとすると、"Authentication failed"(認証に失敗しました) という応答が返されます。

![認証に失敗しました](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>アクセス トークンを取得する

有効なアクセス トークンを取得するには、[Authorization]\(承認\) を選択し、種類として [OAuth 2.0] を選択します。

![OAuth 2.0 の設定](media/tutorial-postman/postman-select-oauth2.png)

[Get New Access Token]\(新しいアクセス トークンの取得\) をクリックすると、ダイアログが表示されます。

![新しいアクセス トークンの要求](media/tutorial-postman/postman-request-token.png)

次の詳細情報が必要になります。

| フィールド                 | 値の例                                                                                                   | 解説                    |
|-----------------------|-----------------------------------------------------------------------------------------------------------------|----------------------------|
| Token Name (トークン名)            | MYTOKEN                                                                                                         | 選択した名前          |
| 付与タイプ            | Authorization Code (承認コード)                                                                                              |                            |
| コールバック URL          | `https://www.getpostman.com/oauth2/callback`                                                                      |                            |
| 認証 URL              | `https://login.microsoftonline.com/{TENANT-ID}/oauth2/authorize?resource=<audience>` | `audience` は Azure API for FHIR の `https://MYACCOUNT.azurehealthcareapis.com` です |
| Access Token URL (アクセス トークン URL)      | `https://login.microsoftonline.com/{TENANT ID}/oauth2/token`                                                      |                            |
| クライアント ID             | `XXXXXXXX-XXX-XXXX-XXXX-XXXXXXXXXXXX`                                                                            | アプリケーション ID             |
| クライアント シークレット         | `XXXXXXXX`                                                                                                        | シークレット クライアント キー          |
| Scope | `<Leave Blank>` |
| State                 | `1234`                                                                                                            |                            |
| クライアント認証 | Send client credentials in body (本文でクライアントの資格情報を送信する)                                                                                 |                 

[Request Token]\(トークンの要求\) をクリックし、Azure Active Directory 認証フローに従うと、Postman にトークンが返されます。 問題が発生した場合は、Postman コンソールを開きます ([View]\(表示\) -> [Show Postman Console]\(Postman コンソールの表示\) メニュー項目から)。

返されたトークン画面で下へスクロールし、[Use Token]\(トークンの使用\) をクリックします。

![トークンの使用](media/tutorial-postman/postman-use-token.png)

これで、トークンが [Access Token]\(アクセス トークン\) フィールドに入力され、[Available Tokens]\(利用可能なトークン\) からトークンを選択できます。 [Send]\(送信\) をもう一度クリックして、`Patient` リソースの検索を繰り返すと、ステータス `200 OK` が表示されます。

![200 OK](media/tutorial-postman/postman-200-OK.png)

この例では、データベース内に患者がなく、検索結果は空です。

[https://jwt.ms](https://jwt.ms) などのツールを使用してアクセス トークンを検査すると、次のような内容が表示されます。

```jsonc
{
  "aud": "https://MYACCOUNT.azurehealthcareapis.com",
  "iss": "https://sts.windows.net/{TENANT-ID}/",
  "iat": 1545343803,
  "nbf": 1545343803,
  "exp": 1545347703,
  "acr": "1",
  "aio": "AUQAu/8JXXXXXXXXXdQxcxn1eis459j70Kf9DwcUjlKY3I2G/9aOnSbw==",
  "amr": [
    "pwd"
  ],
  "appid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "oid": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "appidacr": "1",

  ...// Truncated
}
```

トラブルシューティングの状況では、まずは対象ユーザー (`aud` 要求) が正しいことを検証することをお勧めします。 実際のトークンの発行者 (`iss` 要求) が正しく、対象ユーザー (`aud` 要求) も正しいのに、FHIR API にアクセスできない場合は、ユーザーまたはサービス プリンシパル (`oid` 要求) が FHIR データ プレーンにアクセスできない可能性があります。 [Azure のロールベースのアクセス制御 (Azure RBAC) を使用](configure-azure-rbac.md)して、データ プレーンのロールをユーザーに割り当てることをお勧めします。 ご使用のデータ プレーンに外部のセカンダリ Azure Active Directory テナントを使用する場合は、[ローカル RBAC の割り当てを構成](configure-local-rbac.md)する必要があります。

[Azure CLI を使用して Azure API for FHIR のトークンを取得](get-healthcare-apis-access-token-cli.md)することもできます。 Azure CLI によって取得したトークンを使用する場合は、承認の種類として "ベアラー トークン" を使用し、トークンを直接貼り付ける必要があります。

## <a name="inserting-a-patient"></a>患者を挿入する

これで、アクセス トークンが有効になりました。 新しい患者を挿入することができます。 "POST" メソッドに切り替え、要求の本文に次の JSON ドキュメントを追加します。

[!code-json[](samples/sample-patient.json)]

[Send]\(送信\) をクリックすると、患者が正常に作成されていることがわかります。

![作成された患者](media/tutorial-postman/postman-patient-created.png)

患者の検索を繰り返すと、患者のレコードが表示されます。

![作成された患者](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Postman を使用して FHIR API にアクセスしました。 Microsoft のサポートされる機能に関するセクション内でサポートされている API 機能について確認してください。
 
>[!div class="nextstepaction"]
>[サポートされる機能](fhir-features-supported.md)
