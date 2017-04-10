---
title: "Azure Log Analytics での IIS 用 Blob Storage とイベント用 Table Storage の使用 | Microsoft Docs"
description: "Log Analytics は、Table Storage に診断情報を出力する Azure サービスのログと Blob Storage に出力された IIS のログを読み取ることができます。"
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: bf444752-ecc1-4306-9489-c29cb37d6045
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/27/2017
ms.author: banders
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: a0c8af30fbed064001c3fd393bf0440aa1cb2835
ms.openlocfilehash: b40d7c0acd8cc6a672f25489b745561c24501482
ms.lasthandoff: 02/28/2017


---
# <a name="use-azure-blob-storage-for-iis-and-azure-table-storage-for-events-with-log-analytics"></a>Log Analytics で IIS 用 Azure Blob Storage とイベント用 Azure Table Storage を使用する

Log Analytics は、Table Storage に診断情報を出力する以下のサービスのログと Blob Storage に出力された IIS のログを読み取ることができます。

* Service Fabric クラスター (プレビュー)
* Virtual Machines
* Web/worker ロール

Log Analytics でこれらのリソースのデータを収集する前に、Azure 診断を有効にする必要があります。

診断が有効になったら、Azure Portal または PowerShell を使用して、Log Analytics でログを収集するための構成を行うことができます。

Azure 診断は、Azure で実行している worker ロール、Web ロール、または仮想マシンから診断データを収集できる Azure の拡張機能です。 そのデータは Azure Storage アカウントに保存され、Log Analytics から収集することができます。

Log Analytics でこれらの Azure 診断ログを収集するためには、ログが次の場所に存在している必要があります。

| ログの種類 | リソースの種類 | 場所 |
| --- | --- | --- |
| IIS ログ |Virtual Machines <br> Web ロールの比較 <br> Worker ロール |wad-iis-logfiles (Blob Storage) |
| syslog |Virtual Machines |LinuxsyslogVer2v0 (Table Storage) |
| Service Fabric の操作イベント |Service Fabric ノード |WADServiceFabricSystemEventTable |
| Service Fabric Reliable Actor のイベント |Service Fabric ノード |WADServiceFabricReliableActorEventTable |
| Service Fabric Reliable Service のイベント |Service Fabric ノード |WADServiceFabricReliableServiceEventTable |
| Windows イベント ログ |Service Fabric ノード <br> Virtual Machines <br> Web ロールの比較 <br> Worker ロール |WADWindowsEventLogsTable (Table Storage) |
| Windows ETW のログ |Service Fabric ノード <br> Virtual Machines <br> Web ロールの比較 <br> Worker ロール |WADETWEventTable (Table Storage) |

> [!NOTE]
> 現在、Azure Websites からの IIS ログはサポートされていません。
>
>

仮想マシンの場合、[Log Analytics エージェント](log-analytics-azure-vm-extension.md)を仮想マシンにインストールして、追加のインサイトを有効にすることもできます。 IIS ログとイベント ログを分析できるだけでなく、構成の変更の追跡、SQL の評価、更新の評価などの追加の分析を実行することもできます。

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection"></a>イベント ログと IIS ログの収集のために仮想マシンで Azure 診断を有効にする
イベント ログと IIS ログを収集するために、Microsoft Azure Portal を使用して、仮想マシンで Azure 診断を有効にするには、次の手順に従います。

### <a name="to-enable-azure-diagnostics-in-a-virtual-machine-with-the-azure-portal"></a>Azure Portal を使用して仮想マシンで Azure 診断を有効にするには
1. 仮想マシンを作成する際に、VM エージェントをインストールします。 仮想マシンが既に存在する場合は、VM エージェントが既にインストールされていることを確認します。

   * Azure Portal で仮想マシンに移動し、**[オプションの構成]**、**[診断]** の順に選択して、**[状態]** を **[オン]** に設定します。

     操作が完了すると、VM に Azure 診断の拡張機能がインストールされ、実行されます。 この拡張機能により、診断データが収集されます。
