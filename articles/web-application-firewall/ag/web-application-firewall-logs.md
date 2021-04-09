---
title: Azure Web アプリケーション ファイアウォールのログを監視する
description: Azure Web アプリケーション ファイアウォールのログを有効にして管理する方法について説明します
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 116c12900f015c849c9492ed67bc11d116286c43
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102611931"
---
# <a name="resource-logs-for-azure-web-application-firewall"></a>Azure Web アプリケーション ファイアウォールのリソース ログ

ログを使用して、Web アプリケーション ファイアウォールのリソースを監視することができます。 リソースのパフォーマンス、アクセス、その他のデータを保存し、監視のために使用することができます。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="diagnostic-logs"></a>診断ログ

Azure の各種ログを使用して、アプリケーション ゲートウェイの管理とトラブルシューティングを行うことができます。 一部のログにはポータルからアクセスできます。 どのログも Azure Blob Storage から抽出し、[Azure Monitor ログ](../../azure-monitor/insights/azure-networking-analytics.md)、Excel、Power BI などのさまざまなツールで表示できます。 各種ログの詳細については、以下の一覧を参照してください。

* **アクティビティ ログ**:[Azure アクティビティ ログ](../../azure-resource-manager/management/view-activity-logs.md) を使用すると、Azure サブスクリプションに送信されるすべての操作とその状態を表示できます。 アクティビティ ログ エントリは既定で収集され、Azure Portal で表示できます。
* **アクセス リソース ログ**: このログを使用して Application Gateway のアクセス パターンを表示し、重要な情報を分析できます。 これには、呼び出し元の IP、要求された URL、応答の待機時間、リターン コード、入出力バイトが含まれます。アクセス ログは 300 秒ごとに収集されます。 このログには、Application Gateway のインスタンスごとに 1 つのレコードが含まれます。 Application Gateway のインスタンスは、instanceId プロパティで識別されます。
* **パフォーマンス リソース ログ**: このログを使用すると、Application Gateway のインスタンスの実行状況を確認できます。 このログでは、インスタンスごとのパフォーマンス情報 (処理された要求の総数、スループット (バイト単位)、失敗した要求の数、正常および異常なバックエンド インスタンスの数など) が取得されます。 パフォーマンス ログは 60 秒ごとに収集されます。 パフォーマンス ログは v1 SKU でのみ使用できます。 v2 SKU の場合は、パフォーマンス データに[メトリック](../../application-gateway/application-gateway-metrics.md)を使用します。
* **ファイアウォール リソース ログ**: このログを使用すると、Web アプリケーション ファイアウォールが構成された Application Gateway の、検出モードまたは防止モードでログに記録された要求を表示することができます。

> [!NOTE]
> ログは、Azure Resource Manager デプロイ モデルでデプロイされたリソースについてのみ使用できます。 クラシック デプロイ モデルのリソースには使用できません。 2 つのモデルについて理解を深めるには、「[Resource Manager デプロイとクラシック デプロイ](../../azure-resource-manager/management/deployment-models.md)」を参照してください。

ログを保存するための 3 つのオプションがあります。

* **ストレージ アカウント**: ストレージ アカウントは、ログを長期間保存し、必要に応じて参照する場合に最適です。
* **イベント ハブ**:イベント ハブは、他のセキュリティ情報/イベント管理 (SIEM) ツールと統合してリソースに関するアラートを取得する場合に便利なオプションです。
* **Azure Monitor ログ**: Azure Monitor ログは、アプリケーションをリアルタイムに監視したり、傾向を見たりする一般的な用途に最適です。

### <a name="enable-logging-through-powershell"></a>PowerShell を使用したログの有効化

アクティビティ ログは、Resource Manager のすべてのリソースで自動的に有効になります。 アクセス ログとパフォーマンス ログで使用可能なデータの収集を開始するには、これらのログを有効にする必要があります。 ログ記録を有効にするには、次の手順に従います。

