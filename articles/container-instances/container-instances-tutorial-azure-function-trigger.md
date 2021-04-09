---
title: チュートリアル - Azure Functions でコンテナー グループをトリガーする
description: HTTP によってトリガーされるサーバーレス PowerShell 関数を作成して Azure コンテナー インスタンスの作成を自動化します。
ms.topic: tutorial
ms.date: 06/10/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: e7315796f2f7f89800b58f5fa607e69cd7ae3447
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98935406"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>チュートリアル:HTTP によってトリガーされる Azure Functions を使用してコンテナー グループを作成する

[Azure Functions](../azure-functions/functions-overview.md) は、さまざまなイベント (HTTP 要求、タイマー、Azure Storage キューのメッセージなど) に応じてスクリプトまたはコードを実行できるサーバーレスのコンピューティング サービスです。

このチュートリアルでは、HTTP 要求を受け取って[コンテナー グループ](container-instances-container-groups.md)のデプロイをトリガーする Azure Functions を作成します。 この例では、Azure Functions を使用して Azure Container Instances のリソースを自動的に作成する際の基本を紹介しています。 さらに複雑なシナリオや他のイベント トリガーについては、この例に変更を加えるか拡張してください。 

学習内容は次のとおりです。

> [!div class="checklist"]
> * Visual Studio Code と [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)を使用して、HTTP によってトリガーされる基本的な PowerShell 関数を作成する。
> * 関数アプリの ID を有効にして、Azure リソースを作成するためのアクセス許可を与える。
> * コンテナーを 1 つだけ含んだコンテナー グループのデプロイを自動化するように PowerShell 関数に変更を加えて再発行する。
> * HTTP によってトリガーされたコンテナーのデプロイを確認する。

## <a name="prerequisites"></a>前提条件

