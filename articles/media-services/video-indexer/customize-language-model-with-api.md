---
title: Video Indexer API を使用して言語モデルをカスタマイズする - Azure
titlesuffix: Azure Media Services
description: この記事では、Video Indexer API を使用して言語モデルをカスタマイズする方法を示します。
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.topic: article
ms.date: 02/10/2019
ms.author: anzaman
ms.openlocfilehash: ca1e66d20b19c1a5b85a4f4ff1c433331116bee7
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60002036"
---
# <a name="customize-a-language-model-with-the-video-indexer-apis"></a>Video Indexer API を使用して言語モデルをカスタマイズする

Video Indexer では、カスタム言語モデルを作成し、適応テキストをアップロードすることで音声認識をカスタマイズすることができます。適応テキストとは、エンジンを適応させたいボキャブラリがあるドメインから取得したテキストのことです。 モデルをトレーニングすると、適応テキスト内に含まれる新しい単語が認識されるようになります。 

カスタム言語モデルの詳細な概要とベスト プラクティスについては、「[Customize a Language model with Video Indexer](customize-language-model-overview.md)」(Video Indexer を使って言語モデルをカスタマイズする) をご覧ください。

このトピックで説明しているように、Video Indexer API を使用すると、アカウント内にカスタム言語モデルを作成し、編集することができます。 また、Web サイトを使用することもできます ([Video Indexer Web サイトを使用して言語モデルをカスタマイズする](customize-language-model-with-api.md)方法に関する記事で説明しています)。

## <a name="create-a-language-model"></a>言語モデルの作成

次のコマンドを実行すると、指定されたアカウントに新しいカスタム言語モデルが作成されます。 この呼び出しで、言語モデル用のファイルをアップロードすることができます。 なお、ここでは言語モデルの作成だけを行い、後で言語モデルを更新することで、モデル用のファイルをアップロードすることもできます。

> [!NOTE]
> いずれの場合も、モデル用の対応ファイルでモデルをトレーニングし、ファイルのコンテンツを学習させる必要があります。 言語のトレーニング方法については、次のセクションで説明します。

### <a name="request-url"></a>要求 URL

これは POST 要求です。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?name={name}&accessToken={accessToken}
```

Curl の要求を以下に示します。

```curl
curl -v -X POST "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}&modelName={modelName}&language={language}"

--data-ascii "{body}" 
```

[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Person-Model?)。

### <a name="request-parameters"></a>要求パラメーター

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|string|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountId|string|はい|アカウントのグローバル一意識別子|
|accessToken|string|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|
|modelName|string|はい|言語モデルの名前|
|language|string|はい|言語モデルの言語。 <br/>**language** パラメーターでは、BCP-47 形式の 'language tag-region' (たとえば、'en-US') で言語を指定する必要があります。 サポートされる言語は、英語 (en-US)、ドイツ語 (de-DE)、スペイン語 (es-ES)、アラビア語 (ar-EG)、フランス語 (fr-FR)、ヒンディー語 (hi-HI)、イタリア語 (it-IT)、日本語 (ja-JP)、ポルトガル語 (pt-BR)、ロシア語 (ru-RU)、中国語 (zh-CN) です。  |

### <a name="request-body"></a>要求本文

言語モデルに追加するファイルをアップロードするには、上記の必須パラメーターの値を指定するだけでなく、フォーム データを使用して本文内のファイルをアップロードする必要があります。 この作業を実行する 2 つの方法があります。 

1. キーをファイル名にし、値を txt ファイルにする
2. キーをファイル名にし、値を txt ファイルの URL にする

### <a name="response"></a>Response

応答では、新しく作成された言語モデルのメタデータと、各モデルのファイルのメタデータが、次の JSON 出力サンプルの形式で返されます。

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>言語モデルのトレーニング

次のコマンドを実行すると、言語モデルにアップロードされ、その言語モデル内で有効化されたファイルのコンテンツを使用して、指定されたアカウント内のカスタム言語モデルがトレーニングされます。 

> [!NOTE]
> 最初に、言語モデルを作成し、そのファイルをアップロードする必要があります。 ファイルのアップロードは、言語モデルの作成時に行うか、言語モデルを更新して実行することができます。 

### <a name="request-url"></a>要求 URL

これは PUT 要求です。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}
```

