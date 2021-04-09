---
title: Azure Functions で .NET および .NET Core アプリのスナップショット デバッガーを有効にする | Microsoft Docs
description: Azure Functions で .NET および .NET Core アプリのスナップショット デバッガーを有効にする
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 12/18/2020
ms.openlocfilehash: ac25962cac36a149807b67a44b3b88a4f40c954a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102211942"
---
# <a name="enable-snapshot-debugger-for-net-and-net-core-apps-in-azure-functions"></a>Azure Functions で .NET および .NET Core アプリのスナップショット デバッガーを有効にする

スナップショット デバッガーは、現在、Windows サービス プランの Azure Functions で実行されている ASP.NET アプリと ASP.NET Core アプリで機能します。

スナップショット デバッガーを使用する場合は、Basic サービス レベル以上でアプリケーションを実行することをお勧めします。

ほとんどのアプリケーションの場合、Free および Shared サービス レベルでは、スナップショットを保存するための十分なメモリまたはディスク領域がありません。

## <a name="prerequisites"></a>[前提条件]

* [関数アプリで Application Insights の監視を有効にする](../../azure-functions/configure-monitoring.md#add-to-an-existing-function-app)

## <a name="enable-snapshot-debugger"></a> スナップショット デバッガーを有効にする

別の種類の Azure サービスを実行している場合、他のサポート対象プラットフォームでスナップショット デバッガーを有効にする手順については次を参照してください。
* [Azure App Service](snapshot-debugger-appservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric サービス](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines と仮想マシン スケール セット](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)
* [オンプレミスの仮想マシンまたは物理マシン](snapshot-debugger-vm.md?toc=/azure/azure-monitor/toc.json)

関数アプリでスナップショット デバッガーを有効にするには、以下に定義するようにプロパティ `snapshotConfiguration` を追加して `host.json` ファイルを更新し、関数を再デプロイする必要があります。

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

スナップショット デバッガーは、Azure Functions ランタイムの一部としてプレインストールされており、既定では無効になっています。

スナップショット デバッガーは Azure Functions ランタイムに含まれているため、NuGet パッケージやアプリケーション設定を追加する必要はありません。

単純な関数アプリ (.NET Core) の参照と同様に、次の例では、スナップショット デバッガーを有効にした後に、`.csproj`、`{Your}Function.cs`、および `host.json`を確認する方法について説明します。

プロジェクト csproj

```xml
<Project Sdk="Microsoft.NET.Sdk">
<PropertyGroup>
    <TargetFramework>netcoreapp2.1</TargetFramework>
    <AzureFunctionsVersion>v2</AzureFunctionsVersion>
</PropertyGroup>
<ItemGroup>
    <PackageReference Include="Microsoft.NET.Sdk.Functions" Version="1.0.31" />
</ItemGroup>
<ItemGroup>
    <None Update="host.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    </None>
    <None Update="local.settings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
    </None>
</ItemGroup>
</Project>
```

Function クラス

```csharp
using System;
using System.Threading.Tasks;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.AspNetCore.Http;
using Microsoft.Extensions.Logging;

namespace SnapshotCollectorAzureFunction
{
    public static class ExceptionFunction
    {
        [FunctionName("ExceptionFunction")]
        public static Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", Route = null)] HttpRequest req,
            ILogger log)
        {
            log.LogInformation("C# HTTP trigger function processed a request.");

            throw new NotImplementedException("Dummy");
        }
    }
}
```

ホスト ファイル

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true
      }
    }
  }
}
```

## <a name="enable-snapshot-debugger-for-other-clouds"></a>他のクラウドに対してスナップショット デバッガーを有効にする

現在、エンドポイントの変更が必要なリージョンは [Azure Government](https://docs.microsoft.com/azure/azure-government/compare-azure-government-global-azure#application-insights) と [Azure China](https://docs.microsoft.com/azure/china/resources-developer-guide) のみです。

米国政府のクラウド エージェント エンドポイントで更新された `host.json` の例を次に示します。
```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "samplingExcludedTypes": "Request",
      "samplingSettings": {
        "isEnabled": true
      },
      "snapshotConfiguration": {
        "isEnabled": true,
        "agentEndpoint": "https://snapshot.monitor.azure.us"
      }
    }
  }
}
```

以下は、スナップショット デバッガー エージェント エンドポイントでサポートされているオーバーライドです。

|プロパティ    | 米国政府のクラウド | China Cloud |   
|---------------|---------------------|-------------|
|AgentEndpoint         | `https://snapshot.monitor.azure.us`    | `https://snapshot.monitor.azure.cn` |

## <a name="disable-snapshot-debugger"></a>スナップショット デバッガーを無効にする

関数アプリでスナップショット デバッガーを無効にするには、プロパティ `snapshotConfiguration.isEnabled` を `false` に設定して `host.json` ファイルを更新する必要があります。

```json
{
  "version": "2.0",
  "logging": {
    "applicationInsights": {
      "snapshotConfiguration": {
        "isEnabled": false
      }
    }
  }
}
```

アプリケーション例外の診断を容易にするために、すべてのアプリでスナップショット デバッガーを有効にすることをお勧めします。

## <a name="next-steps"></a>次のステップ

- 例外をトリガーできるアプリケーションへのトラフィックを生成します。 その後、Application Insights インスタンスにスナップショットが送信がされるまで 10 ～ 15 分待機します。
- Azure portal で[スナップショットを確認します](snapshot-debugger.md?toc=/azure/azure-monitor/toc.json#view-snapshots-in-the-portal)。
- 関数アプリのユース ケースに基づいてスナップショット デバッガーの構成をカスタマイズします。 詳細については、[host.json のスナップショット構成](../../azure-functions/functions-host-json.md#applicationinsightssnapshotconfiguration)に関するページを参照してください。
- スナップショット デバッガーの問題のトラブルシューティングについては、[スナップショット デバッガーのトラブルシューティング](snapshot-debugger-troubleshoot.md?toc=/azure/azure-monitor/toc.json)のページを参照してください。