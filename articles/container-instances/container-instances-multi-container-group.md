---
title: "Azure Container Instances - 複数コンテナー グループ | Azure Docs"
description: "Azure Container Instances - 複数コンテナー グループ"
services: container-instances
documentationcenter: 
author: neilpeterson
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: nepeters
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: 94744e8138d40dd777c0c004472804e3af6c0b1e
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---

# <a name="deploy-a-container-group"></a>コンテナー グループのデプロイ

Azure Container Instances では、"*コンテナー グループ*" を使用して、複数のコンテナーを 1 つのホストにデプロイできます。 これは、サービスが 2 番目の接続プロセスを必要とする場合に、ログ記録、監視などの構成用にアプリケーション サイドカーを作成するときに便利です。 

このドキュメントでは、Azure Resource Manager テンプレートを使用して、シンプルな複数コンテナー サイドカー構成を実行します。

## <a name="configure-the-template"></a>テンプレートの構成

`azuredeploy.json` という名前のファイルを作成し、次の json をそのファイルにコピーします。 

このサンプルでは、2 つのコンテナーを含むコンテナー グループと、パブリック IP アドレスが定義されています。 グループの最初のコンテナーでは、インターネットに接続するアプリケーションが実行されます。 2 番目のコンテナーであるサイドカーは、グループのローカル ネットワーク経由でメインの Web アプリケーションに HTTP 要求を実行します。 

このサイドカーの例は、200 OK 以外の HTTP 応答コードを受け取ったときに、アラートをトリガーするように拡張できます。 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
  },
  "variables": {
    "container1name": "aci-tutorial-app",
    "container1image": "microsoft/aci-helloworld:latest",
    "container2name": "aci-tutorial-sidecar",    
    "container2image": "microsoft/aci-tutorial-sidecar"
  },
    "resources": [
      {
        "name": "myContainerGroup",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2017-08-01-preview",
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
              }
            ]
          }
        }
      }
    ],
    "outputs": {
      "containerIPv4Address": {
        "type": "string",
        "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups/', 'myContainerGroup')).ipAddress.ip]"
      }
    }
  }
```

プライベート コンテナー イメージ レジストリを使用するのには、次の形式でオブジェクトを json ドキュメントに追加します。

```json
"imageRegistryCredentials": [
    {
    "server": "[parameters('imageRegistryLoginServer')]",
    "username": "[parameters('imageRegistryUsername')]",
    "password": "[parameters('imageRegistryPassword')]"
    }
]
```

## <a name="deploy-the-template"></a>テンプレートのデプロイ

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

[az group deployment create](/cli/azure/group/deployment#create) コマンドで、テンプレートをデプロイします。

```azurecli-interactive
az group deployment create --name myContainerGroup --resource-group myResourceGroup --template-file azuredeploy.json
```

数秒以内に、Azure から最初の応答を受信します。 

## <a name="view-deployment-state"></a>デプロイ状態の表示

デプロイの状態を表示するには、`az container show` コマンドを使用します。 このコマンドは、プロビジョニングされたパブリック IP アドレスを返します。このアドレスを使用して、アプリケーションにアクセスできます。

```azurecli-interactive
az container show --name myContainerGroup --resource-group myResourceGroup -o table
```

出力:

```azurecli
Name              ResourceGroup    ProvisioningState    Image                                                             IP:ports           CPU/Memory    OsType    Location
----------------  ---------------  -------------------  ----------------------------------------------------------------  -----------------  ------------  --------  ----------
myContainerGroup  myResourceGrou2  Succeeded            microsoft/aci-tutorial-sidecar,microsoft/aci-tutorial-app:v1      40.118.253.154:80  1.0 core/1.5 gb   Linux     westus
```

## <a name="view-logs"></a>ログを表示する。   

コンテナーのログ出力を表示するには、`az container logs` コマンドを使用します。 `--container-name` 引数は、プルするログが含まれるコンテナーを指定します。 この例では、最初のコンテナーが指定されています。 

```azurecli-interactive
az container logs --name myContainerGroup --container-name aci-tutorial-app --resource-group myResourceGroup
```

出力:

```bash
istening on port 80
::1 - - [27/Jul/2017:17:35:29 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:32 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:35 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
::1 - - [27/Jul/2017:17:35:38 +0000] "HEAD / HTTP/1.1" 200 1663 "-" "curl/7.54.0"
```

サイドカー コンテナーのログを表示するには、2 番目のコンテナー名を指定して、同じコマンドを実行します。

```azurecli-interactive
az container logs --name myContainerGroup --container-name aci-tutorial-sidecar --resource-group myResourceGroup
```

出力:

```bash
Every 3.0s: curl -I http://localhost                                                                                                                       Mon Jul 17 11:27:36 2017

  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0  0  1663    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
HTTP/1.1 200 OK
Accept-Ranges: bytes
Content-Length: 1663
Content-Type: text/html; charset=utf-8
Last-Modified: Sun, 16 Jul 2017 02:08:22 GMT
Date: Mon, 17 Jul 2017 18:27:36 GMT
```

このように、サイドカーは、グループのローカル ネットワーク経由で、メインの Web アプリケーションに定期的に HTTP 要求を実行して、アプリケーションが実行中であることを確認します。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、複数コンテナーの Azure コンテナー インスタンスのデプロイに必要な手順について説明します。 エンド ツー エンドの Azure Container Instances の操作については、Azure Container Instances チュートリアルを参照してください。

> [!div class="nextstepaction"]
> [Azure Container Instances チュートリアル]: ./container-instances-tutorial-prepare-app.md

