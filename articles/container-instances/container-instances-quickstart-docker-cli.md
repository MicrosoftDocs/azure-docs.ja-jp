---
title: クイックスタート - コンテナー インスタンスに Docker コンテナーをデプロイする - Docker CLI
description: このクイック スタートでは、Docker CLI を使用して、分離された Azure コンテナー インスタンスで実行されているコンテナー化された Web アプリをすばやくデプロイします
ms.topic: quickstart
ms.date: 07/16/2020
ms.custom: devx-track-javascript
ms.openlocfilehash: 0e4569904ef6aee304518ce012889d10dc2ecbce
ms.sourcegitcommit: 42107c62f721da8550621a4651b3ef6c68704cd3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2020
ms.locfileid: "87408086"
---
# <a name="quickstart-deploy-a-container-instance-in-azure-using-the-docker-cli"></a>クイック スタート:Docker CLI を使用してコンテナー インスタンスを Azure にデプロイする

サーバーレスの Docker コンテナーを Azure 内で簡単にすばやく実行するには、Azure Container Instances を使用します。 クラウドネイティブ アプリを開発していて、ローカル開発環境からクラウド デプロイにシームレスに切り替えたいときに、オンデマンドでコンテナー インスタンスにデプロイします。

このクイックスタートでは、ネイティブ Docker CLI コマンドを使用して Docker コンテナーをデプロイし、そのアプリケーションを Azure Container Instances で使用できるようにします。 この機能は、[Docker と Azure 間の統合](https://docs.docker.com/engine/context/aci-integration/) (ベータ) によって実現されます。 `docker run` コマンドを実行して数秒後には、コンテナーで実行中のアプリケーションを参照できます。

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Azure Container Instances を使用してデプロイされたアプリのブラウザーでの表示":::

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント][azure-account] を作成してください。

