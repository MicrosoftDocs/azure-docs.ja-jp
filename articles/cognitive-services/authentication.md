---
title: 認証
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services リソースへの要求を認証する方法には、サブスクリプション キー、ベアラー トークン、またはマルチサービス サブスクリプションの 3 つがあります。 この記事では、それぞれの方法と、要求を実行する方法について学習します。
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: erhopf
ms.openlocfilehash: c7aeb9e9f4de7b4de62f9b5a8da6d997e32a2399
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94363325"
---
# <a name="authenticate-requests-to-azure-cognitive-services"></a>Azure Cognitive Services に対する要求の認証

Azure Cognitive Service に対する各要求には、認証ヘッダーが含まれている必要があります。 このヘッダーを使用して、サブスクリプション キーまたはアクセス トークンを渡します。これは、サービスまたはサービスのグループについてお客様のサブスクリプションを検証するために使用されます。 この記事では、要求を認証するための 3 つの方法と、そのそれぞれの要件について学習します。

* [単一サービス](#authenticate-with-a-single-service-subscription-key)または[マルチ サービス](#authenticate-with-a-multi-service-subscription-key)のサブスクリプション キーによる認証
* [トークン](#authenticate-with-an-authentication-token)による認証
* [Azure Active Directory (AAD)](#authenticate-with-azure-active-directory) による認証

## <a name="prerequisites"></a>前提条件

要求を実行する前に、Azure アカウントと Azure Cognitive Services サブスクリプションが必要です。 既にアカウントをお持ちの場合は、次のセクションまでスキップして進んでください。 アカウントをお持ちでない場合は、数分で設定を行えるガイドをご覧ください。[Azure の Cognitive Services アカウントの作成](cognitive-services-apis-create-account.md)に関するページを参照してください。

[アカウントの作成](https://azure.microsoft.com/free/cognitive-services/)後に、[Azure portal](cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) からサブスクリプション キーを取得できます。

## <a name="authentication-headers"></a>認証ヘッダー

Azure Cognitive Services で使用できる認証ヘッダーについて簡単に確認しましょう。

| ヘッダー | 説明 |
|--------|-------------|
| Ocp-Apim-Subscription-Key | 特定のサービスのサブスクリプション キーまたはマルチサービスのサブスクリプション キーを使用して認証するには、このヘッダーを使用します。 |
| Ocp-Apim-Subscription-Region | このヘッダーは、[Translator サービス](./Translator/reference/v3-0-reference.md)と共にマルチサービスのサブスクリプション キーを使用する場合にのみ必要です。 このヘッダーを使用して、サブスクリプション リージョンを指定します。 |
| 承認 | お客様が認証トークンを使用している場合は、このヘッダーを使用します。 トークンの交換を実行する手順については、以降のセクションで詳しく説明されています。 値は `Bearer <TOKEN>` 形式で指定します。 |

## <a name="authenticate-with-a-single-service-subscription-key"></a>単一サービスのサブスクリプション キーによる認証

1 つ目の方法では、Translator などの特定のサービスに対してサブスクリプション キーを使用して要求を認証します。 お客様が作成したそれぞれのリソースについて、Azure portal でキーを取得できます。 サブスクリプション キーを使用して要求を認証するには、それを `Ocp-Apim-Subscription-Key` ヘッダーとして渡す必要があります。

以下のサンプル要求は、`Ocp-Apim-Subscription-Key` ヘッダーの使用方法を示しています。 このサンプルを使用する際は有効なサブスクリプション キーを含める必要があることに注意してください。

これは、Bing Web Search API 呼び出しのサンプルです。
```cURL
curl -X GET 'https://api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

これは、Translator サービスの呼び出しの例です。
```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

次のビデオでは、Cognitive Services キーを使用するデモンストレーションを行っています。

## <a name="authenticate-with-a-multi-service-subscription-key"></a>マルチサービスのサブスクリプション キーによる認証

>[!WARNING]
> 現在のところ、以下のサービスではマルチサービス キーがサポートされて **いません**。QnA Maker、Speech Services、Custom Vision、および Anomaly Detector。

この方法も、サブスクリプション キーを使用して要求を認証します。 主な違いは、サブスクリプション キーが特定のサービスに関連付けられておらず、単一のキーを使用して複数の Cognitive Services に対する要求を認証できることです。 リージョン別の提供状況、サポートされている機能、および価格については、「[Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)」を参照してください。

サブスクリプション キーは、各要求内で `Ocp-Apim-Subscription-Key` ヘッダーとして指定されます。

[![Cognitive Services のマルチサービスのサブスクリプション キーのデモ](./media/index/single-key-demonstration-video.png)](https://www.youtube.com/watch?v=psHtA1p7Cas&feature=youtu.be)

### <a name="supported-regions"></a>サポートされているリージョン

マルチサービスのサブスクリプション キーを使用して `api.cognitive.microsoft.com` に対する要求を実行するときは、URL にリージョンを含める必要があります。 (例: `westus.api.cognitive.microsoft.com`)。

Translator サービスと共にマルチサービスのサブスクリプション キーを使用する場合は、`Ocp-Apim-Subscription-Region` ヘッダーを使用してサブスクリプション リージョンを指定する必要があります。

マルチサービス認証は、以下のリージョンでサポートされています。

- `australiaeast`
- `brazilsouth`
- `canadacentral`
- `centralindia`
- `eastasia`
- `eastus`
- `japaneast`
- `northeurope`
- `southcentralus`
- `southeastasia`
- `uksouth`
- `westcentralus`
- `westeurope`
- `westus`
- `westus2`

### <a name="sample-requests"></a>サンプルの要求

これは、Bing Web Search API 呼び出しのサンプルです。

```cURL
curl -X GET 'https://YOUR-REGION.api.cognitive.microsoft.com/bing/v7.0/search?q=Welsch%20Pembroke%20Corgis' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' | json_pp
```

これは、Translator サービスの呼び出しの例です。

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY' \
-H 'Ocp-Apim-Subscription-Region: YOUR_SUBSCRIPTION_REGION' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

## <a name="authenticate-with-an-authentication-token"></a>認証トークンによる認証

Azure Cognitive Services の中には、認証トークンを受け入れるものや、場合によっては認証トークンを必要とするものがあります。 現在、以下のサービスで認証トークンがサポートされています。

* Text Translation API
* Speech Services: Speech to Text REST API
* Speech Services: Text to Speech REST API

>[!NOTE]
> QnA Maker でも Authorization ヘッダーが使用されますが、エンドポイント キーが必要です。 詳細については、[QnA Maker でナレッジ ベースから回答を取得する](./qnamaker/quickstarts/get-answer-from-knowledge-base-using-url-tool.md)方法に関するページを参照してください。

>[!WARNING]
> 認証トークンをサポートするサービスは時間の経過と共に変わる可能性があります。この認証方法を使用する前には、サービスの API リファレンスを確認してください。

単一サービスのサブスクリプション キーとマルチサービスのサブスクリプション キーは、どちらも認証トークンと交換できます。 認証トークンは 10 分間有効です。

認証トークンは、`Authorization` ヘッダーとして要求に含まれます。 指定されるトークンの値の前には `Bearer` が付いている必要があります (例: `Bearer YOUR_AUTH_TOKEN`)。

### <a name="sample-requests"></a>サンプルの要求

サブスクリプション キーを認証トークンと交換するには、`https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken` という URL を使用します。

```cURL
curl -v -X POST \
"https://YOUR-REGION.api.cognitive.microsoft.com/sts/v1.0/issueToken" \
-H "Content-type: application/x-www-form-urlencoded" \
-H "Content-length: 0" \
-H "Ocp-Apim-Subscription-Key: YOUR_SUBSCRIPTION_KEY"
```

以下のマルチサービスのリージョンで、トークンの交換がサポートされています。

- `australiaeast`
- `brazilsouth`
- `canadacentral`
- `centralindia`
- `eastasia`
- `eastus`
- `japaneast`
- `northeurope`
- `southcentralus`
- `southeastasia`
- `uksouth`
- `westcentralus`
- `westeurope`
- `westus`
- `westus2`

認証トークンを取得したら、各要求内でそれを `Authorization` ヘッダーとして渡す必要があります。 これは、Translator サービスの呼び出しの例です。

```cURL
curl -X POST 'https://api.cognitive.microsofttranslator.com/translate?api-version=3.0&from=en&to=de' \
-H 'Authorization: Bearer YOUR_AUTH_TOKEN' \
-H 'Content-Type: application/json' \
--data-raw '[{ "text": "How much for the cup of coffee?" }]' | json_pp
```

[!INCLUDE [](../../includes/cognitive-services-azure-active-directory-authentication.md)]

## <a name="see-also"></a>関連項目

* [Cognitive Services とは](./what-are-cognitive-services.md)
* [Cognitive Services の価格](https://azure.microsoft.com/pricing/details/cognitive-services/)
* [カスタム サブドメイン](cognitive-services-custom-subdomains.md)