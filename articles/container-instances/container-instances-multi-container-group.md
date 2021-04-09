---
title: チュートリアル - 複数コンテナー グループをデプロイする - テンプレート
description: このチュートリアルでは、Azure Resource Manager テンプレートと Azure CLI を使用して複数のコンテナーを含むコンテナー グループを Azure Container Instances にデプロイする方法を説明します。
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: bc956bed8324398c2d60f4641cd0bcb821fb51c2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93091346"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>チュートリアル:Resource Manager テンプレートを使用してマルチコンテナー グループをデプロイする

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [Resource Manager](container-instances-multi-container-group.md)

Azure Container Instances では、[コンテナー グループ](container-instances-container-groups.md)を使用して、複数のコンテナーを 1 つのホストにデプロイできます。 コンテナー グループは、サービスが 2 つ目のアタッチされたプロセスを必要とする場合に、ログ記録、監視などの構成用にアプリケーション サイドカーを作成するときに便利です。

このチュートリアルでは、Azure CLI を使用して Azure Resource Manager テンプレートをデプロイすることで、単純な 2 コンテナー サイドカー構成を実行する手順を行います。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 複数コンテナー グループ テンプレートをデプロイする
> * コンテナー グループをデプロイする
> * コンテナーのログを表示する

Resource Manager テンプレートは、追加の Azure サービス リソース (Azure Files 共有や仮想ネットワークなど) をコンテナー グループと共にデプロイする必要があるシナリオに合わせて簡単に適応させることができます。 

> [!NOTE]
> 複数コンテナー グループは、現在、Linux コンテナーに限定されています。 

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="configure-a-template"></a>テンプレートを構成する

まず、次の JSON を `azuredeploy.json` という名前の新しいファイルにコピーします。 Azure Cloud Shell では、Visual Studio Code を使用して作業ディレクトリにファイルを作成できます。

```
code azuredeploy.json
```

この Resource Manager テンプレートでは、2 つのコンテナー、パブリック IP アドレス、公開された 2 つのポートを備えるコンテナー グループが定義されます。 グループの最初のコンテナーでは、インターネットに接続する Web アプリケーションが実行されます。 2 番目のコンテナーであるサイドカーは、グループのローカル ネットワーク経由でメインの Web アプリケーションに HTTP 要求を実行します。

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "containerGroupName": {
      "type": "string",
      "defaultValue": "myContainerGroup",
      "metadata": {
        "description": "Container Group name."
      }
    }
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "mcr.microsoft.com/azuredocs/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",
    "container2image": "mcr.microsoft.com/azuredocs/aci-tutorial-sidecar"
  },
  "resources": [
    {
      "name": "[parameters('containerGroupName')]",
      "type": "Microsoft.ContainerInstance/containerGroups",
      "apiVersion": "2019-12-01",
      "location": "[resourceGroup().location]",
      "properties": {
        "containers": [
          {
            "name": "[variables('container1name')]",
            "properties": {
              "image": "[variables('container1image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              },
              "ports": [
                {
                  "port": 80
                },
                {
                  "port": 8080
                }
              ]
            }
          },
          {
            "name": "[variables('container2name')]",
            "properties": {
              "image": "[variables('container2image')]",
              "resources": {
                "requests": {
                  "cpu": 1,
                  "memoryInGb": 1.5
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "ipAddress": {
          "type": "Public",
          "ports": [
            {
              "protocol": "tcp",
              "port": 80
            },
            {
                "protocol": "tcp",
                "port": 8080
            }
          ]
        }
      }
    }
  ],
  "outputs": {
    "containerIPv4Address": {
      "type": "string",
      "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', parameters('containerGroupName'))).ipAddress.ip]"
    }
  }
}
```

プライベート コンテナー イメージ レジストリを使用するには、次の形式でオブジェクトを JSON ドキュメントに追加します。 この構成の実装例については、[ACI Resource Manager テンプレートのリファレンス][template-reference]に関するドキュメントを参照してください。

```JSON
"imageRegistryCredentials": [
  {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
  }
]
```

## <a name="deploy-the-template"></a>テンプレートのデプロイ

[az group create][az-group-create] コマンドを使用して、リソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az deployment group create][az-deployment-group-create] コマンドを使用してテンプレートをデプロイします。

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file azuredeploy.json
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

このチュートリアルでは、Azure Resource Manager テンプレートを使用して、Azure Container Instances に複数コンテナー グループをデプロイしました。 以下の方法を学習しました。

> [!div class="checklist"]
> * 複数コンテナー グループ テンプレートをデプロイする
> * コンテナー グループをデプロイする
> * コンテナーのログを表示する

その他のテンプレート サンプルについては、「[Azure Container Instances のための Azure Resource Manager テンプレート](container-instances-samples-rm.md)」を参照してください。

[YAML ファイル](container-instances-multi-container-yaml.md)を使用してマルチコンテナー グループを指定することもできます。 YAML フォーマットは簡潔であるため、コンテナー インスタンスのみを含むデプロイには YAML ファイルを使用するデプロイをお勧めします。


<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[template-reference]: /azure/templates/microsoft.containerinstance/containergroups
