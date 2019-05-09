---
title: Azure Functions ランタイム バージョンの概要
description: Azure Functions では、複数のバージョンのランタイムがサポートされます。 バージョン間の違いと、適切なバージョンを選択する方法について説明します。
services: functions
documentationcenter: ''
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 10/03/2018
ms.author: glenga
ms.openlocfilehash: 6988fb547b07f81891efea3caad8bf34f4c8a476
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "58889755"
---
# <a name="azure-functions-runtime-versions-overview"></a>Azure Functions ランタイム バージョンの概要

 Azure Functions ランタイムには、次の 2 つのメジャー バージョンがあります:1.x と 2.x。 新機能が動作し、機能強化が行われている現在のバージョンは 2.x ですが、どちらも運用シナリオに対してサポートされています。  以下では、2 つのバージョンの違い、各バージョンを作成する方法、および 1.x から 2.x にアップグレードする方法について詳しく説明します。

> [!NOTE]
> この記事は、クラウド サービスの Azure Functions に関するものです。 オンプレミスで Azure Functions を実行できるプレビュー製品については、「[Azure Functions Runtime の概要 (プレビュー)](functions-runtime-overview.md)」をご覧ください。

## <a name="cross-platform-development"></a>クロスプラットフォーム開発

バージョン 2.x のランタイムは .NET Core 2 で実行され、ランタイム 2.x は、macOS や Linux など、.NET Core でサポートされるすべてのプラットフォームで実行できます。 .NET Core 上で実行されることで、クロスプラットフォーム開発とホスティング シナリオが可能になります。

比較すると、バージョン 1.x のランタイムは、Azure portal または Windows コンピューターでの開発とホスティングのみをサポートしています。

## <a name="languages"></a>Languages

バージョン2.x のランタイム では、新しい言語拡張モデルが使用されます。 バージョン 2.x では、関数アプリ内のすべての関数は同じ言語を共有する必要があります。 関数アプリ内の関数の言語は、アプリの作成時に選択されます。

Azure Functions 1.x の試験段階の言語は、新しいモデルを使用するよう更新されていないため、2.x ではサポートされません。 次の表は、各ランタイム バージョンでどのプログラミング言語が現在サポートされているかを示しています。

[!INCLUDE [functions-supported-languages](../../includes/functions-supported-languages.md)]

詳細については、[サポートされている言語](supported-languages.md)に関するページを参照してください。

## <a name="creating-1x-apps"></a>バージョン 1.x での実行

