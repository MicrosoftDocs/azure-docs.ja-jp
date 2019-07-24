---
title: Video Indexer API を使用して人物モデルをカスタマイズする - Azure
titlesuffix: Azure Media Services
description: この記事では、Video Indexer API を使用して人物モデルをカスタマイズする方法を示します。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: e5a34a75c73401c567a0e898a1ce9f85cde96586
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59360507"
---
# <a name="customize-a-person-model-with-the-video-indexer-api"></a>Video Indexer API を使用して人物モデルをカスタマイズする

Video Indexer では、ビデオ コンテンツの顔検出と著名人認識がサポートされます。 著名人認識機能では、IMDB、Wikipedia、LinkedIn のトップ インフルエンサなどの一般的に要求されているデータ ソースに基づく約 100 万人の顔を処理できます。 著名人認識機能で認識されない顔は、検出されますが、名前なしのままとなります。 Video Indexer にビデオをアップロードし、結果が戻ってきた後、前に戻り、認識されなかった顔に名前を付けることができます。 顔に名前でラベルを付けると、その顔と名前が、アカウントの人物モデルに追加されます。 その後、Video Indexer では、今後のビデオと過去のビデオでこの顔が認識されるようになります。

Video Indexer API を使用して、ビデオで検出された顔を編集することができます。これについては、このトピックで説明します。 また、[Video Indexer Web サイトを使用する人物モデルのカスタマイズ](customize-person-model-with-api.md)に関するページで説明されているように、Video Indexer Web サイトを使用することもできます。

## <a name="managing-multiple-person-models"></a>複数の人物モデルの管理 

Video Indexer では、アカウントごとに複数の人物モデルがサポートされます。 この機能は現在、Video Indexer API を通じてのみ利用可能です。

ご利用のアカウントがさまざまなユースケース シナリオに対応している場合は、アカウントごとに複数の人物モデルを作成することができます。 たとえば、コンテンツがスポーツに関連している場合、スポーツ (フットボール、バスケットボール、サッカーなど) ごとに別の人物モデルを作成できます。 

モデルが作成されたら、ビデオのアップロード/インデックスの作成または再作成の際に、特定の人物モデルのモデル ID を指定して使用できます。 ビデオの新しい顔のトレーニングを行うと、そのビデオが関連付けられた特定のカスタム モデルが更新されます。

各アカウントの人物モデルの数は 50 に制限されています。 複数の人物モデルのサポートが必要でない場合は、アップロード/インデックスの作成または再作成の際にビデオに人物モデル ID を割り当てないでください。 この場合、Video Indexer でアカウント内の既定のカスタム人物モデルが使用されます。

## <a name="create-a-new-person-model"></a>新しい人物モデルを作成する

指定されたアカウントで新しい人物モデルを作成します。 

### <a name="request-url"></a>要求 URL

これは POST 要求です。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Curl の要求を以下に示します。

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}"
```

[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?)。

### <a name="request-parameters"></a>要求パラメーター 

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|string|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountId|string|はい|アカウントのグローバル一意識別子|
|name|string|はい|人物モデルの名前|
|accessToken|string|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文

この呼び出しで必要な要求本文はこれ以上ありません。

### <a name="response"></a>Response

応答では、以下の例の形式に従って、先ほど作成した人物モデルの名前と生成されたモデル ID が提供されます。

```json
{
    "id": "227654b4-912c-4b92-ba4f-641d488e3720",
    "name": "Example Person Model"
}
```

その後、[インデックスを作成するためにビデオをアップロードする](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)場合や、[ビデオのインデックスを再作成する](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)場合に、**personModelId** パラメーターの **id** 値を使用する必要があります。

## <a name="delete-a-person-model"></a>人物モデルを削除する

指定されたアカウントからカスタムの人物モデルを削除します。 

人物モデルが正常に削除された後、削除されたモデルを使用していた現在のビデオのインデックスは再作成されるまで変わりません。 インデックスが再作成されると、そのモデルを使用してインデックスが作成された現在のビデオでは、削除されたモデルで名前が付けられていた顔は Video Indexer によって認識されなくなります。しかし、それらの顔は引き続き検出されます。 削除されたモデルを使用してインデックスが作成された現在のビデオでは、アカウントの既定の人物モデルが使用されるようになります。 アカウントの既定のモデルでも削除されたモデルの顔に名前が付けられている場合、それらの顔はビデオで引き続き認識されます。

### <a name="request-url"></a>要求 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}
```

