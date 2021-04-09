---
title: Azure App Configuration REST API - HMAC 認可
description: REST API を使用した Azure App Configuration に対する認可に HMAC を使用する
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 3746fcf06aea48c9c80696b634d6323b9cb21822
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932661"
---
# <a name="hmac-authorization---rest-api-reference"></a>HMAC 認可 - REST API リファレンス

HMAC 認証を使用すると、操作は、読み取りと書き込みの 2 つのカテゴリのいずれかに分類されます。 読み取り/書き込みアクセス キーによって、すべての操作を呼び出すためのアクセス許可が付与されます。 読み取り専用アクセス キーによって、読み取り操作のみを呼び出すアクセス許可が付与されます。 アクセス キーが読み取り専用または読み取り/書き込みのどちらであるかは、`readOnly` プロパティによって決定されます。 読み取り専用アクセス キーを使用して書き込み要求を実行しようとすると、要求が承認されなくなります。

## <a name="obtaining-access-keys"></a>アクセス キーの取得

アクセス キーとそれらを取得するために使用される API を記述する仕様については、[こちら](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/appconfiguration/resource-manager/Microsoft.AppConfiguration/stable/2019-10-01/appconfiguration.json)の Azure App Configuration リソース プロバイダーの仕様で詳しく説明されています。 アクセス キーは、"ConfigurationStores_ListKeys" 操作を通じて取得されます。

## <a name="errors"></a>エラー

```http
HTTP/1.1 403 Forbidden
```

**理由:** 要求の認証に使用されたアクセス キーでは、要求された操作を実行するために必要なアクセス許可が提供されません。
**解決方法:** 要求された操作を実行するためのアクセス許可を提供するアクセス キーを取得し、それを使用して要求を認証します。