既定では、Azure portal で作成された関数アプリはバージョン 2.x に設定されます。 新機能への投資が行われているので、可能であれば、このランタイム バージョンを使用するようにします。 必要に応じて、バージョン 1.x ランタイム上で関数アプリを実行することもできます。 ランタイムのバージョンを変更できるのは、関数アプリを作成してから関数を追加するまでの間のみです。 ランタイム バージョンを 1.x に固定する方法については、「[現在のランタイム バージョンの表示と更新](set-runtime-version.md#view-and-update-the-current-runtime-version)」を参照してください。

## <a name="migrating-from-1x-to-2x"></a>1.x から 2.x への移行

バージョン 2.x ではなくバージョン 1.x のランタイムを使用するように記述された既存のアプリを移行することができます。 必要な変更の大部分は、.NET Framework 4.7 と .NET Core 2 間の C# API の変更など、言語ランタイムの変更に関連する変更です。 また、コードとライブラリが、選択した言語ランタイムと互換性があることを確認する必要があります。 最後に、以下で示すトリガー、バインド、および機能での変更にも注意してください。 最適な移行結果を得るには、バージョン 2.x 用の新しい関数アプリを作成し、既存のバージョン 1.x の関数コードを新しいアプリに移植する必要があります。  

### <a name="changes-in-triggers-and-bindings"></a>トリガーとバインドでの変更

バージョン 2.x では、アプリの関数から使用される特定のトリガーとバインドの拡張機能をインストールする必要があります。 この例外は、拡張機能を必要としない HTTP トリガーとタイマー トリガーのみです。  詳細については、[バインド拡張機能の登録とインストール](./functions-bindings-register.md)に関するページを参照してください。

バージョン間には、関数の `function.json` や属性の変更もいくつかあります。 たとえば、イベント ハブの `path` のプロパティは `eventHubName` になりました。 各バインドのドキュメントへのリンクについては、[既存のバインド一覧](#bindings)を参照してください。

### <a name="changes-in-features-and-functionality"></a>機能の変更

新しいバージョンでは、いくつかの機能が削除、更新、または置き換えられました。 このセクションでは、バージョン 1.x の後にバージョン 2.x を使用した場合に気づく変更点について詳しく説明します。

バージョン 2.x では次の点が変更されました。

* HTTP エンドポイントを呼び出すキーは、Azure Blob Storage 内で常に暗号化されて格納されます。 バージョン 1.x では、既定で Azure Files ストレージにキーが格納されていました。 アプリをバージョン 1.x からバージョン 2.x にアップグレードすると、ファイル ストレージ内の既存のシークレットはリセットされます。

* バージョン 2.x ランタイムには、Webhook プロバイダーの組み込みサポートは含まれていません。 この変更はパフォーマンスを向上するために行われました。 Webhook のエンドポイントとして HTTP トリガーを使用することもできます。

* ホスト構成ファイル (host.json) は空か、文字列 `"version": "2.0"` が含まれる必要があります。

* 監視を改善するために、[`AzureWebJobsDashboard`](functions-app-settings.md#azurewebjobsdashboard) 設定を使用したポータルの WebJobs ダッシュボードは、[`APPINSIGHTS_INSTRUMENTATIONKEY`](functions-app-settings.md#appinsights_instrumentationkey) 設定を使用する Azure Application Insights に置き換えられています。 詳しくは、「[Azure Functions を監視する](functions-monitoring.md)」をご覧ください。

* 関数アプリ内のすべての関数は、同じ言語を共有する必要があります。 関数アプリを作成するときに、そのアプリのランタイム スタックを選択する必要があります。 ランタイム スタックは、アプリケーション設定の [`FUNCTIONS_WORKER_RUNTIME`](functions-app-settings.md#functions_worker_runtime) 値によって指定されます。 メモリ占有領域と起動時間を改善するために、この要件が追加されました。 ローカルで開発する場合は、この設定も [local.settings.json ファイル](functions-run-local.md#local-settings-file) に含める必要があります。

* App Service プランでの関数に対する既定のタイムアウトは 30 分に変更されます。 host.json の [functionTimeout](functions-host-json.md#functiontimeout) 設定を使用して、手動でタイムアウトを無制限に変更することができます。

* 従量課金プラン機能の場合、HTTP 同時実行のスロットルは既定で実装され、インスタンスあたりの既定の同時要求数は 100 です。 これは host.json ファイルの [`maxConcurrentRequests`](functions-host-json.md#http) 設定で変更できます。

* [.NET Core の制限](https://github.com/Azure/azure-functions-host/issues/3414)があるため、F# スクリプト (.fsx) 関数のサポートが削除されました。 コンパイル済みの F# 関数 (.fs) は引き続きサポートされます。

* Event Grid トリガー Webhook の URL 形式は `https://{app}/runtime/webhooks/{triggerName}` に変更されました。

### <a name="migrating-a-locally-developed-application"></a>ローカルで開発されたアプリケーションの移行

バージョン 1.x ランタイムを使用してローカルで開発した既存の関数アプリ プロジェクトがあるとします。 バージョン 2.x にアップグレードするには、バージョン 2.x に対してローカル関数アプリ プロジェクトを作成し、既存のコードを新しいアプリに移植する必要があります。 既存のプロジェクトとコードを手動で更新することができます。これは一種の "インプレース" アップグレードです。 ただし、バージョン 1.x とバージョン 2.x 間にはまだ改善の余地がいくつかあります。 たとえば、C# ではデバッグ オブジェクトが `TraceWriter` から `ILogger` に変更されました。 最新のバージョン 2.x のテンプレートに基づいて新しいバージョン 2.x プロジェクトを作成することで、更新された関数から始めます。

#### <a name="visual-studio-runtime-versions"></a>Visual Studio のランタイム バージョン

Visual Studio では、プロジェクトを作成するときにランタイムのバージョンを選択します。 Visual Studio 用の Azure Functions ツールは、ランタイムの両メジャー バージョンをサポートしています。 デバッグ時と公開時には、プロジェクトの設定に基づいて正しいバージョンが使用されます。 バージョン設定は、`.csproj` ファイルの次のプロパティで定義されています。

##### <a name="version-1x"></a>バージョン 1.x

```xml
<TargetFramework>net461</TargetFramework>
<AzureFunctionsVersion>v1</AzureFunctionsVersion>
```

##### <a name="version-2x"></a>バージョン 2.x

```xml
<TargetFramework>netcoreapp2.2</TargetFramework>
<AzureFunctionsVersion>v2</AzureFunctionsVersion>
```

プロジェクトをデバッグまたは公開すると、正しいバージョンのランタイムが使用されます。

#### <a name="vs-code-and-azure-functions-core-tools"></a>VS Code と Azure Functions Core Tools

[Azure Functions Core Tools](functions-run-local.md) は、コマンド ラインの開発に使用されます。また、Visual Studio Code 用の [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)からも使用されます。 バージョン 2.x に対して開発するには、Core Tools のバージョン 2.x をインストールします。 バージョン 1.x の開発には Core Tools のバージョン 1.x が必要です。 詳細については、「[Azure Functions Core Tools のインストール](functions-run-local.md#install-the-azure-functions-core-tools)」を参照してください。

Visual Studio Code の開発の場合は、必要に応じてインストールされているツールのバージョンと一致するように `azureFunctions.projectRuntime` のユーザー設定を更新します。  この設定で、関数アプリの作成時に使用されるテンプレートと言語も更新されます。

### <a name="changing-version-of-apps-in-azure"></a>Azure でのアプリのバージョンの変更

Azure の公開アプリから使用される Functions ランタイムのバージョンは、[`FUNCTIONS_EXTENSION_VERSION`](functions-app-settings.md#functions_extension_version) アプリケーションの設定によって決まります。 `~2` の値はバージョン 2.x のランタイムをターゲットにし、`~1` はバージョン 1.x のランタイムをターゲットにしています。 他のアプリ設定の変更や関数のコード変更が必要になる可能性があるため、この設定を任意に変更しないでください。 関数アプリを別のランタイム バージョンに移行する場合に推奨される方法については、「[Azure Functions ランタイム バージョンをターゲットにする方法](set-runtime-version.md)」を参照してください。

## <a name="bindings"></a>バインド

バージョン 2.x のランタイムで使用される新しい[バインド拡張モデル](https://github.com/Azure/azure-webjobs-sdk-extensions/wiki/Binding-Extensions-Overview)の利点は、次のとおりです。

* サード パーティのバインド拡張のサポート。

* ランタイムとバインドの分離。 この変更により、バインド拡張を個別にバージョン管理したり、解放したりできます。 たとえば、基になる SDK の新しいバージョンに依存する拡張のバージョンにアップグレードするよう選択できます。

* より軽量な実行環境。ここでは、使用中のバインドのみがランタイムによって識別され、読み込まれます。

HTTP とタイマーのトリガーを除き、すべてのバインドを関数アプリ プロジェクトに明示的に追加するか、ポータルで登録する必要があります。 詳細については、「[バインディング拡張機能を登録する](./functions-bindings-expressions-patterns.md)」を参照してください。

各ランタイム バージョンでサポートされるバインドを次の表に示します。

[!INCLUDE [Full bindings table](../../includes/functions-bindings.md)]

[!INCLUDE [Timeout Duration section](../../includes/functions-timeout-duration.md)]

## <a name="next-steps"></a>次の手順

詳細については、次のリソースを参照してください。

* [Azure Functions をローカルでコーディングしてテストする](functions-run-local.md)
* [Azure Functions Runtime バージョンをターゲットにする方法](set-runtime-version.md)
* [リリース ノート](https://github.com/Azure/azure-functions-host/releases)
