---
title: Azure Container Instances に複数コンテナー グループをデプロイする
description: Azure Resource Manager テンプレートを使用して複数のコンテナーを含むコンテナー グループを Azure Container Instances にデプロイする方法を説明します。
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 06/08/2018
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 93f73e133e99025b479d0b38512e26088a8eaefa
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2019
ms.locfileid: "58369117"
---
# <a name="deploy-a-multi-container-group-with-a-resource-manager-template"></a>Resource Manager テンプレートを使用してマルチコンテナー グループをデプロイする

Azure Container Instances では、[コンテナー グループ](container-instances-container-groups.md)を使用して、複数のコンテナーを 1 つのホストにデプロイできます。 これは、サービスが 2 番目の接続プロセスを必要とする場合に、ログ記録、監視などの構成用にアプリケーション サイドカーを作成するときに便利です。

Azure CLI を使用して複数コンテナー グループをデプロイする方法は 2 つあります。

* Resource Manager テンプレートのデプロイ (この記事)
* [YAML ファイルのデプロイ](container-instances-multi-container-yaml.md)

コンテナー インスタンスのデプロイ時に追加の Azure サービス リソース (Azure Files 共有など) をデプロイする必要がある場合は、Resource Manager テンプレートでのデプロイをお勧めします。 YAML フォーマットは簡潔であるため、コンテナー インスタンス*のみ*を含むデプロイには YAML ファイルを使用するデプロイをお勧めします。

> [!NOTE]
> 複数コンテナー グループは、現在、Linux コンテナーに限定されています。 すべての機能を Windows コンテナーにも採り入れることに取り組んでいますが、現在のプラットフォームの違いは、「[Quotas and region availability for Azure Container Instances](container-instances-quotas.md)」(Azure Container Instances のクォータとリージョンの可用性) で確認できます。

その他のテンプレート サンプルについては、「[Azure Container Instances のための Azure Resource Manager テンプレート](container-instances-samples-rm.md)」を参照してください。 

## <a name="configure-the-template"></a>テンプレートの構成

この記事のセクションでは、Azure Resource Manager テンプレートをデプロイして、シンプルな複数コンテナー サイドカー構成を実行します。

まず、`azuredeploy.json` という名前のファイルを作成し、次の JSON をそのファイルにコピーします。

この Resource Manager テンプレートでは、2 つのコンテナー、パブリック IP アドレス、公開された 2 つのポートを備えるコンテナー グループが定義されます。 コンテナーは、パブリック Microsoft イメージからデプロイされます。 グループの最初のコンテナーでは、インターネットに接続するアプリケーションが実行されます。 2 番目のコンテナーであるサイドカーは、グループのローカル ネットワーク経由でメインの Web アプリケーションに HTTP 要求を実行します。

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

## <a name="view-logs"></a>ログを表示する。

コンテナーのログ出力を表示するには、[az container logs][az-container-logs] コマンドを使用します。 `--container-name` 引数は、プルするログが含まれるコンテナーを指定します。 この例では、最初のコンテナーが指定されています。

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

サイドカー コンテナーのログを表示するには、2 番目のコンテナー名を指定して、同じコマンドを実行します。

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

このように、サイドカーは、グループのローカル ネットワーク経由で、メインの Web アプリケーションに定期的に HTTP 要求を実行して、アプリケーションが実行中であることを確認します。 このサイドカーの例は、200 OK 以外の HTTP 応答コードを受け取ったときに、アラートをトリガーするように拡張できます。

## <a name="next-steps"></a>次の手順

この記事では、複数コンテナーの Azure コンテナー インスタンスのデプロイに必要な手順について説明しました。 エンド ツー エンドの Azure Container Instances の操作については、Azure Container Instances チュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル][aci-tutorial]

<!-- LINKS - Internal -->
[aci-tutorial]: ./container-instances-tutorial-prepare-app.md
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-group-create]: /cli/azure/group#az-group-create
[az-group-deployment-create]: /cli/azure/group/deployment#az-group-deployment-create
[template-reference]: https://docs.microsoft.com/azure/templates/microsoft.containerinstance/containergroups
