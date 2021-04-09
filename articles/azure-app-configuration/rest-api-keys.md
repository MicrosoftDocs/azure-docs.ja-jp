---
title: Azure App Configuration REST API - キー
description: Azure App Configuration REST API を使用してキーを操作するためのレファレンス ページ
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 65ca190d7fbd6d8d4df473fbe2112eafbd031fde
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932542"
---
# <a name="keys"></a>[キー]

api-version: 1.0

次の構文は、キー リソースを表します。

```http
{
    "name": [string]             // Name of the key
}
```

## <a name="operations"></a>Operations

キー リソースを使用すると、次の操作を行うことができます。

- List

すべての操作において、`name` は省略可能なフィルター パラメーターです。 省略されている場合は、*任意の* キーを意味します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="list-keys"></a>キーを一覧表示します。

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**応答:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{key-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>改ページ位置の自動修正

返された項目の数が応答の制限を超えている場合、結果は改ページされます。 省略可能な `Link` 応答ヘッダーに従い、`rel="next"` を使用してナビゲーションを行います。 あるいは、コンテンツによって、`@nextLink` プロパティの形式で次のリンクが指定されます。 次のリンクには `api-version` パラメーターが含まれています。

```http
GET /keys?api-version={api-version} HTTP/1.1
```

**応答:**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json; charset=utf-8
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

```name``` によるフィルター処理がサポートされています。

```http
GET /keys?name={key-name}&api-version={api-version}
```

次のフィルターがサポートされています。

|キー フィルター|結果|
|--|--|
|`name` を省略 (または `name=*`)|**すべての** キーに一致します|
|`name=abc`|**abc** という名前のキーに一致します|
|`name=abc*`|**abc** で始まるキー名に一致します|
|`name=abc,xyz`|キー名 **abc** または **xyz** に一致します (CSV の上限は 5 つ)|

次の文字は予約されています: `*`、`\`、`,`

予約文字が値の一部である場合は、`\{Reserved Character}` を使用してエスケープする必要があります。 予約されていない文字もエスケープできます。

## <a name="filter-validation"></a>フィルター検証

フィルター検証エラーが発生した場合、応答は HTTP `400` で、次のようなエラー詳細が含まれます。

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
```

```json
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid request parameter 'name'",
  "name": "name",
  "detail": "name(2): Invalid character",
  "status": 400
}
```

## <a name="examples"></a>例

- All

    ```http
    GET /keys?api-version={api-version}
    ```

- キー名が **abc** で始まる

    ```http
    GET  /keys?name=abc*&api-version={api-version}
    ```

- キー名が **abc** または **xyz** のいずれか

    ```http
    GET /keys?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>特定のフィールドの要求

省略可能な `$select` クエリ文字列パラメーターを使用して、要求されたフィールドのコンマ区切りリストを指定します。 `$select` パラメーターを省略した場合、応答には既定のセットが含まれます。

```http
GET /keys?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>時間ベースのアクセス

過去の時間のものとして結果の表現を取得します。 [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1) セクションを参照してください。

```http
GET /keys&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**応答:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.keyset+json"
Memento-Datetime: Sat, 12 May 2018 02:10:00 GMT
Link: <relative uri>; rel="original"
```

```json
{
    "items": [
        ....
    ]
}
```
