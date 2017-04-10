---
title: "Azure 診断でのパフォーマンス カウンターの使用 | Microsoft Docs"
description: "Azure Cloud Services と仮想マシンで、パフォーマンス カウンターを使用して、ボトルネックの特定とパフォーマンスの調整を行います。"
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: tysonn
ms.assetid: fc4c61e9-d49d-4ed9-a32c-b91cdf851909
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/29/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 9cf1faabe3ea12af0ee5fd8a825975e30947b03a
ms.openlocfilehash: 55623820a74b5226471d642e9b960480f25b4390


---
# <a name="create-and-use-performance-counters-in-an-azure-application"></a>Azure アプリケーションでのパフォーマンス カウンターの作成と使用
この記事では、Azure アプリケーションにパフォーマンス カウンターを配置する利点と方法について説明します。 パフォーマンス カウンターを使用すると、データの収集、ボトルネックの特定、およびシステムとアプリケーションのパフォーマンスの調整を行うことができます。

また、Windows Server、IIS、ASP.NET で使用できるパフォーマンス カウンターでは、Azure の Web ロール、worker ロール、Virtual Machines のパフォーマンス データを収集して状態を把握することもできます。 カスタム パフォーマンス カウンターを作成して使用することもできます。  

パフォーマンス カウンターのデータを確認するには、

