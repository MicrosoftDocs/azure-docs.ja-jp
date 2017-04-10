---
title: "Virtual Machines で Azure 診断を使用する方法 | Microsoft Docs"
description: "デバッグ、パフォーマンスの測定、監視、トラフィック分析などに使用するために、Azure 診断を使用して、Azure Virtual Machines からデータを収集します。"
services: virtual-machines
documentationcenter: .net
author: davidmu1
manager: 
editor: 
ms.assetid: dfaabc7a-23e7-4af0-8369-f504d2915b3d
ms.service: virtual-machines
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 02/16/2016
ms.author: davidmu
translationtype: Human Translation
ms.sourcegitcommit: 424d8654a047a28ef6e32b73952cf98d28547f4f
ms.openlocfilehash: 529c723a9e071b7cc388cf92423c1f74707cb831
ms.lasthandoff: 03/22/2017


---
# <a name="enabling-diagnostics-in-azure-virtual-machines"></a>Azure Virtual Machines での診断の有効化
Azure 診断の背景については、「 [What is Microsoft Azure Diagnostics](../monitoring-and-diagnostics/azure-diagnostics.md) 」をご覧ください。

## <a name="how-to-enable-diagnostics-in-a-virtual-machine"></a>仮想マシンの診断を有効にする方法
このチュートリアルでは、開発コンピューターから Azure の仮想マシンに診断をリモートでインストールする方法について説明します。 また、その Azure の仮想マシン上で実行するアプリケーションの実装方法と .NET [EventSource クラス][EventSource Class]を使用したテレメトリデータの生成方法を学習します。 Azure 診断を使用してテレメトリを収集し、これを Azure ストレージ アカウントに格納します。

### <a name="pre-requisites"></a>前提条件
このチュートリアルでは、Azure サブスクリプションがあり、Azure SDK で Visual Studio 2013 を使用していることを前提としています。 Azure サブスクリプションがない場合でも、[無料試用版][Free Trial]にサインアップできます。 [Azure PowerShell Version 0.8.7 以降をインストールして構成している][Install and configure Azure PowerShell version 0.8.7 or later]ことを確認してください。

### <a name="step-1-create-a-virtual-machine"></a>手順 1. 仮想マシンを作成する
1. 開発コンピューター上で Visual Studio 2013 を起動します。
2. Visual Studio の**サーバー エクスプローラー**で、**[Azure]** を展開し、**[仮想マシン]** を右クリックして、**[仮想マシンの作成]** を選択します。
3. **[サブスクリプションの選択]** ダイアログで Azure サブスクリプションを選択し、**[次へ]** をクリックします。
4. **[仮想マシン イメージの選択]** ダイアログで **[Windows Server 2012 R2 Datacenter, November 2014]** を選択し、**[次へ]** をクリックします。
5. **[仮想マシン基本設定]**で、仮想マシンに「wadexample」という名前を付けます。 管理者ユーザー名とパスワードを設定し、 **[次へ]**をクリックします。
6. **[クラウド サービスの設定]** ダイアログで「wadexampleVM」という名前の新しいクラウド サービスを作成します。 「wadexample」という名前の新しいストレージ アカウントを作成し、 **[次へ]**をクリックします。
7. **[作成]**をクリックします。

### <a name="step-2-create-your-application"></a>手順 2. アプリケーションを作成する
1. 開発コンピューター上で Visual Studio 2013 を起動します。
2. .NET Framework 4.5 をターゲットとする Visual C# の新しいコンソール アプリケーションを作成します。 プロジェクト名として「WadExampleVM」と入力します。
   ![CloudServices_diag_new_project](./media/virtual-machines-dotnet-diagnostics/NewProject.png)
