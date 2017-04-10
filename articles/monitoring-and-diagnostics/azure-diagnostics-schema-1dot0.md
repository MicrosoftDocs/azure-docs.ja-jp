---
title: "Azure 診断 1.0 構成スキーマ | Microsoft Docs"
description: "この記事は、Azure SDK 2.4 以前を Azure Virtual Machines、Virtual Machine Scale Sets、Service Fabric、または Cloud Services と共に使用している場合にのみ該当します。"
services: monitoring-and-diagnostics
documentationcenter: .net
author: rboucher
manager: carmonm
editor: 
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/21/2017
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 66f733d7602a8d26627fcc205f357e7a4a266d11
ms.openlocfilehash: c749a0929d292913e03fa7340bbbebad411fb44d
ms.lasthandoff: 02/22/2017


---
# <a name="azure-diagnostics-10-configuration-schema"></a>Azure 診断 1.0 構成スキーマ
> [!NOTE]
> Azure 診断は、Azure Virtual Machines、Virtual Machine Scale Sets、Service Fabric、および Cloud Services からパフォーマンス カウンターやその他の統計情報を収集するために使用するコンポーネントです。  このページは、これらのサービスのいずれかを使用している場合にのみ該当します。
>

Azure 診断は、Azure Monitor、Application Insights、Log Analytics など、他の Microsoft 診断製品と共に使用します。

