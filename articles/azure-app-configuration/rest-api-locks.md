---
title: Azure App Configuration REST API - ロック
description: Azure App Configuration REST API を使用してキー値のロックを操作するための参照ページ
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: e99ce5595bae8ed64285317d9249da60e0fc1b83
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932525"
---
# <a name="locks"></a>ロック

この API (バージョン 1.0) では、キー値リソースのロックおよびロック解除のセマンティクスが提供されます。 次の操作がサポートされます。

- ロックを設定する
- ロックを解除する

`label` (存在する場合) は、明示的なラベル値でなければなりません (ワイルドカードは使用できません)。 すべての操作で、これは省略可能なパラメーターです。 省略されている場合、ラベルがないことを意味します。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-rest-api-prereqs.md)]

## <a name="lock-key-value"></a>キー値をロックする

- 必須: ``{key}``、``{api-version}``  
- 省略可能: ``label``

```http
PUT /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
```

**応答:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

キー値が存在しない場合は、次の応答が返されます。

```http
HTTP/1.1 404 Not Found
```

## <a name="unlock-key-value"></a>キー値のロックを解除する

- 必須: ``{key}``、``{api-version}``  
- 省略可能: ``label``

```http
DELETE /locks/{key}?label={label}?api-version={api-version} HTTP/1.1
```

**応答:**

```http
HTTP/1.1 200 OK
Content-Type: application/vnd.microsoft.appconfig.kv+json; charset=utf-8"
```

```json
{
  "etag": "4f6dd610dd5e4deebc7fbaef685fb903",
  "key": "{key}",
  "label": "{label}",
  "content_type": null,
  "value": "example value",
  "created": "2017-12-05T02:41:26.4874615+00:00",
  "locked": true,
  "tags": []
}
```

キー値が存在しない場合は、次の応答が返されます。

```http
HTTP/1.1 404 Not Found
```

## <a name="conditional-lock-and-unlock"></a>条件付きロックとロック解除

競合状態を回避するには、`If-Match` または `If-None-Match` 要求ヘッダーを使用します。 `etag` 引数はキー表現の一部です。 `If-Match` または `If-None-Match` を省略した場合、操作は無条件になります。

次の要求では、現在のキー値表現が指定された `etag` と一致する場合にのみ、操作が適用されます。

```http
PUT|DELETE /locks/{key}?label={label}&api-version={api-version} HTTP/1.1
If-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```

次の要求では、現在のキー値表現が存在するが、指定された `etag` と一致しない場合にのみ、操作が適用されます。

```http
PUT|DELETE /kv/{key}?label={label}&api-version={api-version} HTTP/1.1
If-None-Match: "4f6dd610dd5e4deebc7fbaef685fb903"
```