2. 既存の VM で監視を有効にし、イベント ログを構成します。 診断は VM レベルで有効にすることができます。 診断を有効にして、イベント ログを構成するには、次の手順を実行します。

   1. VM を選択します。
   2. **[監視]**をクリックします。
   3. **[診断]**をクリックします。
   4. **[状態]** を **[オン]** に設定します。
   5. 収集する各診断ログを選択します。
   6. **[OK]**をクリックします。

## <a name="enable-azure-diagnostics-in-a-web-role-for-iis-log-and-event-collection"></a>IIS ログとイベントの収集のために Web ロールで Azure 診断を有効にする
Azure 診断を有効にする一般的な手順については、[クラウド サービスで診断を有効にする方法](../cloud-services/cloud-services-dotnet-diagnostics.md)に関する記事をご覧ください。 以下の手順ではこの情報を使用し、Log Analytics で使用するためにカスタマイズします。

診断を有効にした場合

* 既定では、IIS ログは scheduledTransferPeriod の転送間隔で転送されたログ データと共に格納されます。
* 既定では、Windows イベント ログは転送されません。

### <a name="to-enable-diagnostics"></a>診断を有効にするには
Windows イベント ログを有効にするか、または scheduledTransferPeriod を変更するには、XML 構成ファイル (diagnostics.wadcfg) を使用して Azure 診断を構成します。手順については、「[手順 4. 診断構成ファイルを作成して拡張機能をインストールする](../cloud-services/cloud-services-dotnet-diagnostics.md)」を参照してください。

次の構成ファイルの例では、IIS ログと、アプリケーション ログとシステム ログからすべてのイベントを収集します。

```
    <?xml version="1.0" encoding="utf-8" ?>
    <DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"
          configurationChangePollInterval="PT1M"
          overallQuotaInMB="4096">

      <Directories bufferQuotaInMB="0"
         scheduledTransferPeriod="PT10M">  
        <!-- IISLogs are only relevant to Web roles -->
        <IISLogs container="wad-iis" directoryQuotaInMB="0" />
      </Directories>

      <WindowsEventLog bufferQuotaInMB="0"
         scheduledTransferLogLevelFilter="Verbose"
         scheduledTransferPeriod="PT10M">
        <DataSource name="Application!*" />
        <DataSource name="System!*" />
      </WindowsEventLog>

    </DiagnosticMonitorConfiguration>
```

次の例のように、ConfigurationSettings に必ずストレージ アカウントを指定してください。

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="DefaultEndpointsProtocol=https;AccountName=<AccountName>;AccountKey=<AccountKey>"/>
    </ConfigurationSettings>
