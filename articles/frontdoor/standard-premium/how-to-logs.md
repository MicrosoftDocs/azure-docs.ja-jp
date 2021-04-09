---
title: Azure Front Door Standard/Premium (プレビュー) のログ
description: この記事では、Azure Front Door Standard/Premium でのログのしくみについて説明します。
services: front-door
author: duongau
ms.service: frontdoor
ms.topic: article
ms.date: 03/15/2021
ms.author: duau
ms.openlocfilehash: 531f4a9c9f535779e451ca316a8a5867f6cdaba5
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573899"
---
# <a name="azure-front-door-standardpremium-preview-logging"></a>Azure Front Door Standard/Premium (プレビュー) のログ

> [!Note]
> このドキュメントは、Azure Front Door Standard/Premium (プレビュー) を対象としています。 Azure Front Door については、 [こちら](../front-door-overview.md)を参照してください。

Azure Front Door には、Front Door の追跡、監視、デバッグに役立つさまざまなログが用意されています。 

* アクセス ログには、AFD が受信するすべての要求に関する詳細情報が含まれており、アクセス パターンの分析と監視、および問題のデバッグに役立ちます。 
* アクティビティ ログは、Azure リソースに対して行われた操作を可視化します。  
* 正常性プローブ ログは、配信元に対する失敗したすべてのプローブのログを提供します。 
* Web Application Firewall (WAF) ログは、Azure Front Door エンドポイントの検出または防止のいずれかのモードでログに記録された要求の詳細情報を提供します。 WAF を使用して構成されたカスタム ドメインも、これらのログで表示できます。

> [!IMPORTANT]
> Azure Front Door Standard/Premium (プレビュー) は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。
> 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

アクセス ログ、正常性プローブ ログ、WAF ログは、既定では有効になっていません。 下の手順を使用してログを有効にしてください。 アクティビティ ログ エントリは既定で収集され、Azure Portal で表示できます。 ログでは、最大で数分間の遅延が発生する可能性があります。 

ログを保存するための 3 つのオプションがあります。 

* **ストレージ アカウント**: ストレージ アカウントは、ログを長期間保存し、必要に応じて参照するシナリオに最適です。 
* **イベント ハブ:** イベント ハブは、他のセキュリティ情報イベント管理 (SIEM) ツールや外部データ ストアと統合する場合に便利なオプションです。 例: Splunk/DataDog/Sumo。 
* **Azure Log Analytics:** Azure Monitor の Azure Log Analytics は、Azure Front Door のパフォーマンスの一般的なリアルタイムでの監視と分析に最適です。

## <a name="configure-logs"></a>ログを構成する

