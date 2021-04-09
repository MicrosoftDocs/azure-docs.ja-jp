---
title: Cloud Services (クラシック) で Azure Diagnostics (.NET) を使用する方法 | Microsoft Docs
description: デバッグ、パフォーマンスの測定、監視、トラフィック分析などに使用するために、Azure Diagnostics を使用して、Azure Cloud Services からデータを収集します。
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 97e68d338580132b6927c4cc8b206db60fe93ba2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101703509"
---
# <a name="enabling-azure-diagnostics-in-azure-cloud-services-classic"></a>Azure Cloud Services (クラシック) での Azure Diagnostics の有効化

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そして、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

Azure 診断の背景については、「 [What is Microsoft Azure Diagnostics](../azure-monitor/agents/diagnostics-extension-overview.md) 」をご覧ください。

## <a name="how-to-enable-diagnostics-in-a-worker-role"></a>Worker ロールの診断を有効にする方法
このチュートリアルでは、.NET EventSource クラスを使用してテレメトリ データを生成する Azure Worker ロールの実装方法について説明します。 Azure Diagnostics を使用してテレメトリ データを収集し、これを Azure ストレージ アカウントに格納します。 Worker ロールを作成すると、Visual Studio は Azure SDK for .NET 2.4 以降でソリューションの一部として自動的に診断 1.0 を有効にします。 次の手順では、Worker ロールの作成、ソリューションからの診断、1.0 の無効化、Worker ロールへの診断、1.2 または 1.3 のデプロイに関するプロセスについて説明します。

### <a name="prerequisites"></a>前提条件
この記事では、Azure サブスクリプションがあり、Azure SDK で Visual Studio を使用していることを前提としています。 Azure サブスクリプションがない場合でも、 [無料試用版][Free Trial]にサインアップできます。 [Azure PowerShell Version 0.8.7 以降をインストールして構成している][Install and configure Azure PowerShell version 0.8.7 or later]ことを確認してください。

### <a name="step-1-create-a-worker-role"></a>手順 1. worker ロールを作成する
1. **Visual Studio** を起動します。
2. .NET Framework 4.5 をターゲットとする **[クラウド]** テンプレートから、**Azure クラウド サービス** プロジェクトを作成します。  プロジェクト名を「WadExample」と入力し、[OK] をクリックします。
3. **[worker ロール]** を選択して [OK] をクリックします。 プロジェクトが作成されます。
4. **ソリューション エクスプローラー** で、**WorkerRole1** プロパティ ファイルをダブルクリックします。
5. **[構成]** タブで、**[診断を有効にする]** をオフにして診断 1.0 (Azure SDK 2.4 以前) を無効にします。
6. ソリューションを構築してエラーが発生しないことを確認します。

### <a name="step-2-instrument-your-code"></a>手順 2. コードをインストルメント化する
WorkerRole.cs の内容を次のコードに置き換えます。 [EventSource クラス][EventSource Class]から継承された SampleEventSourceWriter クラスは、4 つのログの作成方法 (**SendEnums**、**MessageMethod**、**SetOther**、**HighFreq**) を実装しています。 **WriteEvent** メソッドの最初のパラメーターは各イベントの ID を定義しています。 Run メソッドは、 **SampleEventSourceWriter** クラスに実装されているログ作成方法をぞれぞれ 10 秒ごとに呼び出す無限ループを実装します。

```csharp
using Microsoft.WindowsAzure.ServiceRuntime;
using System;
using System.Diagnostics;
using System.Diagnostics.Tracing;
using System.Net;
using System.Threading;

namespace WorkerRole1
{
    sealed class SampleEventSourceWriter : EventSource
    {
        public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
        public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); }// Cast enums to int for efficient logging.
        public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
        public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
        public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }

    }

    enum MyColor
    {
        Red,
        Blue,
        Green
    }

    [Flags]
    enum MyFlags
    {
        Flag1 = 1,
        Flag2 = 2,
        Flag3 = 4
    }

    public class WorkerRole : RoleEntryPoint
    {
        public override void Run()
        {
            // This is a sample worker implementation. Replace with your logic.
            Trace.TraceInformation("WorkerRole1 entry point called");

            int value = 0;

            while (true)
            {
                Thread.Sleep(10000);
                Trace.TraceInformation("Working");

                // Emit several events every time we go through the loop
                for (int i = 0; i < 6; i++)
                {
                    SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
                }

                for (int i = 0; i < 3; i++)
                {
                    SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                    SampleEventSourceWriter.Log.SetOther(true, 123456789);
                }

                if (value == int.MaxValue) value = 0;
                SampleEventSourceWriter.Log.HighFreq(value++);
            }
        }

        public override bool OnStart()
        {
            // Set the maximum number of concurrent connections
            ServicePointManager.DefaultConnectionLimit = 12;

            // For information on handling configuration changes
            // see the MSDN topic at https://go.microsoft.com/fwlink/?LinkId=166357.

            return base.OnStart();
        }
    }
}
```


### <a name="step-3-deploy-your-worker-role"></a>手順 3. worker ロールをデプロイする

[!INCLUDE [cloud-services-wad-warning](../../includes/cloud-services-wad-warning.md)]

1. ソリューション エクスプローラーで **[WadExample]** プロジェクトを選択し、**[ビルド]** メニューから **[発行]** を選択して、worker ロールを Visual Studio から Azure にデプロイします。
2. サブスクリプションを選択します。
3. **[Microsoft Azure 発行設定]** ダイアログで、**[新規作成]** を選択します。
4. **[クラウド サービスとストレージ アカウントの作成]** ダイアログで **[名前]** を入力し ("WadExample" など)、リージョンまたはアフィニティ グループを選択します。
5. **[環境]** を **[ステージング]** に設定します。
6. 必要に応じて、他の **[設定]** を変更し、**[発行]** をクリックします。
7. デプロイが完了したら、クラウド サービスが **[実行中]** 状態になっていることを Azure Portal で確認します。

