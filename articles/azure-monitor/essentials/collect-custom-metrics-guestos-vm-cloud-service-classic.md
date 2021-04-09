---
title: 従来の Cloud Services のメトリックを Azure Monitor メトリック データベースに送信する
description: 従来の Azure Cloud Services のゲスト OS のパフォーマンス メトリックを Azure Monitor メトリック ストアに送信するためのプロセスについて説明します。
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.openlocfilehash: 0ef9d8118a8ff1d9fdd69566dd60033f5847f810
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102048966"
---
# <a name="send-guest-os-metrics-to-the-azure-monitor-metric-store-classic-cloud-services"></a>Azure Monitor メトリック ストアの従来の Cloud Services にゲスト OS メトリックを送信する 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor [診断拡張機能](../agents/diagnostics-extension-overview.md)を使用すると、仮想マシン、クラウド サービス、または Service Fabric クラスターの一部として、ゲスト オペレーティング システム (ゲスト OS) からメトリックとログを収集できます。 拡張機能により、[多くの異なる場所](../data-platform.md?toc=%2fazure%2fazure-monitor%2ftoc.json)にテレメトリを送信できます。

この記事では、従来の Azure Cloud Services 用のゲスト OS のパフォーマンス メトリックを Azure Monitor メトリック ストアに送信するプロセスについて説明します。 診断拡張機能バージョン 1.11 以降、標準プラットフォーム メトリックが既に収集されている Azure Monitor メトリック ストアに、メトリックを直接書き込むことができます。 

この場所にこれらを格納することで、プラットフォーム メトリックに対して使用できるのと同じアクションにアクセスできます。 アクションには、ほぼリアルタイムのアラート、グラフ作成、ルーティング、REST API からのアクセスなどの機能があります。  これまで、診断拡張機能では、Azure Monitor データ ストアではなく Azure Storage に書き込んでいました。  

この記事で説明されているプロセスは、Azure Cloud Services でのパフォーマンス カウンターに対してのみ機能します。 他のカスタム メトリックに対しては機能しません。 

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションで、[サービス管理者または共同管理者](../../cost-management-billing/manage/add-change-subscription-administrator.md)である必要があります。 

- サブスクリプションを [Microsoft.Insights](../../azure-resource-manager/management/resource-providers-and-types.md) に登録する必要があります 

- [Azure PowerShell](/powershell/azure) または [Azure Cloud Shell](../../cloud-shell/overview.md) がインストールされている必要があります。