Curl の要求を以下に示します。

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Train?accessToken={accessToken}"
```
 
[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train)。

### <a name="request-parameters"></a>要求パラメーター

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|string|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountID|string|はい|アカウントのグローバル一意識別子|
|modelId|string|はい|言語モデルの ID (言語モデルの作成時に生成されます)|
|accessToken|string|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文

この呼び出しで必要な要求本文はこれ以上ありません。

### <a name="response"></a>Response

応答では、新しくトレーニングされた言語モデルのメタデータと、各モデルのファイルのメタデータが、次の JSON 出力サンプルの形式で返されます。

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

言語モデルの **id** 値は、[インデックスを作成するビデオのアップロード](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?)時に **linguisticModelId** パラメーターとして使用し、[ビデオ インデックスの再作成](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?)時に **languageModelId** パラメーターとして使用します。

## <a name="delete-a-language-model"></a>言語モデルの削除

次のコマンドを実行すると、指定されたアカウントからカスタム言語モデルが削除されます。 削除された言語モデルを使用していたビデオでは、ユーザーがビデオのインデックスを再作成するまで、同じインデックスが保持されます。 ビデオのインデックスを再作成すると、ビデオに新しい言語モデルを割り当てることができます。 それ以外の場合は、Video Indexer により、既定のモデルを使用してビデオのインデックスが再作成されます。

### <a name="request-url"></a>要求 URL

これは DELETE 要求です。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Curl の要求を以下に示します。

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete)。

### <a name="request-parameters"></a>要求パラメーター 

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|string|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountID|string|はい|アカウントのグローバル一意識別子|
|modelId|string|はい|言語モデルの ID (言語モデルの作成時に生成されます)|
|accessToken|string|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文

この呼び出しで必要な要求本文はこれ以上ありません。

### <a name="response"></a>Response

言語モデルが正常に削除されたときに返されるコンテンツはありません。

## <a name="update-a-language-model"></a>言語モデルの更新

次のコマンドを実行すると、指定されたアカウント内のカスタム言語モデルが更新されます。

> [!NOTE]
> あらかじめ言語モデルを作成しておく必要があります。 この呼び出しは、モデルの配下にあるすべてのファイルを有効または無効にしたり、言語モデルの名前を更新したり、言語モデルに追加するファイルをアップロードしたりするために使用できます。

### <a name="request-url"></a>要求 URL

これは PUT 要求です。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}[&modelName][&enable]
```

Curl の要求を以下に示します。

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}?modelName={string}&enable={string}"

--data-ascii "{body}" 
```
 
[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update)。

### <a name="request-parameters"></a>要求パラメーター 

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|string|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountID|string|はい|アカウントのグローバル一意識別子|
|modelId|string|はい|言語モデルの ID (言語モデルの作成時に生成されます)|
|accessToken|string|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|
|modelName|string|いいえ |モデルに付けることができる新しい名前|
|enable|ブール値|いいえ |このモデルの配下にあるすべてのファイルを有効 (true) にするか無効 (false) にするかを選択します|

### <a name="request-body"></a>要求本文

言語モデルに追加するファイルをアップロードするには、上記の必須パラメーターの値を指定するだけでなく、フォーム データを使用して本文内のファイルをアップロードする必要があります。 この作業を実行する 2 つの方法があります。 

1. キーをファイル名にし、値を txt ファイルにする
2. キーをファイル名にし、値を txt ファイルの URL にする

### <a name="response"></a>Response

応答では、新しくトレーニングされた言語モデルのメタデータと、各モデルのファイルのメタデータが、次の JSON 出力サンプルの形式で返されます。

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```
ここで返されたファイルの **id** は、ファイルのコンテンツをダウンロードするために使用できます。

## <a name="update-a-file-from-a-language-model"></a>言語モデル内のファイルを更新する

次のコマンドを使用すると、指定されたアカウント内のカスタム言語モデルに含まれるファイルの名前を更新したり、状態を**有効**にしたりすることができます。

### <a name="request-url"></a>要求 URL

これは PUT 要求です。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}[&fileName][&enable]
```

Curl の要求を以下に示します。

```curl
curl -v -X PUT "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}?fileName={string}&enable={string}"
```
 
[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update)。

### <a name="request-parameters"></a>要求パラメーター 

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|string|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountId|string|はい|アカウントのグローバル一意識別子|
|modelId|string|はい|ファイルを保持している言語モデルの ID (言語モデルの作成時に生成されます)|
|フィールド|string|はい|更新されるファイルの ID (言語モデルが作成または更新される際の、ファイルのアップロード時に生成されます)|
|accessToken|string|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|
|fileName|string|いいえ |更新後のファイル名|
|enable|ブール値|いいえ |言語モデル内でこのファイルを有効 (true) にするか無効 (false) にするかを更新します|

### <a name="request-body"></a>要求本文

この呼び出しで必要な要求本文はこれ以上ありません。

### <a name="response"></a>Response

応答では、更新したファイルのメタデータが、次の JSON 出力サンプルの形式で返されます。

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```
ここで返されたファイルの **id** は、ファイルのコンテンツをダウンロードするために使用できます。

## <a name="get-a-specific-language-model"></a>特定の言語モデルを取得する

次のコマンドを実行すると、指定されたアカウント内の指定された言語モデルに関する情報が返されます (言語や、言語モデルに含まれるファイルなど)。 

### <a name="request-url"></a>要求 URL

これは GET 要求です。
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}
```

Curl の要求を以下に示します。

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}?accessToken={accessToken}"
```
 
[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get)。

### <a name="request-parameters-and-request-body"></a>要求パラメーターと要求本文

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|string|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountID|string|はい|アカウントのグローバル一意識別子|
|modelId|string|はい|言語モデルの ID (言語モデルの作成時に生成されます)|
|accessToken|string|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文

この呼び出しで必要な要求本文はこれ以上ありません。

