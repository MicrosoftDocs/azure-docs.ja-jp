---
title: サービス アクセス トークンを取得する
description: ARR REST API にアクセスするためのトークンを作成する方法について説明します
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7e8e2f3f9dd49693faa26eaaab309fcad58f6f9f
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2020
ms.locfileid: "89076159"
---
# <a name="get-service-access-tokens"></a>サービス アクセス トークンを取得する

ARR REST API へのアクセスは、権限のあるユーザーにのみ許可されています。 権限があることを証明するには、REST 要求と共に*アクセス トークン*を送信する必要があります。 これらのトークンは、*セキュリティ トークン サービス* (STS) からアカウント キーと引き換えに発行されます。 トークンの**有効期間は 24 時間**であるため、サービスへのフルアクセス権を与えずにユーザーに発行できます。

この記事では、そうしたアクセス トークンの作成方法について説明します。

## <a name="prerequisites"></a>前提条件

[ARR アカウントを作成](create-an-account.md)します (まだ作成していない場合)。

## <a name="token-service-rest-api"></a>トークン サービスの REST API

アクセス トークンを作成するために、*セキュリティ トークン サービス*には 1 つの REST API が用意されています。 ARR STS サービスの URL は https:\//sts.mixedreality.azure.com です。

### <a name="get-token-request"></a>'トークンの取得' 要求

| URI | Method |
|-----------|:-----------|
| /accounts/**accountId**/token | GET |

| ヘッダー | 値 |
|--------|:------|
| 承認 | "Bearer **accountId**:**accountKey**" |

*accountId* と *accountKey* をそれぞれのご自分のデータに置き換えてください。

### <a name="get-token-response"></a>'トークンの取得' 応答

| status code | JSON ペイロード | 説明 |
|-----------|:-----------|:-----------|
| 200 | AccessToken: 文字列 | Success |

| ヘッダー | 目的 |
|--------|:------|
| MS-CV | この値は、サービス内の呼び出しをトレースするために使用できます |

## <a name="getting-a-token-using-powershell"></a>PowerShell を使用したトークンの取得

次の PowerShell コードは、必要な REST 要求を STS に送信する方法を示しています。 その後、トークンが PowerShell プロンプトに出力されます。

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

このスクリプトでは、トークンが出力に表示されるだけです。この出力から、トークンをコピーして貼り付けることができます。 実際のプロジェクトでは、このプロセスを自動化する必要があります。

## <a name="next-steps"></a>次のステップ

* [PowerShell スクリプトの例](../samples/powershell-example-scripts.md)
* [Azure フロントエンド API](../how-tos/frontend-apis.md)
* [セッション管理 REST API](../how-tos/session-rest-api.md)
