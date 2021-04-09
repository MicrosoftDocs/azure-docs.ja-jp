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
ms.date: 11/23/2020
ms.author: yuajia
ms.openlocfilehash: 58db217ca173acbe0356596de916216c4ab7f241
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101715549"
---
# <a name="monitoring-metrics-and-logs-in-azure-front-door"></a>Azure Front Door でのメトリックとログの監視

Azure Front Door を使用すると、次の方法でリソースを監視できます。

- **メトリック**。 現在、Azure Front Door にはパフォーマンス カウンターを表示するメトリックが 8 つあります。
- **ログ**。 アクティビティ ログや診断ログでは、リソースのパフォーマンス、アクセス、その他のデータを監視目的で保存または使用することができます。

##  <a name="metrics"></a><a name="metrics"></a>メトリック

メトリックとは、ポータルでパフォーマンス カウンターを表示できるようにする特定の Azure リソース用の機能です。 利用可能な Front Door メトリックは次のとおりです。

| メトリック | メトリックの表示名 | ユニット | Dimensions | 説明 |
| --- | --- | --- | --- | --- |
| RequestCount | 要求数 | Count | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door によって処理されるクライアント要求の数。  |
| RequestSize | 要求サイズ | バイト | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | Front Door にクライアントからの要求として送信されたバイト数。 |
| ResponseSize | 応答サイズ | バイト | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | クライアントに Front Door からの応答として送信されたバイト数。 |
| TotalLatency | 合計待機時間 | ミリ秒 | HttpStatus</br>HttpStatusGroup</br>ClientRegion</br>ClientCountry | クライアント要求が Front Door によって受信されてから、最後の応答バイトが AFD からクライアントに送信されるまでの合計時間。 |
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

    :::image type="content" source="./media/front-door-diagnostics/activity-log.png" alt-text="アクティビティ ログ":::

3. フィルター処理のスコープを選択し、 **[適用]** を選択します。

## <a name="diagnostic-logs"></a><a name="diagnostic-logging"></a>診断ログ
診断ログは、監査やトラブルシューティングにとって重要な操作とエラーに関する豊富な情報を提供します。 診断ログは、アクティビティ ログとは異なります。

アクティビティ ログは、Azure リソースに対して行われた操作に関する分析情報を提供します。 診断ログは、自分のリソースが実行した操作に関する分析情報を提供します。 詳細については、[Azure Monitor の診断ログ](../azure-monitor/essentials/platform-logs-overview.md)に関するドキュメントを参照してください。

:::image type="content" source="./media/front-door-diagnostics/diagnostic-log.png" alt-text="診断ログ":::

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
| SentToOriginShield </br> (非推奨) * **次のセクションの非推奨に関する注意事項を参照してください。**| true の場合、要求は、エッジ ポップではなく、オリジン シールド キャッシュから応答されました。 オリジン シールドは、キャッシュ ヒット率を向上させる目的で使用される親キャッシュです。 |
| isReceivedFromClient | true の場合、要求はクライアントから送信されます。 false の場合、要求はエッジ (子 POP) で不成功となり、オリジン シールド (親 POP) から応答されます。 |
| TimeTaken | Front Door への要求の最初のバイトから、出される応答の最後のバイトまでの時間の長さ (秒単位)。 |
| TrackingReference | Front Door によって提供された要求を識別する一意の参照文字列。X-Azure-Ref ヘッダーとしてクライアントにも送信されます。 アクセス ログで特定の要求の詳細を検索するために必要です。 |
| UserAgent | クライアントで使用されたブラウザーの種類。 |
| ErrorInfo | このフィールドには、詳細なトラブルシューティングのための特定の種類のエラーが含まれています。 </br> 次の値を指定できます。 </br> **NoError**:エラーが見つからなかったことを示します。 </br> **CertificateError**:一般的な SSL 証明書エラー。</br> **CertificateNameCheckFailed**:SSL 証明書のホスト名が無効であるか、一致しません。 </br> **ClientDisconnected**:クライアント ネットワーク接続による要求の失敗。 </br> **UnspecifiedClientError**:一般的なクライアント エラー。 </br> **InvalidRequest**:無効な要求。 ヘッダー、本文、URL の形式が間違っていることが原因で発生する可能性があります。 </br> **DNSFailure**:DNS エラー。 </br> **DNSNameNotResolved**:サーバーの名前またはアドレスを解決できませんでした。 </br> **OriginConnectionAborted**:配信元との接続が突然停止しました。 </br> **OriginConnectionError**:一般的な配信元接続エラー。 </br> **OriginConnectionRefused**:配信元との接続を確立できませんでした。 </br> **OriginError**:一般的な配信元エラー。 </br> **OriginInvalidResponse**:配信元から無効な応答または認識できない応答が返されました。 </br> **OriginTimeout**:配信元要求のタイムアウト期間が経過しました。 </br> **ResponseHeaderTooBig**:配信元から返された応答ヘッダーが大きすぎます。 </br> **RestrictedIP**:制限付き IP のため、要求はブロックされました。 </br> **SSLHandshakeError**:SSL ハンドシェイク エラーのため、配信元との接続を確立できません。 </br> **UnspecifiedError**:テーブルのいずれのエラーにも適合しなかったエラーが発生しました。 |

### <a name="sent-to-origin-shield-deprecation"></a>Sent to origin shield の非推奨化
生ログのプロパティ **isSentToOriginShield** は非推奨化され、新しいフィールド **isReceivedFromClient** に置き換えられました。 非推奨化されたフィールドを既に使用している場合は、新しいフィールドを使用してください。 