### <a name="response"></a>Response

応答では、指定された言語モデルのメタデータと、各モデルのファイルのメタデータが、次の JSON 出力サンプルの形式で返されます。

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

ここで返されたファイルの **id** は、ファイルのコンテンツをダウンロードするために使用できます。

## <a name="get-all-the-language-models"></a>すべての言語モデルを取得する

次のコマンドを実行すると、指定されたアカウント内のすべてのカスタム言語モデルが一覧で返されます。

### <a name="request-url"></a>要求 URL

これは GET 要求です。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}
```

Curl の要求を以下に示します。

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language?accessToken={accessToken}"
```
 
[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get)。

### <a name="request-parameters"></a>要求パラメーター

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|string|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountID|string|はい|アカウントのグローバル一意識別子|
|accessToken|string|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文

この呼び出しで必要な要求本文はこれ以上ありません。

### <a name="response"></a>Response

応答では、アカウント内のすべての言語モデルと、各言語モデルのメタデータとファイルが、次の JSON 出力サンプルの形式で一覧表示されます。

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>言語モデルからファイルを削除する

次のコマンドを実行すると、指定されたアカウント内の指定された言語モデルから、指定されたファイルが削除されます。 

### <a name="request-url"></a>要求 URL

これは DELETE 要求です。
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}
```

Curl の要求を以下に示します。

```curl
curl -v -X DELETE "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete)。

### <a name="request-parameters"></a>要求パラメーター 

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|string|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountID|string|はい|アカウントのグローバル一意識別子|
|modelId|string|はい|ファイルを保持している言語モデルの ID (言語モデルの作成時に生成されます)|
|フィールド|string|はい|更新されるファイルの ID (言語モデルが作成または更新される際の、ファイルのアップロード時に生成されます)|
|accessToken|string|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文

この呼び出しで必要な要求本文はこれ以上ありません。

### <a name="response"></a>Response

言語モデルからファイルが正常に削除された場合、返されるコンテンツはありません。

## <a name="get-metadata-on-a-file-from-a-language-model"></a>言語モデルからファイルのメタデータを取得する

これを実行すると、アカウント内の選択された言語モデルから、指定されたファイルのコンテンツとメタデータが返されます。

### <a name="request-url"></a>要求 URL

これは GET 要求です。

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/PersonModels?accessToken={accessToken}
```

Curl の要求を以下に示します。
```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}?accessToken={accessToken}"
```
 
[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model)。

### <a name="request-parameters"></a>要求パラメーター 

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|string|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountID|string|はい|アカウントのグローバル一意識別子|
|modelId|string|はい|ファイルを保持している言語モデルの ID (言語モデルの作成時に生成されます)|
|フィールド|string|はい|更新されるファイルの ID (言語モデルが作成または更新される際の、ファイルのアップロード時に生成されます)|
|accessToken|string|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文

この呼び出しで必要な要求本文はこれ以上ありません。

### <a name="response"></a>Response

応答では、ファイルのコンテンツとメタデータが、次のような JSON 形式で返されます。

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> このサンプル ファイルのコンテンツは、2 つの行に分けられた、"hello" と "world" という単語です。

## <a name="download-a-file-from-a-language-model"></a>言語モデルからファイルをダウンロードする

次のコマンドを実行すると、指定されたアカウント内の指定された言語モデルから、指定されたファイルのコンテンツを含んだテキスト ファイルがダウンロードされます。 このテキスト ファイルは、最初にアップロードされたテキスト ファイルのコンテンツと一致します。

### <a name="request-url"></a>要求 URL
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}
```

Curl の要求を以下に示します。

```curl
curl -v -X GET "https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Language/{modelId}/Files/{fileId}/download?accessToken={accessToken}"
```
 
[必須のパラメーターを確認し、Video Indexer 開発者ポータルを使用してテストします](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?)。

### <a name="request-parameters"></a>要求パラメーター 

|**Name**|**Type**|**必須**|**説明**|
|---|---|---|---|
|location|string|はい|呼び出しをルーティングする必要がある Azure リージョン。 詳細については、[Azure リージョンと Video Indexer](regions.md) に関するページを参照してください。|
|accountID|string|はい|アカウントのグローバル一意識別子|
|modelId|string|はい|ファイルを保持している言語モデルの ID (言語モデルの作成時に生成されます)|
|フィールド|string|はい|更新されるファイルの ID (言語モデルが作成または更新される際の、ファイルのアップロード時に生成されます)|
|accessToken|string|はい|呼び出しに対して認証を行うアクセス トークン (対象は[アカウント アクセス トークン](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)である必要があります)。 アクセス トークンの有効期限は 1 時間です。|

### <a name="request-body"></a>要求本文 

この呼び出しで必要な要求本文はこれ以上ありません。

### <a name="response"></a>Response

応答では、ファイルのコンテンツを含んだテキスト ファイルが JSON 形式でダウンロードされます。 

## <a name="next-steps"></a>次の手順

[Web サイトを使用して言語モデルをカスタマイズする](customize-language-model-with-website.md)