1. ログ データを保存するストレージ アカウントのリソース ID をメモしておきます。 この値の形式は /subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Storage/storageAccounts/\<storage account name\> です。 サブスクリプション内の任意のストレージ アカウントを使用できます。 この情報は、Azure Portal で確認できます。

    ![ポータル: ストレージ アカウントのリソース ID](../media/web-application-firewall-logs/diagnostics1.png)

2. ログを有効にするアプリケーション ゲートウェイのリソース ID をメモしておきます。 この値の形式は /subscriptions/\<subscriptionId\>/resourceGroups/\<resource group name\>/providers/Microsoft.Network/applicationGateways/\<application gateway name\> です。 この情報は、ポータルで確認できます。

    ![ポータル: Application Gateway のリソース ID](../media/web-application-firewall-logs/diagnostics2.png)

3. 次の PowerShell コマンドレットを使用して、リソース ログを有効にします。

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```

> [!TIP]
>アクティビティ ログでは別のストレージ アカウントは必要ありません。 アクセス ログとパフォーマンス ログにストレージを使用すると、サービス料金が発生します。

### <a name="enable-logging-through-the-azure-portal"></a>Azure Portal を使用したログの有効化

1. Azure portal で、ご使用のリソースを見つけ、 **[診断設定]** を選択します。

   Application Gateway では、次の 3 つのログを使用できます。

   * アクセス ログ
   * パフォーマンス ログ
   * ファイアウォール ログ

2. データの収集を開始するには、 **[診断を有効にする]** を選択します。

   ![診断の有効化][1]

3. **[診断設定]** ページに、リソース ログの設定が表示されます。 この例では、Log Analytics を使用してログを保存します。 イベント ハブとストレージ アカウントを使用してリソース ログを保存することもできます。

   ![構成プロセスの開始][2]

5. 設定の名前を入力し、設定を確認した後、 **[保存]** を選択します。

### <a name="activity-log"></a>アクティビティ ログ

アクティビティ ログは、既定では Azure によって生成されます。 ログは、Azure のイベント ログ ストアに 90 日間保存されます。 これらのログの詳細については、「[イベントとアクティビティ ログの表示](../../azure-resource-manager/management/view-activity-logs.md)」を参照してください。

### <a name="access-log"></a>アクセス ログ

アクセス ログは、前の手順で示したように、Application Gateway のインスタンスごとにログを有効にした場合にのみ生成されます。 データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。 次の v1 の例に示すように、Application Gateway の各アクセスは JSON 形式でログに記録されます。

|値  |説明  |
|---------|---------|
|instanceId     | 要求を処理した Application Gateway のインスタンス。        |
|clientIP     | 要求の送信元 IP。        |
|clientPort     | 要求の送信元ポート。       |
|httpMethod     | 要求で使用される HTTP メソッド。       |
|requestUri     | 受信した要求の URI。        |
|RequestQuery     | **Server-Routed**:要求が送信されたバックエンド プールのインスタンス。</br>**X-AzureApplicationGateway-LOG-ID**:要求に使用する関連付け ID。 この ID を使用すると、バックエンド サーバー上のトラフィックの問題をトラブルシューティングできます。 </br>**SERVER-STATUS**:Application Gateway がバックエンドから受信した HTTP 応答コード。       |
|UserAgent     | HTTP 要求ヘッダーからのユーザー エージェント。        |
|httpStatus     | Application Gateway からクライアントに返される HTTP 状態コード。       |
|httpVersion     | 要求の HTTP バージョン。        |
|receivedBytes     | 受信したパケットのサイズ (バイト単位)。        |
|sentBytes| 送信したパケットのサイズ (バイト単位)。|
|timeTaken| 要求の処理および応答の送信にかかった時間 (ミリ秒単位)。 これは、Application Gateway がHTTP 要求の最初のバイトを受信してから、応答の送信操作が完了するまでの間隔として計算されます。 通常、timeTaken フィールドには、要求パケットと応答パケットがネットワーク経由で移動する時間が含まれています。 |
|sslEnabled| バックエンド プールへの通信に TLS または SSL を使用するかどうか。 有効な値は on と off です。|
|host| 要求がバックエンド サーバーに送信されたときに使用されたホスト名。 バックエンドのホスト名が上書きされている場合、この名前にそのことが反映されます。|
|originalHost| Application Gateway がクライアントから要求を受信したときに使用されたホスト名。|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "timestamp": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "ApplicationGatewayRole_IN_0",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "requestQuery": "X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.4.0.4&X-AzureApplicationGateway-LOG-ID=874f1f0f-6807-41c9-b7bc-f3cfa74aa0b1&SERVER-STATUS=404",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "host": "www.contoso.com",
        "originalHost": "www.contoso.com"
    }
}
```
Application Gateway と WAF v2 の場合、ログにはさらにいくつかの情報が表示されます。

