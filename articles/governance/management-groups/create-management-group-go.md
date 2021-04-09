---
title: 'クイックスタート: Go を使用して管理グループを作成する'
description: このクイックスタートでは、Go を使用して、リソースをリソース階層に整理する管理グループを作成します。
ms.date: 09/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 7b6ca4d10f2a86ecb55fec2afe72b4aabfbb94f9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100101669"
---
# <a name="quickstart-create-a-management-group-with-go"></a>クイックスタート: Go を使用して管理グループを作成する

管理グループは、複数のサブスクリプションのアクセス、ポリシー、コンプライアンスを管理するのに役立つコンテナーです。 これらのコンテナーを作成して、[Azure Policy](../policy/overview.md) と [Azure ロール ベースのアクセス制御](../../role-based-access-control/overview.md)で使用できる効果的で効率的な階層を構築します。 管理グループについて詳しくは、「[Azure 管理グループでリソースを整理する](overview.md)」をご覧ください。

ディレクトリに作成される最初の管理グループは、完了までに最大 15 分かかる場合があります。 Azure 内でディレクトリの管理グループ サービスを初めて設定する際に実行するプロセスがあります。 プロセスが完了すると、通知を受け取ります。 詳細については、「[管理グループの初期セットアップ](./overview.md#initial-setup-of-management-groups)」を参照してください。

## <a name="prerequisites"></a>前提条件

- Azure サブスクリプションをお持ちでない場合は、開始する前に[無料](https://azure.microsoft.com/free/)アカウントを作成してください。

- _clientId_ と _clientSecret_ を含む Azure サービス プリンシパル。 Azure Policy で使用するサービス プリンシパルがない場合、または新規作成したい場合は、[.NET 認証のための Azure 管理ライブラリ](/dotnet/azure/sdk/authentication#mgmt-auth)に関するページを参照してください。
  次の手順で実行するので、.NET Core パッケージをインストールする手順はスキップします。

- [階層の保護](./how-to/protect-resource-hierarchy.md#setting---require-authorization)が有効になっていない場合、テナント内のすべての Azure AD ユーザーは、そのユーザーに割り当てられた管理グループの書き込みアクセス許可なしで管理グループを作成できます。 この新しい管理グループは、ルート管理グループ ([既定の管理グループ](./how-to/protect-resource-hierarchy.md#setting---default-management-group)) の子になり、作成者には "所有者" ロールの割り当てが付与されます。 管理グループ サービスでは、この機能が許可されるため、ルート レベルでのロールの割り当ては必要ありません。 ルート管理グループは、作成されると、どのユーザーもアクセスできません。 管理グループの使用を開始する場合に Azure AD の全体管理者を見つけるという困難を回避するために、ルート レベルで最初の管理グループを作成できるようになっています。

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-management-group-package"></a>管理グループ パッケージを追加する

Go で管理グループを管理できるようにするには、パッケージを追加する必要があります。 このパッケージは、[Windows 10 の bash](/windows/wsl/install-win10) やローカルにインストールされている場合も含め、Go を使用できる場所ならどこでも動作します。

1. 最新の Go がインストールされていることを確認します (**1.15** 以降)。 まだインストールされていない場合は、[Golang.org](https://golang.org/dl/) からダウンロードします。

1. 最新の Azure CLI がインストールされていることを確認します (**2.5.1** 以降)。 まだインストールされていない場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

   > [!NOTE]
   > Azure CLI は、次の例の `auth.NewAuthorizerFromCLI()` メソッドを Go で使用できるようにするために必要です。 その他のオプションについては、「[Azure SDK for Go - その他の認証の詳細](https://github.com/Azure/azure-sdk-for-go#more-authentication-details)」を参照してください。

1. Azure CLI から認証します。

   ```azurecli
   az login
   ```

1. 選択した Go 環境で、管理グループに必要なパッケージをインストールします。

   ```bash
   # Add the management group package for Go
   go get -u github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups

   # Add the Azure auth package for Go
   go get -u github.com/Azure/go-autorest/autorest/azure/auth
   ```

## <a name="application-setup"></a>アプリケーションのセットアップ

選択した環境に Go パッケージを追加したら、管理グループを作成できるように Go アプリケーションをセットアップします。

1. Go アプリケーションを作成し、次のソースを `mgCreate.go` として保存します。

   ```Go
   package main
   
   import (
    "context"
    "fmt"
    "os"
   
    mg "github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups"
    "github.com/Azure/go-autorest/autorest/azure/auth"
   )
   
   func main() {
    // Get variables from command line arguments
    var mgName = os.Args[1]
   
    // Create and authorize a client
    mgClient := mg.NewClient()
    authorizer, err := auth.NewAuthorizerFromCLI()
    if err == nil {
        mgClient.Authorizer = authorizer
    } else {
        fmt.Printf(err.Error())
    }
   
    // Create the request
    Request := mg.CreateManagementGroupRequest{
        Name: &mgName,
    }
   
    // Run the query and get the results
    var results, queryErr = mgClient.CreateOrUpdate(context.Background(), mgName, Request, "no-cache")
    if queryErr == nil {
        fmt.Printf("Results: " + fmt.Sprint(results) + "\n")
    } else {
        fmt.Printf(queryErr.Error())
    }
   }
   ```

1. Go アプリケーションをビルドします。

   ```bash
   go build mgCreate.go
   ```

1. コンパイルされた Go アプリケーションを使用して管理グループを作成します。 `<Name>` を新しい管理グループの名前に置き換えます。

   ```bash
   mgCreate "<Name>"
   ```

その結果、ルート管理グループ内に新しい管理グループが作成されます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

インストールしたパッケージを、お使いの Go 環境から削除する場合は、次のコマンドを使用します。

```bash
# Remove the installed packages from the Go environment
go clean -i github.com/Azure/azure-sdk-for-go/services/preview/resources/mgmt/2018-03-01-preview/managementgroups
go clean -i github.com/Azure/go-autorest/autorest/azure/auth
```

## <a name="next-steps"></a>次のステップ

このクイックスタートでは、リソース階層を整理するための管理グループを作成しました。 管理グループには、サブスクリプションや他の管理グループを含めることができます。

管理グループについて、またリソース階層の管理方法について詳しくは、次の記事に進んでください。

> [!div class="nextstepaction"]
> [管理グループを使用してリソースを管理する](./manage.md)