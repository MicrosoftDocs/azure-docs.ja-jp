---
title: Azure Service Fabric のリバース プロキシ診断
description: Azure Service Fabric アプリケーションのリバース プロキシの要求処理を監視および診断する方法について説明します。
author: kavyako
ms.topic: conceptual
ms.date: 08/08/2017
ms.author: kavyako
ms.openlocfilehash: bbc1fe5a76ecb5720bc49e0a082d5e9151b403d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75645465"
---
# <a name="monitor-and-diagnose-request-processing-at-the-reverse-proxy"></a>リバース プロキシの要求処理の監視と診断

Service Fabric リリース 5.7 以降、リバース プロキシ イベントを収集できるようになりました。 イベントは 2 つのチャネルで使用できます。1 つはリバース プロキシで発生した要求処理エラーに関連するエラー イベントを含むチャネル、もう 1 つは成功した要求と失敗した要求の両方のエントリに関する詳細イベントを含むチャネルです。

ローカルおよび Azure Service Fabric クラスターでこれらのチャネルからのイベントの収集を有効にする方法については、「[リバース プロキシ イベントの収集](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)」を参照してください。

## <a name="troubleshoot-using-diagnostics-logs"></a>診断ツールを使用したトラブルシューティング
ここでは、発生する可能性がある一般的なエラー ログの解釈方法について例をいくつか挙げます。

1. リバース プロキシから応答の状態コード 504 (タイムアウト) が返される場合。

    理由の 1 つとして、要求のタイムアウト期間内にサービスが応答できなかったことが考えられます。
   以下の最初のイベントでは、リバース プロキシで受信された要求の詳細がログに記録されています。 
   2 つ目のイベントは、サービスへの転送中に、"internal error = ERROR_WINHTTP_TIMEOUT" により要求が失敗したことを示しています。 

    次のようなペイロードがあります。

   * **traceId**:この GUID は、単一の要求に関連するすべてのイベントを関連付けるために使用できます。 以下の 2 つのイベントにある traceId = **2f87b722-e254-4ac2-a802-fd315c1a0271** は、同じ要求に属することを示しています。
   * **requestUrl**:要求の送信先 URL (リバース プロキシの URL)。
   * **verb**:HTTP 動詞。
   * **remoteAddress**:要求を送信するクライアントのアドレス。
   * **resolvedServiceUrl**:受信要求が解決されたサービス エンドポイントの URL。 
   * **errorDetails**:このエラーに関する追加情報。

     ```
     {
     "Timestamp": "2017-07-20T15:57:59.9871163-07:00",
     "ProviderName": "Microsoft-ServiceFabric",
     "Id": 51477,
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Request url = https://localhost:19081/LocationApp/LocationFEService?zipcode=98052, verb = GET, remote (client) address = ::1, resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052, request processing start time =     15:58:00.074114 (745,608.196 MSec) ",
     "ProcessId": 57696,
     "Level": "Informational",
     "Keywords": "0x1000000000000021",
     "EventName": "ReverseProxy",
     "ActivityID": null,
     "RelatedActivityID": null,
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?zipcode=98052",
      "verb": "GET",
      "remoteAddress": "::1",
      "resolvedServiceUrl": "Https://localhost:8491/LocationApp/?zipcode=98052",
      "requestStartTime": "2017-07-20T15:58:00.0741142-07:00"
     }
     }

     {
     "Timestamp": "2017-07-20T16:00:01.3173605-07:00",
     ...
     "Message": "2f87b722-e254-4ac2-a802-fd315c1a0271 Error while forwarding request to service: response status code = 504, description = Reverse proxy Timeout, phase = FinishSendRequest, internal error = ERROR_WINHTTP_TIMEOUT ",
     ...
     "Payload": {
      "traceId": "2f87b722-e254-4ac2-a802-fd315c1a0271",
      "statusCode": 504,
      "description": "Reverse Proxy Timeout",
      "sendRequestPhase": "FinishSendRequest",
      "errorDetails": "internal error = ERROR_WINHTTP_TIMEOUT"
     }
     }
     ```

2. リバース プロキシから応答の状態コード 404 (見つかりません) が返される場合。 
    
    一致するサービス エンドポイントが見つからなかったため、リバース プロキシから 404 が返される例を次に示します。
    主なペイロード エントリは次のとおりです。
   * **processRequestPhase**: エラーが発生したときに要求が処理中のフェーズを示します。たとえば、**_TryGetEndpoint_** は 転送先のサービス エンドポイントを取得中であることを示します。 
   * **errorDetails**:エンドポイント検索条件を列挙します。 この例では、listenerName に = **FrontEndListener** が指定されていますが、レプリカ エンドポイント リストには、**OldListener** というリスナーしか含まれていないことがわかります。
    
     ```
     {
     ...
     "Message": "c1cca3b7-f85d-4fef-a162-88af23604343 Error while processing request, cannot forward to service: request url = https://localhost:19081/LocationApp/LocationFEService?ListenerName=FrontEndListener&zipcode=98052, verb = GET, remote (client) address = ::1, request processing start time = 16:43:02.686271 (3,448,220.353 MSec), error = FABRIC_E_ENDPOINT_NOT_FOUND, message = , phase = TryGetEndoint, SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"\":\"Https:\/\/localhost:8491\/LocationApp\/\"}} ",
     "ProcessId": 57696,
     "Level": "Warning",
     "EventName": "ReverseProxy",
     "Payload": {
      "traceId": "c1cca3b7-f85d-4fef-a162-88af23604343",
      "requestUrl": "https://localhost:19081/LocationApp/LocationFEService?ListenerName=NewListener&zipcode=98052",
      ...
      "processRequestPhase": "TryGetEndoint",
      "errorDetails": "SecureOnlyMode = false, gateway protocol = https, listenerName = FrontEndListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Https:\/\/localhost:8491\/LocationApp\/\"}}"
     }
     }
     ```
     リバース プロキシから 404 (見つかりません) が返されるもう 1 つの例として、**HTTPS** をリッスンしているリバース プロキシで、ApplicationGateway\Http 構成パラメーター **SecureOnlyMode** が true に設定されているのに、すべてのレプリカ エンドポイントがセキュリティで保護されていない (HTTP をリッスンしている) 場合があります。
     要求の転送先として HTTPS をリッスンしているエンドポイントが見つからないため、リバース プロキシからは 404 が返されます。 イベント ペイロードのパラメーターを分析すると、問題を絞り込むことができます。
    
     ```
      "errorDetails": "SecureOnlyMode = true, gateway protocol = https, listenerName = NewListener, replica endpoint = {\"Endpoints\":{\"OldListener\":\"Http:\/\/localhost:8491\/LocationApp\/\", \"NewListener\":\"Http:\/\/localhost:8492\/LocationApp\/\"}}"
     ```

