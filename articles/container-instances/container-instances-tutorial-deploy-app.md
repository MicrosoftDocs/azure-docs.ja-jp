---
title: "Azure Container Instances チュートリアル - アプリのデプロイ | Microsoft Docs"
description: "Azure Container Instances チュートリアル - アプリのデプロイ"
services: container-instances
documentationcenter: 
author: seanmck
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/04/2017
ms.author: seanmck
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: f5c887487ab74934cb65f9f3fa512baeb5dcaf2f
ms.openlocfilehash: d8c2056734bc1fdea71543157debd089a9ca743d
ms.contentlocale: ja-jp
ms.lasthandoff: 08/08/2017

---

# <a name="deploy-a-container-to-azure-container-instances"></a>コンテナーを Azure Container Instances にデプロイする

これは 3 つのパートで構成されるチュートリアルの最後のタスクです。 前のセクションでは、[コンテナー イメージを作成](container-instances-tutorial-prepare-app.md)して、[Azure Container Registry にプッシュ](container-instances-tutorial-prepare-acr.md)しました。 このセクションで Azure Container Instances にコンテナーをデプロイして、このチュートリアルは完了です。 手順は次のとおりです。

> [!div class="checklist"]
> * Azure Resource Manager テンプレートを使用したコンテナー グループの定義
> * Azure CLI を使用したコンテナー グループのデプロイ
> * コンテナー ログの表示

## <a name="deploy-the-container-using-the-azure-cli"></a>Azure CLI を使用して、コンテナーをデプロイする

Azure CLI を使用すると、コンテナーを 1 つのコマンドで Azure Container Instances にデプロイできます。 コンテナー イメージはプライベートの Azure Container Registry でホストされているため、アクセスに必要な資格情報を含める必要があります。 必要に応じて、次のようにクエリを実行できます。

コンテナー レジストリ ログイン サーバー (レジストリ名で更新):

```azurecli-interactive
az acr show --name <acrName> --query loginServer
```

コンテナー レジストリ パスワード:

```azurecli-interactive
az acr credential show --name <acrName> --query "passwords[0].value"
```

1 CPU コアおよび 1 GB メモリのリソース要求でコンテナー レジストリからコンテナー イメージをデプロイするには、次のコマンドを実行します。

```azurecli-interactive
az container create --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-password <acrPassword> --ip-address public -g myResourceGroup
```

数秒以内に、Azure Resource Manager から最初の応答を受信します。 デプロイの状態を表示するには、次を使用します。

```azurecli-interactive
az container show --name aci-tutorial-app --resource-group myResourceGroup --query state
```

状態が "*[保留中]*" から "*[実行中]*" に変更されるまで、このコマンドを実行し続けることができます。 その後、次に進むことができます。

## <a name="view-the-application-and-container-logs"></a>アプリケーションとコンテナー ログを表示する

デプロイに成功したら、ブラウザーを開いて、次のコマンドの出力に示されている IP アドレスにアクセスします。

```bash
az container show --name aci-tutorial-app --resource-group myResourceGroup --query ipAddress.ip
```

```json
"13.88.176.27"
```

![ブラウザーでの Hello World アプリ][aci-app-browser]

コンテナーのログ出力を表示することもできます。

```azurecli-interactive
az container logs --name aci-tutorial-app -g myResourceGroup
```

出力:

```bash
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://13.88.176.27/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="next-steps"></a>次のステップ

このチュートリアルの、Azure Container Instances インスタンスへのコンテナーのデプロイ プロセスは完了です。 次の手順を完了しました。

> [!div class="checklist"]
> * Azure CLI を使用した Azure Container Registry からのコンテナーのデプロイ
> * ブラウザーでのアプリケーションの表示
> * コンテナー ログの表示

<!-- LINKS -->
[prepare-app]: ./container-instances-tutorial-prepare-app.md

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