1. [Azure Portal](https://portal.azure.com) にサインインします。

1. Azure Front Door Standard/Premium を検索し、Azure Front Door のプロファイルを選択します。

1. そのプロファイルで、 **[監視]** に移動し、 **[診断設定]** を選択します。 **[診断設定の追加]** を選択します。

   :::image type="content" source="../media/how-to-logging/front-door-logging-1.png" alt-text="[診断設定] ランディング ページのスクリーンショット。":::

1. **[診断設定]** の  **[診断設定の名前]** に名前を入力します。

1. **FrontDoorAccessLog**、**FrontDoorHealthProbeLog**、**FrontDoorWebApplicationFirewallLog** の **ログ** を選択します。

1.  **[宛先の詳細]** を選択します。 出力先の選択肢: 

    * **Log Analytics への送信**
        * *サブスクリプション* と *Log Analytics ワークスペース* を選択します。
    * **ストレージ アカウントへのアーカイブ**
        * *サブスクリプション* と *ストレージ アカウント* を選択します。 また、**保有期間 (日)** を設定します。
    * **イベント ハブへのストリーム**
        *  *[サブスクリプション]、[イベント ハブの名前空間]、[イベント ハブ名 (オプション)]* 、 *[イベント ハブ ポリシー名]* を選択します。 

     :::image type="content" source="../media/how-to-logging/front-door-logging-2.png" alt-text="[診断設定] ページのスクリーンショット。":::

1. **[Save]** をクリックします。

## <a name="access-log"></a>アクセス ログ

現在、Azure Front Door には、各エントリが次のスキーマを持ち、下に示すような JSON 形式でログに記録される個別の API 要求が用意されています。 

| プロパティ | 説明 |
|----------|-------------| 
| TrackingReference | AFD によってサービスが提供された要求を識別する一意の参照文字列。X-Azure-Ref ヘッダーとしてクライアントにも送信されます。 アクセス ログで特定の要求の詳細を検索するために必要です。 |
| Time | 要求されたコンテンツを AFD エッジがクライアントに配信した日付と時刻 (UTC)。 |
| HttpMethod | 要求で使用される HTTP メソッド: DELETE、GET、HEAD、OPTIONS、PATCH、POST、または PUT。 |
| HttpVersion | ビューアーによって要求で指定された HTTP バージョン。 |
| RequestUri | 受信した要求の URI。 このフィールドは、完全なスキーム、ポート、ドメイン、パス、クエリ文字列です。 |
| HostName | クライアントからの要求に含まれるホスト名。 カスタム ドメインを有効にし、ワイルドカード ドメイン (*. contoso.com) を使用している場合、ホスト名は a.contoso.com です。 Azure Front Door ドメイン (contoso.azurefd.net) を使用している場合、ホスト名は contoso.azurefd.net です。 |
| RequestBytes | 要求ヘッダーと要求本文を含む HTTP 要求メッセージのサイズ (バイト単位)。 ビューアーによって要求に含められたデータのバイト数 (ヘッダーを含む)。 |
| ResponseBytes | 応答としてバックエンド サーバーによって送信されたバイト数。 |
| UserAgent | クライアントで使用されたブラウザーの種類。 |
| ClientIp | 元の要求を行ったクライアントの IP アドレス。 要求に X-Forwarded-For ヘッダーがあった場合、同じものからクライアント IP が選択されます。 |
| SocketIp | AFD エッジへの直接接続の IP アドレス。 クライアントが HTTP プロキシまたはロード バランサーを使用して要求を送信した場合、SocketIp の値はプロキシまたはロード バランサーの IP アドレスです。 |
| Latency | AFD エッジ サーバーがクライアントの要求を受信した時点から、AFD が応答の最後のバイトをクライアントに送信した時点までの時間の長さ (ミリ秒単位)。 このフィールドでは、ネットワーク待ち時間と TCP バッファリングは考慮されません。 |
| RequestProtocol | クライアントによって要求で指定されたプロトコル: HTTP、HTTPS。 |
| SecurityProtocol | 要求によって使用された TLS/SSL プロトコルのバージョン。暗号化がない場合は、null 値。 有効な値には、SSLv3、TLSv1、TLSv1.1、TLSv1.2 があります。 |
| SecurityCipher | 要求のプロトコルの値が HTTPS の場合、このフィールドは、暗号化のためにクライアントと AFD によってネゴシエートされた TLS/SSL 暗号を示します。 |
| エンドポイント | AFD エンドポイントのドメイン名。たとえば、contoso.z01.azurefd.net |
| HttpStatusCode | AFD から返された HTTP 状態コード。 |
| Pop | ユーザー要求に応答したエッジ ポップ。  |
| Cache Status | キャッシュに関して、CDN サービスによって要求がどのように処理されるかを示す状態コードを提供します。 有効な値は次のとおりです。HIT: HTTP 要求は AFD エッジ POP キャッシュからサービスが提供されました。 <br> **MISS**: HTTP 要求は配信元からサービスが提供されました。 <br/> **PARTIAL_HIT**: オブジェクト チャンクのシナリオで、要求の一部のバイトが AFD エッジ POP キャッシュからサービスが提供され、一部のバイトが配信元からサービスが提供されました。 <br> **CACHE_NOCONFIG**: バイパスのシナリオを含め、キャッシュ設定なしで要求を転送します。 <br/> **PRIVATE_NOSTORE**: キャッシュ設定で顧客によってキャッシュが構成されていません。 <br> **REMOTE_HIT**: 要求が親ノード キャッシュによってサービスが提供されました。 <br/> **N/A**:* * 署名された URL とルール セットによって拒否された要求。 |
| MatchedRulesSetName | 処理されたルールの名前。 |
| RouteName | 要求が一致したルートの名前。 |
| ClientPort | 要求を行ったクライアントの IP アドレス。 |
| Referrer | 要求を開始したサイトの URL。 |
| TimetoFirstByte | Azure Front Door で測定された、AFD が要求を受信してから最初のバイトがクライアントに送信されるまでの時間の長さ (ミリ秒単位)。 このプロパティでは、クライアント データは測定されません。 |
| ErrorInfo | このフィールドは、各応答のエラー トークンの詳細情報を提供します。 <br> **NoError**:エラーが見つからなかったことを示します。 <br> **CertificateError**:一般的な SSL 証明書エラー。 <br> **CertificateNameCheckFailed**:SSL 証明書のホスト名が無効であるか、一致しません。 <br> **ClientDisconnected**:クライアント ネットワーク接続による要求の失敗。 <br> **ClientGeoBlocked**: IP の地理的な場所が原因で、クライアントがブロックされました。 <br> **UnspecifiedClientError**:一般的なクライアント エラー。 <br> **InvalidRequest**:無効な要求。 ヘッダー、本文、URL の形式が間違っていることが原因で発生する可能性があります。 <br> **DNSFailure**:DNS エラー。 <br> **DNSTimeout**: バックエンドを解決するための DNS クエリがタイムアウトになりました。 <br> **DNSNameNotResolved**:サーバーの名前またはアドレスを解決できませんでした。 <br> **OriginConnectionAborted**: 配信元との接続が異常に切断されました。 <br> **OriginConnectionError**:一般的な配信元接続エラー。 <br> **OriginConnectionRefused**:配信元との接続が確立されませんでした。 <br> **OriginError**:一般的な配信元エラー。 <br> **OriginInvalidRequest**: 無効な要求が配信元に送信されました。 <br> **ResponseHeaderTooBig**: 配信元から返された応答ヘッダーが大きすぎます。 <br> **OriginInvalidResponse**: 配信元から無効な応答または認識できない応答が返されました。 <br> **OriginTimeout**:配信元要求のタイムアウト期間が経過しました。 <br> **ResponseHeaderTooBig**: 配信元から返された応答ヘッダーが大きすぎます。 <br> **RestrictedIP**:制限付き IP のため、要求はブロックされました。 <br> **SSLHandshakeError**:SSL ハンドシェイク エラーのため、配信元との接続を確立できません。 <br> **SSLInvalidRootCA**: RootCA が無効でした。 <br> **SSLInvalidCipher**: HTTPS 接続が確立されたときの暗号が無効でした。 <br> **OriginConnectionAborted**: 配信元との接続が異常に切断されました。 <br> **OriginConnectionRefused**:配信元との接続が確立されませんでした。 <br> **UnspecifiedError**:テーブルのいずれのエラーにも適合しなかったエラーが発生しました。 |
| OriginURL | 要求が送信されている配信元の完全な URL。 スキーム、ホスト ヘッダー、ポート、パス、クエリ文字列で構成されています。 <br> **URL 書き換え**: ルール セットに URL 書き換えルールがある場合、パスは書き換えられたパスを指します。 <br> **エッジ POP のキャッシュ** エッジ POP でキャッシュ ヒットが発生した場合、配信元は N/A です。 <br> **大きい要求** 要求されたコンテンツが大きく、複数のチャンク要求が配信元に戻される場合、このフィールドは配信元への最初の要求に対応します。 詳細については、オブジェクト チャンクに関するページを参照してください。 |
| OriginIP | 要求にサービスを提供した配信元の IP。 <br> **エッジ POP のキャッシュ ヒット** エッジ POP でキャッシュ ヒットが発生した場合、配信元は N/A です。 <br> **大きい要求** 要求されたコンテンツが大きく、複数のチャンク要求が配信元に戻される場合、このフィールドは配信元への最初の要求に対応します。 詳細については、オブジェクト チャンクに関するページを参照してください。 |
| OriginName| 配信元に対する完全な DNS 名 (配信元 URL 内のホスト名)。 <br> **エッジ POP のキャッシュ ヒット** エッジ POP でキャッシュ ヒットが発生した場合、配信元は N/A です。 <br> **大きい要求** 要求されたコンテンツが大きく、複数のチャンク要求が配信元に戻される場合、このフィールドは配信元への最初の要求に対応します。 詳細については、オブジェクト チャンクに関するページを参照してください。 |

## <a name="health-probe-log"></a>正常性プローブ ログ

正常性プローブ ログは、配信元の診断に役立つように、失敗したすべてのプローブに関するログを提供します。 このログには、配信元をサービス可能な状態に戻すために利用できる情報が記載されています。 このログが役立つ可能性のあるシナリオを、いくつか次に示します。

* Azure Front Door トラフィックが配信元の一部に送信されていることに気付きました。 たとえば、4 つの配信元のうち 3 つだけがトラフィックを受信しています。 配信元がプローブを受信しているかどうか、および受信していない場合は、その失敗の理由を知る必要があります。  

* 配信元の正常性の % が想定より低いことに気付き、失敗した配信元と失敗の理由を知る必要があります。

### <a name="health-probe-log-properties"></a>正常性プローブ ログのプロパティ

各正常性プローブ ログには、次のスキーマがあります。

| プロパティ | 説明 |
| --- | --- |
| HealthProbeId  | 要求を識別する一意の ID。 |
| Time | プローブの完了時刻 |
| HttpMethod | 正常性プローブ要求によって使用される HTTP メソッド。 値には、正常性プローブの構成に基づき GET と HEAD があります。 |
| 結果 | 配信元に対する正常性プローブの状態。値には success と、その他のエラー テキストがあります。 |
| HttpStatusCode  | 配信元から返された HTTP 状態コード。 |
| ProbeURL (target) | 要求が送信されている配信元の完全な URL。 スキーム、ホスト ヘッダー、パス、クエリ文字列で構成されています。 |
| OriginName  | 要求が送信されている配信元。 配信元が FDQN に構成されている場合、このフィールドは目的の配信元を見つけるのに役立ちます。 |
| POP | プローブ要求を送信したエッジ POP。 |
| 送信元の IP | ターゲットの配信元の IP。 FDQN を使用して配信元を構成している場合、このフィールドは目的の配信元を見つけるのに役立ちます。 |
| TotolaLatency | AFDX エッジが要求を配信元に送信した時点から、配信元が最後の応答を AFDX エッジに送信した時点までの時間。 |
| ConnectionLatency| HTTP プローブ要求を配信元に送信するための TCP 接続の設定にかかった時間。 | 
| DNSResolution Latency | 配信元が IP ではなく FDQN として構成されている場合に DNS の解決にかかった時間。 配信元が IP に構成されている場合は、N/A です。 |

### <a name="health-probe-log-sample-in-json"></a>JSON での正常性プローブ ログのサンプル

`{ "records": [ { "time": "2021-02-02T07:15:37.3640748Z",
      "resourceId": "/SUBSCRIPTIONS/27CAFCA8-B9A4-4264-B399-45D0C9CCA1AB/RESOURCEGROUPS/AFDXPRIVATEPREVIEW/PROVIDERS/MICROSOFT.CDN/PROFILES/AFDXPRIVATEPREVIEW-JESSIE",
      "category": "FrontDoorHealthProbeLog",
      "operationName": "Microsoft.Cdn/Profiles/FrontDoorHealthProbeLog/Write",
      "properties": { "healthProbeId": "9642AEA07BA64675A0A7AD214ACF746E",
        "POP": "MAA",
        "httpVerb": "HEAD",
        "result": "OriginError",
        "httpStatusCode": "400",
        "probeURL": "http://afdxprivatepreview.blob.core.windows.net:80/",
        "originName": "afdxprivatepreview.blob.core.windows.net",
        "originIP": "52.239.224.228:80",
        "totalLatencyMilliseconds": "141",
        "connectionLatencyMilliseconds": "68",
        "DNSLatencyMicroseconds": "1814" } } ]
} `

## <a name="activity-logs"></a>アクティビティ ログ

アクティビティ ログは、Azure Front Door Standard/Premium に対して行われた操作に関する情報を提供します。 ログには、Azure Front Door で、どのような書き込み操作がだれによっていつ行われたかについての詳細が含まれています。 

> [!NOTE]
> アクティビティ ログには、GET 操作は含まれません。 また、Azure portal または元の Management API を使用して実行する操作も含まれません。 

アクティビティ ログに Front Door でアクセスするか、Azure リソースのすべてのログに Azure Monitor でアクセスします。

アクティビティ ログを表示するには、次の手順に従います。

1. Front Door のプロファイルを選択します。

1. **[アクティビティ ログ]** を選択します。

1. フィルター処理のスコープを選択し、 **[適用]** を選択します。

## <a name="next-steps"></a>次の手順

- [Azure Front Door Standard/Premium (プレビュー) レポート](how-to-reports.md)について学習します。
- [Azure Front Door Standard/Premium (プレビュー) リアルタイム監視メトリック](how-to-monitor-metrics.md)について学習します。