|値  |説明  |
|---------|---------|
|instanceId     | 要求を処理した Application Gateway のインスタンス。        |
|clientIP     | 要求の送信元 IP。        |
|clientPort     | 要求の送信元ポート。       |
|httpMethod     | 要求で使用される HTTP メソッド。       |
|requestUri     | 受信した要求の URI。        |
|UserAgent     | HTTP 要求ヘッダーからのユーザー エージェント。        |
|httpStatus     | Application Gateway からクライアントに返される HTTP 状態コード。       |
|httpVersion     | 要求の HTTP バージョン。        |
|receivedBytes     | 受信したパケットのサイズ (バイト単位)。        |
|sentBytes| 送信したパケットのサイズ (バイト単位)。|
|timeTaken| 要求の処理および応答の送信にかかった時間 (ミリ秒単位)。 これは、Application Gateway がHTTP 要求の最初のバイトを受信してから、応答の送信操作が完了するまでの間隔として計算されます。 通常、timeTaken フィールドには、要求パケットと応答パケットがネットワーク経由で移動する時間が含まれています。 |
|sslEnabled| バックエンド プールへの通信に TLS を使用するかどうか。 有効な値は on と off です。|
|sslCipher| TLS 通信に使用されている暗号スイート (TLS が有効な場合)。|
|sslProtocol| 使用されている TLS プロトコル (TLS が有効な場合)。|
|serverRouted| アプリケーション ゲートウェイから要求がルーティングされる先のバックエンド サーバー。|
|serverStatus| バックエンド サーバーの HTTP 状態コード。|
|serverResponseLatency| バックエンド サーバーからの応答の待機時間。|
|host| 要求のホスト ヘッダーに表示されているアドレス。|
```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/PEERINGTEST/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayAccess",
    "time": "2017-04-26T19:27:38Z",
    "category": "ApplicationGatewayAccessLog",
    "properties": {
        "instanceId": "appgw_1",
        "clientIP": "191.96.249.97",
        "clientPort": 46886,
        "httpMethod": "GET",
        "requestUri": "/phpmyadmin/scripts/setup.php",
        "userAgent": "-",
        "httpStatus": 404,
        "httpVersion": "HTTP/1.0",
        "receivedBytes": 65,
        "sentBytes": 553,
        "timeTaken": 205,
        "sslEnabled": "off",
        "sslCipher": "",
        "sslProtocol": "",
        "serverRouted": "104.41.114.59:80",
        "serverStatus": "200",
        "serverResponseLatency": "0.023",
        "host": "www.contoso.com",
    }
}
```

### <a name="performance-log"></a>パフォーマンス ログ

パフォーマンス ログは、前の手順で示したように、Application Gateway のインスタンスごとにログを有効にした場合にのみ生成されます。 データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。 パフォーマンス ログ データは、1 分間隔で生成されます。 これは v1 SKU でのみ使用できます。 v2 SKU の場合は、パフォーマンス データに[メトリック](../../application-gateway/application-gateway-metrics.md)を使用します。 次のデータがログに記録されます。


