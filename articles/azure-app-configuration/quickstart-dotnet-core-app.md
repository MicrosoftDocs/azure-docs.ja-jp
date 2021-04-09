---
title: .NET Core による Azure App Configuration のクイック スタート | Microsoft Docs
description: このクイックスタートでは、Azure App Configuration でコードとは別にアプリケーション設定のストレージと管理を一元化して、.NET Core アプリを作成します。
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 0ff80287971365b1477be319dc7a04760687f6a9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98663402"
---
# <a name="quickstart-create-a-net-core-app-with-app-configuration"></a>クイック スタート:App Configuration を使用して .NET Core アプリを作成する

このクイック スタートでは、コードとは別にアプリケーション設定のストレージと管理を一元化するために、Azure App Configuration を .NET Core コンソール アプリに組み込みます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/dotnet)
- [.NET Core SDK](https://dotnet.microsoft.com/download) - [Azure Cloud Shell](https://shell.azure.com) でも利用できます。

## <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. **[構成エクスプローラー]**  >  **[+ 作成]**  >  **[キー値]** の順に選択して、次のキーと値のペアを追加します。

    | Key | 値 |
    |---|---|
    | TestApp:Settings:Message | Azure App Configuration からのデータ |

    **[ラベル]** と **[コンテンツの種類]** は、現時点では空にしておきます。

8. **[適用]** を選択します。

## <a name="create-a-net-core-console-app"></a>.NET Core コンソール アプリを作成する

[.NET Core コマンド ライン インターフェイス (CLI)](/dotnet/core/tools/) を使用して、新しい .NET Core コンソール アプリ プロジェクトを作成します。 Visual Studio ではなく .NET Core CLI を使用する利点は、Windows、macOS、および Linux プラットフォームで使用できることです。  代わりに、[Azure Cloud Shell](https://shell.azure.com) で提供されているプレインストールのツールを使用します。

1. プロジェクト用の新規フォルダーを作成します。

2. 新しいフォルダーで次のコマンドを実行して、新しい .NET Core コンソール アプリ プロジェクトを作成します。

    ```dotnetcli
    dotnet new console
    ```

## <a name="connect-to-an-app-configuration-store"></a>App Configuration ストアに接続する

1. 次のコマンドを実行して、`Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet パッケージへの参照を追加します。

    ```dotnetcli
    dotnet add package Microsoft.Extensions.Configuration.AzureAppConfiguration
    ```

2. 次のコマンドを実行して、プロジェクトのパッケージを復元します。

    ```dotnetcli
    dotnet restore
    ```

3. *Program.cs* を開き、.NET Core App Configuration プロバイダーへの参照を追加します。

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

4. `builder.AddAzureAppConfiguration()` メソッドを呼び出して App Configuration を使用するように、`Main` メソッドを更新します。

    ```csharp
    static void Main(string[] args)
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));

        var config = builder.Build();
        Console.WriteLine(config["TestApp:Settings:Message"] ?? "Hello world!");
    }
    ```

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. **ConnectionString** という名前の環境変数に、App Configuration ストアへのアクセス キーを設定します。 コマンド ラインで次のコマンドを実行します。

    ```cmd
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell を使用する場合は、次のコマンドを実行します。

    ```azurepowershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    macOS または Linux を使用する場合は、次のコマンドを実行します。

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

    変更を有効にするために、コマンド プロンプトを再起動します。 環境変数の値を出力して、正しく設定されていることを確認します。

2. 次のコマンドを実行して、コンソール アプリをビルドします。

    ```dotnetcli
    dotnet build
    ```

3. ビルドが正常に完了したら、次のコマンドを実行して、アプリをローカルで実行します。

    ```dotnetcli
    dotnet run
    ```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、新しい App Configuration ストアを作成して、[App Configuration プロバイダー](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration)から .NET Core コンソール アプリと共に使用しました。 構成設定を動的に更新するように .NET Core アプリを構成する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [動的な構成を有効にする](./enable-dynamic-configuration-dotnet-core.md)