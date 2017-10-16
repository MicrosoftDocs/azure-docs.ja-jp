---
title: "Azure Compute リソースの Azure Application Insights Profiler を有効化する | Microsoft Docs"
description: "Azure Cloud Services リソースによってホストされる ASP.NET アプリケーションに Profiler を設定する方法を説明します。"
services: application-insights
documentationcenter: 
author: CFreemanwa
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: bwren
ms.openlocfilehash: 65ba755f35df7bd09dd652ac6fccf96a878c6ca9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="enable-application-insights-profiler-on-an-azure-cloud-services-resource"></a>Azure Application Insights Profiler を Cloud Services リソースで有効にする

このチュートリアルでは、Azure Cloud Services リソースによってホストされる ASP.NET アプリケーションで Azure Application Insights Profiler を有効にする方法を示します。 例には、Azure Virtual Machines、仮想マシン スケール セット、および Azure Services Fabric のサポートが含まれています。 すべての例は、Azure Resource Manager デプロイメント モデルをサポートするテンプレートを活用しています。 デプロイメント モデルについて詳しくは、「[Azure Resource Manager とクラシック デプロイ: デプロイ モデルとリソースの状態について](/azure-resource-manager/resource-manager-deployment-model)」を参照してください。

## <a name="overview"></a>概要

次の図に、プロファイラーが Azure Cloud Services リソースを利用する方法を示します。 例として、Azure 仮想マシンを使用しています。

![概要](./media/enable-profiler-compute/overview.png) Azure ポータルで処理して表示する情報を収集するには、Azure Cloud Services リソース用の診断エージェント コンポーネントをインストールする必要があります。 このチュートリアルの残りの部分では、診断エージェントをインストールして構成し、Application Insights Profiler を有効にする方法を説明します。

## <a name="prerequisites-for-the-walkthrough"></a>このチュートリアルの前提条件

* profiler エージェントを VM ([WindowsVirtualMachine.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)) またはスケール セット ([WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json)) にインストールするデプロイメント Resource Manager テンプレート。

