---
title: Azure Front Door Service でのメトリックとログの監視 | Microsoft Docs
description: この記事では、Azure Front Door Service でサポートされるさまざまなメトリックとアクセス ログについて説明します
services: frontdoor
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: sharadag
ms.openlocfilehash: 5f76df0045fc3939392759ed0edd266380295a85
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2019
ms.locfileid: "71260170"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door-service"></a>Azure Front Door Service でのメトリックとログの監視

Azure Front Door Service を使用すると、次の方法でリソースを監視できます。

- **メトリック**。 現在、Application Gateway にはパフォーマンス カウンターを表示する 7 つのメトリックがあります。
- **ログ**。 アクティビティ ログや診断ログでは、リソースのパフォーマンス、アクセス、その他のデータを監視目的で保存または使用することができます。

### <a name="metrics"></a>メトリック

メトリックとは、ポータルでパフォーマンス カウンターを表示できるようにする特定の Azure リソース用の機能です。 利用可能な Front Door メトリックは次のとおりです。

| メトリック | メトリックの表示名 | 単位 | Dimensions | 説明 |
| --- | --- | --- | --- | --- |
| RequestCount | 要求数 | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door によって処理されるクライアント要求の数。  |
| RequestSize | 要求サイズ | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door にクライアントからの要求として送信されたバイト数。 |
| ResponseSize | 応答サイズ | Bytes | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | クライアントに Front Door からの応答として送信されたバイト数。 |
| TotalLatency | 合計待機時間 | ミリ秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door によってクライアント要求が受信されてから、Front Door からの最後の応答バイトがクライアントで確認されるまでの算出時間。 |
| BackendRequestCount | バックエンド要求数 | Count | HttpStatus</br>HttpStatusGroup</br>バックエンド | バックエンドに Front Door から送信された要求の数。 |
| BackendRequestLatency | バックエンド要求の待機時間 | ミリ秒 | バックエンド | Front Door によってバックエンドに要求が送信されてから、Front Door でバックエンドから最後の応答バイトが受信されるまでの算出時間。 |
| BackendHealthPercentage | バックエンドの正常性の割合 | Percent | バックエンド</br>BackendPool | Front Door からバックエンドへの成功した正常性プローブの割合。 |
| WebApplicationFirewallRequestCount | Web アプリケーション ファイアウォール要求の数 | Count | PolicyName</br>RuleName</br>Action | Front Door のアプリケーション レイヤー セキュリティによって処理されたクライアント要求の数。 |

## <a name="activity-log"></a>アクティビティ ログ

アクティビティ ログは、Front Door Service に対して行われた操作に関する情報を提供します。 また、これらは、Front Door Service で行われた書き込み操作 (PUT、POST、DELETE) について、いつだれが何を行ったのかを特定します。

>[!NOTE]
>アクティビティ ログには、読み取り (GET) 操作は含まれません。 また、Azure portal または元の Management API を使用して実行する操作も含まれません。

アクティビティ ログに Front Door Service でアクセスするか、Azure リソースのすべてのログに Azure Monitor でアクセスします。 アクティビティ ログを表示するには、次の手順に従います。

1. Front Door インスタンスを選択します。
2. **[アクティビティ ログ]** を選択します。

    ![アクティビティ ログ](./media/front-door-diagnostics/activity-log.png)

3. フィルター処理のスコープを選択し、 **[適用]** を選択します。

## <a name="diagnostic-logging"></a>診断ログ
診断ログは、監査やトラブルシューティングにとって重要な操作とエラーに関する豊富な情報を提供します。 診断ログは、アクティビティ ログとは異なります。

アクティビティ ログは、Azure リソースに対して行われた操作に関する分析情報を提供します。 診断ログは、自分のリソースが実行した操作に関する分析情報を提供します。 詳細については、[Azure Monitor の診断ログ](../azure-monitor/platform/resource-logs-overview.md)に関するドキュメントを参照してください。

![診断ログ](./media/front-door-diagnostics/diagnostic-log.png)

Front Door Service の診断ログを構成するには:

1. Azure Front Door Service を選択します。

2. **[診断設定]** を選択します。

3. **[診断を有効にする]** を選択します。 診断ログをメトリックと共にストレージ アカウントにアーカイブし、それらをイベント ハブにストリーム配信したり、Azure Monitor ログに送信したりします。

現在、Front Door Service は診断ログ (1 時間ごとにバッチ処理) を提供しています。 診断ログでは、次のスキーマを使用した各エントリが個々の API 要求に提供されます。

| プロパティ  | 説明 |
| ------------- | ------------- |
| ClientIp | 要求を行ったクライアントの IP アドレス。 |
| ClientPort | 要求を行ったクライアントの IP アドレス。 |
| HttpMethod | 要求で使用される HTTP メソッド。 |
| HttpStatusCode | プロキシから返された HTTP 状態コード。 |
| HttpStatusDetails | 要求の結果の状態。 この文字列値の意味は、状態参照テーブルで確認できます。 |
| HttpVersion | 要求または接続の種類。 |
| RequestBytes | 要求ヘッダーと要求本文を含む HTTP 要求メッセージのサイズ (バイト単位)。 |
| RequestUri | 受信した要求の URI。 |
| ResponseBytes | 応答としてバックエンド サーバーによって送信されたバイト数。  |
| RoutingRuleName | 要求が一致したルーティング規則の名前。 |
| SecurityProtocol | 要求によって使用された TLS/SSL プロトコルのバージョン。暗号化がない場合は、null 値。 |
| TimeTaken | アクションにかかった時間の長さ (ミリ秒単位)。 |
| UserAgent | クライアントで使用されたブラウザーの種類 |
| TrackingReference | Front Door によって提供された要求を識別する一意の参照文字列。X-Azure-Ref ヘッダーとしてクライアントにも送信されます。 アクセス ログで特定の要求の詳細を検索するために必要です。 |

## <a name="next-steps"></a>次の手順

- [Front Door プロファイルの作成](quickstart-create-front-door.md)
- [Front Door のしくみ](front-door-routing-architecture.md)
