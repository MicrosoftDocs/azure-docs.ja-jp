---
title: チュートリアル - 複数コンテナー グループをデプロイする - YAML
description: このチュートリアルでは、複数のコンテナーを含むコンテナー グループを YAML ファイルと Azure CLI を使用して Azure Container Instances にデプロイする方法を説明します。
ms.topic: article
ms.date: 07/01/2020
ms.openlocfilehash: 6f9dda7735587dfee1dde86c85375efcf057daa7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97605163"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-yaml-file"></a>チュートリアル:YAML ファイルを使用して複数コンテナー グループをデプロイする

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)
>

Azure Container Instances では、[コンテナー グループ](container-instances-container-groups.md)を使用して、複数のコンテナーを 1 つのホストにデプロイできます。 コンテナー グループは、サービスが 2 つ目のアタッチされたプロセスを必要とする場合に、ログ記録、監視などの構成用にアプリケーション サイドカーを作成するときに便利です。

このチュートリアルでは、Azure CLI を使用して [YAML ファイル](container-instances-reference-yaml.md)をデプロイすることで、単純な 2 コンテナー サイドカー構成を実行する手順に従います。 YAML ファイルは、インスタンスの設定を指定するための簡潔な形式を提供します。 学習内容は次のとおりです。

> [!div class="checklist"]
> * YAML ファイルを構成する
> * コンテナー グループをデプロイする
> * コンテナーのログを表示する

> [!NOTE]
> 複数コンテナー グループは、現在、Linux コンテナーに限定されています。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-yaml-file"></a>YAML ファイルを構成する

Azure CLI の [az container create][az-container-create] コマンドを使用して複数コンテナー グループをデプロイするには、YAML ファイル内にコンテナー グループ構成を指定する必要があります。 その後、YAML ファイルをパラメーターとしてコマンドに渡します。

まず、次の YAML を **deploy-aci.yaml** という名前の新しいファイルにコピーします。 Azure Cloud Shell では、Visual Studio Code を使用して作業ディレクトリにファイルを作成できます。

```
code deploy-aci.yaml
```

この YAML ファイルでは、2 つのコンテナー、パブリック IP アドレス、および公開された 2 つのポートを備える "myContainerGroup" というコンテナー グループが定義されます。 コンテナーは、パブリック Microsoft イメージからデプロイされます。 グループの最初のコンテナーでは、インターネットに接続する Web アプリケーションが実行されます。 2 番目のコンテナーであるサイドカーは、コンテナー グループのローカル ネットワークを介して最初のコンテナーで実行されている Web アプリケーションに定期的に HTTP 要求を行います。

```YAML
apiVersion: 2019-12-01
location: eastus
name: myContainerGroup
properties:
  containers:
  - name: aci-tutorial-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld:latest
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
      ports:
      - port: 80
      - port: 8080
  - name: aci-tutorial-sidecar
    properties:
      image: mcr.microsoft.com/azuredocs/aci-tutorial-sidecar
      resources:
        requests:
          cpu: 1
          memoryInGb: 1.5
  osType: Linux
  ipAddress:
    type: Public
    ports:
    - protocol: tcp
      port: 80
    - protocol: tcp
      port: 8080
tags: {exampleTag: tutorial}
type: Microsoft.ContainerInstance/containerGroups
```

プライベート コンテナー イメージ レジストリを使用するには、お使いの環境に合わせて変更された値を含む `imageRegistryCredentials` プロパティをコンテナー グループに追加します。

```YAML
  imageRegistryCredentials:
  - server: imageRegistryLoginServer
    username: imageRegistryUsername
    password: imageRegistryPassword
```

## <a name="deploy-the-container-group"></a>コンテナー グループをデプロイする

[az group create][az-group-create] コマンドでリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az container create][az-container-create] コマンドでコンテナー グループをデプロイし、YAML ファイルを引数として渡します。

```azurecli-interactive
az container create --resource-group myResourceGroup --file deploy-aci.yaml
```

数秒以内に、Azure から最初の応答を受信します。

## <a name="view-deployment-state"></a>デプロイ状態の表示

デプロイの状態を表示するには、次の [az container show][az-container-show] コマンドを使用します。

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

実行中のアプリケーションを表示するには、ご利用のブラウザーでその IP アドレスにアクセスします。 たとえば、次の出力例では IP は `52.168.26.124` です。

```console
Name              ResourceGroup    Status    Image                                                                                               IP:ports              Network    CPU/Memory       OsType    Location
----------------  ---------------  --------  --------------------------------------------------------------------------------------------------  --------------------  ---------  ---------------  --------  ----------
myContainerGroup  danlep0318r      Running   mcr.microsoft.com/azuredocs/aci-tutorial-sidecar,mcr.microsoft.com/azuredocs/aci-helloworld:latest  20.42.26.114:80,8080  Public     1.0 core/1.5 gb  Linux     eastus
```

## <a name="view-container-logs"></a>コンテナー ログの表示

コンテナーのログ出力を表示するには、[az container logs][az-container-logs] コマンドを使用します。 `--container-name` 引数は、プルするログが含まれるコンテナーを指定します。 この例では、`aci-tutorial-app` コンテナーが指定されています。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-app
```

出力:

```console
listening on port 80
::1 - - [02/Jul/2020:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [02/Jul/2020:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

サイドカー コンテナーのログを表示するには、`aci-tutorial-sidecar` コンテナーを指定して、同様のコマンドを実行します。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

出力:

```console
Every 3s: curl -I http://localhost                          2020-07-02 20:36:41

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
X-Powered-By: Express
Accept-Ranges: bytes
Cache-Control: public, max-age=0
Last-Modified: Wed, 29 Nov 2017 06:40:40 GMT
ETag: W/"67f-16006818640"
Content-Type: text/html; charset=UTF-8
Content-Length: 1663
Date: Thu, 02 Jul 2020 20:36:41 GMT
Connection: keep-alive
```

このように、サイドカーは、グループのローカル ネットワーク経由で、メインの Web アプリケーションに定期的に HTTP 要求を実行して、アプリケーションが実行中であることを確認します。 このサイドカーの例は、`200 OK` 以外の HTTP 応答コードを受け取ったときに、アラートをトリガーするように拡張できます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、YAML ファイルを使用して、Azure Container Instances に複数コンテナー グループをデプロイしました。 以下の方法を学習しました。

> [!div class="checklist"]
> * マルチコンテナー グループ用の YAML ファイルを構成する
> * コンテナー グループをデプロイする
> * コンテナーのログを表示する

[Resource Manager テンプレート](container-instances-multi-container-group.md)を使用して、マルチコンテナー グループを指定することもできます。 Resource Manager テンプレートは、追加の Azure サービス リソースをコンテナー グループを使用してデプロイする必要があるシナリオに簡単に適応できます。

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-create]: /cli/azure/container#az-container-create
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
