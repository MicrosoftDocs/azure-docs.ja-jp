---
title: Azure Application Insights の IP アドレスの収集 | Microsoft Docs
description: Azure Application Insights を使用して IP アドレスと位置情報を処理する方法について
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 09/11/2019
ms.author: mbullwin
ms.openlocfilehash: 49534cbce7bb0bbf540416785e31b451509d5bf6
ms.sourcegitcommit: 083aa7cc8fc958fc75365462aed542f1b5409623
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2019
ms.locfileid: "70916162"
---
# <a name="geolocation-and-ip-address-handling"></a>位置情報と IP アドレスの処理

この記事では、Application Insights で位置情報検索と IP アドレスの処理がどのように行われるか、また、既定の動作を変更する方法について説明します。

## <a name="default-behavior"></a>既定の動作

既定では、IP アドレスは一時的に収集されますが、Application Insights には格納されません。 基本的なプロセスは次のとおりです。

IP アドレスは、テレメトリ データの一部として Application Insights に送信されます。 Azure のインジェスト エンドポイントに達した場合、[MaxMind の GeoLite2](https://dev.maxmind.com/geoip/geoip2/geolite2/) を使って物位置情報検索を行うために IP アドレスが使用されます。 この検索結果は、`client_City`、`client_StateOrProvince`、`client_CountryOrRegion`の各フィールドを設定するために使用されます。 この時点で、IP アドレスは破棄され、`0.0.0.0` が `client_IP` フィールドに書き込まれます。

* ブラウザー テレメトリ:送信者の IP アドレスを一時的に収集します。 IP アドレスはインジェスト エンドポイントによって計算されます。
* サーバー テレメトリ:Application Insights モジュールでクライアントの IP アドレスが一時的に収集されます。 `X-Forwarded-For` が設定されている場合は収集されません。

この動作の仕様は、個人データの不必要な収集を回避するのに役立ちます。 可能な限り、個人データの収集を回避することをお勧めします。 

## <a name="overriding-default-behavior"></a>既定の動作のオーバーライド

既定の動作では個人データの収集が最小限に抑えられますが、IP アドレス データの収集と格納は引き続き柔軟に行うことができます。 IP アドレスなどの個人データを格納することを選択する前に、これが、適用される可能性があるコンプライアンス要件と地方条例に違反しないことを確認することを強くお勧めします。 Application Insights での個人データ処理の詳細については、[個人データのガイダンス](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)に関するページをご覧ください。

## <a name="storing-ip-address-data"></a>IP アドレス データの格納

IP の収集と格納を有効にするには、Application Insights コンポーネントの `DisableIpMasking` プロパティを `true` に設定する必要があります。 このプロパティは、Azure Resource Manager テンプレートを使用するか、REST API を呼び出すことによって設定できます。 

### <a name="azure-resource-manager-template"></a>Azure Resource Manager テンプレート

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>ポータル 

1 つの Application Insights リソースの動作のみを変更する必要がある場合は、Azure portal を使用するのが最も簡単な方法です。  

1. Application Insights リソース > **[設定]**  >  **[テンプレートのエクスポート]** の順に移動します 

    ![テンプレートのエクスポート](media/ip-collection/export-template.png)

2. **[デプロイ]** を選択します

    ![赤色で強調表示されている [デプロイ] ボタン](media/ip-collection/deploy.png)

3. **[テンプレートの編集]** を選択します (テンプレートに、この例のテンプレートに表示されていない追加のプロパティまたはリソースがある場合は、すべてのリソースでテンプレートのデプロイが増分変更/更新として確実に受け入れられるように注意して作業を進めてください)。

    ![Edit Template](media/ip-collection/edit-template.png)

4. リソースの json を次のように変更してから、 **[保存]** をクリックします。

    ![スクリーンショットでは、"IbizaAIExtension" の後にコンマが追加され、その下の新しい行に "DisableIpMasking": true が追加されています](media/ip-collection/save.png)

    > [!WARNING]
    > 次のようなエラーが発生した場合:" **_リソース グループは、テンプレート内の 1 つ以上のリソースがサポートしていない場所にあります。別のリソース グループを選択してください。_** " 一時的にドロップダウンから別のリソース グループを選択してから、元のリソース グループを再選択してエラーを解決します。

5. **[同意する]**  >  **[購入]** の順に選択します。 

    ![Edit Template](media/ip-collection/purchase.png)

    この場合、新しいものは購入されません。既存の Application Insights リソースの構成を更新するだけです。

6. デプロイが完了すると、新しいテレメトリ データが記録されます。

    テンプレートをもう一度選択して編集すると、既定のテンプレートのみが表示され、新しく追加されたプロパティとそれに関連付けられた値は表示されません。 IP アドレス データが表示されず、`"DisableIpMasking": true` が設定されていることを確認する必要がある場合。 次の PowerShell を実行します (`Fabrikam-dev` は、適切なリソースとリソース グループの名前に置き換えてください)。
    
    ```powershell
    # If you aren't using the cloud shell you will need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    結果として、プロパティの一覧が返されます。 プロパティのいずれかが `DisableIpMasking: true` になっているはずです。 Azure Resource Manager で新しいプロパティをデプロイする前に PowerShell を実行する場合、プロパティは存在しません。

### <a name="rest-api"></a>Rest API

同じ変更を行うための [Rest API](https://docs.microsoft.com/rest/api/azure/) ペイロードは次のとおりです。

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>テレメトリ初期化子

IP アドレスのすべてまたは一部を記録するために `DisableIpMasking` よりも柔軟な代替方法が必要な場合は、[テレメトリ初期化子](https://docs.microsoft.com/azure/azure-monitor/app/api-filtering-sampling#add-properties-itelemetryinitializer)を使用して、IP のすべてまたは一部をカスタム フィールドにコピーすることができます。 

### <a name="aspnet--aspnet-core"></a>ASP.NET または ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> `ISupportProperties` にアクセスできない場合は、Application Insights SDK の最新の安定版リリースを実行していることを確認してください。 `ISupportProperties` は、大きなカーディナリティ値を対象としています。一方、`GlobalProperties` は、リージョン名、環境名などの小さなカーディナリティ値に適しています。 

### <a name="enable-telemetry-initializer-for-aspnet"></a>ASP.NET のテレメトリ初期化子を有効にする

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-telemetry-initializer-for-aspnet-core"></a>ASP.NET Core のテレメトリ初期化子を有効にする

ASP.NET と同じ方法で ASP.NET Core のテレメトリ初期化子を作成できますが、初期化子を有効にするために、参照用に次の例を使用します。

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```

### <a name="client-side-javascript"></a>クライアント側の JavaScript

サーバー側の SDK とは異なり、クライアント側の JavaScript SDK では IP アドレスは計算されません。 既定では、クライアント側のテレメトリの IP アドレス計算は、テレメトリの到着時に Azure のインジェスト エンドポイントで実行されます。 つまり、クライアント側のデータをプロキシに送信してから、インジェスト エンドポイントに転送した場合、IP アドレスの計算では、クライアントではなく、プロキシの IP アドレスが表示されることがあります。 プロキシが使用されていない場合、これは問題にならないはずです。

クライアント側で IP アドレスを直接計算する場合は、独自のカスタム ロジックを追加してこの計算を実行し、結果を使用して `ai.location.ip` タグを設定する必要があります。 `ai.location.ip` が設定されている場合、IP アドレスの計算はインジェスト エンドポイントでは行われず、指定された IP アドレスが受け入れられ、位置情報検索を実行するために使用されます。 このシナリオでは、引き続き、既定で IP アドレスがゼロに設定されます。 

カスタム ロジックから計算された IP アドレス全体を保持する場合は、テレメトリ初期化子を使用できます。これにより、`ai.location.ip` で指定した IP アドレス データが別のカスタム フィールドにコピーされます。 しかし、ここでもサーバー側の SDK とは異なり、サードパーティのライブラリや独自のカスタム クライアント側の IP 収集ロジックに依存することなく、クライアント側の SDK では IP が自動的に計算されません。    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

### <a name="view-the-results-of-your-telemetry-initializer"></a>テレメトリ初期化子の結果を表示する

その後、サイトに対して新しいトラフィックをトリガーし、約 2 分から 5 分待ち、取り込む時間があったことを確認する場合は、Kusto クエリを実行し、IP アドレスの収集が動作しているかどうかを確かめます。

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

新しく収集された IP アドレスは `customDimensions_client-ip` 列に表示されるはずです。 既定の `client-ip` 列では引き続き、4 つのオクテットがすべてゼロに設定されるか、コンポーネント レベルで IP アドレスの収集を構成した方法によっては、最初の 3 つのオクテットのみが表示されます。 テレメトリ初期化子を実装した後、ローカルでテストしており、`customDimensions_client-ip` で表示される値が `::1` である場合は、これが想定される動作です。 `::1` は、IPv6 のループバック アドレスを表します。 これは IPv4 の `127.0.01` と同等であり、localhost からテストする場合に表示される結果です。

## <a name="next-steps"></a>次の手順

* Application Insights での[個人データ収集](https://docs.microsoft.com/azure/azure-monitor/platform/personal-data-mgmt)について、さらに学習します。

* Application Insights の [IP アドレス収集](https://apmtips.com/blog/2016/07/05/client-ip-address/)のしくみについて、さらに学習します (これは、Microsoft のエンジニアの 1 人が書き込んだ外部の古いブログ投稿です。 IP アドレスが `0.0.0.0` として記録される現在の既定の動作より前のものですが、組み込みの `ClientIpHeaderTelemetryInitializer` のメカニズムについてより詳しく説明されています)。