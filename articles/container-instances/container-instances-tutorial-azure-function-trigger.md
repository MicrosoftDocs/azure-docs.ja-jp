---
title: チュートリアル - Azure 関数で Azure Container Instances をトリガーする
description: HTTP によってトリガーされるサーバーレス PowerShell 関数を作成して Azure コンテナー インスタンスの作成を自動化します。
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: tutorial
ms.date: 09/20/2019
ms.author: danlep
ms.custom: ''
ms.openlocfilehash: 00bd017b0bcff6386e678802c301087819792744
ms.sourcegitcommit: 83df2aed7cafb493b36d93b1699d24f36c1daa45
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/22/2019
ms.locfileid: "71179950"
---
# <a name="tutorial-use-an-http-triggered-azure-function-to-create-a-container-group"></a>チュートリアル:HTTP によってトリガーされる Azure 関数を使用してコンテナー グループを作成する

[Azure Functions](../azure-functions/functions-overview.md) は、さまざまなイベント (HTTP 要求、タイマー、Azure Storage キューのメッセージなど) に応じてスクリプトまたはコードを実行できるサーバーレスのコンピューティング サービスです。

このチュートリアルでは、HTTP 要求を受け取って[コンテナー グループ](container-instances-container-groups.md)のデプロイをトリガーする Azure 関数を作成します。 この例では、Azure Functions を使用して Azure Container Instances のリソースを自動的に作成する際の基本を紹介しています。 さらに複雑なシナリオや他のイベント トリガーについては、この例に変更を加えるか拡張してください。 

学習内容は次のとおりです。

> [!div class="checklist"]
> * Visual Studio Code と [Azure Functions 拡張機能](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions)を使用して、HTTP によってトリガーされる基本的な PowerShell 関数を作成する。
> * 関数アプリの ID を有効にして、Azure リソースを作成するためのアクセス許可を与える。
> * コンテナーを 1 つだけ含んだコンテナー グループのデプロイを自動化するように PowerShell 関数に変更を加えて再発行する。
> * HTTP によってトリガーされたコンテナーのデプロイを確認する。

> [!IMPORTANT]
> Azure Functions 用の PowerShell は現在プレビュー段階です。 プレビュー版は、[追加使用条件][terms-of-use]に同意することを条件に使用できます。 この機能の一部の側面は、一般公開 (GA) 前に変更される可能性があります。

## <a name="prerequisites"></a>前提条件

