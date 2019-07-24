---
title: チュートリアル - Azure Container Instances に複数コンテナー グループをデプロイする - テンプレート
description: このチュートリアルでは、Azure Resource Manager テンプレートと Azure CLI を使用して複数のコンテナーを含むコンテナー グループを Azure Container Instances にデプロイする方法を説明します。
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 04/03/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f769beda1654dc9f58ecff733741fb1ab9118031
ms.sourcegitcommit: 045406e0aa1beb7537c12c0ea1fbf736062708e8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/04/2019
ms.locfileid: "59006920"
---
# <a name="tutorial-deploy-a-multi-container-group-using-a-resource-manager-template"></a>チュートリアル:Resource Manager テンプレートを使用してマルチコンテナー グループをデプロイする

> [!div class="op_single_selector"]
> * [YAML](container-instances-multi-container-yaml.md)
> * [リソース マネージャー](container-instances-multi-container-group.md)

Azure Container Instances では、[コンテナー グループ](container-instances-container-groups.md)を使用して、複数のコンテナーを 1 つのホストにデプロイできます。 コンテナー グループは、サービスが 2 番目の接続プロセスを必要とする場合に、ログ記録、監視などの構成用にアプリケーション サイドカーを作成するときに便利です。

このチュートリアルでは、Azure CLI を使用して Azure Resource Manager テンプレートをデプロイすることで、単純な 2 コンテナー サイドカー構成を実行する手順を行います。 学習内容は次のとおりです。

> [!div class="checklist"]
> * 複数コンテナー グループ テンプレートをデプロイする
> * コンテナー グループをデプロイする
> * コンテナーのログを表示する

Resource Manager テンプレートは、追加の Azure サービス リソース (Azure Files 共有や仮想ネットワークなど) をコンテナー グループと共にデプロイする必要があるシナリオに合わせて簡単に適応させることができます。 

> [!NOTE]
> 複数コンテナー グループは、現在、Linux コンテナーに限定されています。 

Azure サブスクリプションがない場合は、開始する前に[無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

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
      "apiVersion": "2018-10-01",
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
              "port": "80"
            },
            {
                "protocol": "tcp",
                "port": "8080"
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

[az group create][az-group-create] コマンドでリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

[az group deployment create][az-group-deployment-create] コマンドで、テンプレートをデプロイします。

```azurecli-interactive
az group deployment create --resource-group myResourceGroup --template-file azuredeploy.json
```

数秒以内に、Azure から最初の応答を受信します。

## <a name="view-deployment-state"></a>デプロイ状態の表示

デプロイの状態を表示するには、次の [az container show][az-container-show] コマンドを使用します。

```azurecli-interactive
az container show --resource-group myResourceGroup --name myContainerGroup --output table
```

実行中のアプリケーションを表示するには、ご利用のブラウザーでその IP アドレスにアクセスします。 たとえば、次の出力例では IP は `52.168.26.124` です。

```bash
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

```bash
listening on port 80
::1 - - [21/Mar/2019:23:17:48 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:51 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [21/Mar/2019:23:17:54 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

サイドカー コンテナーのログを表示するには、`aci-tutorial-sidecar` コンテナーを指定して、同様のコマンドを実行します。

```azurecli-interactive
az container logs --resource-group myResourceGroup --name myContainerGroup --container-name aci-tutorial-sidecar
```

出力:

```bash
Every 3s: curl -I http://localhost                          2019-03-21 20:36:41

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
Date: Thu, 21 Mar 2019 20:36:41 GMT
Connection: keep-alive
```

このように、サイドカーは、グループのローカル ネットワーク経由で、メインの Web アプリケーションに定期的に HTTP 要求を実行して、アプリケーションが実行中であることを確認します。 このサイドカーの例は、`200 OK` 以外の HTTP 応答コードを受け取ったときに、アラートをトリガーするように拡張できます。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Azure Resource Manager テンプレートを使用して、Azure Container Instances に複数コンテナー グループをデプロイしました。 以下の方法について学習しました。

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
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
