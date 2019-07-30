---
title: クイック スタート - Azure Container Instances への Docker コンテナーのデプロイ - CLI
description: このクイック スタートでは、Azure CLI を使用して、分離された Azure コンテナー インスタンスで実行されているコンテナー化された Web アプリをすばやくデプロイします
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: quickstart
ms.date: 03/21/2019
ms.author: danlep
ms.custom: seodec18, mvc
ms.openlocfilehash: 7a4a1c24211e644a796b4e60537978c327501383
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68325782"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-azure-cli"></a>クイック スタート:Azure CLI を使用してコンテナー インスタンスを Azure にデプロイする

サーバーレスの Docker コンテナーを Azure で簡単にすばやく実行するには、Azure Container Instances を使用します。 Azure Kubernetes Service のように完全なコンテナー オーケストレーション プラットフォームが不要な場合は、コンテナー インスタンス オンデマンドにアプリケーションをデプロイします。

このクイック スタートでは、Azure CLI を使用して、分離された Docker コンテナーをデプロイし、そのアプリケーションを完全修飾ドメイン名 (FQDN) を介して使用できるようにします。 1 つのデプロイ コマンドを実行して数秒後には、コンテナーで実行中のアプリケーションを参照できます。

![Azure Container Instances にデプロイされたアプリのブラウザーでの表示][aci-app-browser]

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント][azure-account] を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Azure Cloud Shell または Azure CLI のローカル インストールを使用して、このクイック スタートを完了できます。 Azure CLI をローカルで使用する場合は、バージョン 2.0.55 以降をお勧めします。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール][azure-cli-install]に関するページを参照してください。

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure のコンテナー インスタンスは、すべての Azure リソースと同様に、リソース グループにデプロイする必要があります。 リソース グループを使用すると、関連する Azure リソースを整理して管理できます。

まず、次の [az group create][az-group-create] コマンドを使用して、*myResourceGroup* という名前のリソース グループを *eastus* の場所に作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>コンテナーを作成する

リソース グループを作成すると、Azure でコンテナーを実行できます。 Azure CLI を使用してコンテナー インスタンスを作成するには、リソース グループ名、コンテナー インスタンス名、Docker コンテナー イメージを [az container create][az-container-create] コマンドに指定します。 このクイック スタートでは、パブリックの `mcr.microsoft.com/azuredocs/aci-helloworld` イメージを使用します。 このイメージには、静的な HTML ページを返す、Node.js で作成された小さな Web アプリがパッケージされています。

1 つまたは複数の開くポート、DNS 名ラベル、またはその両方を指定することで、コンテナーをインターネットに公開することができます。 このクイック スタートでは、Web アプリにパブリックに到達できるよう、DNS 名ラベルを指定してコンテナーをデプロイします。

次のようなコマンドを実行して、コンテナー インスタンスを開始します。 `--dns-name-label` の値は、インスタンスを作成する Azure リージョン内で一意の値を設定してください。 エラー メッセージ "DNS 名ラベルは利用できません" が表示された場合は、別の DNS 名ラベルを試してください。

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld --dns-name-label aci-demo --ports 80
```

数秒以内に、Azure CLI からデプロイが完了したことを示す応答を受信します。 [az container show][az-container-show] コマンドを使用して、その状態を確認します。

```azurecli-interactive
az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
```

コマンドを実行すると、コンテナーの完全修飾ドメイン名 (FQDN) とそのプロビジョニング状態が表示されます。

```console
$ az container show --resource-group myResourceGroup --name mycontainer --query "{FQDN:ipAddress.fqdn,ProvisioningState:provisioningState}" --out table
FQDN                               ProvisioningState
---------------------------------  -------------------
aci-demo.eastus.azurecontainer.io  Succeeded
```

コンテナーの `ProvisioningState` が **[Succeeded]\(成功\)** と表示されている場合は、ブラウザーでその FQDN に移動します。 次のような Web ページが表示されたら成功です。 Docker コンテナーで実行されているアプリケーションが Azure に正常にデプロイされました。

![Azure コンテナー インスタンスで実行されているアプリケーションを示すブラウザー スクリーンショット][aci-app-browser]

最初にアプリケーションが表示されない場合は、DNS が反映されるまで数秒待った後、ブラウザーを更新してみてください。

## <a name="pull-the-container-logs"></a>コンテナー ログを取り込む

コンテナーまたはコンテナーで実行されているアプリケーションのトラブルシューティングを行うことが必要になった場合 (またはその出力を確認するだけの場合) は、コンテナー インスタンスのログを表示することから始めます。

[az container logs][az-container-logs] コマンドを使用して、コンテナー インスタンスのログをプルします。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer
```

