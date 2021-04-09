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
ms.date: 03/16/2021
ms.openlocfilehash: e9031dc77054a2bbac8015bbbdd7b9ed2a35e84f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/24/2021
ms.locfileid: "105043344"
---
# <a name="access-azure-api-for-fhir-with-postman"></a>Postman を使用して Azure API for FHIR にアクセスする

クライアントアプリケーションは、 [REST API](https://www.hl7.org/fhir/http.html)を通じて、FHIR の Azure API にアクセスできます。 要求を送信し、応答を表示し、アプリケーションをビルド中にデバッグするには、任意の API テストツールを使用します。 このチュートリアルでは、 [Postman](https://www.getpostman.com/)を使用して FHIR サーバーにアクセスする手順について説明します。 

## <a name="prerequisites"></a>前提条件

- Azure 内の FHIR エンドポイント。 

   FHIR (管理されたサービス) 用の Azure API をデプロイするには、 [Azure portal](fhir-paas-portal-quickstart.md)、 [PowerShell](fhir-paas-powershell-quickstart.md)、または [Azure CLI](fhir-paas-cli-quickstart.md)を使用できます。
- FHIR サービスにアクセスするための登録済みの [機密クライアントアプリケーション](register-confidential-azure-ad-client-app.md) 。
- FHIR サービスにアクセスするための、"FHIR データ共同作成者" などの機密クライアントアプリケーションへのアクセス許可が付与されている。 詳細については、「 [FHIR 用に AZURE RBAC を構成する](./configure-azure-rbac.md)」を参照してください。
- Postman がインストールされている。 
    
    Postman の詳細については、「 [Postman の概要](https://www.getpostman.com)」を参照してください。

## <a name="fhir-server-and-authentication-details"></a>FHIR サーバーと認証の詳細

Postman を使用するには、次の認証パラメーターが必要です。

- FHIR サーバーの URL (例) `https://MYACCOUNT.azurehealthcareapis.com`

- ご使用の FHIR サーバーの ID プロバイダー `Authority` (例: `https://login.microsoftonline.com/{TENANT-ID}`)

- 通常は `audience` FHIR サーバーの URL (またはなど) に構成されている `https://<FHIR-SERVER-NAME>.azurehealthcareapis.com` `https://azurehealthcareapis.com` 。

- `client_id`FHIR サービスへのアクセスに使用される、 [confidential クライアントアプリケーション](register-confidential-azure-ad-client-app.md)のまたはアプリケーション ID。

- `client_secret`機密クライアントアプリケーションのまたはアプリケーションシークレット。

最後に、`https://www.getpostman.com/oauth2/callback` が実際のクライアント アプリケーションに対して登録されている応答 URL であることを確認する必要があります。

## <a name="connect-to-fhir-server"></a>FHIR サーバーに接続する

Postman を開き、[ **取得** ] を選択してに要求し `https://fhir-server-url/metadata` ます。

![Postman のメタデータ機能ステートメント](media/tutorial-postman/postman-metadata.png)

Azure API for FHIR のメタデータ URL は `https://MYACCOUNT.azurehealthcareapis.com/metadata` です。 

この例では、FHIR サーバーの URL は `https://fhirdocsmsft.azurewebsites.net` で、サーバーの機能ステートメントはで利用でき `https://fhirdocsmsft.azurewebsites.net/metadata` ます。 このエンドポイントには、認証なしでアクセスできます。

制限されたリソースにアクセスしようとすると、"認証に失敗しました" という応答が発生します。

![認証に失敗しました](media/tutorial-postman/postman-authentication-failed.png)

## <a name="obtaining-an-access-token"></a>アクセス トークンを取得する
有効なアクセストークンを取得するには、[**承認**] を選択し、[**種類**] ドロップダウンメニューから [ **OAuth 2.0** ] を選択します。

![OAuth 2.0 の設定](media/tutorial-postman/postman-select-oauth2.png)

**[Get New Access Token]\(新しいアクセス トークンを取得する\)** を選択します。

![新しいアクセス トークンの要求](media/tutorial-postman/postman-request-token.png)

[ **新しいアクセストークンの取得** ] ダイアログボックスで、次の情報を入力します。

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
| State                |  `1234`                                                                                                           |                            |
| クライアント認証 | Send client credentials in body (本文でクライアントの資格情報を送信する)                                                                                 |                 

Azure Active Directory 認証フローのガイドとなる **要求トークン** を選択すると、トークンが Postman に返されます。 認証エラーが発生した場合は、Postman コンソールで詳細を確認してください。 **注**: リボンで [ **表示**] を選択し、[ **Show Postman Console**] を選択します。 Postman コンソールへのショートカットキーは、 **Alt + Ctrl + C** です。

下にスクロールして [返されたトークン] 画面を表示し、[ **トークンの使用**] を選択します。

![トークンの使用](media/tutorial-postman/postman-use-token.png)

新しく設定されたトークンを表示するには、[ **アクセストークン** ] フィールドを参照してください。 [ **送信** ] を選択して `Patient` リソース検索を繰り返すと、 **状態** が `200 OK` 返されます。 返された状態は、 `200 OK` 成功した HTTP 要求を示します。

![200 OK](media/tutorial-postman/postman-200-OK.png)

患者の *検索* 例では、検索結果が空の患者はデータベースに存在しません。

[Jwt.ms](https://jwt.ms)などのツールを使用して、アクセストークンを調べることができます。 コンテンツの例を次に示します。

```json
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

トラブルシューティングの状況では、まずは対象ユーザー (`aud` 要求) が正しいことを検証することをお勧めします。 トークンが正しい発行者 (要求) からのものであり、 `iss` 適切な対象ユーザー (クレーム) を持っていても、 `aud` FHIR API にアクセスできない場合は、ユーザーまたはサービスプリンシパル ( `oid` クレーム) が fhir データプレーンにアクセスできない可能性があります。 [Azure のロールベースのアクセス制御 (AZURE RBAC)](configure-azure-rbac.md)を使用して、データプレーンのロールをユーザーに割り当てることをお勧めします。 データプレーンに外部のセカンダリ Azure Active directory テナントを使用している場合は、 [FHIR 割り当て用にローカル RBAC を構成](configure-local-rbac.md) する必要があります。

[Azure CLI を使用して、AZURE API FOR FHIR](get-healthcare-apis-access-token-cli.md)のトークンを取得することもできます。 Azure CLI で取得したトークンを使用している場合は、認証の種類として *ベアラートークン* を使用する必要があります。 トークンをに直接貼り付けます。

## <a name="inserting-a-patient"></a>患者を挿入する

有効なアクセストークンを使用して、新しい患者を挿入できるようになりました。 Postman で、[ **Post**] を選択してメソッドを変更し、要求の本文に次の JSON ドキュメントを追加します。

[!code-json[](../samples/sample-patient.json)]

[ **送信** ] を選択して、患者が正常に作成されたことを確認します。

![患者が正常に作成されたことを示すスクリーンショット。](media/tutorial-postman/postman-patient-created.png)

患者の検索を繰り返すと、患者のレコードが表示されます。

![作成された患者](media/tutorial-postman/postman-patient-found.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Postman を使用して、FHIR 用の Azure API にアクセスしました。 FHIR 機能用の Azure API の詳細については、「」を参照してください。
 
>[!div class="nextstepaction"]
>[サポートされる機能](fhir-features-supported.md)
