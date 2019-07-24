---
title: Azure Digital Twins 内のオブジェクトに BLOB を追加する方法 | Microsoft Docs
description: Azure Digital Twins 内のオブジェクトに BLOB を追加する方法について説明します。
author: kingdomofends
manager: alinast
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 01/11/2019
ms.author: adgera
ms.custom: seodec18
ms.openlocfilehash: ffd7d71c33b569b396b9f8babf8105968ee525b9
ms.sourcegitcommit: c61777f4aa47b91fb4df0c07614fdcf8ab6dcf32
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/14/2019
ms.locfileid: "54263069"
---
# <a name="add-blobs-to-objects-in-azure-digital-twins"></a>Azure Digital Twins 内のオブジェクトに BLOB を追加する

BLOB は、一般的なファイルの種類 (画像やログなど) の非構造化表現です。 BLOB では、MIME の種類 (例:"image/jpeg") とメタデータ (名前、説明、型など) を使用して表現するデータの種類が追跡されます。

Azure Digital Twins では、デバイス、スペース、ユーザーへの BLOB のアタッチがサポートされます。 BLOB では、ユーザーのプロファイル画像、デバイスの写真、ビデオ、マップ、ファームウェア zip、JSON データ、ログなどを表すことができます。

[!INCLUDE [Digital Twins Management API familiarity](../../includes/digital-twins-familiarity.md)]

## <a name="uploading-blobs-overview"></a>BLOB のアップロードの概要

マルチパート要求を使用して、特定のエンドポイントとそれぞれの機能に BLOB をアップロードできます。

[!INCLUDE [Digital Twins multipart requests](../../includes/digital-twins-multipart.md)]

### <a name="blob-metadata"></a>BLOB のメタデータ

**Content-Type** と **Content-Disposition** に加えて、Azure Digital Twins BLOB のマルチパート要求では、正しい JSON 本文も指定する必要があります。 送信する JSON 本文は、実行される HTTP 要求操作の種類によって決まります。

4 つの主な JSON スキーマは次のとおりです。

![JSON スキーマ][1]

JSON BLOB のメタデータは、次のモデルに準拠しています。

```JSON
{
    "parentId": "00000000-0000-0000-0000-000000000000",
    "name": "My First Blob",
    "type": "Map",
    "subtype": "GenericMap",
    "description": "A well chosen description",
    "sharing": "None"
  }
```

| Attribute | type | 説明 |
| --- | --- | --- |
| **parentId** | String | BLOB を (スペース、デバイス、またはユーザー) と関連付ける親エンティティ |
| **name** |String | BLOB のわかりやすい名前 |
| **type** | String | BLOB の種類。*type* と *typeId* は使用できません  |
| **typeId** | 整数 | BLOB の種類の ID。*type* と *typeId* は使用できません |
| **subtype** | String | BLOB のサブタイプ。*subtype* と *subtypeId* は使用できません |
| **subtypeId** | 整数 | BLOB のサブタイプ ID。*subtype* と *subtypeId* は使用できません |
| **description** | String | BLOB のカスタマイズした説明 |
| **sharing** | String | BLOB を共有できるかどうか - 列挙型 [`None`、`Tree`、`Global`] |

BLOB のメタデータは常に最初のチャンクとして指定されます。**Content-type** `application/json` で、または `.json` ファイルとして指定されます。 ファイル データは 2 番目のチャンクで供給され、サポートされているいずれかの MIME の種類である可能性があります。

Swagger のドキュメントでは、これらのモデル スキーマが詳細に説明されています。

[!INCLUDE [Digital Twins Swagger](../../includes/digital-twins-swagger.md)]

参照ドキュメントの使用については、[Swagger の使用方法](./how-to-use-swagger.md)に関する記事をご覧ください。

<div id="blobModel"></div>

### <a name="blobs-response-data"></a>BLOB の応答データ

個々に返される BLOB は、次の JSON スキーマに準拠します。

```JSON
{
  "id": "00000000-0000-0000-0000-000000000000",
  "name": "string",
  "parentId": "00000000-0000-0000-0000-000000000000",
  "type": "string",
  "subtype": "string",
  "typeId": 0,
  "subtypeId": 0,
  "sharing": "None",
  "description": "string",
  "contentInfos": [
    {
      "type": "string",
      "sizeBytes": 0,
      "mD5": "string",
      "version": "string",
      "lastModifiedUtc": "2019-01-12T00:58:08.689Z",
      "metadata": {
        "additionalProp1": "string",
        "additionalProp2": "string",
        "additionalProp3": "string"
      }
    }
  ],
  "fullName": "string",
  "spacePaths": [
    "string"
  ]
}
```