3. Program.cs の内容を次のコードに置き換えます。 **SampleEventSourceWriter** クラスは、4 つのログの作成方法 (**SendEnums**、**MessageMethod**、**SetOther**、**HighFreq**) を実装しています。 WriteEvent メソッドの最初のパラメーターは各イベントの ID を定義しています。 Run メソッドは、 **SampleEventSourceWriter** クラスに実装されているログ作成方法をぞれぞれ 10 秒ごとに呼び出す無限ループを実装します。

    ```csharp
     using System;
     using System.Diagnostics;
     using System.Diagnostics.Tracing;
     using System.Threading;

     namespace WadExampleVM
     {
       sealed class SampleEventSourceWriter : EventSource {
         public static SampleEventSourceWriter Log = new SampleEventSourceWriter();
         public void SendEnums(MyColor color, MyFlags flags) { if (IsEnabled())  WriteEvent(1, (int)color, (int)flags); } // Cast enums to int for efficient logging.
         public void MessageMethod(string Message) { if (IsEnabled())  WriteEvent(2, Message); }
         public void SetOther(bool flag, int myInt) { if (IsEnabled())  WriteEvent(3, flag, myInt); }
         public void HighFreq(int value) { if (IsEnabled()) WriteEvent(4, value); }
       }

       enum MyColor {
         Red,
         Blue,
         Green
       }

       [Flags]
       enum MyFlags {
         Flag1 = 1,
         Flag2 = 2,
         Flag3 = 4
       }

       class Program
       {
         static void Main(string[] args) {
         Trace.TraceInformation("My application entry point called");

         int value = 0;

         while (true) {
             Thread.Sleep(10000);
             Trace.TraceInformation("Working");

             // Emit several events every time we go through the loop
             for (int i = 0; i < 6; i++) {
                 SampleEventSourceWriter.Log.SendEnums(MyColor.Blue, MyFlags.Flag2 | MyFlags.Flag3);
             }

             for (int i = 0; i < 3; i++) {
                 SampleEventSourceWriter.Log.MessageMethod("This is a message.");
                 SampleEventSourceWriter.Log.SetOther(true, 123456789);
             }

             if (value == int.MaxValue) value = 0;
             SampleEventSourceWriter.Log.HighFreq(value++);
         }

        }
      }
     }
     ```
4. ファイルを保存し、**[ビルド]** メニューから **[ソリューションのビルド]** を選択してコードをビルドします。

### <a name="step-3-deploy-your-application"></a>手順 3. アプリケーションをデプロイする
1. **ソリューション エクスプローラー**で **[WadExampleVM]** プロジェクトを右クリックし、**[エクスプローラーでフォルダーを開く]** を選択します。
2. *bin\Debug* フォルダーに移動し、すべてのファイル (WadExampleVM.*) をコピーします。
3. **サーバー エクスプローラー**で仮想マシンを右クリックし、**[リモート デスクトップを使用して接続]** を選択します。
4. VM に接続されたら、WadExampleVM という名前のフォルダーを作成し、コピーしたアプリケーション ファイルをそのフォルダーに貼り付けます。
5. アプリケーション ファイルの WadExampleVM.exe を起動します。 空白のコンソール ウィンドウが表示されます。

### <a name="step-4-create-your-diagnostics-configuration-and-install-the-extension"></a>手順 4. 診断構成を作成して拡張機能をインストールする
1. 次の PowerShell コマンドを実行して、パブリック構成ファイルのスキーマ定義を開発コンピューターにダウンロードします。

     2.(Get-AzureServiceAvailableExtension -ExtensionName 'PaaSDiagnostics' -ProviderNamespace 'Microsoft.Azure.Diagnostics').PublicConfigurationSchema | Out-File -Encoding utf8 -FilePath 'WadConfig.xsd'
