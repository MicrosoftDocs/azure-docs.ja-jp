---
title: 継続的インテグレーションと継続的デリバリーのパイプラインを使用して Azure App Configuration を統合する
description: Azure App Configuration を使用して継続的インテグレーションと継続的デリバリーを導入する方法について説明します
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: tutorial
ms.custom: devx-track-csharp
ms.date: 04/19/2020
ms.author: alkemper
ms.openlocfilehash: 3a4d171f0e3225db195c5c2b71ca99a3386e3a36
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99979846"
---
# <a name="integrate-with-a-cicd-pipeline"></a>CI/CD パイプラインとの統合

この記事では、継続的インテグレーションと継続的デプロイ システムで Azure App Configuration からのデータを使用する方法について説明します。

## <a name="use-app-configuration-in-your-azure-devops-pipeline"></a>Azure DevOps パイプラインで App Configuration を使用する

Azure DevOps パイプラインがある場合は、App Configuration からキー値をフェッチしてタスク変数として設定することができます。 [Azure App Configuration DevOps 拡張機能](https://go.microsoft.com/fwlink/?linkid=2091063)は、この機能を提供するアドオン モジュールです。 ビルドまたはリリース タスク シーケンスでこの拡張機能を使用するには、その指示に従ってください。

## <a name="deploy-app-configuration-data-with-your-application"></a>App Configuration データをアプリケーションと共にデプロイする

アプリケーションは、Azure App Configuration に依存していてそれに到達できない場合、実行に失敗する可能性があります。 アプリケーションと一緒にデプロイされてその起動時にローカルから読み込まれるファイルに構成データをパッケージ化すれば、アプリケーションの回復性を強化することができます。 このアプローチを利用すると、アプリケーションの起動時に、その既定値が確実に設定されます。 これらの値は、App Configuration ストアが使用できる状態になったときに、新しい変更によって上書きされます。

Azure App Configuration の[エクスポート](./howto-import-export-data.md#export-data)機能を使用して、現在の構成データを 1 つのファイルとして取得するプロセスを自動化できます。 次に、継続的インテグレーションと継続的配信 (CI/CD) パイプラインのビルドまたはデプロイ手順にこのファイルを組み込むことができます。

クイック スタートで紹介した Web アプリのビルド手順として App Configuration データを含める方法の例を次に示します。 先に進む前に、[App Configuration を使用した ASP.NET Core アプリの作成](./quickstart-aspnet-core-app.md)を完了しておいてください。

このチュートリアルの手順は、任意のコード エディターを使用して実行できます。 推奨のエディターは [Visual Studio Code](https://code.visualstudio.com/) です (Windows、macOS、および Linux プラットフォームで使用できます)。

### <a name="prerequisites"></a>前提条件

ローカルでビルドする場合、[Azure CLI](/cli/azure/install-azure-cli) をまだインストールしていない場合は、ダウンロードしてインストールします。

(たとえば Azure DevOps で) クラウド ビルドを実行するには、必ず [Azure CLI](/cli/azure/install-azure-cli) をビルド システムにインストールします。

### <a name="export-an-app-configuration-store"></a>App Configuration ストアをエクスポートする

1. *.csproj* ファイルを開き、次のスクリプトを追加します。

    ```xml
    <Target Name="Export file" AfterTargets="Build">
        <Message Text="Export the configurations to a temp file. " />
        <Exec WorkingDirectory="$(MSBuildProjectDirectory)" Condition="$(ConnectionString) != ''" Command="az appconfig kv export -d file --path $(OutDir)\azureappconfig.json --format json --separator : --connection-string $(ConnectionString)" />
    </Target>
    ```
1. *Program.cs* を開き、`config.AddJsonFile()` メソッドを呼び出して、エクスポートされた JSON ファイルを使用するように `CreateWebHostBuilder` メソッドを更新します。  `System.Reflection` 名前空間も追加してください。

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var directory = System.IO.Path.GetDirectoryName(Assembly.GetExecutingAssembly().Location);
                var settings = config.Build();

                config.AddJsonFile(Path.Combine(directory, "azureappconfig.json"));
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

### <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. **ConnectionString** という名前の環境変数に、App Configuration ストアへのアクセス キーを設定します。 
    Windows コマンド プロンプトを使用する場合は、次のコマンドを実行してコマンド プロンプトを再起動し、変更が反映されるようにします。

    ```console
     setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell を使用する場合は、次のコマンドを実行します。

    ```powershell
     $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    macOS または Linux を使用する場合は、次のコマンドを実行します。

    ```console
     export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. .NET Core CLI を使用してアプリケーションをビルドするには、コマンド シェルで次のコマンドを実行します。

    ```console
     dotnet build
    ```

3. ビルドが正常に完了したら、次のコマンドを実行して、Web アプリをローカルで実行します。

    ```console
     dotnet run
    ```

4. ブラウザー ウィンドウを開いて、`http://localhost:5000` (ローカルでホストされた Web アプリの既定の URL) に移動します。

    ![クイック スタートのアプリ (ローカルで起動)](./media/quickstarts/aspnet-core-app-launch-local.png)

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、Azure App Configuration データをデプロイ パイプラインで使用するためにエクスポートしました。 App Configuration の使用方法の詳細については、Azure CLI のサンプルに進んでください。

> [!div class="nextstepaction"]
> [Azure CLI](/cli/azure/appconfig)
