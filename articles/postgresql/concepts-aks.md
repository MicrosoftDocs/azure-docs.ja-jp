---
title: Azure Kubernetes サービスに接続する - Azure Database for PostgreSQL - Single Server
description: Azure Kubernetes Service (AKS) を Azure Database for PostgreSQL - Single Server に接続する方法について学習する
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.date: 07/14/2020
ms.topic: conceptual
ms.openlocfilehash: 4214b01f3f3651f8785f8644cf12326bf182bce7
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87084179"
---
# <a name="connecting-azure-kubernetes-service-and-azure-database-for-postgresql---single-server"></a>Azure Kubernetes Service と Azure Database for PostgreSQL - Single Server を接続する

Azure Kubernetes Service (AKS) からは、Azure で使用できるマネージド Kubernetes クラスターが提供されます。 AKS と Azure Database for PostgreSQL を併用してアプリケーションを作成するときに考慮する選択肢を下にいくつか示します。


## <a name="accelerated-networking"></a>Accelerated Networking
AKS クラスターで高速ネットワーク対応の基礎 VM を使用します。 VM で高速ネットワークが有効になると、待ち時間が少なくなり、ジッターが減り、VM の CPU 使用率が下がります。 高速ネットワークのしくみ、サポートされている OS バージョン、[Linux](../virtual-network/create-vm-accelerated-networking-cli.md) 向けにサポートされている VM インスタンスについて説明します。

2018 年 11 月から、AKS は、これらのサポートされている VM インスタンスでの高速ネットワークに対応します。 これらの VM を使用する新しい AKS クラスターでは、高速ネットワークが既定で有効になっています。

AKS クラスターで高速ネットワークが有効になっているかどうかは次の方法で確認できます。
1. Azure portal に進み、AKS クラスターを選択します。
2. [プロパティ] タブを選択します。
3. **[インフラストラクチャ リソース グループ]** の名前をコピーします。
4. ポータル検索バーを使用し、インフラストラクチャ リソース グループを見つけて開きます。
5. そのリソース グループ内の VM を選択します。
6. VM の **[ネットワーク]** タブに移動します。
7. **[高速ネットワーク]** が "有効" になっていることを確認します。

または、Azure CLI で、次の 2 つのコマンドを使用します。
```azurecli
az aks show --resource-group myResourceGroup --name myAKSCluster --query "nodeResourceGroup"
```
出力は、AKS が作成する、ネットワーク インターフェイスを含む生成されたリソース グループになります。 名前 "nodeResourceGroup" を利用し、次のコマンドで使用します。 **EnableAcceleratedNetworking** は、true または false のいずれかになります。
```azurecli
az network nic list --resource-group nodeResourceGroup -o table
```


## <a name="connection-pooling"></a>接続のプール
接続プーラーにより、データベースへの新しい接続の作成と終了に関連するコストと時間が最小限に抑えられます。 プールは、再利用できる接続のコレクションです。 

PostgreSQL では複数の接続プーラーを使用できます。 その 1 つは [PgBouncer](https://pgbouncer.github.io/) です。 Microsoft Container Registry では、提供されている軽量なコンテナー化された PgBouncer をサイドカーで使用して、AKS から Azure Database for PostgreSQL への接続をプールできます。 このイメージにアクセスして使用する方法については、[Docker Hub のページ](https://hub.docker.com/r/microsoft/azureossdb-tools-pgbouncer/)をご覧ください。 


## <a name="next-steps"></a>次のステップ
-  [Azure Kubernetes Service クラスターを作成する](../aks/kubernetes-walkthrough.md)