2. 既に開いているプロジェクトか、プロジェクトを開かずに Visual Studio インスタンスで、Visual Studio の新しい XML ファイルを開きます。 Visual Studio で、**[追加]** -> **[新しい項目]** -> **[Visual C# アイテム]** -> **[データ]** -> **[XML ファイル]** の順に選択します。 ファイルに「WadExample.xml」という名前を付けます。
3. 構成ファイルに WadConfig.xsd を関連付けます。 WadExample.xml エディター ウィンドウがアクティブになっていることを確認します。 **F4** キーを押し、**[プロパティ]** ウィンドウを開きます。 **[プロパティ]** ウィンドウで **[スキーマ]** プロパティをクリックします。 **[スキーマ]** プロパティで  in the **[…]** をクリックします。 **[追加]** ボタンをクリックし、XSD ファイルを保存した場所に移動して [WadConfig.xsd] を選択します。 **[OK]**をクリックします。
4. WadExample.xml 構成ファイルの内容を次の XML に置き換え、ファイルを保存します。 この構成ファイルは、収集するいくつかのパフォーマンス カウンターを定義します。1 つは CPU 使用率、1 つはメモリ使用率です。 次に、SampleEventSourceWriter クラスのメソッドに対応する&4; つのイベントを定義します。

```
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

### <a name="step-5-remotely-install-diagnostics-on-your-azure-virtual-machine"></a>手順 5. Azure の仮想マシンに診断をリモートでインストールする
VM の診断を管理する PowerShell コマンドレットは、Set-AzureVMDiagnosticsExtension、Get-AzureVMDiagnosticsExtension、Remove-AzureVMDiagnosticsExtension です。

1. 開発コンピューター上で Azure PowerShell を開きます。
2. スクリプトを実行して VM に診断をリモートでインストールします ( *StorageAccountKey* を wadexamplevm ストレージ アカウントのストレージ アカウント キーに置き換えます)。

     $storage_name = "wadexamplevm"   $key = "<StorageAccountKey>"   $config_path="c:\users\<user>\documents\visual studio 2013\Projects\WadExampleVM\WadExampleVM\WadExample.xml"   $service_name="wadexamplevm"   $vm_name="WadExample"   $storageContext = New-AzureStorageContext -StorageAccountName $storage_name -StorageAccountKey $key   $VM1 = Get-AzureVM -ServiceName $service_name -Name $vm_name   $VM2 = Set-AzureVMDiagnosticsExtension -DiagnosticsConfigurationPath $config_path -Version "1.*" -VM $VM1 -StorageContext $storageContext   $VM3 = Update-AzureVM -ServiceName $service_name -Name $vm_name -VM $VM2.VM

### <a name="step-6-look-at-your-telemetry-data"></a>手順 6. テレメトリ データを確認する
Visual Studio の **[サーバー エクスプローラー]** で wadexample ストレージ アカウントに移動します。 VM を 5 分程実行すると、**WADEnumsTable**、**WADHighFreqTable**、**WADMessageTable**、**WADPerformanceCountersTable**、**WADSetOtherTable** の各テーブルが表示されます。 いずれかのテーブルをダブルクリックして、収集した利用統計情報を表示します。

![CloudServices_diag_wadexamplevm_tables](./media/virtual-machines-dotnet-diagnostics/WadExampleVMTables.png)

## <a name="configuration-file-schema"></a>構成ファイル スキーマ
診断構成ファイルでは、診断エージェントの起動時に診断構成設定の初期化に使用される値を定義します。 有効な値と例については、「 [Azure 診断構成スキーマ](https://msdn.microsoft.com/library/azure/mt634524.aspx) 」をご覧ください。

## <a name="troubleshooting"></a>トラブルシューティング
詳細については、「 [Azure Diagnostics Troubleshooting](../monitoring-and-diagnostics/azure-diagnostics-troubleshooting.md) 」をご覧ください。

## <a name="next-steps"></a>次のステップ
[仮想マシンに関連する Azure 診断に関する記事の一覧](../monitoring-and-diagnostics/azure-diagnostics.md#virtual-machines-using-azure-diagnostics) をご覧ください。

[EventSource Class]: http://msdn.microsoft.com/library/system.diagnostics.tracing.eventsource(v=vs.110).aspx

[Debugging an Azure Application]: http://msdn.microsoft.com/library/windowsazure/ee405479.aspx   
[Collect Logging Data by Using Azure Diagnostics]: http://msdn.microsoft.com/library/windowsazure/gg433048.aspx
[Free Trial]: http://azure.microsoft.com/pricing/free-trial/
[Install and configure Azure PowerShell version 0.8.7 or later]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

