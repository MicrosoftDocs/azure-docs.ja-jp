---
title: Azure App Configuration REST API - キー値
description: Azure App Configuration REST API を使用してキー値を操作するための参照ページ
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 95081d6f8c2770d01f7836e08b6851860bf47ba8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932559"
---
# <a name="key-values"></a>キー値

キー値は、`key` + `label` の一意の組み合わせによって識別されるリソースです。 `label` はオプションです。 ラベルがないキー値を明示的に参照する場合は、"\0" (URL エンコード: ``%00``) を使用してください。 各操作の詳細を確認してください。

この記事は、API バージョン 1.0 に適用されます。

## <a name="operations"></a>Operations

- 取得
- 複数を一覧表示
- オン
- 削除

## <a name="prerequisites"></a>前提条件

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="syntax"></a>構文

```json
{
  "etag": [string],
  "key": [string],
  "label": [string, optional],
  "content_type": [string, optional],
  "value": [string],
  "last_modified": [datetime ISO 8601],
  "locked": [boolean],
  "tags": [object with string properties, optional]
}
```

## <a name="get-key-value"></a>キー値の取得

必須: ``{key}``、``{api-version}``  
省略可能:``label`` (省略した場合、ラベルのないキー値を意味します。)

```http
GET /kv/{key}?label={label}&api-version={api-version}
```

**応答:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8;
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26+00:00",
  "locked": "false",
  "tags": {
    "t1": "value1",
    "t2": "value2"
  }
}
```

キーが存在しない場合は、次の応答が返されます。

```http
HTTP/1.1 404 Not Found
```

## <a name="get-conditionally"></a>取得 (条件付き)

クライアントのキャッシュを向上させるには、 `If-Match` または `If-None-Match` の要求ヘッダーを使用します。 `etag` 引数はキー表現の一部です。 詳細については、[セクション 14.24 および 14.26](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html) を参照してください。

次の要求では、現在の表現が指定された `etag` と一致しない場合にのみ、キー値が取得されます。

```http
GET /kv/{key}?api-version={api-version} HTTP/1.1
Accept: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "{etag}"
```

**応答:**

```http
HTTP/1.1 304 NotModified
```

or

```http
HTTP/1.1 200 OK
```

## <a name="list-key-values"></a>キー値の一覧表示

省略可能:``key`` (指定されていない場合は、すべてのキーを意味します。)省略可能:``label`` (指定されていない場合は、すべてのラベルを意味します。)

```http
GET /kv?label=*&api-version={api-version} HTTP/1.1
```

**応答:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json; charset=utf-8
```

その他のオプションについては、この記事で後述する「フィルター処理」のセクションを参照してください。

## <a name="pagination"></a>改ページ位置の自動修正

返された項目の数が応答の制限を超えている場合、結果は改ページされます。 省略可能な `Link` 応答ヘッダーに従い、`rel="next"` を使用してナビゲーションを行います。
あるいは、コンテンツによって、`@nextLink` プロパティの形式で次のリンクが指定されます。 リンクされた URI には、`api-version` 引数が含まれます。

```http
GET /kv?api-version={api-version} HTTP/1.1
```

