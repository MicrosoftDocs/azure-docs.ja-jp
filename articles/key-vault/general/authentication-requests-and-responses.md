---
title: 認証、要求、応答
description: Azure Key Vault で JSON 形式の要求と応答が使用されるしくみとキー コンテナーを使用するために必要な認証について説明します。
services: key-vault
author: amitbapat
manager: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 58616b647affd33e96357e556ab61f85d1c62129
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96752279"
---
# <a name="authentication-requests-and-responses"></a>認証、要求、応答

Azure Key Vault には、クラウド アプリケーションのシークレットを格納および管理するための 2 種類のコンテナーが用意されています。

|コンテナーの種類|サポートされているオブジェクトの種類|データ プレーン エンドポイント|
|--|--|--|
| **資格情報コンテナー**|<ul><li>ソフトウェアで保護されたキー</li><li>HSM で保護されたキー (Premium SKU を使用)</li><li>証明書</li><li>ストレージ アカウント キー</li></ul> | https://{vault-name}.vault.azure.net
|**Managed HSM** |<ul><li>HSM で保護されたキー</li></ul> | https://{hsm-name}.managedhsm.azure.net

各種類のオブジェクトにアクセスするために使用される URL サフィックスは次のとおりです。

|オブジェクトの種類|[URL suffix]\(URL サフィックス\)|
|--|--|
|ソフトウェアで保護されたキー| /keys |
|HSM で保護されたキー| /keys |
|シークレット|/secrets|
|証明書| /certificates|
|ストレージ アカウント キー|/storageaccounts
||

Azure Key Vault は、JSON 形式の要求と応答をサポートします。 Azure Key Vault に対する要求は、HTTPS を使用し、いくつかの URL パラメーターと JSON でエンコードされた要求および応答の本文を含んで、有効な Azure Key Vault の URL に送られます。

このトピックでは、Azure Key Vault サービスの詳細について説明します。 認証/承認およびアクセス トークンを取得する方法など、Azure REST インターフェイスの使用に関して詳しくは、[Azure REST API リファレンス](/rest/api/azure)をご覧ください。

## <a name="request-url"></a>要求 URL  
 キー管理操作には HTTP DELETE、GET、PATCH、PUT、HTTP POST が使用され、既存のキー オブジェクトに対する暗号操作には HTTP POST が使用されます。 特定の HTTP 動詞をサポートできないクライアントも、X-HTTP-REQUEST ヘッダーを使う POST を使用して、目的の動詞を指定できます。通常は本文を必要としない要求でも、HTTP POST を使用するときは (DELETE の代わりに POST を使用する場合など)、空の本文を含める必要があります。  

 Azure Key Vault 内のオブジェクトを操作するときの URL の例を次に示します。  

- Key Vault に TESTKEY という名前のキーを作成する (CREATE) ときに使う URL - `PUT /keys/TESTKEY?api-version=<api_version> HTTP/1.1`  

- Key Vault に IMPORTEDKEY という名前のキーをインポートする (IMPORT) ときに使う URL - `POST /keys/IMPORTEDKEY/import?api-version=<api_version> HTTP/1.1`  

- Key Vault 内の MYSECRET という名前のシークレットを取得する (GET) ときに使う URL - `GET /secrets/MYSECRET?api-version=<api_version> HTTP/1.1`  

- Key Vault の TESTKEY という名前のキーを使用するダイジェストに署名する (SIGN) ときに使う URL - `POST /keys/TESTKEY/sign?api-version=<api_version> HTTP/1.1`  

- Key Vault への要求に対する権限は常に、 です
  - コンテナーの場合: `https://{keyvault-name}.vault.azure.net/`
  - マネージド HSM の場合: `https://{HSM-name}.managedhsm.azure.net/`

  キーは常に /keys パスの下に格納され、シークレットは常に /secrets パスの下に格納されます。  

## <a name="api-version"></a>API Version  
 Azure Key Vault サービスはダウンレベルのクライアントとの互換性を提供するためにプロトコルのバージョン管理をサポートしますが、それらのクライアントですべての機能を使用することはできません。 既定値が存在しないため、クライアントは `api-version` クエリ文字列パラメーターを使って、サポートするプロトコルのバージョンを指定する必要があります。  

 Azure Key Vault のプロトコルのバージョンは、{YYYY}.{MM}.{DD} という形式を使用する日付番号付け方式に従います。  

