---
title: セッション管理 REST API
description: セッションを管理する方法について説明します
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: article
ms.openlocfilehash: 4e65655f1809c6badc50e39a2a5e932516ef99d2
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/18/2020
ms.locfileid: "88509843"
---
# <a name="use-the-session-management-rest-api"></a>セッション管理 REST API を使用する

Azure Remote Rendering 機能を使用するには、"*セッション*" を作成する必要があります。 各セッションは、Azure に割り当てられてクライアント デバイスが接続されるのを待機している仮想マシン (VM) に対応しています。 デバイスが接続されると、VM では要求されたデータがレンダリングされ、結果がビデオ ストリームとして提供されます。 セッションの作成時に、実行するサーバーの種類を選択します。これにより、価格が決まります。 セッションが不要になったら、停止する必要があります。 手動で停止しない場合、セッションの "*リース時間*" が期限切れになると、自動的にシャットダウンされます。

*Scripts* フォルダー内の [ARR サンプル リポジトリ](https://github.com/Azure/azure-remote-rendering)に、サービスの使用方法を示す *RenderingSession.ps1* という名前の PowerShell スクリプトが提供されています。 スクリプトとその構成については、次を参照してください: [PowerShell スクリプトの例](../samples/powershell-example-scripts.md)

> [!TIP]
> このページに記載されている PowerShell コマンドは、相互に補完することを目的としています。 すべてのスクリプトを同じ PowerShell コマンド プロンプト内で順番に実行すると、これらのスクリプトは互いの上に構築されます。

## <a name="regions"></a>リージョン

ベース URL からの要求の送信先として[使用できるリージョンの一覧](../reference/regions.md)を参照してください。

次のサンプル スクリプトでは、リージョン *westus2* を選択しています。

### <a name="example-script-choose-an-endpoint"></a>スクリプトの例:エンドポイントを選択する

```PowerShell
$endPoint = "https://remoterendering.westus2.mixedreality.azure.com"
```

## <a name="accounts"></a>アカウント

Remote Rendering アカウントを持っていない場合は、[1 つ作成します](create-an-account.md)。 各リソースは、セッション API 全体で使用される *accountId* によって識別されます。

### <a name="example-script-set-accountid-and-accountkey"></a>スクリプトの例:accountId と accountKey を設定する

```PowerShell
$accountId = "********-****-****-****-************"
$accountKey = "*******************************************="
```

## <a name="common-request-headers"></a>共通の要求ヘッダー

* *Authorization* ヘッダーには、"`Bearer TOKEN`" の値を指定する必要があります。ここで "`TOKEN`" は、[セキュリティ トークン サービスによって返される](tokens.md)認証トークンです。

### <a name="example-script-request-a-token"></a>スクリプトの例:トークンの要求

```PowerShell
[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -ContentType "application/json" -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content
$token = $response.AccessToken;
```

## <a name="common-response-headers"></a>共通の応答ヘッダー

* 製品チームは、*MS-CV* ヘッダーを使用してサービス内の呼び出しをトレースできます。

## <a name="create-a-session"></a>セッションを作成する

このコマンドでは、セッションが作成されます。 新しい要求の ID が返されます。 他のすべてのコマンドには、セッション ID が必要です。

| URI | Method |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/create | POST |

**要求本文:**

* maxLeaseTime (期間): セッションが自動的に使用停止されるときのタイムアウト値。
* models (配列): 事前に読み込まれるアセット コンテナーの URL。
* size (文字列): 構成するサーバー サイズ ([ **"standard"** ](../reference/vm-sizes.md) または [ **"premium"** ](../reference/vm-sizes.md))。 具体的な[サイズの制限](../reference/limits.md#overall-number-of-polygons)を参照してください。

**応答:**

| status code | JSON ペイロード | 説明 |
|-----------|:-----------|:-----------|
| 202 | - sessionId:GUID | Success |

### <a name="example-script-create-a-session"></a>スクリプトの例:セッションを作成する

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/create" -Method Post -ContentType "application/json" -Body "{ 'maxLeaseTime': '4:0:0', 'models': [], 'size': 'standard' }" -Headers @{ Authorization = "Bearer $token" }
```

出力例:

```PowerShell
StatusCode        : 202
StatusDescription : Accepted
Content           : {"sessionId":"d31bddca-dab7-498e-9bc9-7594bc12862f"}
RawContent        : HTTP/1.1 202 Accepted
                    MS-CV: 5EqPJ1VdTUakDJZc6/ZhTg.0
                    Content-Length: 52
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:17:50 GMT
                    Location: accounts/11111111-1111-1111-11...
Forms             : {}
Headers           : {[MS-CV, 5EqPJ1VdTUakDJZc6/ZhTg.0], [Content-Length, 52], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:17:50 GMT]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 52
```

### <a name="example-script-store-sessionid"></a>スクリプトの例:sessionId を格納する

上記の要求からの応答には、すべてのフォローアップ要求に必要な **sessionId** が含まれます。

```PowerShell
$sessionId = "d31bddca-dab7-498e-9bc9-7594bc12862f"
```

## <a name="update-a-session"></a>セッションを更新する

このコマンドでは、セッションのパラメーターが更新されます。 現時点では、セッションのリース時間のみを延長できます。

> [!IMPORTANT]
> リース時間は、セッションが開始されてからの合計時間として常に指定されます。 つまり、リース時間が 1 時間のセッションを作成し、そのリース時間をさらに 1 時間延長する場合は、maxLeaseTime を 2 時間に更新する必要があります。

| URI | Method |
|-----------|:-----------|
| /v1/accounts/*accountID*/sessions/*sessionId* | PATCH |

**要求本文:**

* maxLeaseTime (期間): セッションが自動的に使用停止されるときのタイムアウト値。

**応答:**

| status code | JSON ペイロード | 説明 |
|-----------|:-----------|:-----------|
| 200 | | Success |

### <a name="example-script-update-a-session"></a>スクリプトの例:セッションを更新する

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Patch -ContentType "application/json" -Body "{ 'maxLeaseTime': '5:0:0' }" -Headers @{ Authorization = "Bearer $token" }
```

出力例:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: Fe+yXCJumky82wuoedzDTA.0
                    Content-Length: 0
                    Date: Thu, 09 May 2019 16:27:31 GMT


Headers           : {[MS-CV, Fe+yXCJumky82wuoedzDTA.0], [Content-Length, 0], [Date, Thu, 09 May 2019 16:27:31 GMT]}
RawContentLength  : 0
```

## <a name="get-active-sessions"></a>アクティブなセッションを取得する

このコマンドでは、アクティブなセッションの一覧が返されます。

| URI | Method |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions | GET |

**応答:**

| status code | JSON ペイロード | 説明 |
|-----------|:-----------|:-----------|
| 200 | -sessions: セッション プロパティの配列 | セッション プロパティの説明については、「セッション プロパティを取得する」セクションを参照してください。 |

### <a name="example-script-query-active-sessions"></a>スクリプトの例:アクティブなセッションをクエリする

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

出力例:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : []
RawContent        : HTTP/1.1 200 OK
                    MS-CV: WfB9Cs5YeE6S28qYkp7Bhw.1
                    Content-Length: 15
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 25 Jul 2019 16:23:50 GMT

                    {"sessions":[]}
Forms             : {}
Headers           : {[MS-CV, WfB9Cs5YeE6S28qYkp7Bhw.1], [Content-Length, 2], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 25 Jul 2019 16:23:50 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 2
```

## <a name="get-sessions-properties"></a>セッション プロパティを取得する

このコマンドでは、VM のホスト名など、セッションに関する情報が返されます。

| URI | Method |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/*sessionId*/properties | GET |

**応答:**

| status code | JSON ペイロード | 説明 |
|-----------|:-----------|:-----------|
| 200 | - message: 文字列<br/>- sessionElapsedTime: 期間<br/>- sessionHostname: 文字列<br/>- sessionId: 文字列<br/>- sessionMaxLeaseTime: 期間<br/>- sessionSize: 列挙型<br/>- sessionStatus: 列挙型 | 列挙型 sessionStatus { starting、ready、stopping、stopped、expired、error}<br/>状態が 'error' または 'expired' である場合、メッセージには詳細情報が含まれます |

### <a name="example-script-get-session-properties"></a>スクリプトの例:セッションのプロパティを取得します

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId/properties" -Method Get -Headers @{ Authorization = "Bearer $token" }
```

出力例:

```PowerShell
StatusCode        : 200
StatusDescription : OK
Content           : {"message":null,"sessionElapsedTime":"00:00:01","sessionHostname":"5018fee8-817e-4366-9179-556af79a4240.remoterenderingvm.westeurope.mixedreality.azure.com","sessionId":"e13d2c44-63e0-4591-991e-f9e05e599a93","sessionMaxLeaseTime":"04:00:00","sessionStatus":"Ready"}
RawContent        : HTTP/1.1 200 OK
                    MS-CV: CMXegpZRMECH4pbOW2j5GA.0
                    Content-Length: 60
                    Content-Type: application/json; charset=utf-8
                    Date: Thu, 09 May 2019 16:30:38 GMT

                    {"message":null,...
Forms             : {}
Headers           : {[MS-CV, CMXegpZRMECH4pbOW2j5GA.0], [Content-Length, 60], [Content-Type, application/json;
                    charset=utf-8], [Date, Thu, 09 May 2019 16:30:38 GMT]}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 60
```

## <a name="stop-a-session"></a>セッションを停止する

このコマンドでは、セッションが停止されます。 割り当てられた VM は、すぐ後に再利用されます。

| URI | Method |
|-----------|:-----------|
| /v1/accounts/*accountId*/sessions/*sessionId* | DELETE |

**応答:**

| status code | JSON ペイロード | 説明 |
|-----------|:-----------|:-----------|
| 204 | | Success |

### <a name="example-script-stop-a-session"></a>スクリプトの例:セッションを停止する

```PowerShell
Invoke-WebRequest -Uri "$endPoint/v1/accounts/$accountId/sessions/$sessionId" -Method Delete -Headers @{ Authorization = "Bearer $token" }
```

出力例:

```PowerShell
StatusCode        : 204
StatusDescription : No Content
Content           : {}
RawContent        : HTTP/1.1 204 No Content
                    MS-CV: YDxR5/7+K0KstH54WG443w.0
                    Date: Thu, 09 May 2019 16:45:41 GMT


Headers           : {[MS-CV, YDxR5/7+K0KstH54WG443w.0], [Date, Thu, 09 May 2019 16:45:41 GMT]}
RawContentLength  : 0
```

## <a name="next-steps"></a>次のステップ

* [PowerShell スクリプトの例](../samples/powershell-example-scripts.md)
