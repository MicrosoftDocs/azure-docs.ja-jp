---
title: Azure Front Door でのメトリックとログの監視 | Microsoft Docs
description: この記事では、Azure Front Door でサポートされるさまざまなメトリックとアクセス ログについて説明します
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/18/2018
ms.author: duau
ms.openlocfilehash: 6f5051dd7dedcc49320557f17148bcdc9bf539ab
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399754"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Azure Front Door でのメトリックとログの監視

Azure Front Door を使用すると、次の方法でリソースを監視できます。

- **メトリック**。 現在、Azure Front Door にはパフォーマンス カウンターを表示するメトリックが 7 つあります。
- **ログ**。 アクティビティ ログや診断ログでは、リソースのパフォーマンス、アクセス、その他のデータを監視目的で保存または使用することができます。

### <a name="metrics"></a>メトリック

メトリックとは、ポータルでパフォーマンス カウンターを表示できるようにする特定の Azure リソース用の機能です。 利用可能な Front Door メトリックは次のとおりです。

| メトリック | メトリックの表示名 | ユニット | Dimensions | 説明 |
| --- | --- | --- | --- | --- |
| RequestCount | 要求数 | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door によって処理されるクライアント要求の数。  |
| RequestSize | 要求サイズ | バイト | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door にクライアントからの要求として送信されたバイト数。 |
| ResponseSize | 応答サイズ | バイト | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | クライアントに Front Door からの応答として送信されたバイト数。 |
| TotalLatency | 合計待機時間 | ミリ秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door によってクライアント要求が受信されてから、Front Door からの最後の応答バイトがクライアントで確認されるまでの算出時間。 |
| BackendRequestCount | バックエンド要求数 | Count | HttpStatus</br>HttpStatusGroup</br>バックエンド | バックエンドに Front Door から送信された要求の数。 |
| BackendRequestLatency | バックエンド要求の待機時間 | ミリ秒 | バックエンド | Front Door によってバックエンドに要求が送信されてから、Front Door でバックエンドから最後の応答バイトが受信されるまでの算出時間。 |
| BackendHealthPercentage | バックエンドの正常性の割合 | Percent | バックエンド</br>BackendPool | Front Door からバックエンドへの成功した正常性プローブの割合。 |
| WebApplicationFirewallRequestCount | Web アプリケーション ファイアウォール要求の数 | Count | PolicyName</br>RuleName</br>アクション | Front Door のアプリケーション レイヤー セキュリティによって処理されたクライアント要求の数。 |

## <a name="activity-logs"></a><a name="activity-log"></a>アクティビティ ログ

アクティビティ ログは、Front Door に対して行われた操作に関する情報を提供します。 また、これらは、Front Door で行われた書き込み操作 (PUT、POST、DELETE) について、いつだれが何を行ったのかを特定します。

>[!NOTE]
>アクティビティ ログには、読み取り (GET) 操作は含まれません。 また、Azure portal または元の Management API を使用して実行する操作も含まれません。

アクティビティ ログに Front Door でアクセスするか、Azure リソースのすべてのログに Azure Monitor でアクセスします。 アクティビティ ログを表示するには、次の手順に従います。

1. Front Door インスタンスを選択します。
2. **[アクティビティ ログ]** を選択します。

    ![アクティビティ ログ](./media/front-door-diagnostics/activity-log.png)

3. フィルター処理のスコープを選択し、 **[適用]** を選択します。

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>診断ログ
診断ログは、監査やトラブルシューティングにとって重要な操作とエラーに関する豊富な情報を提供します。 診断ログは、アクティビティ ログとは異なります。

アクティビティ ログは、Azure リソースに対して行われた操作に関する分析情報を提供します。 診断ログは、自分のリソースが実行した操作に関する分析情報を提供します。 詳細については、[Azure Monitor の診断ログ](../azure-monitor/platform/platform-logs-overview.md)に関するドキュメントを参照してください。

![診断ログ](./media/front-door-diagnostics/diagnostic-log.png)

Front Door の診断ログを構成するには、以下の手順を実行します。

1. Azure Front Door を選択します。

2. **[診断設定]** を選択します。

3. **[診断を有効にする]** を選択します。 診断ログをメトリックと共にストレージ アカウントにアーカイブし、それらをイベント ハブにストリーム配信したり、Azure Monitor ログに送信したりします。