### <a name="step-4-create-your-diagnostics-configuration-file-and-install-the-extension"></a>手順 4. 診断構成ファイルを作成して拡張機能をインストールする
1. 次の PowerShell コマンドを実行して、パブリック構成ファイルのスキーマ定義をダウンロードします。

    ```powershell
    (Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
    ```
2. **WorkerRole1** プロジェクトを右クリックし、**[追加]** -> **[新しいアイテム]** の順に選択して、XML ファイルを **WorkerRole1** プロジェクトに追加します。 ->  **[Visual C# アイテム]**  ->  **[データ]**  ->  **[XML ファイル]** の順に選びます ファイルに「WadExample.xml」という名前を付けます。

   ![CloudServices_diag_add_xml](./media/cloud-services-dotnet-diagnostics/AddXmlFile.png)
3. 構成ファイルに WadConfig.xsd を関連付けます。 WadExample.xml エディター ウィンドウがアクティブになっていることを確認します。 **F4** キーを押し、 **[プロパティ]** ウィンドウを開きます。 **[プロパティ]** ウィンドウで **[スキーマ]** プロパティをクリックします。 **[スキーマ]** プロパティで in the **[…]** をクリックします。 **[追加]**  ボタンをクリックし、XSD ファイルを保存した場所に移動して [WadConfig.xsd] を選択します。 **[OK]** をクリックします。

4. WadExample.xml 構成ファイルの内容を次の XML に置き換え、ファイルを保存します。 この構成ファイルは、収集するいくつかのパフォーマンス カウンターを定義します。1 つは CPU 使用率、1 つはメモリ使用率です。 次に、SampleEventSourceWriter クラスのメソッドに対応する 4 つのイベントを定義します。

```xml
<?xml version="1.0" encoding="utf-8"?>
<PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <WadCfg>
    <DiagnosticMonitorConfiguration overallQuotaInMB="25000">
      <PerformanceCounters scheduledTransferPeriod="PT1M">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT1M" unit="percent" />
        <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT1M" unit="bytes"/>
      </PerformanceCounters>
      <EtwProviders>
        <EtwEventSourceProviderConfiguration provider="SampleEventSourceWriter" scheduledTransferPeriod="PT5M">
          <Event id="1" eventDestination="EnumsTable"/>
          <Event id="2" eventDestination="MessageTable"/>
          <Event id="3" eventDestination="SetOtherTable"/>
          <Event id="4" eventDestination="HighFreqTable"/>
          <DefaultEvents eventDestination="DefaultTable" />
        </EtwEventSourceProviderConfiguration>
      </EtwProviders>
    </DiagnosticMonitorConfiguration>
  </WadCfg>
</PublicConfig>
```

### <a name="step-5-install-diagnostics-on-your-worker-role"></a>手順 5. worker ロールに診断をインストールする
Web ロールまたは Worker ロールの診断を管理する PowerShell コマンドレットは、Set-AzureServiceDiagnosticsExtension、Get-AzureServiceDiagnosticsExtension、Remove-AzureServiceDiagnosticsExtension です。

1. Azure PowerShell を開きます。
2. スクリプトを実行して Worker ロールに診断をインストールします (*StorageAccountKey* を wadexample ストレージ アカウントのストレージ アカウント キーに、*config_path* を *WadExample.xml* ファイルへのパスに置き換えます)。

```powershell
$storage_name = "wadexample"
$key = "<StorageAccountKey>"
$config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExample\WorkerRole1\WadExample.xml"
$service_name="wadexample"
$storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key
Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext -DiagnosticsConfigurationPath $config_path -ServiceName $service_name -Slot Staging -Role WorkerRole1
```

### <a name="step-6-look-at-your-telemetry-data"></a>手順 6. テレメトリ データを確認する
Visual Studio の **サーバー エクスプローラー** で、wadexample ストレージ アカウントに移動します。 クラウド サービスを 5 分程実行すると、**WADEnumsTable**、**WADHighFreqTable**、**WADMessageTable**、**WADPerformanceCountersTable**、**WADSetOtherTable** の各テーブルが表示されます。 いずれかのテーブルをダブルクリックして、収集した利用統計情報を表示します。

![CloudServices_diag_tables](./media/cloud-services-dotnet-diagnostics/WadExampleTables.png)

## <a name="configuration-file-schema"></a>構成ファイル スキーマ
診断構成ファイルでは、診断エージェントの起動時に診断構成設定の初期化に使用される値を定義します。 有効な値と例については、「 [Azure 診断構成スキーマ](../azure-monitor/agents/diagnostics-extension-versions.md) 」をご覧ください。

## <a name="troubleshooting"></a>トラブルシューティング
問題が発生した場合、一般的な問題の解決方法については、「 [Azure Diagnostics Troubleshooting](../azure-monitor/agents/diagnostics-extension-troubleshooting.md) 」をご覧ください。

## <a name="next-steps"></a>次のステップ
収集するデータの変更、問題のトラブルシューティング、または一般的な診断の詳細については、[関連する Azure 仮想マシンの診断に関する記事の一覧](../azure-monitor/agents/diagnostics-extension-overview.md)をご覧ください。

[EventSource Class]: /dotnet/api/system.diagnostics.tracing.eventsource

[Debugging an Azure Application]: https://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: /previous-versions/azure/gg433048(v=azure.100)
[Free Trial]: https://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: /powershell/azure/