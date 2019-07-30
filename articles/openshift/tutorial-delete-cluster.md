---
title: チュートリアル - Azure Red Hat OpenShift クラスターを削除する |Microsoft Docs
description: このチュートリアルでは、Azure CLI を使用して Azure Red Hat OpenShift クラスターを削除する方法を学習します
services: container-service
author: jimzim
ms.author: jzim
manager: jeconnoc
ms.topic: tutorial
ms.service: container-service
ms.date: 05/06/2019
ms.openlocfilehash: 0ad70f4c3681705377a350fee8b02a55c526f26c
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67669335"
---
# <a name="tutorial-delete-an-azure-red-hat-openshift-cluster"></a>チュートリアル:Azure Red Hat OpenShift クラスターを削除する

これは、チュートリアルの最後です。 サンプル クラスターのテストが終了したら、ここで説明されている方法でクラスターとそれに関連するリソースを削除して、使用していないものに対して課金されないようにします。

シリーズの第 3 部で学習する内容は次のとおりです。

> [!div class="checklist"]
> * Azure Red Hat OpenShift クラスターを削除する

このチュートリアル シリーズで学習する内容は次のとおりです。
> [!div class="checklist"]
> * [Azure Red Hat OpenShift クラスターを作成する](tutorial-create-cluster.md)
> * [Azure Red Hat OpenShift クラスターをスケーリングする](tutorial-scale-cluster.md)
> * Azure Red Hat OpenShift クラスターを削除する

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に

* チュートリアルの「[Azure Red Hat OpenShift クラスターを作成する](tutorial-create-cluster.md)」に従ってクラスターを作成します。

## <a name="step-1-sign-in-to-azure"></a>手順 1:Azure へのサインイン

Azure CLI をローカルで実行している場合は、`az login` を実行して Azure にサインインします。

```bash
az login
```

複数のサブスクリプションへのアクセス権がある場合は、`az account set -s {subscription ID}` を実行して、`{subscription ID}` を、使用するサブスクリプションに置き換えます。

## <a name="step-2-delete-the-cluster"></a>手順 2:クラスターを削除する

Bash ターミナルを開き、変数 CLUSTER_NAME にクラスターの名前を設定します。

```bash
CLUSTER_NAME=yourclustername
```

クラスターを削除します。

```bash
az openshift delete --resource-group $CLUSTER_NAME --name $CLUSTER_NAME
```

クラスターを削除するかどうかを確認するメッセージが表示されます。 `y` で確認した後、クラスターが削除されるまでに数分かかります。 コマンドが終了すると、リソース グループ全体とそれに含まれているすべてのリソース (クラスターなど) が削除されます。

## <a name="deleting-a-cluster-using-the-azure-portal"></a>Azure portal を使用したクラスターの削除

また、オンラインの Azure portal を使用して、クラスターの関連付けられたリソース グループを削除することもできます。 リソース グループの名前は、クラスター名と同じです。

現時点では、クラスターを作成するときに作成された `Microsoft.ContainerService/openShiftManagedClusters` リソースは、Azure portal では表示されません。 リソース グループを表示するには、[`Resource group`] ビューで [`Show hidden types`] をオンにします。

![非表示の種類のチェック ボックスのスクリーンショット](./media/aro-portal-hidden-type.png)

リソース グループを削除すると、Azure Red Hat OpenShift クラスターを構築するときに作成されるすべての関連リソースが削除されます。

## <a name="next-steps"></a>次の手順

チュートリアルのこの部分で学習した内容は次のとおりです。
> [!div class="checklist"]
> * Azure Red Hat OpenShift クラスターを削除する

OpenShift の使用の詳細については、公式の [Red Hat OpenShift ドキュメント](https://docs.openshift.com/aro/welcome/index.html)を参照してください