| Attribute | type | 説明 |
| --- | --- | --- |
| **id** | String | BLOB の一意識別子 |
| **name** |String | BLOB のわかりやすい名前 |
| **parentId** | String | BLOB を (スペース、デバイス、またはユーザー) と関連付ける親エンティティ |
| **type** | String | BLOB の種類。*type* と *typeId* は使用できません  |
| **typeId** | 整数 | BLOB の種類の ID。*type* と *typeId* は使用できません |
| **subtype** | String | BLOB のサブタイプ。*subtype* と *subtypeId* は使用できません |
| **subtypeId** | 整数 | BLOB のサブタイプ ID。*subtype* と *subtypeId* は使用できません |
| **sharing** | String | BLOB を共有できるかどうか - 列挙型 [`None`、`Tree`、`Global`] |
| **description** | String | BLOB のカスタマイズした説明 |
| **contentInfos** | Array | バージョンを含む構造化されていないメタデータ情報を指定します |
| **fullName** | String | BLOB の完全な名前 |
| **spacePaths** | String | スペース パス |

BLOB のメタデータは常に最初のチャンクとして指定されます。**Content-type** `application/json` で、または `.json` ファイルとして指定されます。 ファイル データは 2 番目のチャンクで供給され、サポートされているいずれかの MIME の種類である可能性があります。

### <a name="blob-multipart-request-examples"></a>BLOB マルチパート要求の例

[!INCLUDE [Digital Twins Management API](../../includes/digital-twins-management-api.md)]

BLOB をテキスト ファイルとしてアップロードし、それをスペースに関連付けるには、以下に対して認証済みの HTTP POST 要求を実行します。

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs
```

さらに以下の本体を追加します。

```plaintext
--USER_DEFINED_BOUNDARY
Content-Type: application/json; charset=utf-8
Content-Disposition: form-data; name="metadata"

{
  "ParentId": "54213cf5-285f-e611-80c3-000d3a320e1e",
  "Name": "My First Blob",
  "Type": "Map",
  "SubType": "GenericMap",
  "Description": "A well chosen description",
  "Sharing": "None"
}
--USER_DEFINED_BOUNDARY
Content-Disposition: form-data; name="contents"; filename="myblob.txt"
Content-Type: text/plain

This is my blob content. In this case, some text, but I could also be uploading a picture, an JSON file, a firmware zip, etc.

--USER_DEFINED_BOUNDARY--
```

| 値 | 置換後の文字列 |
| --- | --- |
| USER_DEFINED_BOUNDARY | マルチパート コンテンツ境界名 |

次に示すコードは、[MultipartFormDataContent](https://docs.microsoft.com/dotnet/api/system.net.http.multipartformdatacontent) クラスを使用した同じ BLOB アップロードの .NET での実装です。

```csharp
//Supply your metadata in a suitable format
var multipartContent = new MultipartFormDataContent("USER_DEFINED_BOUNDARY");

var metadataContent = new StringContent(JsonConvert.SerializeObject(metaData), Encoding.UTF8, "application/json");
metadataContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json; charset=utf-8");
multipartContent.Add(metadataContent, "metadata");

//MY_BLOB.txt is the String representation of your text file
var fileContents = new StringContent("MY_BLOB.txt");
fileContents.Headers.ContentType = MediaTypeHeaderValue.Parse("text/plain");
multipartContent.Add(fileContents, "contents");

var response = await httpClient.PostAsync("spaces/blobs", multipartContent);
```

最後に、[cURL](https://curl.haxx.se/) ユーザーは、同じ方法でマルチパート フォーム要求を行うことができます。

![デバイスの BLOB][5]

```bash
curl
 -X POST "YOUR_MANAGEMENT_API_URL/spaces/blobs"
 -H "Authorization: Bearer YOUR_TOKEN"
 -H "Accept: application/json"
 -H "Content-Type: multipart/form-data"
 -F "meta={\"ParentId\": \"YOUR_SPACE_ID\",\"Name\":\"My CURL Blob",\"Type\":\"Map\",\"SubType\":\"GenericMap\",\"Description\": \"A well chosen description\", \"Sharing\": \"None\"};type=application/json"
 -F "text=PATH_TO_FILE;type=text/plain"