このクイックスタートでは、[Windows](https://desktop.docker.com/win/edge/Docker%20Desktop%20Installer.exe) または [macOS](https://desktop.docker.com/mac/edge/Docker.dmg) で使用できる Docker Desktop Edge バージョン 2.3.2.0 以降をインストールする必要があります。 または、[Docker ACI Integration CLI for Linux](https://docs.docker.com/engine/context/aci-integration/#install-the-docker-aci-integration-cli-on-linux) (ベータ) をインストールします。 

> [!IMPORTANT]
> 現在この機能はプレビュー段階であり、Docker のベータ (プレビュー) 機能が必要です。 詳細については、[Docker Desktop の Stable バージョンと Edge バージョン](https://docs.docker.com/desktop/#stable-and-edge-versions)に関するページを参照してください。 Azure Container Instances の一部の機能はサポートされていません。 [aci-integration-beta](https://github.com/docker/aci-integration-beta) GitHub リポジトリでイシューを作成して、Docker と Azure の統合に関するフィードバックを提供してください。

## <a name="create-azure-context"></a>Azure コンテキストを作成する

Docker コマンドを使用して Azure Container Instances でコンテナーを実行するには、最初に Azure にログインします。

```bash
docker login azure
```

メッセージが表示されたら、Azure の資格情報を入力または選択します。


`docker context create aci` を実行して、ACI コンテキストを作成します。 このコンテキストにより、Docker と Azure サブスクリプションが関連付けられて、コンテナー インスタンスを作成できるようになります。 たとえば、*myacicontext* というコンテキストを作成するには、次のように入力します。

```
docker context create aci myacicontext
```

プロンプトが表示されたら、Azure サブスクリプション ID を選択してから、既存のリソース グループを選択するか、**新しいリソース グループを作成**します。 新しいリソース グループを選択すると、システムによって生成された名前で作成されます。 Azure のコンテナー インスタンスは、すべての Azure リソースと同様に、リソース グループにデプロイする必要があります。 リソース グループを使用すると、関連する Azure リソースを整理して管理できます。


`docker context ls` を実行して、Docker コンテキストに ACI コンテキストを追加したことを確認します。

```
docker context ls
```

## <a name="create-a-container"></a>コンテナーを作成する

Docker コンテキストを作成した後、Azure でコンテナーを作成できます。 このクイック スタートでは、パブリックの `mcr.microsoft.com/azuredocs/aci-helloworld` イメージを使用します。 このイメージには、静的な HTML ページを返す、Node.js で作成された小さな Web アプリがパッケージされています。

まず、ACI コンテキストに変更します。 以降のすべての Docker コマンドは、このコンテキストで実行されます。

```
docker context use myacicontext
```

次の `docker run` コマンドを実行し、インターネットに公開されているポート 80 を使用して Azure コンテナー インスタンスを作成します。

```
docker run -p 80:80 mcr.microsoft.com/azuredocs/aci-helloworld
```

正常に行われたデプロイのサンプル出力:

```
[+] Running 2/2
 ⠿ hungry-kirch            Created                                                                               5.1s
 ⠿ single--container--aci  Done                                                                                 11.3s
hungry-kirch
```

`docker ps` を実行して、パブリック IP アドレスなど、実行中のコンテナーについての詳細を取得します。

```
docker ps
```


サンプル出力では、パブリック IP アドレス (この例では *52.230.225.232*) が示されています。

```
CONTAINER ID        IMAGE                                        COMMAND             STATUS              PORTS
hungry-kirch        mcr.microsoft.com/azuredocs/aci-helloworld                       Running             52.230.225.232:80->80/tcp
```

 次に、ブラウザーでその IP アドレスにアクセスします。 次のような Web ページが表示されたら成功です。 Docker コンテナーで実行されているアプリケーションが Azure に正常にデプロイされました。

:::image type="content" source="media/container-instances-quickstart-docker-cli/view-application-running-in-an-azure-container-instance.png" alt-text="Azure Container Instances を使用してデプロイされたアプリのブラウザーでの表示":::

## <a name="pull-the-container-logs"></a>コンテナー ログを取り込む

コンテナーまたはコンテナーで実行されているアプリケーションのトラブルシューティングを行うことが必要になった場合 (またはその出力を確認するだけの場合) は、コンテナー インスタンスのログを表示することから始めます。

たとえば、`docker logs` コマンドを実行して、ACI コンテキストの *kirch* コンテナーのログを表示します。

```azurecli-interactive
docker logs hungry-kirch
```

出力はコンテナーのログを表示し、ブラウザーでアプリケーションを表示したときに生成された HTTP GET 要求が表示されます。

```output
listening on port 80
::ffff:10.240.255.55 - - [07/Jul/2020:17:43:53 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
::ffff:10.240.255.55 - - [07/Jul/2020:17:44:36 +0000] "GET / HTTP/1.1" 304 - "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/72.0.3626.121 Safari/537.36"
```


## <a name="clean-up-resources"></a>リソースをクリーンアップする

コンテナーを使い終えたら、`docker rm` を実行して削除します。 このコマンドを実行すると、Azure コンテナー インスタンスが停止されて、削除されます。

```
docker rm hungry-kirch
```


## <a name="next-steps"></a>次のステップ

このクイックスタートでは、Docker と Azure の統合を使用して、パブリック イメージから Azure コンテナー インスタンスを作成しました。 統合シナリオの詳細については、[Docker のドキュメント](https://docs.docker.com/engine/context/aci-integration/)を参照してください。 

また、コンテナー、イメージ、コンテキストを開発、実行、管理するための統合エクスペリエンスとして、Visual Studio Code 用の [Docker 拡張機能](https://aka.ms/VSCodeDocker)を使用することもできます。

Azure ツールを使用してコンテナー インスタンスを作成および管理する方法については、[Azure CLI](container-instances-quickstart.md)、[Azure PowerShell](container-instances-quickstart-powershell.md)、[Azure portal](container-instances-quickstart-portal.md)、[Azure Resource Manager テンプレート](container-instances-quickstart-template.md)を使用する他のクイックスタートを参照してください。

コンテナー イメージをビルドし、プライベート Azure コンテナー レジストリからデプロイする場合は、Azure Container Instances のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル](./container-instances-tutorial-prepare-app.md)

<!-- LINKS - External -->

[azure-account]: https://azure.microsoft.com/free/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

