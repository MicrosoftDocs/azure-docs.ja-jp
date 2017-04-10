---
title: "Azure Storage への診断データの保存と表示 | Microsoft Docs"
description: "Azure Storage に Azure 診断データを保存し、それを表示する"
services: cloud-services
documentationcenter: .net
author: rboucher
manager: jwhit
editor: tysonn
ms.assetid: 18e0780d-43e7-41e4-b8e9-f1fb9a36eb03
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/01/2016
ms.author: robb
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 16ce99cab9077450a5cfa8e95f7e306ffc1afb6d


---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Azure Storage への診断データの保存と表示
診断データは、Microsoft Azure ストレージ エミュレーターまたは Azure ストレージに転送しない限り、永続的に保存されません。 診断データは、いったんストレージに保存されると、用意されているいくつかのツールの 1 つを使用して確認することができます。

## <a name="specify-a-storage-account"></a>ストレージ アカウントの指定
ServiceConfiguration.cscfg ファイル内で使用するストレージ アカウントを指定します。 アカウント情報は、構成設定で接続文字列として定義されます。 次の例では、Visual Studio で新しい Cloud Service プロジェクト用に作成された既定の接続文字列を示します。

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

この接続文字列を変更することで、Azure ストレージ アカウントのアカウント情報を指定できます。

収集される診断データの種類に応じて、Azure 診断では BLOB サービスまたは Table サービスのいずれかを使用します。 次の表では、保持されるデータ ソースとその形式を示します。

| データ ソース | ストレージ形式 |
| --- | --- |
| Azure ログ |テーブル |
| IIS 7.0 ログ |BLOB |
| Azure 診断インフラストラクチャ ログ |テーブル |
| 失敗した要求トレース ログ |BLOB |
| Windows イベント ログ |テーブル |
| パフォーマンス カウンター |テーブル |
| クラッシュ ダンプ |BLOB |
| カスタム エラー ログ |BLOB |

## <a name="transfer-diagnostic-data"></a>診断データの転送
SDK 2.5 以降では、診断データの転送要求は構成ファイルを介して発生します。 構成で指定したスケジュール間隔で、診断データを転送することができます。

SDK 2.4 およびそれ以前のバージョンでは、構成ファイルを介して、またプログラムによって、診断データの転送を要求することができます。 プログラムを使用した方法では、オンデマンド転送を行うこともできます。

> [!IMPORTANT]
> Azure ストレージ アカウントに診断データを転送する場合、診断データが使用するストレージ リソースのコストが発生します。
> 
> 

## <a name="store-diagnostic-data"></a>診断データの保存
ログ データは、次の名前の BLOB ストレージまたはテーブル ストレージに保存されます。

**テーブル**

* **WadLogsTable** -トレース リスナーを使用してコードで記述されたログを含みます。
* **WADDiagnosticInfrastructureLogsTable** -診断モニターと構成の変更に関する情報を含みます。
* **WADDirectoriesTable** – 診断モニターが監視するディレクトリに関する情報を含みます。  これには、IIS ログ、IIS 失敗要求ログ、およびカスタム ディレクトリが含まれます。  BLOB ログ ファイルの場所は Container フィールドで指定され、BLOB の名前は RelativePath フィールドで指定されます。  AbsolutePath フィールドでは、Azure 仮想マシンに存在するファイルの場所と名前を示します。
* **WADPerformanceCountersTable** – パフォーマンス カウンター。
* **WADWindowsEventLogsTable** – Windows イベント ログ。

**BLOB**

* **wad-control-container** – (SDK 2.4 およびそれ以前のバージョンのみ) Azure 診断を制御する XML 構成ファイルが含まれます。
* **wad-iis-failedreqlogfiles** – IIS の失敗した要求ログからの情報が含まれます。
* **wad-iis-logfiles** – IIS ログに関する情報が含まれます。
* **"custom"** – 診断モニターによって監視されるディレクトリの構成に基づくカスタム コンテナーです。  この BLOB コンテナーの名前は WADDirectoriesTable で指定されます。

## <a name="tools-to-view-diagnostic-data"></a>診断データを表示するツール
ストレージへの転送後にデータを表示するには、いくつかのツールを利用できます。 次に例を示します。

* Visual Studio のサーバー エクスプローラー - Azure Tools for Microsoft Visual Studio がインストールされている場合、サーバー エクスプローラーの Azure Storage ノードを使用して、Azure ストレージ アカウントの読み取り専用の BLOB およびテーブル データを表示できます。 データは、ローカルのストレージ エミュレーター アカウントから表示できます。また、Azure 用に作成したストレージ アカウントから表示することもできます。 詳細については、「[サーバー エクスプローラーを使用したストレージ リソースの参照と管理](../vs-azure-tools-storage-resources-server-explorer-browse-manage.md)」を参照してください。
* [Microsoft Azure ストレージ エクスプローラー](../vs-azure-tools-storage-manage-with-storage-explorer.md) は、Windows、OSX、Linux で Azure Storage データを容易に操作できるスタンドアロン アプリです。
* [Azure Management Studio](http://www.cerebrata.com/products/azure-management-studio/introduction) に含まれている Azure Diagnostics Manager では、Azure で実行されているアプリケーションによって収集された診断データの表示、ダウンロード、管理を行うことができます。

## <a name="next-steps"></a>次のステップ
[Azure 診断で Cloud Services アプリケーションのフローをトレースする](cloud-services-dotnet-diagnostics-trace-flow.md)




<!--HONumber=Nov16_HO3-->


