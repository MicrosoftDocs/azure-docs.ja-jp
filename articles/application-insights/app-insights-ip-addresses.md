---
title: "Application Insights によって使用される IP アドレス | Microsoft Docs"
description: "Application Insights で必要なサーバー ファイアウォール例外"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 44d989f8-bae9-40ff-bfd5-8343d3e59358
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/01/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 032d32be1719d4b459c88b13616bbef8d2d7be09
ms.openlocfilehash: d5d6065aa2958ebb858d421ea7c3eb2a198b2a80
ms.lasthandoff: 01/05/2017


---
# <a name="ip-addresses-used-by-application-insights"></a>Application Insights によって使用される IP アドレス
[Azure Application Insights](app-insights-overview.md) サービスは、多くの IP アドレスを使用します。 監視しているアプリがファイアウォールの背後でホストされている場合は、これらのアドレスを確認する必要があります。

> [!NOTE]
> これらは静的アドレスですが、しばしば変更の必要が生じることがあります。
> 
> 

## <a name="outgoing-ports"></a>送信ポート
Application Insights SDK や Status Monitor がポータルにデータを送信できるように、サーバーのファイアウォールでいくつかの送信ポートを開く必要があります。

| 目的 | URL | IP | ポート |
| --- | --- | --- | --- |
| テレメトリ |dc.services.visualstudio.com<br/>dc.applicationinsights.microsoft.com |40.114.241.141<br/>104.45.136.42<br/>40.84.189.107<br/>168.63.242.221 |443 |
| ライブ メトリック ストリーム |rt.services.visualstudio.com<br/>rt.applicationinsights.microsoft.com |23.96.28.38<br/>13.92.40.198 |443 |

## <a name="status-monitor"></a>Status Monitor
Status Monitor の構成 - 変更を加える場合にのみ必要です。

| 目的 | URL | IP | ポート |
| --- | --- | --- | --- |
| 構成 |`management.core.windows.net` | |`443` |
| 構成 |`management.azure.com` | |`443` |
| 構成 |`login.windows.net` | |`443` |
| 構成 |`login.microsoftonline.com` | |`443` |
| 構成 |`secure.aadcdn.microsoftonline-p.com` | |`443` |
| 構成 |`auth.gfx.ms` | |`443` |
| 構成 |`login.live.com` | |`443` |
| インストール |`packages.nuget.org` | |`443` |

## <a name="hockeyapp"></a>HockeyApp
| 目的 | URL | IP | ポート |
| --- | --- | --- | --- |
| クラッシュ データ |gate.hockeyapp.net |104.45.136.42 |80、443 |

## <a name="availability-tests"></a>可用性テスト
これは [可用性 Web テスト](app-insights-monitor-web-app-availability.md) の実行元のアドレスの一覧です。 アプリで Web テストを実行しようとするが、Web サーバーが特定のクライアントの処理に制限されている場合は、可用性テスト サーバーからの着信トラフィックを許可する必要があります。

これらのアドレスからの着信トラフィック用にポート 80 (http) と 443 (https) を開きます。

```
13.106.106.20
13.106.106.21
13.106.106.22
13.106.106.23
13.106.106.24
13.106.106.25
13.106.106.26
13.106.106.27
13.106.106.28
13.106.106.29
157.55.14.43
157.55.14.44
157.55.14.47
157.55.14.49
157.55.14.50
157.55.14.60
157.55.14.61
157.55.14.62
157.55.14.64
157.55.14.65
202.89.228.57
202.89.228.67
202.89.228.68
202.89.228.69
207.46.14.51
207.46.14.52
207.46.14.55
207.46.14.56
207.46.14.60
207.46.14.61
207.46.14.62
207.46.14.63
207.46.14.64
207.46.14.65
207.46.14.67
207.46.14.68
207.46.56.57
207.46.56.58
207.46.56.59
207.46.56.61
207.46.56.62
207.46.56.63
207.46.56.64
207.46.56.67
207.46.71.37
207.46.71.38
207.46.71.51
207.46.71.52
207.46.71.54
207.46.71.55
207.46.71.57
207.46.71.58
207.46.98.152
207.46.98.153
207.46.98.156
207.46.98.157
207.46.98.158
207.46.98.159
207.46.98.160
207.46.98.162
207.46.98.169
207.46.98.170
207.46.98.171
207.46.98.172
213.199.178.54
213.199.178.55
213.199.178.56
213.199.178.57
213.199.178.58
213.199.178.59
213.199.178.60
213.199.178.61
213.199.178.63
213.199.178.64
65.54.66.56
65.54.66.57
65.54.66.58
65.54.66.61
65.54.78.49
65.54.78.50
65.54.78.51
65.54.78.54
65.54.78.57
65.54.78.58
65.54.78.59
65.54.78.60
65.55.82.77
65.55.82.78
65.55.82.81
65.55.82.84
65.55.82.85
65.55.82.86
65.55.82.87
65.55.82.88
65.55.82.89
65.55.82.90
65.55.82.91
65.55.82.92
70.37.147.43
70.37.147.44
70.37.147.45
70.37.147.48
94.245.66.43
94.245.66.44
94.245.66.45
94.245.66.48
94.245.72.44
94.245.72.45
94.245.72.46
94.245.72.49
94.245.72.52
94.245.72.53
94.245.78.40
94.245.78.41
94.245.78.42
94.245.78.45
94.245.82.32
94.245.82.33
94.245.82.37
94.245.82.38


```  

## <a name="data-access-api"></a>データ アクセス API
| 目的 | URI | IP | ポート |
| --- | --- | --- | --- |
| API |api.applicationinsights.io<br/>api1.applicationinsights.io<br/>api2.applicationinsights.io<br/>api3.applicationinsights.io<br/>api4.applicationinsights.io<br/>api5.applicationinsights.io |13.82.26.252<br/>40.76.213.73 |80,443 |
| API ドキュメント |dev.applicationinsights.io<br/>dev.applicationinsights.microsoft.com<br/>dev.aisvc.visualstudio.com<br/>www.applicationinsights.io<br/>www.applicationinsights.microsoft.com<br/>www.aisvc.visualstudio.com |13.82.24.149<br/>40.114.82.10 |80,443 |