* プロファイリングのために有効化されている Application Insights インスタンス。 手順については、「[プロファイルを有効にする](https://docs.microsoft.com/en-us/azure/application-insights/app-insights-profiler#enable-the-profiler)」を参照してください。

* ターゲットの Azure Cloud Services リソースにインストールされている .NET Framework 4.6.1 以降。

## <a name="create-a-resource-group-in-your-azure-subscription"></a>Azure サブスクリプションにリソース グループを作成する
PowerShell スクリプトを使用してリソース グループを作成する方法を次の例で説明します。

```
New-AzureRmResourceGroup -Name "Replace_With_Resource_Group_Name" -Location "Replace_With_Resource_Group_Location"
```

## <a name="create-an-application-insights-resource-in-the-resource-group"></a>リソース グループに Application Insights リソースを作成する
**[Application Insights]** ブレードで、この例で示すように、リソースの情報を入力します。 

![[Application Insights] ブレード](./media/enable-profiler-compute/createai.png)

## <a name="apply-an-application-insights-instrumentation-key-in-the-azure-resource-manager-template"></a>Azure Resource Manager テンプレートの Application Insights のインストルメンテーション キーを適用する

1. テンプレートをまだダウンロードしていない場合は、[GitHub](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachine.json)からダウンロードしてください。

2. Application Insights キーを見つけます。
   
   ![キーの場所](./media/enable-profiler-compute/copyaikey.png)

3. テンプレートの値を置換します。
   
   ![テンプレート内の置換される値](./media/enable-profiler-compute/copyaikeytotemplate.png)

## <a name="create-an-azure-vm-to-host-the-web-application"></a>Web アプリケーションをホストする Azure VM を作成する
1. パスワードを保護するセキュアな文字列を作成します。

   ```
   $password = ConvertTo-SecureString -String "Replace_With_Your_Password" -AsPlainText -Force
   ```

2. Azure Resource Manager テンプレートをデプロイします。

   PowerShell コンソール内のディレクトリを Resource Manager テンプレートが含まれているフォルダーに変更します。 テンプレートをデプロイするには、次のコマンドを実行します。

   ```
   New-AzureRmResourceGroupDeployment -ResourceGroupName "Replace_With_Resource_Group_Name" -TemplateFile .\WindowsVirtualMachine.json -adminUsername "Replace_With_your_user_name" -adminPassword $password -dnsNameForPublicIP "Replace_WIth_your_DNS_Name" -Verbose
   ```

スクリプトが正常に実行されると、リソース グループ内に **MyWindowsVM** という名前の VM ができます。

## <a name="configure-web-deploy-on-the-vm"></a>VM での Web デプロイの構成
Web アプリケーションを Visual Studio から公開できるように、VM で Web 配置が有効になっていることを確認してください。

WebPI 経由で VM に Web 配置を手動でインストールするには、[IIS 8.0 以降への Web 配置のインストールと構成](https://docs.microsoft.com/en-us/iis/install/installing-publishing-technologies/installing-and-configuring-web-deploy-on-iis-80-or-later)に関するページを参照してください。 Azure Resource Manager テンプレートを使用して Web 配置のインストールを自動化する方法の例については、[Azure VM への Web アプリの作成、構成、および配置](https://azure.microsoft.com/en-us/resources/templates/201-web-app-vm-dsc/)に関するページを参照してください。

ASP.NET MVC アプリケーションをデプロイする場合は、サーバー マネージャーに移動し、**[役割と機能の追加]** > **[Web サーバー (IIS)]** > **[Web サーバー]** > **[アプリケーション開発]** を選択して、サーバーで ASP.NET 4.5 を有効にします。

![Add ASP.NET を追加する](./media/enable-profiler-compute/addaspnet45.png)

## <a name="install-the-azure-application-insights-sdk-for-your-project"></a>Azure Application Insights SDK をプロジェクトにインストールする
1. Visual Studio で ASP.NET Web アプリケーションを開きます。

2. プロジェクトを右クリックし、**[追加]** > **[接続済みサービス]** を選択します。

3. **[Application Insights]**を選択します。

4. このページで説明されている手順に従います。 先ほど作成した Application Insights リソースを選択します。

5. **[登録]** ボタンをクリックします。


## <a name="publish-the-project-to-an-azure-vm"></a>プロジェクトを Azure VM に公開する
Azure VM にアプリケーションを公開するにはいくつかの方法があります。 1 つの方法は、Visual Studio 2017 を使用することです。

1. プロジェクトを右クリックし、**[発行]** を選択します。

2. 公開ターゲットとして **[Microsoft Azure Virtual Machines]** を選択し、次の手順に従います。

   ![Publish-FromVS](./media/enable-profiler-compute/publishtoVM.png)

3. アプリケーションに対してロード テストを実行します。 結果は、Application Insights のインスタンス ポータルの Web ページに表示されます。


## <a name="enable-the-profiler"></a>プロファイラーを有効にする

1. Profiler を構成するために、Application Insights の **[パフォーマンス]** ブレードに移動し、右上の **[Profiler]** をクリックします。

   ![Profiler の構成ボタン](./media/enable-profiler-compute/PerformanceTriageViewPofilerButton.png)

2. **[Profiler の有効化]** を選択します。

   ![[Profiler の有効化] ボタン](./media/enable-profiler-compute/enableprofiler2.png)


## <a name="add-a-performance-test-to-your-application"></a>アプリケーションにパフォーマンス テストを追加する
次の手順に従うと、Application Insights Profiler に表示されるサンプル データを収集できます。

1. 先ほど作成した Application Insights リソースを参照します。 

2. **[可用性]** ブレードに移動し、アプリケーションの URL に Web リクエストを送信するパフォーマンス テストを追加します。 

   ![パフォーマンス テストを追加する](./media/enable-profiler-compute/AvailabilityTest.png)

## <a name="view-your-performance-data"></a>パフォーマンス データの表示

1. Profiler がデータを収集して分析するまで 10 ～ 15 分待ちます。 

2. Application Insights リソースの **[パフォーマンス]** ブレードに移動し、アプリケーションに負荷がかかったときのパフォーマンスを表示します。 操作グリッドを [カウント] 列の値で並べ替えて、十分な使用量があり低速な、興味を引く操作に注目します。 期間分布の上にある [Profiler] レーンを見て、プロファイラー トレースがある期間範囲を確認します。 アプリケーションを監視する時間が長くなればなるほど Profiler によって収集されるトレースが多くなるため、プロファイラー トレースでサポートされる豊富なコード レベルの例でより多くの分布がカバーされることに注意してください。 

   ![パフォーマンス トリアージ ビューのプロファイラー トレース](./media/enable-profiler-compute/PerformanceTriageViewProfilerTraces.png)

    95 パーセンタイル付近の 3 番目のスパイクなど、興味を引く期間範囲にズームインすることができます。 これにより、[Take Action]\(アクションの実行\) ボタンに表示されるサンプルとプロファイラー トレースの数が制限されます。 

    ![期間範囲へのズームイン](./media/enable-profiler-compute/DurationRangeZoomedTo95th.png)

    **[Profiler traces]\(Profiler トレース\)** ボタンをクリックして、適切なトレースがある Profiler を開きます。

3. **[例]** の下のアイコンを選択して **[トレース ビュー]** ブレードを開きます。

   ![[トレース ビュー] ブレードを開く](./media/enable-profiler-compute/traceview.png)


## <a name="work-with-an-existing-template"></a>既存のテンプレートの使用

1. デプロイ テンプレートで Azure 診断 リソース宣言を見つけます。
   
   宣言がない場合は、次の例に示した宣言に似た宣言を作成できます。 [Azure Resource Explorer Web サイト](https://resources.azure.com)でテンプレートを更新できます。

2. 公開元を `Microsoft.Azure.Diagnostics` から `AIP.Diagnostics.Test` に変更します。

3. `typeHandlerVersion` では `0.0` を使用します。

4. `autoUpgradeMinorVersion` が `true` に設定されていることを確認します。

5. 次の例に示すように、新しい `ApplicationInsightsProfiler` シンク インスタンスを `WadCfg` オブジェクトに追加します。

```
"resources": [
        {
          "type": "extensions",
          "name": "Microsoft.Insights.VMDiagnosticsSettings",
          "apiVersion": "2016-03-30",
          "properties": {
            "publisher": "AIP.Diagnostics.Test",
            "type": "IaaSDiagnostics",
            "typeHandlerVersion": "0.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
              "WadCfg": {
                "SinksConfig": {
                  "Sink": [
                    {
                      "name": "Give a descriptive short name. E.g.: MyApplicationInsightsProfilerSink",
                      "ApplicationInsightsProfiler": "Enter the Application Insights instance instrumentation key guid here"
                    }
                  ]
                },
                "DiagnosticMonitorConfiguration": {
                    ...
                }
                ...
              }
              ...
            }
            ...
          }
          ...
]
```

## <a name="enable-the-profiler-on-virtual-machine-scale-sets"></a>仮想マシン スケール セットで Profiler を有効にする
Profiler を有効にする方法を確認するには、[WindowsVirtualMachineScaleSet.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/WindowsVirtualMachineScaleSet.json) テンプレートをダウンロードします。 VM テンプレートと同じ変更を、仮想マシン スケール セットの診断拡張リソースに適用します。

スケール セット内の各インスタンスが、インターネットにアクセスできることを確認します。 これで、Profiler Agent は、収集されたサンプルを、表示と分析のために Application Insights に送信できます。

## <a name="enable-the-profiler-on-service-fabric-applications"></a>Service Fabric アプリケーションで Profiler を有効化する
1. Profiler Agent をインストールする Azure 診断拡張を持つように Service Fabric クラスターをプロビジョニングします。

2. Application Insights SDK をプロジェクトにインストールし、Application Insights キーを構成します。

3. テレメトリをインストルメント化するアプリケーション コードを追加します。

### <a name="provision-the-service-fabric-cluster-to-have-the-azure-diagnostics-extension-that-installs-the-profiler-agent"></a>Profiler Agent をインストールする Azure 診断拡張を持つように Service Fabric クラスターをプロビジョニングします。
Service Fabric クラスターはセキュリティ保護するかしないかを選択できます。 1 つのゲートウェイ クラスターをセキュリティ保護なしの設定にすることで、アクセスするための証明書を不要にすることができます。 ビジネス ロジックとデータをホストするクラスターは、セキュリティで保護する必要があります。 Profiler は、セキュリティ保護されている Service Fabric クラスターとセキュリティ保護されていない Service Fabric クラスターの両方で有効にすることができます。 このチュートリアルでは、Profiler を有効にするために必要な変更を説明する目的で、セキュリティ保護されていないクラスターを例として使用しています。 セキュリティ保護されたクラスターも同じ方法でプロビジョニングできます。

1. [ServiceFabricCluster.json](https://github.com/Azure/azure-docs-json-samples/blob/master/application-insights/ServiceFabricCluster.json) をダウンロードします。 VM と仮想マシン スケール セット用の操作と同じように、`Application_Insights_Key` を Application Insights キーで置き換えます。

   ```
   "publisher": "AIP.Diagnostics.Test",
                 "settings": {
                   "WadCfg": {
                     "SinksConfig": {
                       "Sink": [
                         {
                           "name": "MyApplicationInsightsProfilerSinkVMSS",
                           "ApplicationInsightsProfiler": "[Application_Insights_Key]"
                         }
                       ]
                     },
   ```

2. PowerShell スクリプトを使用してテンプレートをデプロイします。

   ```
   Login-AzureRmAccount
   New-AzureRmResourceGroup -Name [Your_Resource_Group_Name] -Location [Your_Resource_Group_Location] -Verbose -Force
   New-AzureRmResourceGroupDeployment -Name [Choose_An_Arbitrary_Name] -ResourceGroupName [Your_Resource_Group_Name] -TemplateFile [Path_To_Your_Template]

   ```

### <a name="install-the-application-insights-sdk-in-the-project-and-configure-the-application-insights-key"></a>Application Insights SDK をプロジェクトにインストールし、Application Insights キーを構成します。
Install Application Insights SDK を [NuGet パッケージ](https://www.nuget.org/packages/Microsoft.ApplicationInsights.Web/)からインストールします。 必ず、安定バージョンの 2.3 以降をインストールしてください。 

プロジェクトでの Application Insights の構成の詳細については、「[Using Service Fabric with Application Insights](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/blob/dev/appinsights/ApplicationInsights.md)」(Service Fabric と Application Insights の使用) を参照してください。

### <a name="add-application-code-to-instrument-telemetry"></a>テレメトリをインストルメント化するアプリケーション コードを追加する
1. インストルメント化するすべてのコードについて、その近くに using ステートメントを追加します。 

   次の例では、`RunAsync` メソッドが何らかの動作を行い、`telemetryClient` クラスがテレメトリの開始後にそれをキャプチャします。 イベントにはアプリケーション全体で一意の名前を付ける必要があります。

   ```
   protected override async Task RunAsync(CancellationToken cancellationToken)
       {
           // TODO: Replace the following sample code with your own logic
           //       or remove this RunAsync override if it's not needed in your service.

           while (true)
           {
               using( var operation = telemetryClient.StartOperation<RequestTelemetry>("[Insert_Event_Unique_Name]"))
               {
                   cancellationToken.ThrowIfCancellationRequested();

                   ++this.iterations;

                   ServiceEventSource.Current.ServiceMessage(this.Context, "Working-{0}", this.iterations);

                   await Task.Delay(TimeSpan.FromSeconds(1), cancellationToken);
               }

           }
       }
   ```

2. Service Fabric クラスターにアプリケーションをデプロイします。 アプリの実行まで 10 分待ちます。 さらに効果を高めるため、アプリのロード テストも実行できます。 Application Insights ポータルの **[パフォーマンス]** ブレードで、プロファイリング トレースの例が表示されるのを確認できます。

<!---
Commenting out these sections for now
## Enable the Profiler on Cloud Services applications
[TODO]
## Enable the Profiler on classic Azure Virtual Machines
[TODO]
## Enable the Profiler on on-premise servers
[TODO]
--->

## <a name="next-steps"></a>次のステップ

- Profiler の問題をトラブルシューティングするためのヘルプを [Profilerのトラブルシューティング](app-insights-profiler.md#troubleshooting)に関する項で探します。

- [Application Insights Profiler](app-insights-profiler.md) で profiler の詳細を確認します。
