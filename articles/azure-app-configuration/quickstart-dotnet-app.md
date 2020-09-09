---
title: .NET Framework を使用した Azure App Configuration のクイック スタート | Microsoft Docs
description: この記事では、Azure App Configuration でコードとは別にアプリケーション設定のストレージと管理を一元化して、.NET Framework アプリを作成します。
services: azure-app-configuration
documentationcenter: ''
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 12/17/2019
ms.author: lcozzens
ms.openlocfilehash: 6795d10950ddd7b03dfa505ab44d2f43837c9045
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590270"
---
# <a name="quickstart-create-a-net-framework-app-with-azure-app-configuration"></a>クイック スタート:Azure App Configuration を使用して .NET Framework アプリを作成する

このクイック スタートでは、コードとは別にアプリケーション設定のストレージと管理を一元化するために、Azure App Configuration を .NET Framework ベースのコンソール アプリに組み込みます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.7.2](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. **[構成エクスプローラー]**  >  **[+ 作成]**  >  **[キー値]** の順に選択して、次のキーと値のペアを追加します。

    | Key | 値 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration からのデータ |

    **[ラベル]** と **[コンテンツの種類]** は、現時点では空にしておきます。

7. **[適用]** を選択します。

## <a name="create-a-net-console-app"></a>.NET コンソール アプリを作成する

1. Visual Studio を起動し、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に選択します。

1. **[新しいプロジェクトの作成]** で、 **[コンソール]** プロジェクトの種類をフィルターで選択し、 **[コンソール アプリ (.NET Framework)]** をクリックします。 **[次へ]** を選択します。

1. **[新しいプロジェクトの構成]** で、プロジェクト名を入力します。 **[フレームワーク]** で、 **.NET Framework 4.7.1** 以上を選択します。 **［作成］** を選択します

## <a name="connect-to-an-app-configuration-store"></a>App Configuration ストアに接続する

1. プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。 **[参照]** タブで以下の NuGet パッケージを検索し、プロジェクトに追加します。

    ```
    Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration 1.0.0 or later
    Microsoft.Configuration.ConfigurationBuilders.Environment 2.0.0 or later
    System.Configuration.ConfigurationManager version 4.6.0 or later
    ```

1. プロジェクトの *App.config* ファイルを次のように更新します。

    ```xml
    <configSections>
        <section name="configBuilders" type="System.Configuration.ConfigurationBuildersSection, System.Configuration, Version=4.0.0.0, Culture=neutral, PublicKeyToken=b03f5f7f11d50a3a" restartOnExternalChanges="false" requirePermission="false" />
    </configSections>

    <configBuilders>
        <builders>
            <add name="MyConfigStore" mode="Greedy" connectionString="${ConnectionString}" type="Microsoft.Configuration.ConfigurationBuilders.AzureAppConfigurationBuilder, Microsoft.Configuration.ConfigurationBuilders.AzureAppConfiguration" />
            <add name="Environment" mode="Greedy" type="Microsoft.Configuration.ConfigurationBuilders.EnvironmentConfigBuilder, Microsoft.Configuration.ConfigurationBuilders.Environment" />
        </builders>
    </configBuilders>

    <appSettings configBuilders="Environment,MyConfigStore">
        <add key="AppName" value="Console App Demo" />
        <add key="ConnectionString" value ="Set via an environment variable - for example, dev, test, staging, or production connection string." />
    </appSettings>
    ```

   App Configuration ストアの接続文字列は環境変数 `ConnectionString` から読み取られます。 `appSettings` セクションの `configBuilders` プロパティの `MyConfigStore` の前に、`Environment` 構成ビルダーを追加します。

1. *Program.cs* を開き、`ConfigurationManager` を呼び出して App Configuration を使用するように `Main` メソッドを更新します。

    ```csharp
    static void Main(string[] args)
    {
        string message = System.Configuration.ConfigurationManager.AppSettings["TestApp:Settings:Message"];

        Console.WriteLine(message);
    }
    ```

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. **ConnectionString** という名前の環境変数を App Configuration ストアの接続文字列に設定します。 Windows コマンド プロンプトを使用する場合は、次のコマンドを実行します。

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell を使用する場合は、次のコマンドを実行します。

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```
1. 変更を有効にするために、Visual Studio を再起動します。 Ctrl + F5 キーを押して、アプリをビルドし、実行します。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、新しい App Configuration ストアを作成して、.NET Framework コンソール アプリと共に使用しました。 アプリケーションの起動後、`ConfigurationManager` の値 `AppSettings` は変化しません。 ただし、App Configuration .NET Standard 構成プロバイダー ライブラリは、.NET Framework アプリでも使用できます。 .NET Framework アプリで構成設定を動的に更新できるようにする方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [動的な構成を有効にする](./enable-dynamic-configuration-dotnet.md)