3. リバース プロキシに対する要求がタイムアウト エラーで失敗した場合。 
    イベント ログには、イベントと、受信した要求の詳細情報 (ここには記載しません) が含まれています。
    次のイベントは、サービスが 404 状態コードで応答し、リバース プロキシが再解決を開始する例を示します。 

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Request to service returned: status code = 404, status description = , Reresolving ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "statusCode": 404,
        "statusDescription": ""
      }
    }
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Re-resolved service url = Https://localhost:8491/LocationApp/?zipcode=98052 ",
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "requestUrl": "Https://localhost:8491/LocationApp/?zipcode=98052"
      }
    }
    ```
    すべてのイベントが収集されると、すべての解決と転送の試行を示す一連のイベントが表示されます。
    その最後のイベントは、要求処理がタイムアウトで失敗したことと、成功した解決試行数を示しています。
    
    > [!NOTE]
    > 既定では詳細チャネル イベントの収集を無効にしておき、トラブルシューティングで必要な場合にのみ有効にすることをお勧めします。

    ```
    {
      ...
      "Message": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e Error while processing request: number of successful resolve attempts = 12, error = FABRIC_E_TIMEOUT, message = , phase = ResolveServicePartition,  ",
      "EventName": "ReverseProxy",
      ...
      "Payload": {
        "traceId": "7ac6212c-c8c4-4c98-9cf7-c187a94f141e",
        "resolveCount": 12,
        "errorval": -2147017729,
        "errorMessage": "",
        "processRequestPhase": "ResolveServicePartition",
        "errorDetails": ""
      }
    }
    ```
    
    重要/エラー イベントについてのみ収集を有効にすると、タイムアウトと解決試行数の詳細情報を含む 1 つのイベントが表示されます。 
    
    ユーザーに 404 状態コードを返すサービスは、応答のヘッダーに "X-ServiceFabric" を追加する必要があります。 応答にこのヘッダーが追加されると、リバース プロキシはクライアントに状態コードを転送します。  

4. クライアントが要求を切断した場合。

    以下のイベントは、リバース プロキシがクライアントに応答を転送したが、クライアントが切断した場合に記録されます。

    ```
    {
      ...
      "Message": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8 Unable to send response to client: phase = SendResponseHeaders, error = -805306367, internal error = ERROR_SUCCESS ",
      "ProcessId": 57696,
      "Level": "Warning",
      ...
      "EventName": "ReverseProxy",
      "Payload": {
        "traceId": "6e2571a3-14a8-4fc7-93bb-c202c23b50b8",
        "sendResponsePhase": "SendResponseHeaders",
        "errorval": -805306367,
        "winHttpError": "ERROR_SUCCESS"
      }
    }
    ```
5. リバース プロキシから 404 FABRIC_E_SERVICE_DOES_NOT_EXIST が返される場合。

    FABRIC_E_SERVICE_DOES_NOT_EXIST エラーは、サービス マニフェストでサービス エンドポイントの URI スキームが指定されていない場合に返されます。

    ```
    <Endpoint Name="ServiceEndpointHttp" Port="80" Protocol="http" Type="Input"/>
    ```

    この問題を解決するには、マニフェストで URI スキームを指定します。
    ```
    <Endpoint Name="ServiceEndpointHttp" UriScheme="http" Port="80" Protocol="http" Type="Input"/>
    ```

> [!NOTE]
> 現在、Web ソケット要求処理に関連するイベントはログに記録されていません。 この機能は次のリリースで追加予定です。

## <a name="next-steps"></a>次のステップ
* 「[Windows Azure Diagnostics を使用したイベントの集計と収集](service-fabric-diagnostics-event-aggregation-wad.md)」では、Azure クラスターでログの収集を有効にする方法について説明します。
* Visual Studio で Service Fabric イベントを表示するには、「[ローカル コンピューターの開発のセットアップでのサービスの監視と診断](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)」を参照してください。
* 「[Configure reverse proxy to connect to secure services (セキュリティで保護されたサービスに接続するためのリバース プロキシの構成)](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/Reverse-Proxy-Sample#configure-reverse-proxy-to-connect-to-secure-services)」を参照して Azure Resource Manager テンプレートのサンプルを取得し、サービス証明書のさまざまな検証オプションでセキュリティ保護されたリバース プロキシを構成します。
* 詳細については、「[Azure Service Fabric のリバース プロキシ](service-fabric-reverseproxy.md)」を参照してください。