## <a name="request-body"></a>要求本文  
 HTTP の仕様に従って、GET 操作は要求本文を持っていてはならず、POST 操作と PUT 操作は要求本文を持つ必要があります。 DELETE 操作の本文は、HTTP では省略可能です。  

 操作の説明で特に明記されていない限り、要求本文のコンテンツ タイプは application/json でなければならず、コンテンツ タイプに準拠するシリアル化された JSON オブジェクトを含む必要があります。  

 操作の説明で特に明記されていない限り、Accept 要求ヘッダーには application/json メディア タイプが含まれる必要があります。  

## <a name="response-body"></a>応答本文  
 操作の説明で特に明記されていない限り、応答本文のコンテンツ タイプは、成功と失敗どちらの操作についても application/json であり、詳細なエラー情報が含まれます。  

## <a name="using-http-post"></a>HTTP POST の使用  
 一部のクライアントは、特定の HTTP 動詞 (PATCH や DELETE など) を使用できない場合があります。 クライアントが "X-HTTP-METHOD" ヘッダーを使用して元の HTTP 動詞を指定する場合に限り、Azure Key Vault は、そのようなクライアントに対する代替措置として HTTP POST をサポートします。 HTTP POST のサポートは、このドキュメントで定義されている API ごとに明記されています。  

## <a name="error-responses"></a>エラー応答  
 エラー処理では、HTTP の状態コードを使います。 一般的な結果は次のとおりです。  

- 2xx - 成功:通常の操作に使用されます。 応答本文には、予期される結果が含まれます  

- 3xx - リダイレクト:304 "Not Modified" は、条件付き GET を実行するために返される場合があります。 その他の 3xx コードは、将来的に、DNS とパスの変更を示すために使用される可能性があります。  

- 4xx - クライアント エラー:不正な要求、見つからないキー、構文エラー、無効なパラメーター、認証エラーなどに使用されます。応答本文には、詳細なエラーの説明が含まれます。  

- 5xx - サーバー エラー:内部サーバー エラーに使用されます。 応答本文には、エラーの要約情報が含まれます。  

  システムは、プロキシまたはファイアウォールの背後で動作するように設計されています。 そのため、クライアントは他のエラー コードを受け取る可能性があります。  

  Azure Key Vault も、問題が発生したときにエラー情報を応答本文で返します。 応答本文は次のような JSON 形式です。  

```  

{  
  "error":  
  {  
    "code": "BadArgument",  
    "message":  

      "’Foo’ is not a valid argument for ‘type’."  
    }  
  }  
}  

```  

## <a name="authentication"></a>認証  
 Azure Key Vault に対するすべての要求は、認証を受ける必要があります。 Azure Key Vault は、OAuth2 [[RFC6749](https://tools.ietf.org/html/rfc6749)] を使用して取得できる Azure Active Directory アクセス トークンをサポートしています。 
 
 アプリケーションの登録と、Azure Key Vault を使用するための認証について詳しくは、[Azure AD でのクライアント アプリケーションの登録](/rest/api/azure/index#register-your-client-application-with-azure-ad)に関するページをご覧ください。
 
 HTTP Authorization ヘッダーを使用して、サービスにアクセス トークンを送信する必要があります。  

```  
PUT /keys/MYKEY?api-version=<api_version>  HTTP/1.1  
Authorization: Bearer <access_token>  

```  

 アクセス トークンを送信しない場合、またはトークンがサービスによって受け付けられない場合は、WWW-Authenticate ヘッダーを含む HTTP 401 エラーがクライアントに返されます。次はその例です。  

```  
401 Not Authorized  
WWW-Authenticate: Bearer authorization="…", resource="…"  

```  

 WWW-Authenticate ヘッダーのパラメーターは次のとおりです。  

-   authorization:要求のアクセス トークンの取得に使用できる OAuth2 承認サービスのアドレス。  

-   resource:承認要求で使用するリソースの名前 (`https://vault.azure.net`)。

> [!NOTE]
> シークレット、証明書、およびキーについて、Key Vault SDK クライアントは Key Vault への最初の呼び出しでテナント情報を取得するためのアクセス トークンを提供しません。 Key Vault SDK クライアントを使用して HTTP 401 を受け取ることが想定されています。Key Vault からアプリケーションに示されるのは、リソースを含む WWW-Authenticate ヘッダーとテナントであり、そこにアクセスしてトークンを要求する必要があります。 すべてが正しく構成されている場合、アプリケーションから Key Vault への 2 回目の呼び出しには有効なトークンが含まれているため正常に終了します。 