現在、Front Door では診断ログ (1 時間ごとにバッチ処理) が提供されています。 診断ログでは、次のスキーマを使用した各エントリが個々の API 要求に提供されます。

| プロパティ  | 説明 |
| ------------- | ------------- |
| BackendHostname | 要求がバックエンドに転送されていた場合、このフィールドではバックエンドのホスト名が示されます。 要求がリージョンのキャッシュにリダイレクトまたは転送された場合は (ルーティング規則でキャッシュが有効になっている場合)、このフィールドは空白になります。 |
| CacheStatus | キャッシュのシナリオの場合、このフィールドでは POP でのキャッシュのヒットとミスが定義されます |
| ClientIp | 要求を行ったクライアントの IP アドレス。 要求に X-Forwarded-For ヘッダーがあった場合、同じものからクライアント IP が選択されます。 |
| ClientPort | 要求を行ったクライアントの IP アドレス。 |
| HttpMethod | 要求で使用される HTTP メソッド。 |
| HttpStatusCode | プロキシから返された HTTP 状態コード。 |
| HttpStatusDetails | 要求の結果の状態。 この文字列値の意味は、状態参照テーブルで確認できます。 |
| HttpVersion | 要求または接続の種類。 |
| POP | 要求が到着したエッジの短い名前。 |
| RequestBytes | 要求ヘッダーと要求本文を含む HTTP 要求メッセージのサイズ (バイト単位)。 |
| RequestUri | 受信した要求の URI。 |
| ResponseBytes | 応答としてバックエンド サーバーによって送信されたバイト数。  |
| RoutingRuleName | 要求が一致したルーティング規則の名前。 |
| RulesEngineMatchNames | 要求が一致した規則の名前。 |
| SecurityProtocol | 要求によって使用された TLS/SSL プロトコルのバージョン。暗号化がない場合は、null 値。 |
| SentToOriginShield | 最初の環境でキャッシュ ミスがあり、要求がリージョンのキャッシュに送信されたかどうかを表すブール値フィールド。 ルーティング規則がリダイレクトの場合、またはルーティング規則でキャッシュが有効になっていない場合は、このフィールドを無視します。 |
| TimeTaken | Front Door への要求の最初のバイトから、出される応答の最後のバイトまでの時間の長さ (秒単位)。 |
| TrackingReference | Front Door によって提供された要求を識別する一意の参照文字列。X-Azure-Ref ヘッダーとしてクライアントにも送信されます。 アクセス ログで特定の要求の詳細を検索するために必要です。 |
| UserAgent | クライアントで使用されたブラウザーの種類。 |

**注:** さまざまなルーティング構成やトラフィック動作に対し、backendHostname、cacheStatus、sentToOriginShield、POP などの一部のフィールドでは、応答の値が異なる場合があります。 次の表では、さまざまなシナリオでこれらのフィールドに設定される異なる値について説明します。

| シナリオ | ログ エントリの数 | POP | BackendHostname | SentToOriginShield | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| キャッシュが有効になっていないルーティング規則 | 1 | エッジの POP コード | 要求が転送されたバックエンド | False | CONFIG_NOCACHE |
| キャッシュが有効になっているルーティング規則。 エッジ POP でのキャッシュ ヒット | 1 | エッジの POP コード | Empty | False | HIT |
| キャッシュが有効になっているルーティング規則。 エッジ POP ではキャッシュ ミスだが、親キャッシュ POP でキャッシュ ヒット | 2 | 1.エッジの POP コード</br>2.親キャッシュの POP コード | 1.親キャッシュの POP ホスト名</br>2.Empty | 1.True</br>2.False | 1.MISS</br>2.PARTIAL_HIT |
| キャッシュが有効になっているルーティング規則。 エッジと親キャッシュの両方の POP でキャッシュ ミス | 2 | 1.エッジの POP コード</br>2.親キャッシュの POP コード | 1.親キャッシュの POP ホスト名</br>2.キャッシュの設定に役立つバックエンド | 1.True</br>2.False | 1.MISS</br>2.MISS |


## <a name="next-steps"></a>次のステップ

- [Front Door プロファイルの作成](quickstart-create-front-door.md)
- [Front Door のしくみ](front-door-routing-architecture.md)
