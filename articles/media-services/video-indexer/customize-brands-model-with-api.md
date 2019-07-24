---
title: Azure Video Indexer を使用してブランド モデルをカスタマイズする
titlesuffix: Azure Media Services
description: この記事では、Azure Video Indexer を使用して、ブランド モデルをカスタマイズする方法を示します。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: df77a745ef6508b15b5a8bcde5eede0e06eb1afc
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/11/2019
ms.locfileid: "56001657"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Video Indexer API を使用してブランド モデルをカスタマイズする

Video Indexer では、ビデオとオーディオ コンテンツのインデックスの作成と再作成中に、音声と視覚テキストからのブランドの検出がサポートされます。 ブランド検出機能では、Bing のブランド データベースで提案される製品、サービス、および企業の説明が識別されます。 たとえば、Microsoft がビデオまたはオーディオ コンテンツで説明されている場合や、ビデオ内の視覚テキストに表示される場合、Video Indexer ではそのコンテンツ内でブランドとして Microsoft が検出されます。 カスタム ブランド モデルでは、特定のブランドを検出対象から除外し、Bing のブランド データベースにはない可能性があるモデルの一部である必要があるブランドを含めることができます。

詳細な概要については、[概要](customize-brands-model-overview.md)に関するページを参照してください。

このトピックで説明されているように、Video Indexer API を使って、ビデオで検出されるカスタム ブランド モデルを作成、使用、および編集することができます。 また、[Video Indexer Web サイトを使用するブランド モデルのカスタマイズ](customize-brands-model-with-api.md)に関するページで説明されているように、Video Indexer Web サイトを使用することもできます。

## <a name="create-a-brand"></a>ブランドを作成する

新しいカスタム ブランドを作成し、指定されたアカウント用のカスタム ブランド モデルに追加します。

### <a name="request-url"></a>要求 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand)。

### <a name="request-parameters"></a>要求パラメーター

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|文字列|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountId|文字列|はい|アカウントのグローバル一意識別子|
|accessToken|文字列|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文

これらのパラメーターに加え、以下の例の形式に従って、新しいブランドに関する情報を提供する要求本文の JSON オブジェクトを指定する必要があります。

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

**enabled** を true に設定すると、Video Indexer での検出対象として *[含める]* リストにブランドが配置されます。 **enabled** を false に設定すると、*[除外]* リストにブランドが配置されます。したがって、Video Indexer では検出されません。

**referenceUrl** 値は、Wikipedia ページへのリンクなど、ブランドの参照 Web サイトにすることができます。

**tags** 値はブランドのタグ リストです。 これは、Video Indexer Web サイトのブランドの *[カテゴリ]* フィールドに表示されます。 たとえば、ブランドが "Azure" の場合、"クラウド" としてタグを付けたり、分類したりすることができます。

### <a name="response"></a>Response

応答では、次の例の形式に従って、先ほど作成したブランドに関する情報が提供されます。

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>ブランドを削除する

指定されたアカウントのカスタム ブランド モデルからブランドを削除します。 アカウントは **accountId** パラメーターで指定されます。 呼び出しが正常に行われると、ブランドは、*[含める]* や *[除外]* のブランド リストに配置されなくなります。

### <a name="request-url"></a>要求 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?)。

### <a name="request-parameters"></a>要求パラメーター

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|文字列|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountId|文字列|はい|アカウントのグローバル一意識別子|
|id|integer|はい|ブランド ID (ブランドの作成時に生成されます)|
|accessToken|文字列|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文

この呼び出しで必要な要求本文はこれ以上ありません。

### <a name="response"></a>Response

ブランドが正常に削除されたときに返されるコンテンツはありません。

## <a name="get-a-specific-brand"></a>特定のブランドを取得する

ブランド ID を使用して、指定されたアカウント用のカスタム ブランド モデルで、ブランドの詳細を検索できます。

### <a name="request-url"></a>要求 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?)。

### <a name="request-parameters"></a>要求パラメーター

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|文字列|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountId|文字列|はい|アカウントのグローバル一意識別子|
|id|integer|はい|ブランド ID (ブランドの作成時に生成されます)|
|accessToken|文字列|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文

この呼び出しで必要な要求本文はこれ以上ありません。

### <a name="response"></a>Response

応答では、次の例の形式に従って、(ブランド ID を使用して) 検索したブランドに関する情報が提供されます。

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> **enabled** が **true** に設定されている場合、ブランドが Video Indexer での検出対象として *[含める]* リストにあることを意味します。**enabled** が false の場合、ブランドが *[除外]* リストにあることを意味するため、Video Indexer では検出されません。

