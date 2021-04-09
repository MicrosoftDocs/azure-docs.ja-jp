---
title: .NET Framework アプリに機能フラグを追加するためのクイック スタート | Microsoft Docs | Microsoft Docs
description: .NET Framework アプリに機能フラグを追加し、Azure App Configuration で管理するためのクイック スタートです。
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.tgt_pltfrm: .NET
ms.workload: tbd
ms.date: 10/19/2020
ms.author: alkemper
ms.openlocfilehash: 513c826e11ff9dfe6ea94349c67620da9d1bba48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96932049"
---
# <a name="quickstart-add-feature-flags-to-a-net-framework-app"></a>クイック スタート:.NET Framework アプリに機能フラグを追加する

このクイックスタートでは、Azure App Configuration を .NET Framework アプリに組み込み、機能管理のエンドツーエンド実装を作成します。 App Configuration サービスを使用し、すべての機能フラグを一箇所に格納し、その状態を制御できます。 

.NET 機能管理ライブラリは、機能フラグのサポートにより、フレームワークを拡張します。 これらのライブラリは、.NET 構成システム上に構築されます。 .NET 構成プロバイダーを介して、App Configuration と統合されます。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション - [無料アカウントを作成する](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs)
- [.NET Framework 4.8](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>App Configuration ストアを作成する

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. **[機能マネージャー]**  >  **[+追加]** を選択して、`Beta` という機能フラグを追加します。

    > [!div class="mx-imgBorder"]
    > ![Beta という名前の機能フラグを有効にする](media/add-beta-feature-flag.png)

    現時点では `label` を空欄にしておいてください。

## <a name="create-a-net-console-app"></a>.NET コンソール アプリを作成する

1. Visual Studio を起動し、 **[ファイル]**  >  **[新規]**  >  **[プロジェクト]** の順に選択します。

1. **[新しいプロジェクトの作成]** で、 **[コンソール]** プロジェクトの種類をフィルターで選択し、 **[コンソール アプリ (.NET Framework)]** をクリックします。 **[次へ]** をクリックします。

1. **[新しいプロジェクトの構成]** で、プロジェクト名を入力します。 **[フレームワーク]** で、 **.NET Framework 4.8** 以上を選択します。 **Create** をクリックしてください。

## <a name="connect-to-an-app-configuration-store"></a>App Configuration ストアに接続する

1. プロジェクトを右クリックし、 **[NuGet パッケージの管理]** を選択します。 **[参照]** タブで以下の NuGet パッケージを検索し、プロジェクトに追加します。 見つからない場合は、 **[プレリリースを含める]** チェック ボックスをオンにします。

    ```
    Microsoft.Extensions.DependencyInjection
    Microsoft.Extensions.Configuration.AzureAppConfiguration
    Microsoft.FeatureManagement
    ```

1. *Program.cs* を開き、次のステートメントを追加します。

    ```csharp
    using Microsoft.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    using Microsoft.FeatureManagement;
    using System.Threading.Tasks;
    ```

1. `Main` メソッドを更新してアプリ構成に接続し、`UseFeatureFlags` オプションを指定して機能フラグが取得されるようにします。 `Beta` 機能フラグが有効になっている場合は、メッセージを表示します。

    ```csharp
        public static async Task Main(string[] args)
        {         
            IConfigurationRoot configuration = new ConfigurationBuilder()
                .AddAzureAppConfiguration(options =>
                {
                    options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                           .UseFeatureFlags();
                }).Build();

            IServiceCollection services = new ServiceCollection();

            services.AddSingleton<IConfiguration>(configuration).AddFeatureManagement();

            using (ServiceProvider serviceProvider = services.BuildServiceProvider())
            {
                IFeatureManager featureManager = serviceProvider.GetRequiredService<IFeatureManager>();

                if (await featureManager.IsEnabledAsync("Beta"))
                {
                    Console.WriteLine("Welcome to the beta!");
                }
            }

            Console.WriteLine("Hello World!");
            Console.WriteLine("Press any key to continue ...");
            Console.Read();
        }
    ```

## <a name="build-and-run-the-app-locally"></a>アプリをビルドしてローカルで実行する

1. **ConnectionString** という名前の環境変数を App Configuration ストアの接続文字列に設定します。 Windows コマンド プロンプトを使用する場合は、次のコマンドを実行します。

    ```console
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Windows PowerShell を使用する場合は、次のコマンドを実行します。

    ```powershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

1. 変更を有効にするために、Visual Studio を再起動します。 

1. Ctrl + F5 キーを押して、アプリをビルドし、実行します。

    ![機能フラグが有効になっているアプリ](./media/quickstarts/dotnet-app-feature-flag.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>次のステップ

このクイック スタートでは、アプリ構成に機能フラグを作成して、.NET Framework コンソール アプリと共に使用しました。 アプリケーションを再起動せずに機能フラグとその他の構成値を動的に更新する方法については、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [動的な構成を有効にする](./enable-dynamic-configuration-dotnet.md)