|値  |説明  |
|---------|---------|
|instanceId     |  パフォーマンス データを生成中の Application Gateway のインスタンス。 複数インスタンスの Application Gateway の場合、インスタンスごとに 1 行が使用されます。        |
|healthyHostCount     | バックエンド プール内の正常なホストの数。        |
|unHealthyHostCount     | バックエンド プール内の異常なホストの数。        |
|requestCount     | 処理された要求の数。        |
|latency | インスタンスから要求を処理するバックエンドへの要求の平均待機時間 (ミリ秒単位)。 |
|failedRequestCount| 失敗した要求の数。|
|throughput| 最後のログ以降の平均スループット (1 秒あたりのバイト数)。|

```json
{
    "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
    "operationName": "ApplicationGatewayPerformance",
    "time": "2016-04-09T00:00:00Z",
    "category": "ApplicationGatewayPerformanceLog",
    "properties":
    {
        "instanceId":"ApplicationGatewayRole_IN_1",
        "healthyHostCount":"4",
        "unHealthyHostCount":"0",
        "requestCount":"185",
        "latency":"0",
        "failedRequestCount":"0",
        "throughput":"119427"
    }
}
```

> [!NOTE]
> 待機時間は、HTTP 要求の最初のバイトが受信されたときから、HTTP 応答の最後のバイトが送信されたときまでで計算されます。 これは、Application Gateway の処理時間、バックエンドへのネットワーク コスト、およびバックエンドが要求の処理に要した時間を加えたものです。

### <a name="firewall-log"></a>ファイアウォール ログ

ファイアウォール ログは、前の手順で示したように、Application Gateway のインスタンスごとにログを有効にした場合にのみ生成されます。 このログを使用するには、Application Gateway で Web アプリケーション ファイアウォールを構成する必要もあります。 データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。 次のデータがログに記録されます。


|値  |説明  |
|---------|---------|
|instanceId     | ファイアウォール データを生成中の Application Gateway のインスタンス。 複数インスタンスの Application Gateway の場合、インスタンスごとに 1 行が使用されます。         |
|clientIp     |   要求の送信元 IP。      |
|clientPort     |  要求の送信元ポート。       |
|requestUri     | 受信した要求の URL。       |
|ruleSetType     | ルール セットの種類。 使用できる値は OWASP です。        |
|ruleSetVersion     | 使用されるルール セットのバージョン。 使用できる値は 2.2.9 と 3.0 です。     |
|ruleId     | トリガーするイベントのルール ID。        |
|message     | トリガーするイベントのわかりやすいメッセージ。 詳細は details セクションに示されます。        |
|action     |  要求に対して実行されるアクション。 使用可能な値は Blocked と Allowed (カスタム ルールの場合)、Matched (ルールが要求の一部と一致する場合)、Detected と Blocked (どちらも必須ルールの場合。WAF が検出または防止モードであるかどうかによって異なる)。      |
|site     | ログの生成対象のサイト。 ルールがグローバルであるため、現時点では Global のみ表示されます。|
|details     | トリガーするイベントの詳細。        |
|details.message     | ルールの説明。        |
|details.data     | 要求で見つかった、ルールに一致するデータ。         |
|details.file     | ルールが含まれている構成ファイル。        |
|details.line     | イベントをトリガーした、構成ファイル内の行番号。       |
|hostname   | Application Gateway のホスト名または IP アドレス。    |
|transactionId  | 同じ要求内で発生した複数の規則違反をグループ化するのに役立つ、指定されたトランザクションの一意の ID。   |
|policyId   | Application Gateway、リスナー、またはパスに関連付けられているファイアウォール ポリシーの一意の ID。   |
|policyScope    | ポリシーの場所。値は、"Global"、"Listener"、"Location" のいずれかです。   |
|policyScopeName   | ポリシーが適用されるオブジェクトの名前。    |