## <a name="update-a-specific-brand"></a>特定のブランドを更新する

ブランド ID を使用して、指定されたアカウント用のカスタム ブランド モデルで、ブランドの詳細を検索できます。

### <a name="request-url"></a>要求 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?)。

### <a name="request-parameters"></a>要求パラメーター

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|文字列|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountId|文字列|はい|アカウントのグローバル一意識別子|
|id|integer|はい|ブランド ID (ブランドの作成時に生成されます)|
|accessToken|文字列|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文

これらのパラメーターに加え、以下の例の形式に従って、更新するブランドに関する更新された情報を提供する、要求本文の JSON オブジェクトを指定する必要があります。

```json
{
  "name": "Example",
  "enabled": false,
  "tags": ["Tag1", "NewTag2"],
  "description": "This is an update example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-28T15:52:22.3413983",
}
```

> [!NOTE]
> この例では、「**ブランドを作成する**」セクションの要求本文例で作成されたブランドが、ここで新しいタグと説明で更新されます。 *[除外]* リストに配置するために、**enabled** 値も false に変更されています。

### <a name="response"></a>Response

応答では、次の例の形式に従って、更新したブランドに関する更新された情報が提供されます。

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>すべてのブランドを取得する

ブランドが *[含める]* と *[除外]* のどちらのブランド リストに配置されるようになっているかに関係なく、指定されたアカウント用のカスタム ブランド モデルのブランドがすべて返されます。

### <a name="request-url"></a>要求 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?)。

### <a name="request-parameters"></a>要求パラメーター

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|文字列|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountId|文字列|はい|アカウントのグローバル一意識別子|
|accessToken|文字列|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文

この呼び出しで必要な要求本文はこれ以上ありません。

### <a name="response"></a>Response

応答では、以下の例の形式に従って、アカウント内のすべてのブランドとそれらの詳細がそれぞれリストされます。

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> *Example* という名前のブランドは、Video Indexer での検出対象として *[含める]* リストにあります。*Example2* という名前のブランドは、*[除外]* リストにあるため、Video Indexer では検出されません。

## <a name="get-brands-model-settings"></a>ブランド モデルの設定を取得する

指定されたアカウントのブランド モデル設定が返されます。 モデル ブランド設定は、Bing ブランド データベースからの検出が有効であるかどうかを表します。 Bing ブランドが有効でない場合、Video Indexer では指定されたアカウントのカスタム ブランド モデルからのみブランドが検出されます。

### <a name="request-url"></a>要求 URL

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands)。

### <a name="request-parameters"></a>要求パラメーター

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|文字列|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountId|文字列|はい|アカウントのグローバル一意識別子|
|accessToken|文字列|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文

この呼び出しで必要な要求本文はこれ以上ありません。

### <a name="response"></a>Response

応答では、次の例の形式に従って、Bing ブランドが有効かどうかが示されます。

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> **useBuiltIn** が true に設定されている場合、Bing ブランドが有効であることを表します。 *useBuiltin* が false の場合、Bing ブランドは無効です。 **state** 値は、推奨されていないため、無視してかまいません。

## <a name="update-brands-model-settings"></a>ブランド モデルの設定を更新する

指定されたアカウントのブランド モデル設定が更新されます。 モデル ブランド設定は、Bing ブランド データベースからの検出が有効であるかどうかを表します。 Bing ブランドが有効でない場合、Video Indexer では指定されたアカウントのカスタム ブランド モデルからのみブランドが検出されます。

### <a name="request-url"></a>要求 URL:
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```

[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?)。

### <a name="request-parameters"></a>要求パラメーター

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|文字列|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountId|文字列|はい|アカウントのグローバル一意識別子|
|accessToken|文字列|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文

これらのパラメーターに加え、以下の例の形式に従って、新しいブランドに関する情報を提供する要求本文の JSON オブジェクトを指定する必要があります。

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> **useBuiltIn** が true に設定されている場合、Bing ブランドが有効であることを表します。 *useBuiltin* が false の場合、Bing ブランドは無効です。

### <a name="response"></a>Response

ブランド モデル設定が正常に更新されたときに返されるコンテンツはありません。

## <a name="next-steps"></a>次の手順

[Web サイトを使用してブランド モデルをカスタマイズする](customize-brands-model-with-website.md)
