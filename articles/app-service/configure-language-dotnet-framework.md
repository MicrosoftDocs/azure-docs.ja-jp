---
title: ASP.NET アプリの構成
description: Azure App Service で ASP.NET アプリを構成する方法を説明します。 この記事では、最も一般的な構成タスクを紹介しています。
ms.devlang: dotnet
ms.custom: devx-track-csharp, devx-track-azurecli
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 8ed6835583cc4881b19eee14ed392b193324535e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92744154"
---
# <a name="configure-an-aspnet-app-for-azure-app-service"></a>Azure App Service 向けの ASP.NET アプリを構成する

> [!NOTE]
> ASP.NET Core については、[Azure App Service 向けに ASP.NET Core アプリを構成する](configure-language-dotnetcore.md)に関するページを参照してください

ASP.NET アプリは、コンパイル済みバイナリとして Azure App Service にデプロイする必要があります。 Visual Studio 発行ツールではソリューションがビルドされてからコンパイル済みバイナリが直接デプロイされますが、App Service 展開エンジンではまずコード リポジトリがデプロイされ、その後にバイナリがコンパイルされます。

このガイドでは、ASP.NET 開発者向けに主要な概念と手順を説明します。 Azure App Service を初めて使用する場合は、まず [ASP.NET クイックスタート](quickstart-dotnet-framework.md)と [SQL Database を使った ASP.NET のチュートリアル](app-service-web-tutorial-dotnet-sqldatabase.md)に従ってください。

## <a name="show-supported-net-framework-runtime-versions"></a>サポートされている .NET Framework ランタイム バージョンを表示する

App Service では、サポートされているすべての .NET Framework バージョンが Windows インスタンスに既にインストールされています。 使用可能な .NET Framework ランタイムと SDK のバージョンを表示するには、`https://<app-name>.scm.azurewebsites.net/DebugConsole` に移動し、ブラウザーベースのコンソールで適切なコマンドを実行します。

CLR 4 ランタイム バージョンの場合 (.NET Framework 4 以上):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\.NETFramework\"
```

最新の .NET Framework バージョンをすぐに使用できない可能性があります。

CLR 2 ランタイム バージョンの場合 (.NET Framework 3.5 以下):

```CMD
ls "D:\Program Files (x86)\Reference Assemblies\Microsoft\Framework\"
```

## <a name="show-current-net-framework-runtime-version"></a>現在の .NET Framework ランタイム バージョンを表示する

[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp config show --resource-group <resource-group-name> --name <app-name> --query netFrameworkVersion
```

値 `v4.0` は、最新の CLR 4 バージョン (.NET Framework 4.x) が使用されていることを意味します。 値 `v2.0` は、CLR 2 バージョン (.NET Framework 3.5) が使用されていることを意味します。

## <a name="set-net-framework-runtime-version"></a>.NET Framework ランタイム バージョンを設定する

既定では、App Service はサポートされている最新の .NET Framework バージョンを使用して、ASP.NET アプリを実行します。 代わりに .NET Framework 3.5 を使用してアプリを実行するには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します (v2.0 は CLR 2 を意味します)。

```azurecli-interactive
az webapp config set --resource-group <resource-group-name> --name <app-name> --net-framework-version v2.0
```

## <a name="access-environment-variables"></a>環境変数へのアクセス

App Service では、アプリ コードの外部で[アプリ設定](configure-common.md#configure-app-settings)と接続文字列を設定できます。 その後、標準の ASP.NET パターンを使用して、すべてのクラスでそれらにアクセスできます。

```csharp
using System.Configuration;
...
// Get an app setting
ConfigurationManager.AppSettings["MySetting"];
// Get a connection string
ConfigurationManager.ConnectionStrings["MyConnection"];
}
```

App Service と *web.config* で同じ名前のアプリ設定を構成した場合は、App Service の値が *web.config* の値よりも優先されます。 ローカルの *web.config* の値ではアプリをローカルでデバッグできますが、App Service の値では実稼働設定の製品内でアプリを実行できます。 接続文字列は同じように機能します。 これにより、コード リポジトリの外部にアプリケーション シークレットを保存し、コードを変更することなく適切な値にアクセスできます。

## <a name="deploy-multi-project-solutions"></a>マルチ プロジェクト ソリューションをデプロイする

Visual Studio ソリューションに複数のプロジェクトが含まれている場合、Visual Studio の発行プロセスには、デプロイするプロジェクトの選択が既に含まれています。 ビルド自動化を有効にした状態で、Git や ZIP デプロイなどを使用して App Service 展開エンジンにデプロイすると、App Service 展開エンジンは、App Service アプリとして検出された最初の Web サイトまたは Web アプリケーション プロジェクトを選択します。 `PROJECT` アプリ設定を指定することで、App Service で使用するプロジェクトを指定できます。 たとえば、[Cloud Shell](https://shell.azure.com) で以下を実行します。

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings PROJECT="<project-name>/<project-name>.csproj"
```

## <a name="get-detailed-exceptions-page"></a>例外の詳細ページを表示する

Visual Studio デバッガーで ASP.NET アプリの実行中に例外が発生すると、ブラウザーに例外の詳細ページが表示されますが、App Service ではそのページは汎用のエラー メッセージに置き換わります。 App Service で例外の詳細ページを表示するには、*Web.config* ファイルを開き、`<system.web>` 要素の下に `<customErrors mode="Off"/>` 要素を追加します。 次に例を示します。

```xml
<system.web>
    <customErrors mode="Off"/>
</system.web>
```

更新された *Web.config* でアプリを再デプロイします。これで、同じ例外の詳細ページが表示されます。

## <a name="access-diagnostic-logs"></a>診断ログにアクセスする

[System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) を使用して、アプリケーション コードに診断メッセージを追加できます。 次に例を示します。 

```csharp
Trace.TraceError("Record not found!"); // Error trace
Trace.TraceWarning("Possible data loss"); // Warning trace
Trace.TraceInformation("GET /Home/Index"); // Information trace
```

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-no-h.md)]

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [チュートリアル:SQL Database を使用して Azure に ASP.NET アプリを作成する](app-service-web-tutorial-dotnet-sqldatabase.md)