Azure 診断構成ファイルは、診断モニターを初期化するときに使用される値を定義します。 このファイルは、診断モニターの開始時に、診断構成設定を初期化するときに使用されます。  

 既定では、Azure 診断構成スキーマ ファイルは `C:\Program Files\Microsoft SDKs\Azure\.NET SDK\<version>\schemas` ディレクトリにインストールされます。 `<version>` は、インストールされている [Azure SDK](http://www.windowsazure.com/develop/downloads/) バージョンで置き換えてください。  

> [!NOTE]
>  診断構成ファイルは、通常、スタートアップ プロセスの初期段階で診断データ収集が必要なスタートアップ タスクで使用されます。 Azure 診断の詳細については、「[Azure 診断を使用したログ データの収集](assetId:///83a91c23-5ca2-4fc9-8df3-62036c37a3d7)」を参照してください。  

## <a name="example-of-the-diagnostics-configuration-file"></a>診断構成ファイルの例  
 一般的な診断構成ファイルの例を次に示します。  

```xml  
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticMonitorConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration"  
      configurationChangePollInterval="PT1M"  
      overallQuotaInMB="4096">  
   <DiagnosticInfrastructureLogs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  
   <Logs bufferQuotaInMB="1024"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M" />  

   <Directories bufferQuotaInMB="1024"   
      scheduledTransferPeriod="PT1M">  

      <!-- These three elements specify the special directories   
           that are set up for the log types -->  
      <CrashDumps container="wad-crash-dumps" directoryQuotaInMB="256" />  
      <FailedRequestLogs container="wad-frq" directoryQuotaInMB="256" />  
      <IISLogs container="wad-iis" directoryQuotaInMB="256" />  

      <!-- For regular directories the DataSources element is used -->  
      <DataSources>  
         <DirectoryConfiguration container="wad-panther" directoryQuotaInMB="128">  
            <!-- Absolute specifies an absolute path with optional environment expansion -->  
            <Absolute expandEnvironment="true" path="%SystemRoot%\system32\sysprep\Panther" />  
         </DirectoryConfiguration>  
         <DirectoryConfiguration container="wad-custom" directoryQuotaInMB="128">  
            <!-- LocalResource specifies a path relative to a local   
                 resource defined in the service definition -->  
            <LocalResource name="MyLoggingLocalResource" relativePath="logs" />  
         </DirectoryConfiguration>  
      </DataSources>  
   </Directories>  

   <PerformanceCounters bufferQuotaInMB="512" scheduledTransferPeriod="PT1M">  
      <!-- The counter specifier is in the same format as the imperative   
           diagnostics configuration API -->  
      <PerformanceCounterConfiguration   
         counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT5S" />  
   </PerformanceCounters>  

   <WindowsEventLog bufferQuotaInMB="512"  
      scheduledTransferLogLevelFilter="Verbose"  
      scheduledTransferPeriod="PT1M">  
      <!-- The event log name is in the same format as the imperative   
           diagnostics configuration API -->  
      <DataSource name="System!*" />  
   </WindowsEventLog>  
</DiagnosticMonitorConfiguration>  
```  

## <a name="diagnosticsconfiguration-namespace"></a>DiagnosticsConfiguration 名前空間  
 診断構成ファイルの XML 名前空間は、次のとおりです。  

```  
http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration  
```  

## <a name="schema-elements"></a>スキーマ要素  
 診断構成ファイルには、次の要素が含まれています。


## <a name="diagnosticmonitorconfiguration-element"></a>DiagnosticMonitorConfiguration 要素  
診断構成ファイルの最上位要素。  

属性:

|Attribute  |種類   |必須| 既定値 | Description|  
|-----------|-------|--------|---------|------------|  
|**configurationChangePollInterval**|duration|省略可能 | PT1M| 診断モニターが診断構成変更をポーリングする間隔を指定します。|  
|**overallQuotaInMB**|unsignedInt|省略可能| 4000 MB。 値を指定した場合、その容量を超えることはできません |すべてのログ バッファーに割り当てられたファイル システム ストレージの合計量。|  

## <a name="diagnosticinfrastructurelogs-element"></a>DiagnosticInfrastructureLogs 要素  
基になる診断インフラストラクチャによって生成されるログのバッファー構成を定義します。

親要素: [DiagnosticMonitorConfiguration 要素](#DiagnosticMonitorConfiguration)。  

属性:

|Attribute|型|Description|  
|---------|----|-----------------|  
|**bufferQuotaInMB**|unsignedInt|省略可能。 指定されたデータに使用できるファイル システム ストレージの最大量を指定します。<br /><br /> 既定値は 0 です。|  
|**scheduledTransferLogLevelFilter**|string|省略可能。 転送されるログ エントリの最小重大度レベルを指定します。 既定値は **Undefined** です。 他の有効値は **Verbose**、**Information**、**Warning**、**Error**、および **Critical** です。|  
|**scheduledTransferPeriod**|duration|省略可能。 最も近い分単位の値に丸められた、スケジュールされているデータ転送の間隔を指定します。<br /><br /> 既定値は PT0S です。|  

## <a name="logs-element"></a>Logs 要素  
 基本的な Azure ログのバッファー構成を定義します。

 親要素: [DiagnosticMonitorConfiguration 要素](#DiagnosticMonitorConfiguration)。  

属性:  

|Attribute|型|Description|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|省略可能。 指定されたデータに使用できるファイル システム ストレージの最大量を指定します。<br /><br /> 既定値は 0 です。|  
|**scheduledTransferLogLevelFilter**|string|省略可能。 転送されるログ エントリの最小重大度レベルを指定します。 既定値は **Undefined** です。 他の有効値は **Verbose**、**Information**、**Warning**、**Error**、および **Critical** です。|  
|**scheduledTransferPeriod**|duration|省略可能。 最も近い分単位の値に丸められた、スケジュールされているデータ転送の間隔を指定します。<br /><br /> 既定値は PT0S です。|  

## <a name="directories-element"></a>Directories 要素  
定義できるファイル ベースのログのバッファー構成を定義します。

親要素: [DiagnosticMonitorConfiguration 要素](#DiagnosticMonitorConfiguration)。  


属性:  

|Attribute|型|Description|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|省略可能。 指定されたデータに使用できるファイル システム ストレージの最大量を指定します。<br /><br /> 既定値は 0 です。|  
|**scheduledTransferPeriod**|duration|省略可能。 最も近い分単位の値に丸められた、スケジュールされているデータ転送の間隔を指定します。<br /><br /> 既定値は PT0S です。|  

## <a name="crashdumps-element"></a>CrashDumps 要素  
 クラッシュ ダンプ ディレクトリを定義します。

 親要素: [Directories 要素](#Directories)。  

属性:  

|Attribute|型|Description|  
|---------------|----------|-----------------|  
|**container**|string|ディレクトリの内容を転送するコンテナーの名前。|  
|**directoryQuotaInMB**|unsignedInt|省略可能。 ディレクトリの最大サイズをメガバイト単位で指定します。<br /><br /> 既定値は 0 です。|  

## <a name="failedrequestlogs-element"></a>FailedRequestLogs 要素  
 失敗した要求ログ ディレクトリを定義します。

 親要素: [Directories 要素](#Directories)。  

属性:  

|Attribute|型|Description|  
|---------------|----------|-----------------|  
|**container**|string|ディレクトリの内容を転送するコンテナーの名前。|  
|**directoryQuotaInMB**|unsignedInt|省略可能。 ディレクトリの最大サイズをメガバイト単位で指定します。<br /><br /> 既定値は 0 です。|  

##  <a name="iislogs-element"></a>IISLogs 要素  
 IIS ログ ディレクトリを定義します。

 親要素: [Directories 要素](#Directories)。  

属性:  

|Attribute|型|Description|  
|---------------|----------|-----------------|  
|**container**|string|ディレクトリの内容を転送するコンテナーの名前。|  
|**directoryQuotaInMB**|unsignedInt|省略可能。 ディレクトリの最大サイズをメガバイト単位で指定します。<br /><br /> 既定値は 0 です。|  

## <a name="datasources-element"></a>DataSources 要素  
 0 個以上の追加のログ ディレクトリを定義します。

 親要素: [Directories 要素](#Directories)。

## <a name="directoryconfiguration-element"></a>DirectoryConfiguration 要素  
 監視するログ ファイルのディレクトリを定義します。

 親要素: [DataSources 要素](#DataSources)。

属性:

|Attribute|型|Description|  
|---------------|----------|-----------------|  
|**container**|string|ディレクトリの内容を転送するコンテナーの名前。|  
|**directoryQuotaInMB**|unsignedInt|省略可能。 ディレクトリの最大サイズをメガバイト単位で指定します。<br /><br /> 既定値は 0 です。|  

## <a name="absolute-element"></a>Absolute 要素  
 省略可能な環境変数の展開を使用して監視するディレクトリの絶対パスを定義します。

 親要素: [DirectoryConfiguration 要素](#DirectoryConfiguration)。  

属性:  

|Attribute|型|説明|  
|---------------|----------|-----------------|  
|**path**|string|必須。 監視するディレクトリの絶対パス。|  
|**expandEnvironment**|boolean|必須。 **true** に設定した場合は、このパスの環境変数が展開されます。|  

## <a name="localresource-element"></a>LocalResource 要素  
 サービス定義で定義されているローカル リソースの相対パスを定義します。

 親要素: [DirectoryConfiguration 要素](#DirectoryConfiguration)。  

属性:  

|Attribute|型|Description|  
|---------------|----------|-----------------|  
|**name**|string|必須。 監視するディレクトリを含むローカル リソースの名前。|  
|**relativePath**|string|必須。 監視するローカル リソースの相対パス。|  

## <a name="performancecounters-element"></a>PerformanceCounters 要素  
 収集するパフォーマンス カウンターのパスを定義します。

 親要素: [DiagnosticMonitorConfiguration 要素](#DiagnosticMonitorConfiguration)。


 属性:  

|Attribute|型|Description|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|省略可能。 指定されたデータに使用できるファイル システム ストレージの最大量を指定します。<br /><br /> 既定値は 0 です。|  
|**scheduledTransferPeriod**|duration|省略可能。 最も近い分単位の値に丸められた、スケジュールされているデータ転送の間隔を指定します。<br /><br /> 既定値は PT0S です。|  

## <a name="performancecounterconfiguration-element"></a>PerformanceCounterConfiguration 要素  
 収集するパフォーマンス カウンターを定義します。

 親要素: [PerformanceCounters 要素](#PerformanceCounters)。  

 属性:  

|Attribute|型|Description|  
|---------------|----------|-----------------|  
|**counterSpecifier**|string|必須。 収集するパフォーマンス カウンターのパス。|  
|**sampleRate**|duration|必須。 パフォーマンス カウンターを収集する頻度。|  

## <a name="windowseventlog-element"></a>WindowsEventLog 要素  
 監視するイベント ログを定義します。

 親要素: [DiagnosticMonitorConfiguration 要素](#DiagnosticMonitorConfiguration)。

  属性:

|Attribute|型|Description|  
|---------------|----------|-----------------|  
|**bufferQuotaInMB**|unsignedInt|省略可能。 指定されたデータに使用できるファイル システム ストレージの最大量を指定します。<br /><br /> 既定値は 0 です。|  
|**scheduledTransferLogLevelFilter**|string|省略可能。 転送されるログ エントリの最小重大度レベルを指定します。 既定値は **Undefined** です。 他の有効値は **Verbose**、**Information**、**Warning**、**Error**、および **Critical** です。|  
|**scheduledTransferPeriod**|duration|省略可能。 最も近い分単位の値に丸められた、スケジュールされているデータ転送の間隔を指定します。<br /><br /> 既定値は PT0S です。|  

## <a name="datasource-element"></a>DataSource 要素  
 監視するイベント ログを定義します。

 親要素: [WindowsEventLog 要素](#windowsEventLog)。  

 属性:

|Attribute|型|Description|  
|---------------|----------|-----------------|  
|**name**|string|必須。 収集するログを指定するための XPath 式。|  