出力はコンテナーのログを表示し、ブラウザーでアプリケーションを表示したときに生成された HTTP GET 要求が表示されます。

```console
$ az container logs --resource-group myResourceGroup --name mycontainer
listening on port 80
::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="attach-output-streams"></a>出力ストリームのアタッチ

ログを表示するだけでなく、ローカルの標準出力ストリームと標準エラー ストリームをコンテナーのストリームにアタッチすることができます。

最初に [az container attach][az-container-attach] コマンドを実行して、ローカルのコンソールをコンテナーの出力ストリームにアタッチします。

```azurecli-interactive
az container attach --resource-group myResourceGroup --name mycontainer
```

アタッチしたら、ブラウザーを数回更新して、追加の出力を生成します。 完了したら、`Control+C` キーを押してコンソールをデタッチします。 次のような出力が表示されます。

```console
$ az container attach --resource-group myResourceGroup --name mycontainer
Container 'mycontainer' is in state 'Running'...
(count: 1) (last timestamp: 2019-03-21 17:27:20+00:00) pulling image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:24+00:00) Successfully pulled image "mcr.microsoft.com/azuredocs/aci-helloworld"
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Created container
(count: 1) (last timestamp: 2019-03-21 17:27:27+00:00) Started container

Start streaming logs:
listening on port 80

::ffff:10.240.255.55 - - [21/Mar/2019:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [21/Mar/2019:17:47:01 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.56 - - [21/Mar/2019:17:47:12 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

コンテナーでの処理が完了したら、[az container delete][az-container-delete] コマンドを使用して、そのコンテナーを削除します。

```azurecli-interactive
az container delete --resource-group myResourceGroup --name mycontainer
```

コンテナーが削除されたことを確認するには、[az container list](/cli/azure/container#az-container-list) コマンドを実行します。

```azurecli-interactive
az container list --resource-group myResourceGroup --output table
```

**mycontainer** コンテナーは、コマンドの出力に表示されません。 リソース グループに別のコンテナーがない場合、出力は表示されません。

*myResourceGroup* リソース グループとそこに含まれるすべてのリソースの処理が完了したら、[az group delete][az-group-delete] コマンドを使用してそれを削除します。

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>次の手順

このクイック スタートでは、パブリック Microsoft イメージを使用して Azure コンテナー インスタンスを作成しました。 コンテナー イメージをビルドし、プライベート Azure コンテナー レジストリからデプロイする場合は、Azure Container Instances のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル](./container-instances-tutorial-prepare-app.md)

Azure のオーケストレーション システムでコンテナーを実行するためのオプションを試すには、[Azure Kubernetes Service (AKS)][container-service] のクイック スタートを参照してください。

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - External -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git
[azure-account]: https://azure.microsoft.com/free/
[node-js]: https://nodejs.org

<!-- LINKS - Internal -->
[az-container-attach]: /cli/azure/container#az-container-attach
[az-container-create]: /cli/azure/container#az-container-create
[az-container-delete]: /cli/azure/container#az-container-delete
[az-container-list]: /cli/azure/container#az-container-list
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[container-service]: ../aks/kubernetes-walkthrough.md