ご利用の OS に Visual Studio Code と Azure Functions をインストールして使用するための前提条件については、[Azure で初めての PowerShell 関数を作成する方法](../azure-functions/functions-create-first-function-powershell.md#prerequisites)に関するページを参照してください。

この記事の一部の手順では、Azure CLI を使用します。 Azure Cloud Shell または Azure CLI のローカル インストールを使用してそれらの手順を実行できます。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="create-a-basic-powershell-function"></a>基本的な PowerShell 関数を作成する

[Azure で初めての PowerShell 関数を作成する方法](../azure-functions/functions-create-first-function-powershell.md)の手順に従い、HTTP トリガー テンプレートを使用して PowerShell 関数を作成します。 Azure 関数の既定の名前 **HttpTrigger** を使用します。 このクイックスタートに示すように、関数をローカルでテストし、Azure の関数アプリにプロジェクトを発行します。 この例は、テキスト文字列を返す、HTTP によってトリガーされた関数です。 この記事の後続の手順では、コンテナー グループを作成するよう関数を変更します。

この記事では、*myfunctionapp* という名前を使用して Azure リソース グループにプロジェクトを発行することを想定しています。Azure リソース グループの名前は、関数アプリの名前に従って自動的に付けられています (こちらも *myfunctionapp*)。 その後の手順で、一意の関数アプリ名とリソース グループ名に置き換えます。

## <a name="enable-an-azure-managed-identity-in-the-function-app"></a>関数アプリで Azure のマネージド ID を有効にする

ここで、お使いの関数アプリで、システムによって割り当てられた[マネージド ID](../app-service/overview-managed-identity.md?toc=/azure/azure-functions/toc.json#adding-a-system-assigned-identity) を有効にします。 このアプリを実行している PowerShell ホストは、この ID を使用して自動的に認証できるため、関数は、その ID にアクセス権が付与されている Azure サービスに対してアクションを実行できます。 このチュートリアルでは、関数アプリのリソース グループにリソースを作成するためのアクセス許可をマネージド ID に付与します。 

まず、[az group show][az-group-show] コマンドを使用して、関数アプリのリソース グループの ID を取得して環境変数に格納します。 この例では、Bash シェルでコマンドを実行することを想定しています。

```azurecli
rgID=$(az group show --name myfunctionapp --query id --output tsv)
```

[az functionapp identity app assign][az-functionapp-identity-app-assign] を実行して、関数アプリにローカル ID を割り当てたうえで、リソース グループに共同作成者ロールを割り当てます。 このロールにより、この ID では、リソース グループに新たなリソース (コンテナー グループなど) を作成できます。

```azurecli
az functionapp identity assign \
  --name myfunctionapp \
  --resource-group myfunctionapp \
  --role contributor --scope $rgID
```

## <a name="modify-httptrigger-function"></a>HttpTrigger 関数を変更する

コンテナー グループを作成するように **HttpTrigger** 関数の PowerShell コードを変更します。 関数の `run.ps1` ファイルで、次のコード ブロックを探します。 このコードは、関数の URL のクエリ文字列として名前が渡された場合に、その値を表示するものです。

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    $body = "Hello $name"
}
[...]
```

このコードを次の例のブロックに置き換えます。 ここで、クエリ文字列に name 値が渡された場合、[New-AzContainerGroup][new-azcontainergroup] コマンドレットを使用したコンテナー グループの名前付けと作成に使用されます。 リソース グループの名前 *myfunctionapp* は、実際の関数アプリのリソース グループの名前に置き換えてください。

```powershell
[...]
if ($name) {
    $status = [HttpStatusCode]::OK
    New-AzContainerGroup -ResourceGroupName myfunctionapp -Name $name `
        -Image alpine -OsType Linux `
        -Command "echo 'Hello from an Azure container instance triggered by an Azure function'" `
        -RestartPolicy Never
    $body = "Started container group $name"
}
[...]
```

この例では、`alpine` イメージを実行する 1 つのコンテナー インスタンスから成るコンテナー グループを作成します。 このコンテナーは、`echo` コマンドを 1 回だけ実行して終了します。 実際の例では、バッチ ジョブを実行するための 1 つ以上のコンテナー グループの作成をトリガーする場合があります。
 
## <a name="test-function-app-locally"></a>関数アプリをローカルでテストする

関数アプリ プロジェクトを Azure に再発行する前に、関数がローカルで正常に動作することを確認します。 [PowerShell のクイックスタート](../azure-functions/functions-create-first-function-powershell.md)で示したように、PowerShell スクリプトにローカル ブレークポイントを挿入し、その上に `Wait-Debugger` 呼び出しを挿入します。 デバッグのガイダンスについては、「[PowerShell Azure Functions をローカル環境でデバッグする](../azure-functions/functions-debug-powershell-local.md)」を参照してください。


## <a name="republish-azure-function-app"></a>Azure 関数アプリを再発行する

関数がローカル コンピューター上で正常に動作することを確認したら、Azure 上の既存の関数アプリにそのプロジェクトを再発行します。

> [!NOTE]
> 関数を Azure に公開する前に、`Wait-Debugger` への呼び出しを必ず削除してください。

1. Visual Studio Code で、コマンド パレットを開きます。 `Azure Functions: Deploy to function app...` を検索して選択します。
1. 現在の作業フォルダーを選択して zip 圧縮し、デプロイします。
1. サブスクリプションを選択し、既存の関数アプリの名前 (*myfunctionapp*) を選択します。 以前のデプロイを上書きしてもよいことを確認します。

関数アプリが作成され、展開パッケージが適用されると、通知が表示されます。 この通知の **[View Output]\(出力の表示\)** を選択すると、更新済みの Azure リソースなど、作成とデプロイの結果が表示されます。

## <a name="run-the-function-in-azure"></a>Azure で関数を実行する

デプロイが正常に完了したら、関数の URL を取得します。 たとえば、Visual Studio Code の **[Azure:Functions]** 領域を使用して **HttpTrigger** 関数の URL をコピーするか、または [Azure portal](../azure-functions/functions-create-first-azure-function.md#test-the-function) で関数の URL を取得します。

この関数の URL には一意のコードが含まれており、次の形式になっています。

```
https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==
```

### <a name="run-function-without-passing-a-name"></a>名前を渡さずに関数を実行する

1 回目のテストとして、`name` クエリ文字列は追加せずに、`curl` コマンドを実行して関数の URL を渡します。 必ず関数の一意のコードを含めてください。

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M=="
```

この関数から状態コード 400 と `Please pass a name on the query string or in the request body` というテキストが返されます。

```
[...]
> GET /api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M== HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 400 
< content-length: 62
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:08:15 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Please pass a name on the query string or in the request body.
```

### <a name="run-function-and-pass-the-name-of-a-container-group"></a>関数を実行してコンテナー グループの名前を渡す

次に、クエリ文字列 `&name=mycontainergroup` としてコンテナー グループの名前 (*mycontainergroup*) を追加して `curl` コマンドを実行します。

```bash
curl --verbose "https://myfunctionapp.azurewebsites.net/api/HttpTrigger?code=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup"
```

この関数から状態コード 200 が返され、コンテナー グループの作成がトリガーされます。

```
[...]
> GET /api/HttpTrigger?ode=bmF/GljyfFWISqO0GngDPCtCQF4meRcBiHEoaQGeRv/Srx6dRcrk2M==&name=mycontainergroup HTTP/2
> Host: myfunctionapp.azurewebsites.net
> User-Agent: curl/7.54.0
> Accept: */*
> 
* Connection state changed (MAX_CONCURRENT_STREAMS updated)!
< HTTP/2 200 
< content-length: 28
< content-type: text/plain; charset=utf-8
< date: Mon, 05 Aug 2019 22:15:30 GMT
< 
* Connection #0 to host myfunctionapp.azurewebsites.net left intact
Started container group mycontainergroup
```

[az container logs][az-container-logs] コマンドを使用して、コンテナーが実行されたことを確認します。

```azurecli
az container logs --resource-group myfunctionapp --name mycontainergroup
```

サンプル出力:

```console
Hello from an Azure container instance triggered by an Azure function
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

このチュートリアルで作成したリソースのいずれかが不要になった場合は、[az group delete][az-group-delete] コマンドを実行して、リソース グループとそれに含まれているすべてのリソースを削除できます。 このコマンドは、作成したコンテナー レジストリだけでなく、実行中のコンテナーと関連するすべてのリソースを削除します。

```azurecli-interactive
az group delete --name myfunctionapp
```

## <a name="next-steps"></a>次の手順

このチュートリアルでは、HTTP 要求を受け取ってコンテナー グループのデプロイをトリガーする Azure 関数を作成しました。 以下の方法について学習しました。

> [!div class="checklist"]
> * Visual Studio Code と Azure Functions 拡張機能を使用して、HTTP によってトリガーされる基本的な PowerShell 関数を作成する。
> * 関数アプリの ID を有効にして、Azure リソースを作成するためのアクセス許可を与える。
> * コンテナーを 1 つだけ含んだコンテナー グループのデプロイを自動化するように PowerShell 関数のコードに変更を加える。
> * HTTP によってトリガーされたコンテナーのデプロイを確認する。

コンテナー化されたジョブを起動して監視する詳細な例については、ブログ記事「[PowerShell Azure Functions と Azure Container Instances を使用したイベント駆動型のサーバーレス コンテナー](https://dev.to/azure/event-driven-serverless-containers-with-powershell-azure-functions-and-azure-container-instances-e9b)」と付随する[コード サンプル](https://github.com/anthonychu/functions-powershell-run-aci)を参照してください。

Azure 関数の作成と関数プロジェクトの発行に関する詳しいガイダンスについては、[Azure Functions のドキュメント](/azure/azure-functions/)を参照してください。 

<!-- IMAGES -->


<!-- LINKS - external -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->

[azure-cli-install]: /cli/azure/install-azure-cli
[az-group-show]: /cli/azure/group#az-group-show
[az-group-delete]: /cli/azure/group#az-group-delete
[az-functionapp-identity-app-assign]: /cli/azure/functionapp/identity#az-functionapp-identity-assign
[new-azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[az-container-logs]: /cli/azure/container#az-container-logs