ご利用の OS に Visual Studio Code と Azure Functions 拡張機能をインストールして使用するための前提条件については、[Visual Studio Code を使用して Azure で初めての関数を作成する方法](../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-powershell#configure-your-environment)に関するページを参照してください。

この記事の追加の手順では、Azure PowerShell を使用します。 インストールまたはアップグレードする必要がある場合は、[Azure PowerShell のインストール][azure-powershell-install]および [Azure へのサインイン](/powershell/azure/get-started-azureps#sign-in-to-azure)に関する記事を参照してください。

## <a name="create-a-basic-powershell-function"></a>基本的な PowerShell 関数を作成する

[Azure で初めての PowerShell 関数を作成する方法](../azure-functions/create-first-function-vs-code-csharp.md?pivots=programming-language-powershell)の手順に従い、HTTP トリガー テンプレートを使用して PowerShell 関数を作成します。 Azure Functions の既定の名前 **HttpTrigger** を使用します。 このクイックスタートに示すように、関数をローカルでテストし、Azure の関数アプリにプロジェクトを発行します。 この例は、テキスト文字列を返す、HTTP によってトリガーされた関数です。 この記事の後続の手順では、コンテナー グループを作成するよう関数を変更します。

この記事では、*myfunctionapp* という名前を使用して Azure リソース グループにプロジェクトを発行することを想定しています。Azure リソース グループの名前は、関数アプリの名前に従って自動的に付けられています (こちらも *myfunctionapp*)。 その後の手順で、一意の関数アプリ名とリソース グループ名に置き換えます。

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>関数アプリで Azure のマネージド ID を有効にする

次のコマンドを使用すると、お使いの関数アプリで、システムによって割り当てられた[マネージド ID](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#add-a-system-assigned-identity) を有効にできます。 このアプリを実行している PowerShell ホストは、この ID を使用して Azure に対して自動的に認証できるため、関数は、その ID にアクセス権が付与されている Azure サービスに対してアクションを実行できます。 このチュートリアルでは、関数アプリのリソース グループにリソースを作成するためのアクセス許可をマネージド ID に付与します。 

関数アプリに [ID を追加する](../app-service/overview-managed-identity.md?tabs=dotnet#using-azure-powershell-1)には、次のようにします。

```powershell
Update-AzFunctionApp -Name myfunctionapp `
    -ResourceGroupName myfunctionapp `
    -IdentityType SystemAssigned
```

リソース グループのスコープに共同作成者ロールが指定されている ID を割り当てます。

```powershell
$SP=(Get-AzADServicePrincipal -DisplayName myfunctionapp).Id
$RG=(Get-AzResourceGroup -Name myfunctionapp).ResourceId
New-AzRoleAssignment -ObjectId $SP -RoleDefinitionName "Contributor" -Scope $RG
```

## <a name="modify-httptrigger-function"></a>HttpTrigger 関数を変更する

コンテナー グループを作成するように **HttpTrigger** 関数の PowerShell コードを変更します。 関数の `run.ps1` ファイルで、次のコード ブロックを探します。 このコードは、関数の URL のクエリ文字列として名前が渡された場合に、その値を表示するものです。

```powershell
[...]
if ($name) {
    $body = "Hello, $name. This HTTP triggered function executed successfully."
}
[...]
```

このコードを次の例のブロックに置き換えます。 ここで、クエリ文字列で name 値が渡された場合、[New-AzContainerGroup][new-azcontainergroup] コマンドレットを使用したコンテナー グループの名前付けと作成に使用されます。 リソース グループの名前 *myfunctionapp* は、実際の関数アプリのリソース グループの名前に置き換えてください。

```powershell
[...]
if ($name) {
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    if ($?) {
        $body = "This HTTP triggered function executed successfully. Started container group $name"
    }
    else  {
        $body = "There was a problem starting the container group."
    }
[...]
```

この例では、`alpine` イメージを実行する 1 つのコンテナー インスタンスから成るコンテナー グループを作成します。 このコンテナーは、`echo` コマンドを 1 回だけ実行して終了します。 実際の例では、バッチ ジョブを実行するための 1 つ以上のコンテナー グループの作成をトリガーする場合があります。
 
## <a name="test-function-app-locally"></a>関数アプリをローカルでテストする

関数アプリ プロジェクトを Azure に再発行する前に、関数がローカルで動作することを確認します。 関数をローカルで実行しても、Azure リソースは作成されません。 ただし、クエリ文字列で name 値を渡したり渡さなかったりすることで、関数のフローをテストすることができます。 関数をデバッグするには、「[PowerShell Azure Functions をローカル環境でデバッグする](../azure-functions/functions-debug-powershell-local.md)」を参照してください。

## <a name="republish-azure-function-app"></a>Azure Functions アプリを再発行する

関数がローカルで動作することを確認したら、Azure 上の既存の関数アプリにそのプロジェクトを再発行します。

1. Visual Studio Code で、コマンド パレットを開きます。 `Azure Functions: Deploy to Function App...` を検索して選択します。
1. 現在の作業フォルダーを選択して zip 圧縮し、デプロイします。
1. サブスクリプションを選択し、既存の関数アプリの名前 (*myfunctionapp*) を選択します。 以前のデプロイを上書きしてもよいことを確認します。

関数アプリが作成され、展開パッケージが適用されると、通知が表示されます。 この通知の **[View Output]\(出力の表示\)** を選択すると、更新済みの Azure リソースなど、作成とデプロイの結果が表示されます。

## <a name="run-the-function-in-azure"></a>Azure で関数を実行する

デプロイが正常に完了したら、関数の URL を取得します。 たとえば、Visual Studio Code の **[Azure: Functions]** 領域を使用して **HttpTrigger** 関数の URL をコピーするか、[Azure portal](../azure-functions/functions-get-started.md) で関数の URL を取得します。

関数の URL の形式は次のとおりです。

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger
```

### <a name="run-function-without-passing-a-name"></a>名前を渡さずに関数を実行する

1 回目のテストとして、`name` クエリ文字列は追加せずに、`curl` コマンドを実行して関数の URL を渡します。 

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger"
```

この関数から状態コード 200 と `This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response` というテキストが返されます。

```
[...]
> GET /api/HttpTrigger? HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/1.1 200 OK
< Content-Length: 135
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:50:27 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response.* Closing connection 0
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>関数を実行してコンテナー グループの名前を渡す

次に、コンテナー グループの名前 (*mycontainergroup*) をクエリ文字列 `?name=mycontainergroup` として追加して、`curl` コマンドを実行します。

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?name=mycontainergroup"
```

この関数から状態コード 200 が返され、コンテナー グループの作成がトリガーされます。

```
[...]
> GET /api/HttpTrigger1?name=mycontainergroup HTTP/1.1
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.64.1
> Accept: */*
> 
< HTTP/1.1 200 OK
< Content-Length: 92
< Content-Type: text/plain; charset=utf-8
< Request-Context: appId=cid-v1:d0bd0123-f713-4579-8990-bb368a229c38
< Date: Wed, 10 Jun 2020 17:54:31 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
This HTTP triggered function executed successfully. Started container group mycontainergroup* Closing connection 0
```

[Get-AzContainerInstanceLog][get-azcontainerinstancelog] コマンドを使用して、コンテナーが実行されたことを確認します。

```azurecli
Get-AzContainerInstanceLog -ResourceGroupName myfunctionapp `
  -ContainerGroupName mycontainergroup 
```

サンプル出力:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成したリソースがどれも不要になった場合は、[az group delete][az-group-delete] コマンドを実行して、リソース グループとそこに含まれているすべてのリソースを削除できます。 このコマンドによって、作成した関数アプリだけでなく、実行中のコンテナーおよびすべての関連リソースが削除されます。

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、HTTP 要求を受け取ってコンテナー グループのデプロイをトリガーする Azure Functions を作成しました。 以下の方法を学習しました。

> [!div class="checklist"]
> * Visual Studio Code と Azure Functions 拡張機能を使用して、HTTP によってトリガーされる基本的な PowerShell 関数を作成する。
> * 関数アプリの ID を有効にして、Azure リソースを作成するためのアクセス許可を与える。
> * コンテナーを 1 つだけ含んだコンテナー グループのデプロイを自動化するように PowerShell 関数のコードに変更を加える。
> * HTTP によってトリガーされたコンテナーのデプロイを確認する。

コンテナー化されたジョブを起動して監視する詳細な例については、ブログ記事「[PowerShell Azure Functions と Azure Container Instances を使用したイベント駆動型のサーバーレス コンテナー](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b)」と付随する[コード サンプル](https://github.com/anthonychu/functions-powershell-run-aci)を参照してください。

Azure Functions の作成と関数プロジェクトの発行に関する詳しいガイダンスについては、[Azure Functions のドキュメント](../azure-functions/index.yml)を参照してください。 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-powershell-install]: /powershell/azure/install-az-ps
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[get-azcontainerinstancelog]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
