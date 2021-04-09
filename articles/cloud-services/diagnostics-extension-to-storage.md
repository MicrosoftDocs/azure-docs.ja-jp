---
title: Azure Storage への診断データを保存および表示する
description: Azure Storage アカウントで Azure 診断データを収集して、使用可能なツールのいずれかで表示できるようにする方法について説明します。
services: azure-monitor
author: bwren
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/01/2016
ms.author: bwren
ms.subservice: diagnostic-extension
ms.openlocfilehash: 4962d5f048cf41eca50a77a0dedad3cef48ac1f0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98740076"
---
# <a name="store-and-view-diagnostic-data-in-azure-storage"></a>Azure Storage への診断データの保存と表示

> [!IMPORTANT]
> [Azure Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) は、Azure Cloud Services 製品向けの新しい Azure Resource Manager ベースのデプロイ モデルです。 この変更により、Azure Service Manager ベースのデプロイ モデルで実行されている Azure Cloud Services は Cloud Services (クラシック) という名前に変更されました。そのため、すべての新しいデプロイでは [Cloud Services (延長サポート)](../cloud-services-extended-support/overview.md) を使用する必要があります。

診断データは、Microsoft Azure Storage Emulator または Azure ストレージに転送しない限り、永続的に保存されません。 診断データは、いったんストレージに保存されると、用意されているいくつかのツールの 1 つを使用して確認することができます。

## <a name="specify-a-storage-account"></a>ストレージ アカウントの指定
ServiceConfiguration.cscfg ファイル内で使用するストレージ アカウントを指定します。 アカウント情報は、構成設定で接続文字列として定義されます。 次の例では、Visual Studio で新しい Cloud Service プロジェクト用に作成された既定の接続文字列を示します。

```
    <ConfigurationSettings>
       <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="UseDevelopmentStorage=true" />
    </ConfigurationSettings>
```

この接続文字列を変更することで、Azure ストレージ アカウントのアカウント情報を指定できます。

収集される診断データの種類に応じて、Azure Diagnostics では Blob service または Table service のいずれかを使用します。 次の表では、保持されるデータ ソースとその形式を示します。

| データ ソース | ストレージ形式 |
| --- | --- |
| Azure ログ |テーブル |
| IIS 7.0 ログ |BLOB |
| Azure Diagnostics インフラストラクチャ ログ |テーブル |
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

* **wad-control-container** – (SDK 2.4 およびそれ以前のバージョンのみ) Azure Diagnostics を制御する XML 構成ファイルが含まれます。
* **wad-iis-failedreqlogfiles** – IIS の失敗した要求ログからの情報が含まれます。
* **wad-iis-logfiles** – IIS ログに関する情報が含まれます。
* **"custom"** – 診断モニターによって監視されるディレクトリの構成に基づくカスタム コンテナーです。  この BLOB コンテナーの名前は WADDirectoriesTable で指定されます。

## <a name="tools-to-view-diagnostic-data"></a>診断データを表示するツール
ストレージへの転送後にデータを表示するには、いくつかのツールを利用できます。 次に例を示します。

* Visual Studio のサーバー エクスプローラー - Azure Tools for Microsoft Visual Studio がインストールされている場合、サーバー エクスプローラーの Azure Storage ノードを使用して、Azure ストレージ アカウントの読み取り専用の BLOB およびテーブル データを表示できます。 データは、ローカルのストレージ エミュレーター アカウントから表示できます。また、Azure 用に作成したストレージ アカウントから表示することもできます。 詳細については、「[サーバー エクスプローラーを使用したストレージ リソースの参照と管理](/visualstudio/azure/vs-azure-tools-storage-resources-server-explorer-browse-manage)」を参照してください。
* [Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) は、Windows、OSX、Linux で Azure Storage データを容易に操作できるスタンドアロン アプリです。
* [Azure Management Studio](https://www.cerebrata.com/products/azure-management-studio/introduction) に含まれている Azure Diagnostics Manager では、Azure で実行されているアプリケーションによって収集された診断データの表示、ダウンロード、管理を行うことができます。

## <a name="next-steps"></a>次の手順
[Azure Diagnostics で Cloud Services アプリケーションのフローをトレースする](../cloud-services/cloud-services-dotnet-diagnostics-trace-flow.md)


