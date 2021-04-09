---
title: Azure App Configuration REST API - ラベル
description: Azure App Configuration REST API を使用してラベルを操作するための参照ページ
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 5a59f5910d44f2a2b4cd75e7a1d51c2ed5dd51a4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932508"
---
# <a name="labels"></a>ラベル

api-version: 1.0

**ラベル** リソースは次のように定義されます。

```json
{
      "name": [string]             // Name of the label
}
```

次の操作がサポートされています。

- List

すべての操作について、``name`` は省略可能なフィルター パラメーターです。 省略されている場合、これは **任意の** ラベルを意味します。

## <a name="prerequisites"></a>前提条件

- すべての HTTP 要求が認証される必要があります。 [認証](./rest-api-authentication-index.md)に関するセクションを参照してください。
- すべての HTTP 要求で、明示的な `api-version` を指定する必要があります。 [バージョン管理](./rest-api-versioning.md)に関するセクションを参照してください。

## <a name="list-labels"></a>ラベルを一覧表示する

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**応答:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8"
```

```json
{
    "items": [
        {
          "name": "{label-name}"
        },
        ...
    ],
    "@nextLink": "{relative uri}"
}
```

## <a name="pagination"></a>改ページ位置の自動修正

返された項目の数が応答の制限を超えている場合、結果は改ページされます。 省略可能な `Link` 応答ヘッダーに従い、`rel="next"` を使用してナビゲーションを行います。 また、コンテンツで、`@nextLink` プロパティの形式で次のリンクが指定されます。 次のリンクには `api-version` パラメーターが含まれています。

```http
GET /labels?api-version={api-version} HTTP/1.1
```

**応答:**

```http
HTTP/1.1 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json; charset=utf-8
Accept-Ranges: items
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

`name` によるフィルター処理がサポートされています。

```http
GET /labels?name={label-name}&api-version={api-version}
```

### <a name="supported-filters"></a>サポートされているフィルター

|キー フィルター|結果|
|--|--|
|`name` を省略 (または `name=*`)|**任意の** ラベルに一致します|
|`name=abc`|**abc** という名前のラベルに一致します|
|`name=abc*`|**abc** で始まるラベル名に一致します|
|`name=abc,xyz`|ラベル名 **abc** または **xyz** に一致します (CSV の上限は 5 つ)|

### <a name="reserved-characters"></a>予約文字

`*`, `\`, `,`

予約文字が値の一部である場合は、`\{Reserved Character}` を使用してエスケープする必要があります。 予約されていない文字もエスケープできます。

### <a name="filter-validation"></a>フィルター検証

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

### <a name="examples"></a>例

- All

    ```http
    GET /labels?api-version={api-version}
    ```

- ラベル名が **abc** で始まる

    ```http
    GET  /labels?name=abc*&api-version={api-version}
    ```

- ラベル名が **abc** または **xyz** のいずれかである

    ```http
    GET /labels?name=abc,xyz&api-version={api-version}
    ```

## <a name="request-specific-fields"></a>要求に固有のフィールド

省略可能な `$select` クエリ文字列パラメーターを使用して、要求されたフィールドのコンマ区切りリストを指定します。 `$select` パラメーターを省略した場合、応答には既定のセットが含まれます。

```http
GET /labels?$select=name&api-version={api-version} HTTP/1.1
```

## <a name="time-based-access"></a>時間ベースのアクセス

過去の時間のものとして結果の表現を取得します。 [2.1.1](https://tools.ietf.org/html/rfc7089#section-2.1) セクションを参照してください。

```http
GET /labels&api-version={api-version} HTTP/1.1
Accept-Datetime: Sat, 12 May 2018 02:10:00 GMT
```

**応答:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.labelset+json"
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