```

| 値 | 置換後の文字列 |
| --- | --- |
| YOUR_TOKEN | 有効な OAuth 2.0 トークン |
| YOUR_SPACE_ID | BLOB を関連付けるスペースの ID |
| PATH_TO_FILE | テキスト ファイルへのパス |

POST が成功すると、新しい BLOB の ID が返されます (前に赤で強調表示)。

## <a name="api-endpoints"></a>API エンドポイント

次のセクションでは、コアとなる BLOB 関連の API エンドポイントとその機能について説明します。

### <a name="devices"></a>デバイス

BLOB をデバイスにアタッチできます。 次の図では、Management API に関する Swagger のリファレンス ドキュメントを示します。 BLOB を使用するためのデバイス関連の API エンドポイントと、それらに渡す必要があるパス パラメーターが指定されています。

![デバイスの BLOB][2]

たとえば、BLOB を更新または作成して、BLOB をデバイスにアタッチするために、以下に対して認証済みの HTTP PATCH 要求を行います。

```plaintext
YOUR_MANAGEMENT_API_URL/devices/blobs/YOUR_BLOB_ID
```

| パラメーター | 置換後の文字列 |
| --- | --- |
| *YOUR_BLOB_ID* | 目的の BLOB ID |

要求が成功すると、[前述](#blobModel)のように JSON オブジェクトが返されます。

### <a name="spaces"></a>スペース

スペースに BLOB をアタッチすることもできます。 次の図では、BLOB を処理するすべてのスペース API エンドポイントの一覧を示します。 また、それらのエンドポイントに渡すすべてのパス パラメーターの一覧も示します。

![スペース BLOB][3]

たとえば、スペースにアタッチされている BLOB を返すには、以下に対して認証済みの HTTP GET 要求を行います。

```plaintext
YOUR_MANAGEMENT_API_URL/spaces/blobs/YOUR_BLOB_ID
```

| パラメーター | 置換後の文字列 |
| --- | --- |
| *YOUR_BLOB_ID* | 目的の BLOB ID |

要求が成功すると、[前述](#blobModel)のように JSON オブジェクトが返されます。

同じエンドポイントに対して PATCH 要求を実行すると、メタデータの説明が更新され、BLOB のバージョンが作成されます。 HTTP 要求は、必要なメタデータ、およびマルチパート フォーム データと共に PATCH メソッドを使って実行されます。

### <a name="users"></a>ユーザー

BLOB を (たとえば、プロファイル画像を関連付けるために) ユーザー モデルにアタッチできます。 次の図では、関連するユーザー API エンドポイントと、`id` などの必須のパス パラメーターを示します。

![ユーザー BLOB][4]

たとえば、ユーザーにアタッチされている BLOB をフェッチするには、必要なフォーム データを使用して認証済みの HTTP GET 要求を行います。

```plaintext
YOUR_MANAGEMENT_API_URL/users/blobs/YOUR_BLOB_ID
```

| パラメーター | 置換後の文字列 |
| --- | --- |
| *YOUR_BLOB_ID* | 目的の BLOB ID |

要求が成功すると、[前述](#blobModel)のように JSON オブジェクトが返されます。

## <a name="common-errors"></a>一般的なエラー

一般的なエラーでは、正確なヘッダー情報が提供されません。

```JSON
{
    "error": {
        "code": "400.600.000.000",
        "message": "Invalid media type in first section."
    }
}
```

このエラーを解決するには、要求全体が、適切な **Content-type** ヘッダーを持っていることを確認します。

* `multipart/mixed`
* `multipart/form-data`

また、それぞれのマルチパート チャンクが、必要とされる、対応する **Content-type** になっていることを確認します。

## <a name="next-steps"></a>次の手順

- Azure Digital Twins に関する Swagger の参照ドキュメントについて詳しくは、[Azure Digital Twins Swagger の使用方法](how-to-use-swagger.md)に関する記事をご覧ください。

- Postman を通して BLOB をアップロードする場合は、[Postman を構成する方法](./how-to-configure-postman.md)に関するページを参照してください。

<!-- Images -->
[1]: media/how-to-add-blobs/blob-models.PNG
[2]: media/how-to-add-blobs/blobs-device-api.PNG
[3]: media/how-to-add-blobs/blobs-space-api.PNG
[4]: media/how-to-add-blobs/blobs-users-api.PNG
[5]: media/how-to-add-blobs/curl.PNG