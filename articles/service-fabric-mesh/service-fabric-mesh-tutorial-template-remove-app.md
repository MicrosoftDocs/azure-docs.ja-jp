---
title: チュートリアル-Azure Service Fabric Mesh で実行されているアプリの削除 |Microsoft Docs
description: このチュートリアルでは、Service Fabric Mesh で実行されているアプリケーションを削除する方法について説明し、リソースを削除します。
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: adc5b96f29f610c63bcfa24a3b5f761c04d41d5b
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/21/2019
ms.locfileid: "58339671"
---
# <a name="tutorial-remove-an-application-and-resources"></a>チュートリアル:アプリケーションとリソースを削除する

このチュートリアルは、シリーズの第 4 部です。 [Service Fabric Mesh に対し事前に展開されていた](service-fabric-mesh-tutorial-template-deploy-app.md)実行中のアプリケーションを削除する方法について説明します。 

シリーズの第 4 部では、次の方法を学習します。

> [!div class="checklist"]
> * Service Fabric Mesh で実行されているアプリを削除します
> * アプリケーション リソースを削除します

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [テンプレートを使用して Azure Service Fabric Mesh にアプリケーションをデプロイする](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [ローカルで実行している Service Fabric Mesh アプリケーションをスケールする](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Service Fabric Mesh で実行されているアプリケーションをアップグレードする](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * アプリケーションの削除

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウントを作成](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)できます。

* [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md) を開くか、または[Azure CLI と Service Fabric Mesh CLI をローカルにインストール](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli)します。

## <a name="delete-the-resource-group-and-all-the-resources"></a>リソース グループとすべてのリソースを削除します

不要になったら、作成したすべてのリソースを削除します。 以前は、Azure Container Registry インスタンスと Service Fabric Mesh アプリケーションのリソースをホストするために[、新しいリソース グループを作成しました](service-fabric-mesh-tutorial-template-deploy-app.md#create-a-container-registry)。  関連付けられているリソースはすべて削除されますが、このリソース グループを削除することができます。

```azurecli
az group delete --resource-group myResourceGroup
```

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="individually-delete-the-resources"></a>リソースを個別に削除する
ACR インスタンス、Service Fabric Mesh アプリケーション、およびネットワーク リソースを個別に削除することもできます。

ACR インスタンスを削除します：

```azurecli
az acr delete --resource-group myResourceGroup --name myContainerRegistry
```

Service Fabric Mesh アプリケーションを削除します：

```azurecli
az mesh app delete --resource-group myResourceGroup --name todolistapp
```

ネットワークを削除します：
```azurecli
az mesh network delete --resource-group myResourceGroup --name todolistappNetwork
```

## <a name="next-steps"></a>次の手順

チュートリアルのこの部分で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Service Fabric Mesh で実行されているアプリを削除します
> * アプリケーション リソースを削除します