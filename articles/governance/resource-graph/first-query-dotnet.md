---
title: クイック スタート:自分の初めての .NET Core クエリ
description: このクイックスタートでは、手順に従い、.NET Core 用の Resource Graph NuGet パッケージを有効にし、自分の初めてのクエリを実行します。
ms.date: 01/27/2021
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 0135dfd499af48b3c60314679f4c9b635a5ce15a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98917573"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-net-core"></a>クイック スタート:.NET Core を使用して自分の初めての Resource Graph クエリを実行する

初めて Azure Resource Graph を使用するには、まず .NET Core に必要なパッケージがインストールされていることを確認します。 このクイックスタートでは、パッケージをお使いの .NET Core のインストールに追加するプロセスについて説明します。

このプロセスを完了すると、パッケージがお使いの .NET Core のインストールに追加され、自分の初めての Resource Graph クエリを実行できるようになります。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプション。 Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。
- _clientId_ と _clientSecret_ を含む Azure サービス プリンシパル。 Resource Graph で使用するサービス プリンシパルがない場合、または新規作成したい場合は、[.NET 認証のための Azure 管理ライブラリ](/dotnet/azure/sdk/authentication#mgmt-auth)に関するページを参照してください。
  次の手順で実行するので、.NET Core パッケージをインストールする手順はスキップします。

## <a name="create-the-resource-graph-project"></a>Resource Graph プロジェクトを作成する

.NET Core で Azure Resource Graph に対しクエリを実行できるようにするには、新しいコンソール アプリケーションを作成し、必要なパッケージをインストールします。

1. 最新の .NET Core がインストールされていることを確認します (**3.1.5** 以降)。 まだインストールされていない場合は、[dotnet.microsoft.com](https://dotnet.microsoft.com/download/dotnet-core) からダウンロードします。

1. "argQuery" という名前の新しい .NET Core コンソール アプリケーションを初期化します。

   ```dotnetcli
   dotnet new console --name "argQuery"
   ```

1. 新しいプロジェクト フォルダーにディレクトリを変更し、次のように Azure Resource Graph に必要なパッケージをインストールします。

   ```dotnetcli
   # Add the Resource Graph package for .NET Core
   dotnet add package Microsoft.Azure.Management.ResourceGraph --version 2.0.0

   # Add the Azure app auth package for .NET Core
   dotnet add package Microsoft.Azure.Services.AppAuthentication --version 1.5.0
   ```

1. 既定の `program.cs` を次のコードに置き換え、更新したファイルを保存します。

   ```csharp
   using System;
   using System.Collections.Generic;
   using System.Threading.Tasks;
   using Microsoft.IdentityModel.Clients.ActiveDirectory;
   using Microsoft.Rest;
   using Microsoft.Azure.Management.ResourceGraph;
   using Microsoft.Azure.Management.ResourceGraph.Models;

   namespace argQuery
   {
       class Program
       {
           static async Task Main(string[] args)
           {
               string strTenant = args[0];
               string strClientId = args[1];
               string strClientSecret = args[2];
               string strSubscriptionId = args[3];
               string strQuery = args[4];

               AuthenticationContext authContext = new AuthenticationContext("https://login.microsoftonline.com/" + strTenant);
               AuthenticationResult authResult = await authContext.AcquireTokenAsync("https://management.core.windows.net", new ClientCredential(strClientId, strClientSecret));
               ServiceClientCredentials serviceClientCreds = new TokenCredentials(authResult.AccessToken);

               ResourceGraphClient argClient = new ResourceGraphClient(serviceClientCreds);
               QueryRequest request = new QueryRequest();
               request.Subscriptions = new List<string>(){ strSubscriptionId };
               request.Query = strQuery;

               QueryResponse response = argClient.Resources(request);
               Console.WriteLine("Records: " + response.Count);
               Console.WriteLine("Data:\n" + response.Data);
           }
       }
   }
   ```

1. `argQuery` コンソール アプリケーションをビルドして発行します。

   ```dotnetcli
   dotnet build
   dotnet publish -o {run-folder}
   ```

## <a name="run-your-first-resource-graph-query"></a>最初の Resource Graph クエリを実行する

.NET Core コンソール アプリケーションをビルドして発行したので、単純な Resource Graph を試してみましょう。 このクエリでは、各リソースの **名前** と **リソースの種類** を使用して、最初の 5 つの Azure リソースが返されます。

`argQuery` に対するそれぞれの呼び出しには、自分独自の値で置き換える必要のある変数があります。

- `{tenantId}` - 実際のテナント ID に置き換えます
- `{clientId}` - ご自分のサービス プリンシパルのクライアント ID に置き換えます
- `{clientSecret}` - ご自分のサービス プリンシパルのクライアント シークレットに置き換えます
- `{subscriptionId}` - サブスクリプション ID で置き換えます

1. ディレクトリを、前の `dotnet publish` コマンドで定義した `{run-folder}` に変更します。

1. コンパイル済みの .NET Core アプリケーションを使用して、ご自分の初めての Azure Resource Graph クエリを実行します。

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5"
   ```

   > [!NOTE]
   > このクエリの例では `order by` などの並べ替え修飾子が指定されていないため、、このクエリを複数回実行すると要求ごとに異なるリソース セットが生成される可能性があります。

1. 最後のパラメーターを `argQuery.exe` に変更し、**Name** プロパティで並べ替える (`order by`) ようにクエリを変更します。

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | limit 5 | order by name asc"
   ```

   > [!NOTE]
   > 最初のクエリと同様に、このクエリを複数回実行すると要求あたり異なる一連のリソースを生成する可能性があります。 クエリ コマンドの順序が重要です。 この例では、`limit` の後に `order by` がきます。 このコマンドの順序によって、まずクエリ結果が制限され、次にその結果が並べ替えられます。

1. 最後のパラメーターを `argQuery.exe` に変更し、まず **Name** プロパティで並べ替え (`order by`)、次に上位 5 件の結果に制限 (`limit`) するようにクエリを変更します。

   ```bash
   argQuery "{tenantId}" "{clientId}" "{clientSecret}" "{subscriptionId}" "Resources | project name, type | order by name asc | limit 5"
   ```

最後のクエリを複数回実行した場合、環境内で何も変更がないと仮定すると、返される結果は変わらず、**Name** プロパティで並べ替えられますが、引き続き上位 5 件の結果に制限されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

.NET Core コンソール アプリケーションとインストールされているパッケージを削除する場合は、`argQuery` プロジェクト フォルダーを削除します。

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、必要な Resource Graph パッケージを使用して .NET Core コンソール アプリケーションを作成し、ご自分の最初のクエリを実行しました。 Resource Graph 言語の詳細については、クエリ言語の詳細ページに進んでください。

> [!div class="nextstepaction"]
> [クエリ言語に関する詳細情報を入手します](./concepts/query-language.md)