---
title: Azure App Configuration REST API - バージョン管理
description: Azure App Configuration REST API を使用してバージョン管理を行うための参照ページ
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: a869531860942e5a8b2b05212e778aca2170c89b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932423"
---
# <a name="versioning"></a>バージョン管理

各クライアント要求には、明示的な API バージョンがクエリ文字列パラメーターとして指定されている必要があります。 たとえば、`https://{myconfig}.azconfig.io/kv?api-version=1.0` のように指定します。

`api-version` は、SemVer (major.minor) 形式で表されます。 範囲またはバージョンのネゴシエーションはサポートされていません。

この記事は、API バージョン 1.0 に適用されます。

要求された API バージョンを一致させることができない場合にサーバーによって返される可能性があるエラー応答の概要を次に示します。

## <a name="api-version-unspecified"></a>API バージョンが指定されていない

このエラーは、API バージョンを指定せずにクライアントによって要求が行われた場合に発生します。

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "API version is not specified",
  "name": "api-version",
  "detail": "An API version is required, but was not specified.",
  "status": 400
}
```

## <a name="unsupported-api-version"></a>サポートされていない API バージョン

このエラーは、クライアントによって要求された API バージョンが、サーバーによってサポートされているどの API バージョンとも一致しない場合に発生します。

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Unsupported API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

## <a name="invalid-api-version"></a>無効な API バージョン

このエラーは、クライアントによって API バージョンを指定して要求が行われたが、その値の形式に誤りがあるか、サーバーによって解決できない場合に発生します。

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8  
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Invalid API version",
  "name": "api-version",
  "detail": "The HTTP resource that matches the request URI '{request uri}' does not support the API version '{api-version}'.",
  "status": 400
}
```

## <a name="ambiguous-api-version"></a>あいまいな API バージョン

このエラーは、クライアントによってサーバーに対してあいまいな API バージョンが要求された場合に発生します (複数の異なる値など)。

```http
HTTP/1.1 400 Bad Request
Content-Type: application/problem+json; charset=utf-8
{
  "type": "https://azconfig.io/errors/invalid-argument",
  "title": "Ambiguous API version",
  "name": "api-version",
  "detail": "The following API versions were requested: {comma separated api versions}. At most, only a single API version may be specified. Please update the intended API version and retry the request.",
  "status": 400
}
```
