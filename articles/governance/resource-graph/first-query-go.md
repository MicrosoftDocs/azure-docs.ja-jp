---
title: クイック スタート:初めての Go クエリ
description: このクイックスタートでは、手順に従って、Go 用の Resource Graph パッケージを有効にし、初めてのクエリを実行します。
ms.date: 01/27/2021
ms.topic: quickstart
ms.openlocfilehash: 4949801b3dc97904680d09e685fd225812a0e14d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98920064"
---
# <a name="quickstart-run-your-first-resource-graph-query-using-go"></a>クイック スタート:Go を使用して初めての Resource Graph クエリを実行する

Azure Resource Graph を使用する最初の手順では、Go に必要なパッケージがインストールされていることを確認します。 このクイックスタートでは、パッケージを Go のインストールに追加するプロセスについて説明します。

このプロセスを完了すると、パッケージを Go のインストールに追加し、初めての Resource Graph クエリを実行できます。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

## <a name="add-the-resource-graph-package"></a>Resource Graph パッケージを追加する

Go で Azure Resource Graph のクエリを実行できるようにするには、パッケージを追加する必要があります。 このパッケージは、[Windows 10 の bash](/windows/wsl/install-win10) やローカルにインストールされている場合も含め、Go を使用できる場所ならどこでも動作します。

1. 最新の Go がインストールされていることを確認します (**1.14** 以降)。 まだインストールされていない場合は、[Golang.org](https://golang.org/dl/) からダウンロードします。

1. 最新の Azure CLI がインストールされていることを確認します (**2.5.1** 以降)。 まだインストールされていない場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

   > [!NOTE]
   > Azure CLI は、次の例の `auth.NewAuthorizerFromCLI()` メソッドを Go で使用できるようにするために必要です。 その他のオプションについては、「[Azure SDK for Go - その他の認証の詳細](https://github.com/Azure/azure-sdk-for-go#more-authentication-details)」を参照してください。

1. Azure CLI から認証します。

   ```azurecli
   az login
   ```

1. 選択した Go 環境で、Azure Resource Graph に必要なパッケージをインストールします。

   ```bash
   # Add the Resource Graph package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="run-your-first-resource-graph-query"></a>最初の Resource Graph クエリを実行する

選択した環境に Go パッケージが追加されたので、簡単な Resource Graph クエリを試してみましょう。 このクエリでは、各リソースの **名前** と **リソースの種類** を使用して、最初の 5 つの Azure リソースが返されます。

1. Go アプリケーションを作成し、次のソースを `argQuery.go` として保存します。

   ```Go
   package main
   
   import (
      "fmt"
      "os"
      "context"
      "strconv"
      arg "github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph"
      "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
       // Get variables from command line arguments
       var query = os.Args[1]
       var subList = os.Args[2:]
   
       // Create and authorize a ResourceGraph client
       argClient := arg.New()
       authorizer, err := auth.NewAuthorizerFromCLI()
       if err == nil {
           argClient.Authorizer = authorizer
       } else {
           fmt.Printf(err.Error())
       }
     
       // Set options
       RequestOptions := arg.QueryRequestOptions {
           ResultFormat: "objectArray",
       }
     
       // Create the query request
       Request := arg.QueryRequest {
           Subscriptions: &subList,
           Query: &query,
           Options: &RequestOptions,
       }
     
       // Run the query and get the results
       var results, queryErr = argClient.Resources(context.Background(), Request)
       if queryErr == nil {
           fmt.Printf("Resources found: " + strconv.FormatInt(*results.TotalRecords, 10) + "\n")
           fmt.Printf("Results: " + fmt.Sprint(results.Data) + "\n")
       } else {
           fmt.Printf(queryErr.Error())
       }
   }
   ```

1. Go アプリケーションをビルドします。

   ```bash
   go build argQuery.go
   ```

1. コンパイル済みの Go アプリケーションを使用して、初めての Azure Resource Graph クエリを実行します。 `<SubID>` をサブスクリプション ID で置き換えます。

   ```bash
   argQuery "Resources | project name, type | limit 5" "<SubID>"
   ```

   > [!NOTE]
   > このクエリは`order by`などの並べ替え修飾子を示しませんので、このクエリを複数回実行すると要求あたり異なる一連のリソースを中断する可能性があります。

1. `argQuery` の最初のパラメーターを変更し、**Name** プロパティで並べ替える (`order by`) ようにクエリを変更します。 `<SubID>` をサブスクリプション ID で置き換えます。

   ```bash
   argQuery "Resources | project name, type | limit 5 | order by name asc" "<SubID>"
   ```

   > [!NOTE]
   > 最初のクエリと同様に、このクエリを複数回実行すると要求あたり異なる一連のリソースを生成する可能性があります。 クエリ コマンドの順序が重要です。 この例では、`limit` の後に `order by` がきます。 このコマンドの順序によって、まずクエリ結果が制限され、次にその結果が並べ替えられます。

1. `argQuery` の最初のパラメーターを変更し、まず **Name** プロパティで並べ替え (`order by`)、次に上位 5 件の結果に制限 (`limit`) するようにクエリを変更します。 `<SubID>` をサブスクリプション ID で置き換えます。

   ```bash
   argQuery "Resources | project name, type | order by name asc | limit 5" "<SubID>"
   ```

最後のクエリを複数回実行した場合、環境内で何も変更がないと仮定すると、返される結果は変わらず、**Name** プロパティで並べ替えられますが、引き続き上位 5 件の結果に制限されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

インストールしたパッケージを、お使いの Go 環境から削除する場合は、次のコマンドを使用します。

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/resourcegraph/mgmt/2019-04-01/resourcegraph
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Resource Graph パッケージを、お使いの Go 環境に追加し、初めてのクエリを実行しました。 Resource Graph 言語の詳細については、クエリ言語の詳細ページに進んでください。

> [!div class="nextstepaction"]
> [クエリ言語に関する詳細情報を入手します](./concepts/query-language.md)