- お使いのクラウド サービスが、[カスタム メトリックをサポートするリージョン](./metrics-custom-overview.md#supported-regions)に存在する必要があります。

## <a name="provision-a-cloud-service-and-storage-account"></a>クラウド サービスとストレージ アカウントのプロビジョニング 

1. 従来のクラウド サービスを作成し、デプロイします。 従来の Cloud Services アプリケーションとデプロイのサンプルは、「[Azure Cloud Services と ASP.NET を使ってみる](../../cloud-services/cloud-services-dotnet-get-started.md)」に示されています。 

2. 既存のストレージ アカウントを使用できるか、新しいストレージ アカウントを配置できます。 ストレージ アカウントが、作成した従来のクラウド サービスと同じ領域にある場合は最適です。 Azure portal で、**ストレージ アカウント** のリソース ブレードに移動し、**キー** を選択します。 ストレージ アカウント名とストレージ アカウント キーをメモしておきます。 この情報は後続の手順で必要になります。

   ![ストレージ アカウント キー](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/storage-keys.png)

## <a name="create-a-service-principal"></a>サービス プリンシパルの作成 

「[リソースにアクセスできる Azure Active Directory アプリケーションとサービス プリンシパルをポータルで作成する](../../active-directory/develop/howto-create-service-principal-portal.md)」の手順を使用して、お使いの Azure Active Directory テナントでサービス プリンシパルを作成します。 このプロセスを進める際には、次の点に注意してください。 

- サインイン URL には任意の URL を入力できます。  
- このアプリ用に新しいクライアント シークレットを作成します。  
- 後の手順で使用するために、キーとクライアント ID を保存します。  

以前の手順の *メトリック パブリッシャーの監視* で作成したアプリに、メトリックを発行するリソースへのアクセス許可を付与します。 アプリを使用して多くのリソースに対してカスタム メトリックを発行する予定である場合は、リソース グループまたはサブスクリプション レベルでこれらのアクセス許可を付与できます。  

> [!NOTE]
> 診断拡張機能では、サービス プリンシパルを使用して、Azure Monitor を認証し、クラウド サービス用のメトリックを発行します。

## <a name="author-diagnostics-extension-configuration"></a>診断拡張機能の構成の作成 

診断拡張機能の構成ファイルを準備します。 このファイルにより、お使いのクラウド サービスに対して、診断拡張機能が収集するパフォーマンス カウンターとログが決まります。 サンプルの診断構成ファイルを以下に示します。  

```XML
<?xml version="1.0" encoding="utf-8"?> 
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <WadCfg> 
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096"> 
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" /> 
        <Directories scheduledTransferPeriod="PT1M"> 
          <IISLogs containerName="wad-iis-logfiles" /> 
          <FailedRequestLogs containerName="wad-failedrequestlogs" /> 
        </Directories> 
        <PerformanceCounters scheduledTransferPeriod="PT1M"> 
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Committed Bytes" sampleRate="PT15S" /> 
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Page Faults/sec" sampleRate="PT15S" /> 
        </PerformanceCounters> 
        <WindowsEventLog scheduledTransferPeriod="PT1M"> 
          <DataSource name="Application!*[System[(Level=1 or Level=2 or Level=3)]]" /> 
          <DataSource name="Windows Azure!*[System[(Level=1 or Level=2 or Level=3 or Level=4)]]" /> 
        </WindowsEventLog> 
        <CrashDumps> 
          <CrashDumpConfiguration processName="WaIISHost.exe" /> 
          <CrashDumpConfiguration processName="WaWorkerHost.exe" /> 
          <CrashDumpConfiguration processName="w3wp.exe" /> 
        </CrashDumps> 
        <Logs scheduledTransferPeriod="PT1M" scheduledTransferLogLevelFilter="Error" /> 
      </DiagnosticMonitorConfiguration> 
      <SinksConfig> 
      </SinksConfig> 
    </WadCfg> 
    <StorageAccount /> 
  </PublicConfig> 
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
    <StorageAccount name="" endpoint="" /> 
</PrivateConfig> 
  <IsEnabled>true</IsEnabled> 
</DiagnosticsConfiguration> 
```

診断ファイルの "SinksConfig" セクションで、新しい Azure Monitor シンクを定義します。 

```XML
  <SinksConfig> 
    <Sink name="AzMonSink"> 
    <AzureMonitor> 
      <ResourceId>-Provide ClassicCloudService’s Resource ID-</ResourceId> 
      <Region>-Azure Region your Cloud Service is deployed in-</Region> 
    </AzureMonitor> 
    </Sink> 
  </SinksConfig> 
```

収集するパフォーマンス カウンターをリストした構成ファイルのセクションに、Azure Monitor シンクを追加します。 このエントリにより、指定されたすべてのパフォーマンス カウンターが Azure Monitor にメトリックとしてルーティングされます。 パフォーマンス カウンターは必要に応じて追加したり、削除することができます。 

```xml
    <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="AzMonSink">
        <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT15S" />
    ...
    </PerformanceCounters>
```

最後に、プライベート構成に *Azure Monitor アカウント* セクションを追加します。 前の手順で作成したサービス プリンシパルのクライアント ID とシークレットを入力します。 

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"> 
  <StorageAccount name="" endpoint="" /> 
    <AzureMonitorAccount> 
      <ServicePrincipalMeta> 
        <PrincipalId>clientId from step 3</PrincipalId> 
        <Secret>client secret from step 3</Secret> 
      </ServicePrincipalMeta> 
    </AzureMonitorAccount> 
</PrivateConfig> 
```

この診断ファイルをローカルに保存します。  

## <a name="deploy-the-diagnostics-extension-to-your-cloud-service"></a>診断拡張機能をクラウド サービスに配置する 

PowerShell を起動し、Azure にログインします。 

```powershell
Login-AzAccount 
```

次のコマンドを使用して、先ほど作成したストレージ アカウントの詳細情報を格納します。 

```powershell
$storage_account = <name of your storage account from step 3> 
$storage_keys = <storage account key from step 3> 
```

同様に、次のコマンドを使用して、診断ファイルのパスを変数に設定します。

```powershell
$diagconfig = “<path of the Diagnostics configuration file with the Azure Monitor sink configured>” 
```

次のコマンドを使用して、Azure Monitor シンクが構成された診断ファイルで、診断拡張機能をクラウド サービスに配置します。  

```powershell
Set-AzureServiceDiagnosticsExtension -ServiceName <classicCloudServiceName> -StorageAccountName $storage_account -StorageAccountKey $storage_keys -DiagnosticsConfigurationPath $diagconfig 
```

> [!NOTE] 
> 診断拡張機能のインストールの一環として、引き続きストレージ アカウントを指定する必要があります。 診断構成ファイルで指定されたログやパフォーマンス カウンターは、指定したストレージ アカウントに書き込まれます。  

## <a name="plot-metrics-in-the-azure-portal"></a>Azure portal でメトリックをプロットします 

1. Azure Portal にアクセスします。 

   ![Azure portal のスクリーンショット。[モニター] が選択され、[メトリック] が選択されています。](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/navigate-metrics.png)

2. 左側のメニューで **[モニター]** を選択します。

3. **[モニター]** ブレードで、 **[メトリックのプレビュー]** タブを選択します。

4. リソースのドロップダウン メニューで、お使いのクラシック クラウド サービスを選択します。

5. 名前空間のドロップダウン メニューで、 **[azure.vm.windows.guest]** を選択します。 

6. メトリックのドロップダウン メニューで、 **[Memory\Committed Bytes in Use]** を選択します。 

ディメンションのフィルタリング機能と分割機能を使用することで、特定のロールとロール インスタンスにより使用されている合計メモリを選択して表示することができます。 

 ![メトリック データのスクリーンショット。](./media/collect-custom-metrics-guestos-vm-cloud-service-classic/metrics-graph.png)

## <a name="next-steps"></a>次のステップ

- [カスタム メトリック](./metrics-custom-overview.md)の詳細を確認します。