```

**AccountName** と **AccountKey** の値は、Azure Portal のストレージ アカウント ダッシュボードの [アクセス キーの管理] に表示されます。 接続文字列のプロトコルは **https**である必要があります。

更新された診断の構成がクラウド サービスに適用され、Azure Storage に診断データが書き込まれていれば、Log Analytics を構成する準備が整っています。

## <a name="use-the-azure-portal-to-collect-logs-from-azure-storage"></a>Azure Portal を使用して Azure Storage からログを収集する
以下の Azure サービスのログを Log Analytics で収集するための構成は Azure Portal で行うことができます。

* Service Fabric クラスター
* Virtual Machines
* Web/worker ロール

Azure Portal で Log Analytics ワークスペースに移動し、次の作業を行います。

1. *[Storage accounts logs (ストレージ アカウントのログ)]* をクリックします。
2. *[追加]* タスクをクリックします。
3. 診断ログが格納されているストレージ アカウントを選択します。
   * このアカウントには、クラシック ストレージ アカウントまたは Azure Resource Manager ストレージ アカウントを使用できます。
4. ログの収集対象となるデータの種類を選択します。
   * 選択肢は、IIS ログ、イベント、Syslog (Linux)、ETW ログ、Service Fabric イベントです。
5. ソースの値はデータの種類に応じて自動的に設定され、変更することはできません。
6. [OK] をクリックして構成を保存します。

Log Analytics で収集する他のストレージ アカウントとデータの種類についても手順 2. ～ 6. を繰り返します。

約 30 分で、ストレージ アカウントのデータを Log Analytics で確認できるようになります。 表示されるのは、構成の適用後にストレージに書き込まれたデータだけです。 Log Analytics は、過去のデータをさかのぼってストレージ アカウントから読み取ることはしません。

> [!NOTE]
> ストレージ アカウントにソースが存在するかどうかや、新しいデータが書き込まれているかどうかの確認は、ポータルでは実行されません。
>
>

## <a name="enable-azure-diagnostics-in-a-virtual-machine-for-event-log-and-iis-log-collection-using-powershell"></a>イベント ログと IIS ログの収集のために PowerShell を使用して仮想マシンで Azure 診断を有効にする
「[Azure 診断で収集されたデータのインデックスを作成するように Log Analytics を構成する](log-analytics-powershell-workspace-configuration.md#configuring-log-analytics-to-index-azure-diagnostics)」の手順に従い、PowerShell を使用してテーブル ストレージに書き込まれた Azure 診断データから読み取ります。

Azure PowerShell を使用すると、Azure Storage に書き込むイベントをより細かく指定できます。
詳細については、「[Azure Virtual Machines での診断の有効化](../virtual-machines-dotnet-diagnostics.md)」をご覧ください。

次の PowerShell スクリプトを使用して、Azure 診断を有効にし、更新できます。
このスクリプトでカスタム ログ構成を使用することもできます。
その場合、スクリプトを変更して、ストレージ アカウント、サービス名、仮想マシン名を設定します。
このスクリプトでは、従来の仮想マシン用のコマンドレットを使用します。

次のサンプル スクリプトを確認、コピーして、必要に応じて変更し、PowerShell スクリプト ファイルとして保存します。その後、スクリプトを実行します。

```
    #Connect to Azure
    Add-AzureAccount

    # settings to change:
    $wad_storage_account_name = "myStorageAccount"
    $service_name = "myService"
    $vm_name = "myVM"

    #Construct Azure Diagnostics public config and convert to config format

    # Collect just system error events:
    $wad_xml_config = "<WadCfg><DiagnosticMonitorConfiguration><WindowsEventLog scheduledTransferPeriod=""PT1M""><DataSource name=""System!* "" /></WindowsEventLog></DiagnosticMonitorConfiguration></WadCfg>"

    $wad_b64_config = [System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($wad_xml_config))
    $wad_public_config = [string]::Format("{{""xmlCfg"":""{0}""}}",$wad_b64_config)

    #Construct Azure diagnostics private config

    $wad_storage_account_key = (Get-AzureStorageKey $wad_storage_account_name).Primary
    $wad_private_config = [string]::Format("{{""storageAccountName"":""{0}"",""storageAccountKey"":""{1}""}}",$wad_storage_account_name,$wad_storage_account_key)

    #Enable Diagnostics Extension for Virtual Machine

    $wad_extension_name = "IaaSDiagnostics"
    $wad_publisher = "Microsoft.Azure.Diagnostics"
    $wad_version = (Get-AzureVMAvailableExtension -Publisher $wad_publisher -ExtensionName $wad_extension_name).Version # Gets latest version of the extension

    (Get-AzureVM -ServiceName $service_name -Name $vm_name) | Set-AzureVMExtension -ExtensionName $wad_extension_name -Publisher $wad_publisher -PublicConfiguration $wad_public_config -PrivateConfiguration $wad_private_config -Version $wad_version | Update-AzureVM
```


## <a name="next-steps"></a>次のステップ
* サポートされている Azure サービスの[ログとメトリックを収集](log-analytics-azure-storage.md)します。
* [ソリューションを有効](log-analytics-add-solutions.md) にして、データに対する洞察を得ます。
* [検索クエリを使用](log-analytics-log-searches.md) して、データを分析します。