Curl の要求を以下に示します。
```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels/{id}?accessToken={accessToken}"
```

[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Person-Model?)。

### <a name="request-parameters"></a>要求パラメーター

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|string|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountId|string|はい|アカウントのグローバル一意識別子|
|id|string|はい|人物モデルの ID (人物モデルの作成時に生成されます)|
|accessToken|string|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文

この呼び出しで必要な要求本文はこれ以上ありません。

### <a name="response"></a>Response

人物モデルが正常に削除されたときに返されるコンテンツはありません。

## <a name="get-all-person-models"></a>すべての人物モデルを取得する

指定されたアカウントのすべての人物モデルを取得します。 

### <a name="request-call"></a>要求呼び出し

これは GET 要求です。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Curl の要求を以下に示します。

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}"
```

[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Person-Models?)。

### <a name="request-parameters"></a>要求パラメーター

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|string|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountId|string|はい|アカウントのグローバル一意識別子|
|accessToken|string|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文

この呼び出しで必要な要求本文はこれ以上ありません。

### <a name="response"></a>Response

応答では、アカウント内のすべての人物モデル (指定されたアカウント内の既定の人物モデルを含む) のリストが提供され、以下の例の形式に従ってそれぞれの名前と ID が示されます。

```json
[
    {
        "id": "59f9c326-b141-4515-abe7-7d822518571f",
        "name": "Default"
    }, 
    {
        "id": "9ef2632d-310a-4510-92e1-cc70ae0230d4",
        "name": "Test"
    }
]
```

ビデオで使用するモデルを選択することができます。その場合、[インデックスを作成するためにビデオをアップロードする](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)際、または[ビデオのインデックスを再作成する](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)際に **personModelId** パラメーターの人物モデルの **id** 値を使用します。

## <a name="update-a-face"></a>顔を更新する

このコマンドでは、ビデオの ID と顔の ID を使用し、名前でビデオの顔を更新できます。 これにより、ビデオがアップロード/インデックスの作成または再作成の際に関連付けられた人物モデルが更新されます。 人物モデルが割り当てられていない場合は、アカウントの既定の人物モデルが更新されます。 

このような場合、同じ人物モデルを共有する、他の現在のビデオに出現する同じ顔が認識されます。 他の現在のビデオの顔の認識は、バッチ プロセスであるため、有効になるまで少し時間がかかることがあります。

Video Indexer で著名人として認識された顔は、新しい名前で更新できます。 指定した新しい名前は、組み込みの著名人認識より優先されます。

### <a name="request-call"></a>要求呼び出し

これは POST 要求です。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}
```

Curl の要求を以下に示します。

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Videos/{videoId}/Index/Faces/{faceId}?accessToken={accessToken}&newName={newName}"
```

[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Video-Face?)。

### <a name="request-parameters"></a>要求パラメーター

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|string|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountId|string|はい|アカウントのグローバル一意識別子|
|videoId|string|はい|更新する顔が表示されるビデオの ID。 これは、ビデオがアップロードされ、インデックスが付けられるときに作成されます。|
|faceId|integer|はい|更新される顔の ID。 faceId はビデオ インデックスから取得できます|
|accessToken|string|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間以内に切れます。|
|name|string|はい|顔を更新するときに使用する新しい名前。|

名前は人物モデルに対して一意であるため、同じ人物モデルの 2 つの異なる顔に、同じ **name** パラメーター値を指定した場合、Video Indexer ではこれらの顔は同じ人物であると見なされ、ビデオのインデックスを再作成したときに集約されます。 

### <a name="request-body"></a>要求本文

この呼び出しで必要な要求本文はこれ以上ありません。

### <a name="response"></a>Response

顔が正常に更新されたときに返されるコンテンツはありません。

## <a name="next-steps"></a>次の手順

[Video Indexer Web サイトを使用して人物モデルをカスタマイズする](customize-person-model-with-website.md)
