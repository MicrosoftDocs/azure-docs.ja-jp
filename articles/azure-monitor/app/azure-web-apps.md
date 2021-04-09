---
title: Azure App Services のパフォーマンスを監視する | Microsoft Docs
description: Azure App Services のアプリケーション パフォーマンスの監視。 チャートの読み込みおよび応答時間、依存関係の情報やパフォーマンス警告を設定します。
ms.topic: conceptual
ms.date: 08/06/2020
ms.custom: devx-track-js, devx-track-dotnet
ms.openlocfilehash: 7661066bc2666070c8b3ed9263b1223c09d6c720
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101734725"
---
# <a name="monitor-azure-app-service-performance"></a>Azure App Service のパフォーマンスの監視

[Azure App Services](../../app-service/index.yml) 上で実行されている ASP.NET、ASP.NET Core、Node.js ベースの Web アプリケーションで、これまでよりも簡単に監視を有効にすることができるようになりました。 以前は手動でサイト拡張機能をインストールする必要がありましたが、最新の拡張機能/エージェントが既定でアプリ サービス イメージに組み込まれました。 この記事では、Application Insights の監視を有効にする手順を説明し、大規模なデプロイ プロセスを自動化する準備となるガイダンスを提供します。

> [!NOTE]
> **[開発ツール]**  >  **[拡張機能]** を使用して Application Insights のサイト拡張機能を手動で追加することは、非推奨になりました。 この拡張機能のインストール方法は、新しい各バージョンの手動更新が必要でした。 拡張機能の最新の安定版リリースが、App Service イメージの一部として[プレインストール](https://github.com/projectkudu/kudu/wiki/Azure-Site-Extensions)されるようになりました。 これらのファイルは `d:\Program Files (x86)\SiteExtensions\ApplicationInsightsAgent` にあり、安定版リリースごとに自動的に更新されます。 以下の監視を有効にするエージェント ベースの手順を実行すると、非推奨の拡張機能は自動的に削除されます。

## <a name="enable-application-insights"></a>Application Insights を有効にする

Azure App Services がホストするアプリケーションについてアプリケーション監視を有効にする方法は 2 つあります。

* **エージェントベースのアプリケーション監視** (ApplicationInsightsAgent)。  
    * 有効にするにはこの方法が最も簡単であり、高度な構成は不要です。 多くの場合、これは "ランタイム" 監視と呼ばれます。 Azure App Services では、少なくともこのレベルの監視を有効にすることをお勧めします。それから、お客様固有のシナリオに基づいて、手動のインストルメンテーションによる高度な監視が必要かどうかを評価できます。

* Application Insights SDK をインストールし、コードを介して **手動でアプリケーションをインストルメント化する方法**。

    * このアプローチはカスタマイズできる部分がはるかに多いのですが、[Application Insights SDK NuGet パッケージへの依存関係を追加](./asp-net.md)する必要があります。 また、この方法では、最新バージョンのパッケージへの更新を自分で管理する必要があります。

    * エージェントベースの監視の既定ではキャプチャされないイベント/依存関係を追跡するためにカスタム API 呼び出しを行う必要がある場合は、この方法を使用する必要があります。 詳細については、[カスタムのイベントとメトリックのための API に関する記事](./api-custom-events-metrics.md)を参照してください。 これは、現在、Linux ベースのワークロードで唯一サポートされているオプションでもあります。

> [!NOTE]
> エージェント ベースの監視と手動の SDK ベースのインストルメンテーションの両方が検出された場合は、手動のインストルメンテーション設定のみが受け付けられます。 これは、重複したデータが送信されないようにするためです。 この詳細については、以下の「[トラブルシューティング](#troubleshooting)」セクションを参照してください。

## <a name="enable-agent-based-monitoring"></a>エージェント ベースの監視を有効にする

# <a name="aspnet"></a>[ASP.NET](#tab/net)

> [!NOTE]
> APPINSIGHTS_JAVASCRIPT_ENABLED と urlCompression の組み合わせはサポートされていません。 詳細については、[トラブルシューティングのセクション](#troubleshooting)の説明を参照してください。


1. アプリ サービスの Azure コントロール パネルで **[Application Insights]** を選択します。

    ![[設定] の [Application Insights] を選択する](./media/azure-web-apps/settings-app-insights-01.png)

   * このアプリケーションの Application Insights リソースを既に設定している場合を除き、新しいリソースの作成を選択します。 

     > [!NOTE]
     > **[OK]** をクリックして新しいリソースを作成すると、**監視の設定を適用する** ように求めるメッセージが表示されます。 **[続行]** を選択すると、新しい Application Insights リソースがアプリ サービスにリンクされ、**アプリ サービスの再起動がトリガー** されます。 

     ![Web アプリをインストルメント化する](./media/azure-web-apps/create-resource-01.png)

2. 使用するリソースを指定した後、アプリケーションのプラットフォームごとのデータを Application Insights でどのように収集するかを選択できます。 ASP.NET アプリの監視は既定でオンであり、2 つの異なる収集レベルがあります。

    ![Application Insights サイト拡張機能ページのスクリーンショット。[新しいリソースの作成] が選択されています。](./media/azure-web-apps/choose-options-new.png)
 
 それぞれのルートで収集されるデータは次のとおりです。
        
| Data | ASP.NET 基本収集 | ASP.NET 推奨収集 |
| --- | --- | --- |
| CPU、メモリ、および I/O の使用状況の傾向が追加されます |はい |はい |
| 使用状況の傾向が収集され、可用性の結果からトランザクションへの相関関係が有効になります | はい |はい |
| ホスト プロセスによって処理されていない例外が収集されます | はい |はい |
| 負荷がかかっているときの APM メトリックの精度が上がります (サンプリングが使用される場合) | はい |はい |
| 要求/依存関係の境界を越えてマイクロサービスが相互に関連付けられます | いいえ (シングルインスタンス APM 機能のみ) |はい |

3. 以前に applicationinsights.config ファイルで制御できたサンプリングなどの設定を構成する場合、[アプリケーション設定] で対応するプレフィックスを使用して同じ設定と対話できるようになりました。 

    * たとえば、最初のサンプリング率を変更するには、`MicrosoftAppInsights_AdaptiveSamplingTelemetryProcessor_InitialSamplingPercentage` と `100` の値の [アプリケーション設定] を作成できます。

    * サポートされるアダプティブ サンプリング テレメトリ プロセッサ設定の一覧については、[コード](https://github.com/microsoft/ApplicationInsights-dotnet/blob/master/BASE/Test/ServerTelemetryChannel.Test/TelemetryChannel.Tests/AdaptiveSamplingTelemetryProcessorTest.cs)と[関連ドキュメント](./sampling.md)を参照してください。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/netcore)

> [!IMPORTANT]
> 次のバージョンの ASP.NET Core がサポートされます。ASP.NET Core 2.1 および 3.1。 バージョン 2.0、2.2、3.0 は廃止され、サポートされなくなりました。 自動インストルメンテーションを機能させるには、[サポートされているバージョン](https://dotnet.microsoft.com/platform/support/policy/dotnet-core)の .NET Core にアップグレードしてください。

現時点では、ASP.NET Core の完全なフレームワーク、自己完結型のデプロイ、および Linux ベースのアプリケーションをターゲットにすることは、エージェント/拡張機能ベースの監視では **サポートされていません**。 (コードによる[手動インストルメンテーション](./asp-net-core.md)は、上記のすべてのシナリオで機能します)。

1. アプリ サービスの Azure コントロール パネルで **[Application Insights]** を選択します。

    ![[設定] の [Application Insights] を選択する](./media/azure-web-apps/settings-app-insights-01.png)

   * このアプリケーションの Application Insights リソースを既に設定している場合を除き、新しいリソースの作成を選択します。 

     > [!NOTE]
     > **[OK]** をクリックして新しいリソースを作成すると、**監視の設定を適用する** ように求めるメッセージが表示されます。 **[続行]** を選択すると、新しい Application Insights リソースがアプリ サービスにリンクされ、**アプリ サービスの再起動がトリガー** されます。 

     ![Web アプリをインストルメント化する](./media/azure-web-apps/create-resource-01.png)

2. 使用するリソースを指定した後、アプリケーションのプラットフォームごとのデータを Application Insights でどのように収集するかを選択できます。 ASP.NET Core では、ASP.NET Core 2.1 および 3.1 の場合、 **[Recommended collection (推奨収集)]** または **[Disabled (無効)]** が提供されます。

    ![プラットフォームごとのオプションを選択する](./media/azure-web-apps/choose-options-new-net-core.png)

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Windows エージェント ベースの監視はサポートされていません。Linux で有効にするには、[Node.js App Service のドキュメント](../../app-service/configure-language-nodejs.md?pivots=platform-linux#monitor-with-application-insights)を参照してください。

# <a name="java"></a>[Java](#tab/java)

[Application Insights Java 3.0 エージェント](./java-in-process-agent.md)のガイドラインに従って、コードを変更せずに Java アプリの自動インストルメンテーションを有効にします。
自動統合は、App Service ではまだ使用できません。

# <a name="python"></a>[Python](#tab/python)

Python App Service ベースの Web アプリケーションでは、エージェント/拡張機能ベースの自動監視は現在サポートされていません。 Python アプリケーションの監視を有効にするには、[アプリケーションを手動でインストルメント化する](./opencensus-python.md)必要があります。

---

## <a name="enable-client-side-monitoring"></a>クライアント側の監視を有効にする

# <a name="aspnet"></a>[ASP.NET](#tab/net)

ASP.NET の場合、クライアント側の監視はオプトインです。 クライアント側の監視を有効にするには:

* **[設定]** **>** **[構成]**
   * [アプリケーション設定] で、**新しいアプリケーション設定** を作成します。

     名前: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     値: `true`

   * 設定を **[保存]** し、アプリを **[再起動]** します。

クライアント側の監視を無効にするには、[アプリケーション設定] から関連付けられているキーと値のペアを削除するか、値を false に設定します。

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/netcore)

アプリ設定 'APPINSIGHTS_JAVASCRIPT_ENABLED' の有無に関係なく、ASP.NET Core アプリと **推奨収集** の組み合わせの場合、クライアント側の監視は **既定で有効** です。

何らかの理由でクライアント側の監視を無効にするには、次の手順を実行します。

* **[設定]** **>** **[構成]**
   * [アプリケーション設定] で、**新しいアプリケーション設定** を作成します。

     名前: `APPINSIGHTS_JAVASCRIPT_ENABLED`

     値: `false`

   * 設定を **[保存]** し、アプリを **[再起動]** します。

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

Node.js アプリケーションでクライアント側の監視を有効にするには、[クライアント側の JavaScript SDK をアプリケーションに手動で追加する](./javascript.md)必要があります。

# <a name="java"></a>[Java](#tab/java)

Java アプリケーションでクライアント側の監視を有効にするには、[クライアント側の JavaScript SDK をアプリケーションに手動で追加する](./javascript.md)必要があります。

# <a name="python"></a>[Python](#tab/python)

Python アプリケーションでクライアント側の監視を有効にするには、[クライアント側の JavaScript SDK をアプリケーションに手動で追加する](./javascript.md)必要があります。

---

## <a name="automate-monitoring"></a>監視の自動化

Application Insights を使用したテレメトリの収集を有効にするのに必要なのは、アプリケーション設定の設定のみです。

   ![利用可能な Application Insights の設定が表示された App Service のアプリケーション設定](./media/azure-web-apps/application-settings.png)

### <a name="application-settings-definitions"></a>アプリケーション設定の定義

|アプリ設定の名前 |  定義 | 値 |
|-----------------|:------------|-------------:|
|ApplicationInsightsAgent_EXTENSION_VERSION | メインの拡張機能で、実行時の監視を制御します。 | `~2` |
|XDT_MicrosoftApplicationInsights_Mode |  既定モードのみ、最適なパフォーマンスを保証するため重要な機能が有効になります。 | `default` または `recommended`。 |
|InstrumentationEngine_EXTENSION_VERSION | バイナリ再書き込みエンジン `InstrumentationEngine` がオンにされるかどうかを制御します。 この設定は、パフォーマンスに影響し、コールド スタート/起動時間に影響を与えます。 | `~1` |
|XDT_MicrosoftApplicationInsights_BaseExtensions | SQL と Azure テーブル テキストが依存関係呼び出しと共にキャプチャされるかどうかを制御します。 パフォーマンスの警告: アプリケーションのコールド スタートアップ時間が影響を受けます。 この設定には `InstrumentationEngine` が必要です。 | `~1` |
|XDT_MicrosoftApplicationInsights_PreemptSdk | ASP.NET Core アプリの場合のみ。 Application Insights SDK で相互運用を有効にします。 拡張機能を SDK とサイドバイサイドで読み込み、それを使用してテレメトリを送信します (Application Insights SDK を無効にします)。 |`1`|

### <a name="app-service-application-settings-with-azure-resource-manager"></a>Azure Resource Manager を使用した App Service のアプリケーション設定

App Services のアプリケーション設定は、[Azure Resource Manager テンプレート](../../azure-resource-manager/templates/template-syntax.md)を使用して管理および構成できます。 この手法は、Azure Resource Manager オートメーションで新しい App Service リソースをデプロイするとき、または既存のリソースの設定を変更するときに使用できます。

アプリ サービスに使用されるアプリケーション設定 JSON の基本構造を次に示します。

```JSON
      "resources": [
        {
          "name": "appsettings",
          "type": "config",
          "apiVersion": "2015-08-01",
          "dependsOn": [
            "[resourceId('Microsoft.Web/sites', variables('webSiteName'))]"
          ],
          "tags": {
            "displayName": "Application Insights Settings"
          },
          "properties": {
            "key1": "value1",
            "key2": "value2"
          }
        }
      ]
```

アプリケーション設定が Application Insights 用に構成されている Azure Resource Manager テンプレートの例として、この[テンプレート](https://github.com/Andrew-MSFT/BasicImageGallery)は役立ちます。[238 行目](https://github.com/Andrew-MSFT/BasicImageGallery/blob/c55ada54519e13ce2559823c16ca4f97ddc5c7a4/CoreImageGallery/Deploy/CoreImageGalleryARM/azuredeploy.json#L238)以降のセクションは特に有益です。

### <a name="automate-the-creation-of-an-application-insights-resource-and-link-to-your-newly-created-app-service"></a>Application Insights のリソース、および新しく作成された App Service へのリンクの作成を自動化します。

Application Insights の設定をすべて既定の設定にして Azure Resource Manager テンプレートを作成するには、Application Insights を有効にして新しい Web アプリを作成する場合のようにプロセスを開始します。

**Automation オプション** の選択

   ![App Service の Web アプリ作成メニュー](./media/azure-web-apps/create-web-app.png)

このオプションにより、必要な設定がすべて設定済みの、最新の Azure Resource Manager テンプレートが生成されます。

  ![App Service の Web アプリ テンプレート](./media/azure-web-apps/arm-template.png)

以下はサンプルです。すべての `AppMonitoredSite` をサイト名に置き換えてください。

```json
{
    "resources": [
        {
            "name": "[parameters('name')]",
            "type": "Microsoft.Web/sites",
            "properties": {
                "siteConfig": {
                    "appSettings": [
                        {
                            "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').InstrumentationKey]"
                        },
                        {
                            "name": "APPLICATIONINSIGHTS_CONNECTION_STRING",
                            "value": "[reference('microsoft.insights/components/AppMonitoredSite', '2015-05-01').ConnectionString]"
                        },
                        {
                            "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
                            "value": "~2"
                        }
                    ]
                },
                "name": "[parameters('name')]",
                "serverFarmId": "[concat('/subscriptions/', parameters('subscriptionId'),'/resourcegroups/', parameters('serverFarmResourceGroup'), '/providers/Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Web/serverfarms/', parameters('hostingPlanName'))]",
                "microsoft.insights/components/AppMonitoredSite"
            ],
            "apiVersion": "2016-03-01",
            "location": "[parameters('location')]"
        },
        {
            "apiVersion": "2016-09-01",
            "name": "[parameters('hostingPlanName')]",
            "type": "Microsoft.Web/serverfarms",
            "location": "[parameters('location')]",
            "properties": {
                "name": "[parameters('hostingPlanName')]",
                "workerSizeId": "[parameters('workerSize')]",
                "numberOfWorkers": "1",
                "hostingEnvironment": "[parameters('hostingEnvironment')]"
            },
            "sku": {
                "Tier": "[parameters('sku')]",
                "Name": "[parameters('skuCode')]"
            }
        },
        {
            "apiVersion": "2015-05-01",
            "name": "AppMonitoredSite",
            "type": "microsoft.insights/components",
            "location": "West US 2",
            "properties": {
                "ApplicationId": "[parameters('name')]",
                "Request_Source": "IbizaWebAppExtensionCreate"
            }
        }
    ],
    "parameters": {
        "name": {
            "type": "string"
        },
        "hostingPlanName": {
            "type": "string"
        },
        "hostingEnvironment": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "sku": {
            "type": "string"
        },
        "skuCode": {
            "type": "string"
        },
        "workerSize": {
            "type": "string"
        },
        "serverFarmResourceGroup": {
            "type": "string"
        },
        "subscriptionId": {
            "type": "string"
        }
    },
    "$schema": "https://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0"
}
```

### <a name="enabling-through-powershell"></a>PowerShell で有効にする

PowerShell を使用してアプリケーションの監視を有効にするために必要な操作は、基になるアプリケーション設定の変更のみです。 以下は、リソース グループ "AppMonitoredRG" 内の "AppMonitoredSite" という Web サイトのアプリケーションの監視を有効にし、データを "012345678-abcd-ef01-2345-6789abcd" インストルメンテーション キーに送信するように構成するサンプルです。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

```powershell
$app = Get-AzWebApp -ResourceGroupName "AppMonitoredRG" -Name "AppMonitoredSite" -ErrorAction Stop
$newAppSettings = @{} # case-insensitive hash map
$app.SiteConfig.AppSettings | %{$newAppSettings[$_.Name] = $_.Value} # preserve non Application Insights application settings.
$newAppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"] = "012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights instrumentation key
$newAppSettings["APPLICATIONINSIGHTS_CONNECTION_STRING"] = "InstrumentationKey=012345678-abcd-ef01-2345-6789abcd"; # set the Application Insights connection string
$newAppSettings["ApplicationInsightsAgent_EXTENSION_VERSION"] = "~2"; # enable the ApplicationInsightsAgent
$app = Set-AzWebApp -AppSettings $newAppSettings -ResourceGroupName $app.ResourceGroup -Name $app.Name -ErrorAction Stop
```

## <a name="upgrade-monitoring-extensionagent"></a>監視の拡張機能/エージェントのアップグレード

### <a name="upgrading-from-versions-289-and-up"></a>バージョン 2.8.9 以降からのアップグレード

バージョン 2.8.9 からのアップグレードは自動的に実行され、追加の操作は必要ありません。 新しい監視ビットは、バックグラウンドでターゲット アプリ サービスに配信され、アプリケーションの再起動時に取得されます。

実行している拡張機能のバージョンを確認するには、`http://yoursitename.scm.azurewebsites.net/ApplicationInsights` にアクセスします

![URL パス http://yoursitename.scm.azurewebsites.net/ApplicationInsights のスクリーンショット](./media/azure-web-apps/extension-version.png)

### <a name="upgrade-from-versions-100---265"></a>バージョン 1.0.0 - 2.6.5 からのアップグレード

バージョン 2.8.9 以降では、プレインストールされたサイト拡張機能が使用されています。 以前のバージョンを使用している場合は、次の 2 つの方法のいずれかを使用して更新できます。

* [ポータル経由で有効にしてアップグレードする](#enable-application-insights) (Azure App Service の Application Insights 拡張機能がインストールされている場合でも、UI には **[有効]** ボタンのみが表示されます。 背後では、以前のプライベート サイト拡張機能が削除されます)。

* [PowerShell を使用してアップグレードする](#enabling-through-powershell):

    1. プレインストールされたサイト拡張機能 ApplicationInsightsAgent を有効にするようにアプリケーション設定を指定します。 「[PowerShell で有効にする](#enabling-through-powershell)」を参照してください。
    2. Azure App Service の Application Insights 拡張機能という名前のプライベート サイト拡張機能を手動で削除します。

2\.5.1 より前のバージョンからアップグレードする場合は、ApplicationInsigths dll がアプリケーションの bin フォルダーから削除されていることを確認します。[トラブルシューティングの手順](#troubleshooting)を参照してください。

## <a name="troubleshooting"></a>トラブルシューティング

以下は、Azure App Services 上で実行されている ASP.NET および ASP.NET Core ベースのアプリケーションの拡張機能/エージェント ベースの監視について、手順を追って説明するトラブルシューティング ガイドです。

> [!NOTE]
> Java アプリケーションを監視する際に推奨される方法は、コードを変更せずに自動インストルメンテーションを使用することです。 [Application Insights Java 3.0 エージェント](./java-in-process-agent.md)のガイドラインに従ってください。


1. `ApplicationInsightsAgent` を介してアプリケーションが監視されていることを確認します。
    * `ApplicationInsightsAgent_EXTENSION_VERSION` アプリ設定が "2 以下" の値に設定されていることを確認します。
2. 監視対象のアプリケーションが要件を満たしていることを確認します。
    * `https://yoursitename.scm.azurewebsites.net/ApplicationInsights` に移動します

    ![https://yoursitename.scm.azurewebsites/applicationinsights 結果ページのスクリーンショット](./media/azure-web-apps/app-insights-sdk-status.png)

    * `Application Insights Extension Status` が `Pre-Installed Site Extension, version 2.8.12.1527, is running.` であることを確認します 
    * 実行中ではない場合は、[Application Insights の監視を有効にする手順](#enable-application-insights)を実行します。

    * 状態ソースが存在し、以下のようになっていることを確認します。`Status source D:\home\LogFiles\ApplicationInsights\status\status_RD0003FF0317B6_4248_1.json`
        * 似た値が存在しない場合は、アプリケーションが現在実行されていないか、サポートされていないことを意味します。 アプリケーションが実行されていることを確認するには、手動でアプリケーションの URL/アプリケーション エンドポイントにアクセスしてみてください。これで、ランタイム情報を使用できるようになります。

    * `IKeyExists` が `true` であることを確認します
        * `false` の場合は、お客様の ikey guid を指定した `APPINSIGHTS_INSTRUMENTATIONKEY` と `APPLICATIONINSIGHTS_CONNECTION_STRING` をアプリケーション設定に追加します。

    * `AppAlreadyInstrumented`、`AppContainsDiagnosticSourceAssembly`、および `AppContainsAspNetTelemetryCorrelationAssembly` のエントリがないことを確認します。
        * これらのエントリのいずれかが存在する場合は、アプリケーションから `Microsoft.ApplicationInsights`、`System.Diagnostics.DiagnosticSource`、および `Microsoft.AspNet.TelemetryCorrelation` のパッケージを削除します。
        * ASP.NET Core アプリのみ: アプリケーションが Application Insights パッケージを参照している場合 (たとえば、以前に [ASP.NET Core SDK](./asp-net-core.md) を使用してアプリをインストルメント化した場合 (またはインストルメント化しようとした場合))、App Service の統合が有効にならず、データが Application Insights に表示されないことがあります。 この問題を解決するには、ポータルで [Interop with Application Insights SDK]\(Application Insights SDK との相互運用\) をオンにすると、Application Insights にデータが表示されます。 
        > [!IMPORTANT]
        > この機能はプレビュー状態です 

        ![既存のアプリの設定を有効にする](./media/azure-web-apps/netcore-sdk-interop.png)

        これで、Application Insights SDK がもともと使用されていたか、使用しようとしていた場合でも、コード不要アプローチを使用してデータが送信されるようになりました。

        > [!IMPORTANT]
        > アプリケーションが Application Insights SDK を使用してテレメトリを送信していた場合、そのようなテレメトリは無効になります。つまり、たとえば、Track*() メソッドなどのカスタム テレメトリ (存在する場合)、およびサンプリングなどのカスタム設定は無効になります。 


### <a name="php-and-wordpress-are-not-supported"></a>PHP および WordPress はサポートされていない

PHP および WordPress サイトはサポートされていません。 現時点では、これらのワークロードをサーバー側で監視するために正式にサポートされている SDK/エージェントはありません。 ただし、クライアント側の JavaScript を Web ページに追加することで PHP または WordPress サイトでクライアント側のトランザクションを手動でインストルメント化するのは、[JavaScript SDK](./javascript.md) を使用することで実行できます。

以下の表は、これらの値の意味、その根本的な原因、推奨される修正について詳細に説明したものです。

|問題の値|説明|Fix
|---- |----|---|
| `AppAlreadyInstrumented:true` | この値は、SDK の一部の側面が既にアプリケーションに存在することが拡張機能で検出され、拡張機能が停止されることを示します。 これは、`System.Diagnostics.DiagnosticSource`、`Microsoft.AspNet.TelemetryCorrelation`、または `Microsoft.ApplicationInsights` への参照が原因である可能性があります  | これらの参照を削除します。 これらの参照の一部は、特定の Visual Studio テンプレートによって既定で追加されており、以前のバージョンの Visual Studio で `Microsoft.ApplicationInsights` への参照が追加されている可能性があります。
|`AppAlreadyInstrumented:true` | アプリケーションで ASP.NET Core 2.1 または 2.2 をターゲットにしている場合、この値は、SDK の一部の側面が既にアプリケーションに存在することが拡張機能で検出され、拡張機能が停止されることを示します。 | .NET Core 2.1、2.2 をご利用のお客様は、代わりに Microsoft.AspNetCore.App メタパッケージを使用することを[お勧めします](https://github.com/aspnet/Announcements/issues/287)。 さらに、ポータルで [Interop with Application Insights SDK]\(Application Insights SDK との相互運用\) をオンにします (上記の手順を参照してください)。|
|`AppAlreadyInstrumented:true` | この値は、以前のデプロイのアプリ フォルダーに上記の dll が存在する場合でも発生する可能性があります。 | アプリ フォルダーを消去し、これらの dll が削除されたことを確認してください。 ローカル アプリの bin ディレクトリと App Service の wwwroot ディレクトリの両方を確認します。 (App Service Web アプリの wwwroot ディレクトリを確認するには、次のようにします。高度なツール (Kudu) > デバッグ コンソール > CMD > home\site\wwwroot)。
|`AppContainsAspNetTelemetryCorrelationAssembly: true` | この値は、アプリケーション内の `Microsoft.AspNet.TelemetryCorrelation` への参照が拡張機能で検出され、拡張機能が停止されることを示します。 | 参照を削除します。
|`AppContainsDiagnosticSourceAssembly**:true`|この値は、アプリケーション内の `System.Diagnostics.DiagnosticSource` への参照が拡張機能で検出され、拡張機能が停止されることを示します。| ASP.NET の場合は、参照を削除します。 
|`IKeyExists:false`|この値は、インストルメンテーション キーが AppSetting `APPINSIGHTS_INSTRUMENTATIONKEY` に存在しないことを示します。 考えられる原因:値が誤って削除された、自動化スクリプトで値を設定し忘れたなどの原因が考えられます。 | 設定が App Service アプリケーション設定に存在することを確認します。

### <a name="appinsights_javascript_enabled-and-urlcompression-is-not-supported"></a>APPINSIGHTS_JAVASCRIPT_ENABLED と urlCompression はサポートされていません

APPINSIGHTS_JAVASCRIPT_ENABLED=true を使用する場合 (この場合、コンテンツがエンコードされます)、次のようなエラーが発生する可能性があります。 

- 500 URL 書き換えエラー
- 500.53 URL 書き換えモジュール エラー。HTTP 応答のコンテンツがエンコード ('gzip') されている場合、アウトバウンド書き換えルールを適用することはできませんというメッセージを伴います。 

これは、APPINSIGHTS_JAVASCRIPT_ENABLED アプリケーション設定が true に設定されており、同時に content-encoding が存在することが原因です。 このシナリオはまだサポートされていません。 回避策は、アプリケーション設定から APPINSIGHTS_JAVASCRIPT_ENABLED を削除することです。 残念ながら、これは、クライアント/ブラウザー側の JavaScript インストルメンテーションがまだ必要な場合、Web ペーでは手動の SDK 参照が必要であることを意味します。 JavaScript SDK での手動のインストルメンテーションに関する[手順](https://github.com/Microsoft/ApplicationInsights-JS#snippet-setup-ignore-if-using-npm-setup)に従ってください。

Application Insights エージェント/拡張機能の最新情報については、[リリース ノート](https://github.com/MohanGsk/ApplicationInsights-Home/blob/master/app-insights-web-app-extensions-releasenotes.md)のページを参照してください。

### <a name="default-website-deployed-with-web-apps-does-not-support-automatic-client-side-monitoring"></a>Web アプリでデプロイされた既定の Web サイトでは、クライアント側の自動監視はサポートされません

Azure App Services で `ASP.NET` または `ASP.NET Core` ランタイムを使用して Web アプリを作成すると、1 つの静的な HTML ページがスターター Web サイトとしてデプロイされます。 静的な Web ページには、IIS の ASP.NET マネージド Web パーツも読み込まれます。 これにより、サーバー側のコードなし監視をテストすることはできますが、クライアント側の自動監視はサポートされません。

Azure App Services Web アプリで ASP.NET または ASP.NET Core に対するクライアント側のコードなし監視をテストする場合は、[ASP.NET Core Web アプリの作成](../../app-service/quickstart-dotnetcore.md)と [ASP.NET Framework Web アプリの作成](../../app-service/quickstart-dotnet-framework.md)に関する公式のガイドに従って、この記事にある監視を有効にするための手順を使用することをお勧めします。

### <a name="connection-string-and-instrumentation-key"></a>接続文字列とインストルメンテーション キー

コード不要の監視を使用している場合は、接続文字列のみが必要です。 ただし、手動のインストルメンテーションを実行する場合は、SDK の以前のバージョンとの下位互換性を維持するためにインストルメンテーション キーを設定することをお勧めします。

### <a name="difference-between-standard-metrics-from-application-insights-vs-azure-app-service-metrics"></a>Application Insights の標準メトリックと Azure App Service メトリックの違い

Application Insights では、アプリケーションに対して行われた要求のテレメトリを収集します。 WebApps/IIS でエラーが発生し、要求がユーザー アプリケーションに到達しなかった場合、Application Insights にはそれに関するテレメトリがありません。

Application Insights によって計算された `serverresponsetime` の期間は、必ずしも Web Apps によって監視されているサーバーの応答時間と一致しません。 これは、Application Insights では、要求がユーザー アプリケーションに実際に到達した場合の期間のみがカウントされるためです。 IIS で要求が停止しているか、キューに入れられた場合、その待機時間は Web Apps のメトリックに含まれますが、Application Insights のメトリックには含まれません。

## <a name="release-notes"></a>リリース ノート

最新の更新プログラムとバグ修正については、[リリース ノートを参照してください](./web-app-extension-release-notes.md)。

## <a name="next-steps"></a>次のステップ
* [実行中のアプリに対してプロファイラーを実行](./profiler.md)します。
* [Azure Functions](https://github.com/christopheranderson/azure-functions-app-insights-sample) - Application Insights で Azure Functions を監視する
* [Azure Diagnostics](../agents/diagnostics-extension-to-application-insights.md) が Application Insights に送信されるように設定します。
* [サービスの正常性メトリックを監視](../data-platform.md)して、サービスの可用性と応答性を確認します。
* 操作イベントが発生したり、メトリックがしきい値を超えたりするたびに、[アラート通知を受け取り](../alerts/alerts-overview.md)ます。
* [JavaScript のアプリや Web ページに Application Insights](javascript.md) を使用して、Web ページを参照しているブラウザーからクライアント テレメトリを取得します。
* [可用性 Web テストを設定](monitor-web-app-availability.md) して、サイトがダウンした場合にアラートを送信するようにします。