1. リモート デスクトップを使用してアクセスするパフォーマンス モニター ツールでアプリケーション ホストを直接調べるか、
2. System Center Operations Manager の Azure 管理パック、または
3. Azure Storage に転送された診断データにアクセスするその他の監視ツールを使用します。 詳細については、「 [Azure Storage への診断データの保存と表示](https://msdn.microsoft.com/library/azure/hh411534.aspx) 」を参照してください。  

[Azure クラシック ポータル](http://manage.azure.com/)でアプリケーションのパフォーマンスを監視する方法の詳細については、[Cloud Services の監視方法](https://www.azure.com/manage/services/cloud-services/how-to-monitor-a-cloud-service/)に関する記述を参照してください。

ログとトレース戦略を作成し、診断機能やその他の手法で問題のトラブルシューティングを行って Azure アプリケーションを最適化する方法の詳細なガイダンスについては、 [Azure アプリケーションの開発に関するトラブルシューティングのベスト プラクティス](https://msdn.microsoft.com/library/azure/hh771389.aspx)を参照してください。

## <a name="enable-performance-counter-monitoring"></a>パフォーマンス カウンターの監視を有効にする
パフォーマンス カウンターは既定では有効になっていません。 アプリケーションまたはスタートアップ タスクで、各ロール インスタンスを監視する特定のパフォーマンス カウンターを含めるように、診断エージェントの既定の構成を変更する必要があります。

### <a name="performance-counters-available-for-microsoft-azure"></a>Microsoft Azure で使用できるパフォーマンス カウンター
Azure は、Windows Server、IIS、および ASP.NET スタックで使用できるパフォーマンス カウンターのサブセットを備えています。 次の表に、Azure アプリケーションに特に関係のあるパフォーマンス カウンターの一部を示します。

| カウンター カテゴリ: オブジェクト (インスタンス) | カウンター名 | リファレンス |
| --- | --- | --- |
| .NET CLR Exceptions(*Global*) |# Exceps Thrown / sec |例外パフォーマンス カウンター |
| .NET CLR Memory(*Global*) |% Time in GC |メモリ パフォーマンス カウンター |
| ASP.NET |Application Restarts |ASP.NET 用のパフォーマンス カウンター |
| ASP.NET |Request Execution Time |ASP.NET 用のパフォーマンス カウンター |
| ASP.NET |Requests Disconnected |ASP.NET 用のパフォーマンス カウンター |
| ASP.NET |Worker Process Restarts |ASP.NET 用のパフォーマンス カウンター |
| ASP.NET Applications(**Total**) |Requests Total |ASP.NET 用のパフォーマンス カウンター |
| ASP.NET Applications(**Total**) |Requests/Sec |ASP.NET 用のパフォーマンス カウンター |
| ASP.NET v4.0.30319 |Request Execution Time |ASP.NET 用のパフォーマンス カウンター |
| ASP.NET v4.0.30319 |Request Wait Time |ASP.NET 用のパフォーマンス カウンター |
| ASP.NET v4.0.30319 |Requests Current |ASP.NET 用のパフォーマンス カウンター |
| ASP.NET v4.0.30319 |Requests Queued |ASP.NET 用のパフォーマンス カウンター |
| ASP.NET v4.0.30319 |Requests Rejected |ASP.NET 用のパフォーマンス カウンター |
| メモリ |Available MBytes |メモリ パフォーマンス カウンター |
| メモリ |Committed Bytes |メモリ パフォーマンス カウンター |
| Processor(_Total) |% Processor Time |ASP.NET 用のパフォーマンス カウンター |
| TCPv4 |接続に関するエラー |TCP オブジェクト |
| TCPv4 |Connections Established |TCP オブジェクト |
| TCPv4 |Connections Reset |TCP オブジェクト |
| TCPv4 |Segments Sent/sec |TCP オブジェクト |
| Network Interface(*) |Bytes Received/sec |ネットワーク インターフェイス オブジェクト |
| Network Interface(*) |Bytes Sent/sec |ネットワーク インターフェイス オブジェクト |
| Network Interface(Microsoft Virtual Machine Bus Network Adapter _2) |Bytes Received/sec |ネットワーク インターフェイス オブジェクト |
| Network Interface(Microsoft Virtual Machine Bus Network Adapter _2) |Bytes Sent/sec |ネットワーク インターフェイス オブジェクト |
| Network Interface(Microsoft Virtual Machine Bus Network Adapter _2) |Bytes Total/sec |ネットワーク インターフェイス オブジェクト |

## <a name="create-and-add-custom-performance-counters-to-your-application"></a>カスタム パフォーマンス カウンターの作成とアプリケーションへの追加
Azure は、Web ロールと worker ロールのカスタム パフォーマンス カウンターの作成と変更をサポートしています。 カスタム パフォーマンス カウンターを使用すると、アプリケーション固有の動作を追跡および監視できます。 管理者特権のアクセス許可を使用して、スタートアップ タスク、Web ロール、または worker ロール用のカスタム パフォーマンス カウンターのカテゴリと指定子を作成または削除できます。

> [!NOTE]
> カスタム パフォーマンス カウンターに変更を加えるコードを実行するには、管理者特権のアクセス許可が必要です。 コードが Web ロールまたは worker ロールに含まれる場合、ロールを正しく初期化するために、ロールの ServiceDefinition.csdef ファイルにタグ <Runtime executionContext="elevated" /> を含める必要があります。
> 
> 

カスタム パフォーマンス カウンターのデータを診断エージェントを使用して Azure Storage に送信できます。

標準のパフォーマンス カウンターのデータは、Azure プロセスによって生成されます。 カスタム パフォーマンス カウンターのデータは、Web ロールまたは worker ロール アプリケーションで作成する必要があります。 カスタム パフォーマンス カウンターに保存できるデータ型の詳細については、「 [パフォーマンス カウンターの型](https://msdn.microsoft.com/library/z573042h.aspx) 」を参照してください。 Web ロールでカスタム パフォーマンス カウンターのデータを作成および設定する例については、 [PerformanceCounters サンプル](http://code.msdn.microsoft.com/azure/) を参照してください。

## <a name="store-and-view-performance-counter-data"></a>パフォーマンス カウンター データの保存および表示
パフォーマンス カウンターのデータは、他の診断情報と共に Azure にキャッシュされます。 このデータは、パフォーマンス モニターなどのツールを表示するためにリモート デスクトップ アクセスを使用してロール インスタンスが実行されているときに、リモート監視を行うために使用できます。 このデータをロール インスタンスの外部に保存するには、診断エージェントによってデータが Azure Storage に転送される必要があります。 キャッシュされるパフォーマンス カウンター データのサイズ制限は、診断エージェントで構成するか、すべての診断データに対する共有制限の一部として構成することができます。 バッファー サイズの設定の詳細については、[OverallQuotaInMB](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.diagnosticmonitorconfiguration.overallquotainmb.aspx) および [DirectoriesBufferConfiguration](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.diagnostics.directoriesbufferconfiguration.aspx) に関する記述を参照してください。 データをストレージ アカウントに転送するように診断エージェントを設定する方法の概要については、「 [Azure Storage への診断データの保存と表示](https://msdn.microsoft.com/library/azure/hh411534.aspx) 」を参照してください。

構成された各パフォーマンス カウンター インスタンスは指定したサンプリング レートで記録され、サンプリングされたデータは、スケジュール設定された転送要求またはオンデマンドの転送要求によってストレージ アカウントに転送されます。 自動転送を毎分 1 回の頻度でスケジュール設定することもできます。 診断エージェントによって転送されたパフォーマンス カウンター データは、ストレージ アカウント内のテーブル WADPerformanceCountersTable に保存されます。 このテーブルには、標準の Azure Storage API メソッドを使用してアクセスし、クエリを実行することができます。 WADPerformanceCountersTable テーブルに対してクエリを実行し、テーブル内のパフォーマンス カウンター データを表示する例については、 [Microsoft Azure PerformanceCounters のサンプル](http://code.msdn.microsoft.com/Windows-Azure-PerformanceCo-7d80ebf9) を参照してください。

> [!NOTE]
> 診断エージェントの転送頻度やキューの待機時間によっては、ストレージ アカウント内の最新のパフォーマンス カウンター データが数分前のデータになる場合があります。
> 
> 

## <a name="enable-performance-counters-using-diagnostics-configuration-file"></a>診断構成ファイルを使用してパフォーマンス カウンターを有効にする
Azure アプリケーションで、パフォーマンス カウンターを有効にするには、以下の手順を実行します。

## <a name="prerequisites"></a>前提条件
このセクションは、アプリケーションに診断モニターがインポートされ、Visual Studio ソリューションに診断構成ファイル (SDK 2.4 以前の場合は diagnostics.wadcfg、SDK 2.5 以降の場合は diagnostics.wadcfgx) が追加されていることを前提としています。 詳細については、[Azure の Cloud Services および Virtual Machines での診断機能の有効化](cloud-services-dotnet-diagnostics.md)に関するページの手順 1. と手順 2. を参照してください。

## <a name="step-1-collect-and-store-data-from-performance-counters"></a>手順 1. パフォーマンス カウンターからデータを収集して保存する
Visual Studio ソリューションに診断ファイルを追加すると、Azure アプリケーションでパフォーマンス カウンター データの収集と保存を構成できます。 そのためには、診断ファイルにパフォーマンス カウンターを追加します。 パフォーマンス カウンターを含む診断データは、まずインスタンスで収集されます。 その後、データが Azure Table サービスの WADPerformanceCountersTable テーブルに保存されるため、アプリケーションでストレージ アカウントを指定する必要もあります。 コンピューティング エミュレーターでアプリケーションをローカルにテストする場合、ストレージ エミュレーターで診断データをローカルに保存することもできます。 診断データを保存する前に、 [Azure クラシック ポータル](http://manage.windowsazure.com/) でストレージ アカウントを作成する必要があります。 ベスト プラクティスとして、Azure アプリケーションと同じ地理的な場所にあるストレージ アカウントを特定することにより、外部帯域幅のコストが生じないようにしたり、遅延時間を短縮することができます。

### <a name="add-performance-counters-to-the-diagnostics-file"></a>診断ファイルにパフォーマンス カウンターを追加する
使用できるパフォーマンス カウンターは多数あります。 次の例に、Web ロールおよび worker ロールの監視に推奨されるパフォーマンス カウンターをいくつか示します。

診断ファイル (SDK 2.4 以前の場合は diagnostics.wadcfg、SDK 2.5 以降の場合は diagnostics.wadcfgx) を開き、次のコードを DiagnosticMonitorConfiguration 要素に追加します。

```xml
<PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
    <PerformanceCounterConfiguration counterSpecifier="\Memory\Available Bytes" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT30S" />

    <!-- Use the Process(w3wp) category counters in a web role -->

    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\% Processor Time" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Private Bytes" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\Process(w3wp)\Thread Count" sampleRate="PT30S" />

    <!-- Use the Process(WaWorkerHost) category counters in a worker role.
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\% Processor Time" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Private Bytes" sampleRate="PT30S" />
        <PerformanceCounterConfiguration counterSpecifier="\Process(WaWorkerHost)\Thread Count" sampleRate="PT30S" />
    -->

    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Interop(_Global_)\# of marshalling" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Loading(_Global_)\% Time Loading" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR LocksAndThreads(_Global_)\Contention Rate / sec" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Memory(_Global_)\# Bytes in all Heaps" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Networking(_Global_)\Connections Established" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Remoting(_Global_)\Remote Calls/sec" sampleRate="PT30S" />
    <PerformanceCounterConfiguration counterSpecifier="\.NET CLR Jit(_Global_)\% Time in Jit" sampleRate="PT30S" />
</PerformanceCounters>
```

bufferQuotaInMB 属性: データ コレクション タイプに使用可能なファイル システム ストレージの最大量を指定します (Azure ログ、IIS ログなど)。 既定値は 0 です。 クォータに達した場合、新しいデータが追加されるときに最も古いデータが削除されます。 すべての bufferQuotaInMB プロパティの合計が、OverallQuotaInMB 属性の値より大きくなる必要があります。 診断データを収集するために必要なストレージのサイズを求める方法の詳細については、 [Azure アプリケーションの開発に関するトラブルシューティングのベスト プラクティス](https://msdn.microsoft.com/library/windowsazure/hh771389.aspx)の WAD のセットアップに関するセクションを参照してください。

scheduledTransferPeriod 属性: 分単位で四捨五入したデータ転送のスケジュール間隔を指定します。 次の例では、PT30M (30 分) に設定されます。 転送期間を小さい値 (1 分など) に設定すると、運用環境のアプリケーションのパフォーマンスにマイナスの影響が及びますが、テスト時に診断が機能するかをすぐに確認する場合に役立つことがあります。 スケジュールされた転送期間は、インスタンスで診断データが上書きされないような小さい値にし、かつアプリケーションのパフォーマンスに影響を与えないような大きい値にしてください。

counterSpecifier 属性は、収集するパフォーマンス カウンターを指定します。sampleRate 属性は、パフォーマンス カウンターをサンプリングする間隔 (ここでは 30 秒) を指定します。

収集するパフォーマンス カウンターを追加したら、変更を診断ファイルに保存します。 次に、診断データを保存するストレージ アカウントを指定する必要があります。

### <a name="specify-the-storage-account"></a>ストレージ アカウントを指定する
診断情報を Azure ストレージ アカウントに保存するには、サービス構成 (ServiceConfiguration.cscfg) ファイルで接続文字列を指定する必要があります。

Azure SDK 2.5 の場合、ストレージ アカウントは diagnostics.wadcfgx ファイルで指定できます。

> [!NOTE]
> 以下の手順は、Azure SDK 2.4 以前にのみ適用されます。 Azure SDK 2.5 の場合、ストレージ アカウントは diagnostics.wadcfgx ファイルで指定できます。
> 
> 

接続文字列を設定するには、次の手順に従います。

1. 任意のテキスト エディターを使用して ServiceConfiguration.Cloud.cscfg ファイルを開き、ストレージの接続文字列を設定します。 *AccountName* 値と *AccountKey* 値は、Azure クラシック ポータルのストレージ アカウント ダッシュボードの [キーの管理] で確認できます。
  
    ```xml
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<name>;AccountKey=<key>"/>
    </ConfigurationSettings>
    ```
2. ServiceConfiguration.Cloud.cscfg ファイルを保存します。
3. ServiceConfiguration.Local.cscfg ファイルを開き、UseDevelopmentStorage が true に設定されていることを確認します。
   
    ```xml
    <ConfigurationSettings>
      <Settingname="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true"/>
    </ConfigurationSettings>
    ```
   これで、接続文字列が設定され、アプリケーションのデプロイ時にアプリケーションで診断データがストレージ アカウントに保存されるようになります。
4. プロジェクトを保存してビルドし、アプリケーションをデプロイします。

## <a name="step-2-optional-create-custom-performance-counters"></a>手順 2. (省略可能) カスタム パフォーマンス カウンターを作成する
事前定義されたパフォーマンス カウンターに加えて、独自のカスタム パフォーマンス カウンターを追加して Web ロールまたは worker ロールを監視できます。 カスタム パフォーマンス カウンターを使用すると、アプリケーション固有の動作を追跡して監視することができます。また、カスタム パフォーマンス カウンターは、昇格されたアクセス許可を持つスタートアップ タスク、Web ロール、worker ロールで作成または削除できます。

Azure 診断エージェントは起動の 1 分後に .wadcfg ファイルのパフォーマンス カウンター構成を更新します。  OnStart メソッドでカスタム パフォーマンス カウンターを作成していて、スタートアップ タスクの実行に 1 分以上かかる場合は、Azure 診断エージェントがカスタム パフォーマンス カウンターの読み込みを試行する時点でカスタム パフォーマンス カウンターがまだ作成されていません。  このようなシナリオでは、Azure 診断でカスタム パフォーマンス カウンターを除くすべての診断データが正しくキャプチャされていると表示されます。  この問題を解決するには、スタートアップ タスクでパフォーマンス カウンターを作成するか、パフォーマンス カウンターの作成後にスタートアップ タスクの作業の一部を OnStart メソッドに移します。

次の手順を実行して、"\MyCustomCounterCategory\MyButton1Counter" という簡単なカスタム パフォーマンス カウンターを作成します。

1. アプリケーションのサービス定義ファイル (CSDEF) を開きます。
2. Runtime 要素を WebRole または WorkerRole 要素に追加して、 昇格された権限で実行できるようにします。
   
    ```xml
    <runtime executioncontext="elevated"/>
    ```
3. ファイルを保存します。
4. 診断ファイル (SDK 2.4 以前の場合は diagnostics.wadcfg、SDK 2.5 以降の場合は diagnostics.wadcfgx) を開き、次のコードを DiagnosticMonitorConfiguration に追加します。 
   
    ```xml
    <PerformanceCounters bufferQuotaInMB="0" scheduledTransferPeriod="PT30M">
      <PerformanceCounterConfiguration counterSpecifier="\MyCustomCounterCategory\MyButton1Counter" sampleRate="PT30S"/>
    </PerformanceCounters>
    ```
5. ファイルを保存します。
6. base.OnStart を呼び出す前に、ロールの OnStart メソッドで、 カスタム パフォーマンス カウンター カテゴリを作成します。 次の C# の例では、まだ存在しない場合にカスタム カテゴリを作成します。
   
    ```csharp
    public override bool OnStart()
    {
      if (!PerformanceCounterCategory.Exists("MyCustomCounterCategory"))
      {
         CounterCreationDataCollection counterCollection = new CounterCreationDataCollection();
   
         // add a counter tracking user button1 clicks
         CounterCreationData operationTotal1 = new CounterCreationData();
         operationTotal1.CounterName = "MyButton1Counter";
         operationTotal1.CounterHelp = "My Custom Counter for Button1";
         operationTotal1.CounterType = PerformanceCounterType.NumberOfItems32;
         counterCollection.Add(operationTotal1);
   
         PerformanceCounterCategory.Create(
           "MyCustomCounterCategory",
           "My Custom Counter Category",
           PerformanceCounterCategoryType.SingleInstance, counterCollection);
   
         Trace.WriteLine("Custom counter category created.");
      }
      else {
        Trace.WriteLine("Custom counter category already exists.");
      }
   
    return base.OnStart();
    }
    ```
7. アプリケーション内でカウンターを更新します。 次の例では、Button1_Click イベントでカスタム パフォーマンス カウンターを更新しています。
   
    ```csharp
    protected void Button1_Click(object sender, EventArgs e)
    {
      PerformanceCounter button1Counter = new PerformanceCounter(
        "MyCustomCounterCategory",
        "MyButton1Counter",
        string.Empty,
        false);
      button1Counter.Increment();
      this.Button1.Text = "Button 1 count: " +
        button1Counter.RawValue.ToString();
    }
    ```
8. ファイルを保存します。  

これらの手順を完了すると、カスタム パフォーマンス カウンター データが Azure 診断モニターによって収集されます。

## <a name="step-3-query-performance-counter-data"></a>手順 3. パフォーマンス カウンターのデータのクエリを実行する
アプリケーションがデプロイされたら、診断モニターを実行すると、パフォーマンス カウンターの収集が開始され、そのデータが Azure ストレージに保存されます。 Visual Studio のサーバー エクスプローラー、[Azure ストレージ エクスプローラー](http://azurestorageexplorer.codeplex.com/)、[Azure 診断マネージャー](http://www.cerebrata.com/Products/AzureDiagnosticsManager/Default.aspx) (Cerebrata) などのツールを使用して、WADPerformanceCountersTable テーブルにあるパフォーマンス カウンター データを確認します。 [C#](../storage/storage-dotnet-how-to-use-tables.md)、[Java](../storage/storage-java-how-to-use-table-storage.md)、[Node.js](../storage/storage-nodejs-how-to-use-table-storage.md)、[Python](../storage/storage-python-how-to-use-table-storage.md)、[Ruby](../storage/storage-ruby-how-to-use-table-storage.md)、[PHP](../storage/storage-php-how-to-use-table-storage.md) を使用して、Table service をプログラムにより照会することもできます。

次の C# の例は、WADPerformanceCountersTable テーブルに対する簡単なクエリを示しており、CSV ファイルに診断データを保存します。 パフォーマンス カウンターが CSV ファイルに保存されたら、Microsoft Excel や他のツールのグラフ作成機能を使用してデータを視覚化できます。 必ず、Microsoft.WindowsAzure.Storage.dll への参照を追加してください。これは、Azure SDK for .NET (2012 年 10 月) 以降に含まれています。 このアセンブリは、%Program Files%\Microsoft SDKs\Microsoft Azure.NET SDK\version-num\ref\ ディレクトリにインストールされます。

```csharp
using Microsoft.WindowsAzure.Storage;
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Table;
...

// Get the connection string. When using Microsoft Azure Cloud Services, it is recommended
// you store your connection string using the Microsoft Azure service configuration
// system (*.csdef and *.cscfg files). You can you use the CloudConfigurationManager type
// to retrieve your storage connection string.  If you're not using Cloud Services, it's
// recommended that you store the connection string in your web.config or app.config file.
// Use the ConfigurationManager type to retrieve your storage connection string.

string connectionString = Microsoft.WindowsAzure.CloudConfigurationManager.GetSetting("StorageConnectionString");
//string connectionString = System.Configuration.ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString;

// Get a reference to the storage account using the connection string.  You can also use the development
// storage account (Storage Emulator) for local debugging.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);
//CloudStorageAccount storageAccount = CloudStorageAccount.DevelopmentStorageAccount;

// Create the table client.
CloudTableClient tableClient = storageAccount.CreateCloudTableClient();

// Create the CloudTable object that represents the "WADPerformanceCountersTable" table.
CloudTable table = tableClient.GetTableReference("WADPerformanceCountersTable");

// Create the table query, filter on a specific CounterName, DeploymentId and RoleInstance.
TableQuery<PerformanceCountersEntity> query = new TableQuery<PerformanceCountersEntity>()
  .Where(
    TableQuery.CombineFilters(
      TableQuery.GenerateFilterCondition("CounterName", QueryComparisons.Equal, @"\Processor(_Total)\% Processor Time"),
      TableOperators.And,
      TableQuery.CombineFilters(
      TableQuery.GenerateFilterCondition("DeploymentId", QueryComparisons.Equal, "ec26b7a1720447e1bcdeefc41c4892a3"),
      TableOperators.And,
      TableQuery.GenerateFilterCondition("RoleInstance", QueryComparisons.Equal, "WebRole1_IN_0")
    )
  )
);

// Execute the table query.
IEnumerable<PerformanceCountersEntity> result = table.ExecuteQuery(query);

// Process the query results and build a CSV file.
StringBuilder sb = new StringBuilder("TimeStamp,EventTickCount,DeploymentId,Role,RoleInstance,CounterName,CounterValue\n");

foreach (PerformanceCountersEntity entity in result)
{
  sb.Append(entity.Timestamp + "," + entity.EventTickCount + "," + entity.DeploymentId + ","
    + entity.Role + "," + entity.RoleInstance + "," + entity.CounterName + "," + entity.CounterValue+"\n");
}

StreamWriter sw = File.CreateText(@"C:\temp\PerfCounters.csv");
sw.Write(sb.ToString());
sw.Close();
```

エンティティは、 **TableEntity**から派生するカスタム クラスを使用して C# オブジェクトにマップされます。 次のコードは、 **WADPerformanceCountersTable** テーブルのパフォーマンス カウンターを表すエンティティ クラスを定義します。

```csharp
public class PerformanceCountersEntity : TableEntity
{
  public long EventTickCount { get; set; }
  public string DeploymentId { get; set; }
  public string Role { get; set; }
  public string RoleInstance { get; set; }
  public string CounterName { get; set; }
  public double CounterValue { get; set; }
}
```

## <a name="next-steps"></a>次のステップ
[Azure 診断に関するその他の記事を確認します](../azure-diagnostics.md)



<!--HONumber=Nov16_HO3-->