生ログには、CDN エッジ (子 POP) とオリジン シールドの両方から生成されたログが含まれます。 オリジン シールドとは、世界中に戦略的に配置された親ノードを指します。 これらのノードが配信元サーバーと通信を行うことで、配信元におけるトラフィック負荷が軽減されます。 

オリジン シールドに向かう各要求について、2 つのログ エントリが存在します。

* エッジ ノード由来
* オリジン シールド由来 

エグレスまたは応答がエッジ ノード由来かオリジン シールド由来かは、**isReceivedFromClient** フィールドを使用して正しいデータを取得することで区別できます。 

この値が false の場合、その要求に対する応答はオリジン シールドからエッジ ノードに返されたことを意味します。 このアプローチは、生ログを課金データと比較する際に有効です。 オリジン シールドからエッジ ノードへのエグレスに料金は発生しません。 料金は、エッジ ノードからクライアントへのエグレスで発生します。 

**Log Analytics のオリジン シールドで生成されたログを除外するための Kusto クエリ サンプル。**

`AzureDiagnostics 
| where Category == "FrontdoorAccessLog" and isReceivedFromClient_b == true`

> [!NOTE]
> さまざまなルーティング構成やトラフィック動作に対し、backendHostname、cacheStatus、isReceivedFromClient、POP などの一部のフィールドでは、応答の値が異なる場合があります。 次の表では、さまざまなシナリオでこれらのフィールドに設定される異なる値について説明します。

| シナリオ | ログ エントリの数 | POP | BackendHostname | isReceivedFromClient | CacheStatus |
| ------------- | ------------- | ------------- | ------------- | ------------- | ------------- |
| キャッシュが有効になっていないルーティング規則 | 1 | エッジの POP コード | 要求が転送されたバックエンド | True | CONFIG_NOCACHE |
| キャッシュが有効になっているルーティング規則。 エッジ POP でのキャッシュ ヒット | 1 | エッジの POP コード | Empty | True | HIT |
| キャッシュが有効になっているルーティング規則。 エッジ POP ではキャッシュ ミスだが、親キャッシュ POP でキャッシュ ヒット | 2 | 1.エッジの POP コード</br>2.親キャッシュの POP コード | 1.親キャッシュの POP ホスト名</br>2.Empty | 1.True</br>2.False | 1.MISS</br>2.HIT |
| キャッシュが有効になっているルーティング規則。 エッジ POP ではキャッシュ ミスだが、親キャッシュ POP で PARTIAL キャッシュ ヒット | 2 | 1.エッジの POP コード</br>2.親キャッシュの POP コード | 1.親キャッシュの POP ホスト名</br>2.キャッシュの設定に役立つバックエンド | 1.True</br>2.False | 1.MISS</br>2.PARTIAL_HIT |
| キャッシュが有効になっているルーティング規則。 エッジ POP ではキャッシュ PARTIAL_HIT だが、親キャッシュ POP でキャッシュ ヒット | 2 | 1.エッジの POP コード</br>2.親キャッシュの POP コード | 1.エッジの POP コード</br>2.親キャッシュの POP コード | 1.True</br>2.False | 1.PARTIAL_HIT</br>2.HIT |
| キャッシュが有効になっているルーティング規則。 エッジと親キャッシュの両方の POP でキャッシュ ミス | 2 | 1.エッジの POP コード</br>2.親キャッシュの POP コード | 1.エッジの POP コード</br>2.親キャッシュの POP コード | 1.True</br>2.False | 1.MISS</br>2.MISS |

> [!NOTE]
> キャッシュのシナリオでは、要求のバイトの一部が Front Door エッジまたはオリジン シールド キャッシュから処理され、バイトの一部がラージ オブジェクト用の配信元から提供される場合、Cache Status の値は partial_hit になります。

Front Door では、オブジェクト チャンクと呼ばれる方法を使用します。 大きなファイルが要求されると、Front Door は配信元からファイルを分割して取得します。 Front Door POP サーバーが、要求されたファイルの全体またはバイト範囲を受け取った後、Front Door エッジ サーバーは、8 MB のチャンク単位で配信元にファイルを要求します。

Front Door エッジに届いたチャンクはキャッシュされ、即座にユーザーに提供されます。 その後、Front Door は並列処理で次のチャンクをプリフェッチします。 このプリフェッチにより、コンテンツはチャンク 1 つ分だけ常にユーザーより先行することになるため、待ち時間が短縮されます。 この処理は、ファイル全体がダウンロードされるか (要求があった場合)、すべてのバイト範囲が利用可能になるか (要求があった場合)、クライアントが接続を終了するまで続けられます。 バイト範囲の要求の詳細については、RFC 7233 をご覧ください。 Front Door は受信したチャンクをすべてキャッシュします。 ファイル全体を Front Door のキャッシュに保存する必要はありません。 ファイルまたはバイト範囲に対する後続の要求に対しては、Front Door キャッシュの内容が提供されます。 すべてのチャンクが Front Door にキャッシュされていない場合、プリフェッチを使用して配信元にチャンクが要求されます。 この最適化は、配信元サーバーのバイト範囲要求をサポートする機能に依存します。 配信元サーバーがバイト範囲要求をサポートしていない場合、この最適化の効果はありません。

## <a name="next-steps"></a>次のステップ

- [Front Door プロファイルの作成](quickstart-create-front-door.md)
- [Front Door のしくみ](front-door-routing-architecture.md)
