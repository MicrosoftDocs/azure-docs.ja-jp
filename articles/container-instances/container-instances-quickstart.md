---
title: "最初の Azure Container Instances コンテナーの作成 | Azure Docs"
description: "Azure Container Instances でのデプロイおよび作業の開始"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/26/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: 137671152878e6e1ee5ba398dd5267feefc435b7
ms.openlocfilehash: ad053391e6b3927ab11faaf4d9e70b610e86f3c3
ms.contentlocale: ja-jp
ms.lasthandoff: 07/28/2017

---

# <a name="create-your-first-container-in-azure-container-instances"></a>Azure Container Instances での最初のコンテナーの作成

Azure Container Instances を使用すると、Azure でコンテナーを簡単に作成して管理できます。 このクイック スタートでは、Azure でコンテナーを作成し、パブリック IP アドレスを使用してインターネットに公開します。 この操作は、1 つのコマンドで完結します。 わずか数秒で、お使いのブラウザーに次のように表示されます。

![Azure Container Instances を使用してデプロイされたアプリのブラウザーでの表示][aci-app-browser]

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイックスタートを実施するには、Azure CLI バージョン 2.0.12 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、「[Azure CLI 2.0 のインストール]( /cli/azure/install-azure-cli)」を参照してください。 

## <a name="create-a-resource-group"></a>リソース グループの作成

Azure Container Instances は、Azure のリソースであり、Azure リソース グループに配置する必要があります。Azure リソース グループとは、Azure のリソースがデプロイおよび管理される論理的なコレクションです。

[az group create](/cli/azure/group#create) コマンドでリソース グループを作成します。 

次の例では、*myResourceGroup* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-container"></a>コンテナーを作成する

コンテナーを作成するには、名前、Docker イメージ、および Azure リソース グループを指定します。 コンテナーは、必要に応じて、パブリック IP アドレスを使用してインターネットに公開できます。 この例では、[Node.js](http://nodejs.org) で記述された非常にシンプルな Web アプリをホストしているコンテナーを使用します。

```azurecli-interactive
az container create --name mycontainer --image microsoft/aci-helloworld --resource-group myResourceGroup --ip-address public 
```

数秒のうちに、要求に対する応答が得られます。 最初に、コンテナーは**作成中**の状態になりますが、数秒のうちに起動されます。 `show` コマンドを使用して状態を確認することができます。

```azurecli-interactive
az container show --name mycontainer --resource-group myResourceGroup
```

出力の下部に、コンテナーのプロビジョニングの状態とその IP アドレスが表示されます。

```json
...
"ipAddress": {
      "ip": "13.88.8.148",
      "ports": [
        {
          "port": 80,
          "protocol": "TCP"
        }
      ]
    },
    "osType": "Linux",
    "provisioningState": "Succeeded"
...
```

コンテナーが**成功**状態に推移すると、指定した IP アドレスを使用してブラウザー内でこのコンテナーにアクセスすることができます。 

![Azure Container Instances を使用してデプロイされたアプリのブラウザーでの表示][aci-app-browser]

## <a name="pull-the-container-logs"></a>コンテナー ログを取り込む

`logs` コマンドを使用して、作成したコンテナーのログを取り込むことができます。

```azurecli-interactive
az container logs --name mycontainer --resource-group myResourceGroup
```

出力:

```bash
listening on port 80
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.255.105 - - [21/Jul/2017:00:01:46 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://104.210.39.122/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="delete-the-container"></a>コンテナーを削除する

コンテナーを使い終えたら、`delete` コマンドを使用してそのコンテナーを削除することができます。

```azurecli-interactive
az container delete --name mycontainer --resource-group myResourceGroup
```

## <a name="next-steps"></a>次のステップ

このクイック スタートで使用したコンテナーのコードはすべて、その Dockerfile と共に [GitHub][app-github-repo] で入手できます。 Azure Container Registry を使用してビルドと Azure Container Instances へのデプロイを自分で試す場合は、Azure Container Instances のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [Azure Container Instances のチュートリアル](./container-instances-tutorial-prepare-app.md)


<!-- LINKS -->
[app-github-repo]: https://github.com/Azure-Samples/aci-helloworld.git

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png