```json
{
  "resourceId": "/SUBSCRIPTIONS/{subscriptionId}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/{applicationGatewayName}",
  "operationName": "ApplicationGatewayFirewall",
  "time": "2017-03-20T15:52:09.1494499Z",
  "category": "ApplicationGatewayFirewallLog",
  "properties": {
      "instanceId": "ApplicationGatewayRole_IN_0",
      "clientIp": "52.161.109.147",
      "clientPort": "0",
      "requestUri": "/",
      "ruleSetType": "OWASP",
      "ruleSetVersion": "3.0",
      "ruleId": "920350",
      "ruleGroup": "920-PROTOCOL-ENFORCEMENT",
      "message": "Host header is a numeric IP address",
      "action": "Matched",
      "site": "Global",
      "details": {
        "message": "Warning. Pattern match \"^[\\\\d.:]+$\" at REQUEST_HEADERS:Host ....",
        "data": "127.0.0.1",
        "file": "rules/REQUEST-920-PROTOCOL-ENFORCEMENT.conf",
        "line": "791"
      },
      "hostname": "127.0.0.1",
      "transactionId": "16861477007022634343",
      "policyId": "/subscriptions/1496a758-b2ff-43ef-b738-8e9eb5161a86/resourceGroups/drewRG/providers/Microsoft.Network/ApplicationGatewayWebApplicationFirewallPolicies/perListener",
      "policyScope": "Listener",
      "policyScopeName": "httpListener1"
    }
  }
}

```

### <a name="view-and-analyze-the-activity-log"></a>アクティビティ ログの表示と分析

次のいずれかの方法を使用して、アクティビティ ログのデータを表示および分析できます。

* **Azure Tools**:Azure PowerShell、Azure CLI、Azure REST API、または Azure portal を使用して、アクティビティ ログから情報を取得します。 それぞれの方法の詳細な手順については、「[リソース マネージャーの監査操作](../../azure-resource-manager/management/view-activity-logs.md)」を参照してください。
* **Power BI**: [Power BI](https://powerbi.microsoft.com/pricing) アカウントをまだ所有していない場合は、無料で試すことができます。 [Power BI テンプレート アプリ](/power-bi/service-template-apps-overview)を使用して、データを分析できます。

### <a name="view-and-analyze-the-access-performance-and-firewall-logs"></a>アクセス ログ、パフォーマンス ログ、ファイアウォール ログの表示と分析

[Azure Monitor ログ](../../azure-monitor/insights/azure-networking-analytics.md)では、BLOB ストレージ アカウントからカウンターとイベントのログ ファイルを収集できます。 このツールには、ログを分析するための視覚化と強力な検索機能が含まれています。

自身のストレージ アカウントに接続して、アクセス ログとパフォーマンス ログの JSON ログ エントリを取得することもできます。 JSON ファイルをダウンロードした後、そのファイルを CSV に変換し、Excel、Power BI などのデータ視覚化ツールで表示できます。

> [!TIP]
> Visual Studio を使い慣れていて、C# の定数と変数の値を変更する基本的な概念を理解している場合は、GitHub から入手できる[ログ変換ツール](https://github.com/Azure-Samples/networking-dotnet-log-converter)を使用できます。
>
>

#### <a name="analyzing-access-logs-through-goaccess"></a>GoAccess を介してアクセス ログを分析する

Microsoft は、人気のある [GoAccess](https://goaccess.io/) ログ アナライザーをインストールし、Application Gateway アクセス ログに対して実行する、Resource Manager テンプレートを発行しています。 GoAccess では、ユニーク ビジター、要求されたファイル、ホスト、オペレーティング システム、ブラウザー、HTTP 状態コードなど、重要な HTTP トラフィック統計情報が提供されます。 詳細については、[GitHub の Resource Manager テンプレート フォルダーにある Readme ファイル](https://aka.ms/appgwgoaccessreadme)を参照してください。

## <a name="next-steps"></a>次のステップ

* [Azure Monitor ログ](../../azure-monitor/insights/azure-networking-analytics.md)を使用して、カウンターとイベント ログを視覚化します。
* [Power BI を使用した Azure アクティビティ ログの視覚化](https://powerbi.microsoft.com/blog/monitor-azure-audit-logs-with-power-bi/)に関するブログ
* [Power BI などでの Azure アクティビティ ログの表示と分析](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)に関するブログ

[1]: ../media/web-application-firewall-logs/figure1.png
[2]: ../media/web-application-firewall-logs/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png
