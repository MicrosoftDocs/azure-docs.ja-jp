---
title: Azure Active Directory REST API - Postman を使用してテストする
description: Postman を使用して Azure App Configuration REST API をテストする
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 0b4444b049d181c2f66f8b02a5202dd17a3f4b6b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932491"
---
# <a name="test-the-azure-app-configuration-rest-api-using-postman"></a>Postman を使用して Azure App Configuration REST API をテストする

[Postman](https://www.getpostman.com/) を使用して REST API をテストするには、[認証](./rest-api-authentication-hmac.md)に必要な HTTP ヘッダーを要求に含める必要があります。 REST API をテストし、認証ヘッダーを自動的に生成するように Postman を構成する方法を次に示します。

1. 新しい[要求](https://learning.getpostman.com/docs/postman/sending_api_requests/requests/)を作成します。
1. [JavaScript 認証サンプル](./rest-api-authentication-hmac.md#javascript)から、要求の[要求前スクリプト](https://learning.getpostman.com/docs/postman/scripts/pre_request_scripts/)に `signRequest` 関数を追加します。
1. 要求前スクリプトの末尾に次のコードを追加します。 TODO コメントの指示に従ってアクセス キーを更新します。

    ```js
    // TODO: Replace the following placeholders with your access key
    var credential = "<Credential>"; // Id
    var secret = "<Secret>"; // Value

    var isBodyEmpty = pm.request.body === null || pm.request.body === undefined || pm.request.body.isEmpty();

    var headers = signRequest(
        pm.request.url.getHost(),
        pm.request.method,
        pm.request.url.getPathWithQuery(),
        isBodyEmpty ? undefined : pm.request.body.toString(),
        credential,
        secret);

    // Add headers to the request
    headers.forEach(header => {
        pm.request.headers.upsert({key: header.name, value: header.value});
    })
    ```

1. 要求を送信する