**応答:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvs+json; charset=utf-8
Link: <{relative uri}>; rel="next"
```

```json
{
    "items": [
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="filtering"></a>フィルター処理

`key` と `label` のフィルター処理の組み合わせがサポートされています。
省略可能な `key` と `label` のクエリ文字列パラメーターを使用します。

```http
GET /kv?key={key}&label={label}&api-version={api-version}
```

### <a name="supported-filters"></a>サポートされているフィルター

|キー フィルター|結果|
|--|--|
|`key` を省略 (または `key=*`)|**すべての** キーに一致します|
|`key=abc`|**abc** という名前のキーに一致します|
|`key=abc*`|**abc** で始まるキー名に一致します|
|`key=abc,xyz`|キー名 **abc** または **xyz** に一致します (CSV の上限は 5 つ)|

|ラベル フィルター|結果|
|--|--|
|`label` を省略 (または `label=*`)|**すべての** ラベルに一致します|
|`label=%00`|ラベルなしの KV に一致します|
|`label=prod`|**prod** というラベルに一致します|
|`label=prod*`|**prod** で始まるラベルに一致します|
|`label=prod,test`|**prod** または **test** というラベルに一致します (CSV の上限は 5 つ)|

***予約文字***

`*`, `\`, `,`

予約文字が値の一部である場合は、`\{Reserved Character}` を使用してエスケープする必要があります。 予約されていない文字もエスケープできます。

***フィルター検証***

フィルター検証エラーが発生した場合、応答は HTTP `400` で、次のようなエラー詳細が含まれます。

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter '{filter}'",
  "name": "{filter}",
  "detail": "{filter}(2): Invalid character",
  "status": 400
}
```

**使用例**

- All

    ```http
    GET /kv?api-version={api-version}
    ```

- キー名が **abc** で始まり、すべてのラベルが含まれる

    ```http
    GET /kv?key=abc*&label=*&api-version={api-version}
    ```

- キー名が **abc** で始まり、ラベルが **v1** または **v2** と等しい

    ```http
    GET /kv?key=abc*&label=v1,v2&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>特定のフィールドの要求

省略可能な `$select` クエリ文字列パラメーターを使用して、要求するフィールドのコンマ区切りリストを指定します。 `$select` パラメーターを省略した場合、応答には既定のセットが含まれます。

```http
GET /kv?$select=key,value&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>時間ベースのアクセス

過去の時間のものとして結果の表現を取得します。 詳細については、セクション [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1) を参照してください。 この記事で既に定義したように、改ページ位置の自動修正は引き続きサポートされます。

```http
GET /kv?api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**応答:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kvset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <{relative uri}>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```

## <a name="set-key"></a>キーの設定

- [必須ですか?]\: ``{key}``オン
- 省略可能:``label`` (指定されていない場合、または label=%00 の場合、ラベルなしのキー値を意味します。)

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
```

```json
{
  "value": "example value",         // optional
  "content_type": "user defined",   // optional
  "tags": {                         // optional
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

**応答:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
Last-Modified: Tue, 05 Dec 2017 02:41:26 GMT
ETag: "4f6dd610dd5e4deebc7fbaef685fb903"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": "user defined",
  "value": "example value",
  "last_modified": "2017-12-05T02:41:26.4874615+00:00",
  "tags": {
    "tag1": "value1",
    "tag2": "value2",
  }
}
```

項目がロックされている場合は、次の応答が表示されます。

```http
HTTP/1.1 409 Conflict
Content-Type: application/problem+json; charset="utf-8"
```

```json
{
    "type": "https://azconfig.io/errors/key-locked",
    "title": "Modifing key '{key}' is not allowed",
    "name": "{key}",
    "detail": "The key is read-only. To allow modification unlock it first.",
    "status": "409"
}
```

## <a name="set-key-conditionally"></a>キーの設定 (条件付き)

競合状態を回避するには、`If-Match` または `If-None-Match` 要求ヘッダーを使用します。 `etag` 引数はキー表現の一部です。
`If-Match` または `If-None-Match` を省略した場合、操作は無条件になります。

次の応答では、現在の表現が指定された `etag` と一致する場合にのみ、値が更新されます。

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

次の応答では、現在の表現が指定された `etag` と一致しない場合にのみ、値が更新されます。

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

次の要求では、表現が既に存在する場合にのみ、値が追加されます。

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json;
If-Match: "*"
```

次の要求では、表現がまだ存在 "しない" 場合にのみ、値が追加されます。

```http
PUT /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
Content-Type: application/vnd.microsoft.appconfig.kv+json
If-None-Match: "*"
```

**応答**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

or

```http
HTTP/1.1 412 PreconditionFailed
```

## <a name="delete"></a>削除

- 必須: `{key}`、`{api-version}`
- 省略可能:`{label}` (指定されていない場合、または label=%00 の場合、ラベルなしのキー値を意味します。)

```http
DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**応答:** 削除されたキー値を返すか、キー値が存在しない場合は none を返します。

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8
...
```

or

```http
HTTP/1.1 204 No Content
```

## <a name="delete-key-conditionally"></a>キーの削除 (条件付き)

これは、この記事の前述の「キーの設定 (条件付き)」セクションに似ています。
