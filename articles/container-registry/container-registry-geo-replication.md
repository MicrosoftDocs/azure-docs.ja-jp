---
title: Azure Container Registry の geo レプリケーション
description: geo レプリケートされた Azure コンテナー レジストリの作成と管理の概要について説明します。
services: container-registry
author: stevelas
manager: gwallace
ms.service: container-registry
ms.topic: overview
ms.date: 05/24/2019
ms.author: stevelas
ms.openlocfilehash: e17b70843fcda1d183de1b81a98da53138835340
ms.sourcegitcommit: f5075cffb60128360a9e2e0a538a29652b409af9
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/18/2019
ms.locfileid: "68309603"
---
# <a name="geo-replication-in-azure-container-registry"></a>Azure Container Registry の geo レプリケーション

ローカル プレゼンスやホット バックアップを必要とする企業は、複数の Azure リージョンからサービスを実行しています。 ベスト プラクティスとして、イメージが実行されている各リージョンにコンテナー レジストリを配置してネットワーク上の近い場所での操作を可能にすることで、高速で信頼性の高いイメージ レイヤー転送を実現します。 geo レプリケーションにより、Azure コンテナー レジストリが単一のレジストリとして機能することが可能になり、マルチマスター リージョン レジストリで複数のリージョンに対応できます。 

geo レプリケートされたレジストリには次の利点があります。

* 複数のリージョンで 1 つのレジストリ/イメージ/タグ名を使用できる
* リージョン デプロイからネットワーク上の近い場所のレジストリにアクセスできる
* コンテナー ホストと同じリージョンにあるレプリケートされたローカルのレジストリからイメージがプルされるので、追加のエグレス料金が発生しない
* 複数のリージョンにまたがってレジストリを一元管理できる

> [!NOTE]
> コンテナー イメージのコピーを複数の Azure コンテナー レジストリに保持する必要がある場合、Azure Container Registry も[イメージのインポート](container-registry-import-images.md)をサポートします。 たとえば DevOps ワークフローでは、Docker コマンドを使用する必要なしに、開発レジストリから運用レジストリにイメージをインポートできます。
>

## <a name="example-use-case"></a>ユース ケースの例
Contoso では、米国、カナダ、ヨーロッパにまたがる パブリック プレゼンスの Web サイトを運用しています。 Contoso は、ネットワーク上の近い場所にあるローカルのコンテンツでこれらの市場に対応するために、米国西部、米国東部、カナダ中部、西ヨーロッパで [Azure Kubernetes Service](/azure/aks/) (AKS) クラスターを実行しています。 Docker イメージとしてデプロイされた Web サイト アプリケーションでは、すべてのリージョンで同じコードとイメージを使用します。 リージョンのローカルのコンテンツは、各リージョンに独自にプロビジョニングされたデータベースから取得されます。 各リージョン デプロイには、ローカル データベースなどのリソースの独自の構成があります。

開発チームは、米国西部データ センターを利用するワシントン州シアトルを拠点としています。

![複数のレジストリへのプッシュ](media/container-registry-geo-replication/before-geo-replicate.png)<br />*複数のレジストリへのプッシュ*

Contoso では、geo レプリケーション機能を使用する前は、米国西部では米国ベースのレジストリを使用し、西ヨーロッパでは追加のレジストリを使用していました。 これらの異なるリージョンに対応するために、開発チームは 2 つの異なるレジストリにイメージをプッシュしていました。

```bash
docker push contoso.azurecr.io/public/products/web:1.2
docker push contosowesteu.azurecr.io/public/products/web:1.2
```
![複数のレジストリからのプル](media/container-registry-geo-replication/before-geo-replicate-pull.png)<br />*複数のレジストリからのプル*

複数のレジストリの一般的な課題は次のとおりです。

* 米国東部、米国西部、カナダ中部のクラスターは、いずれも米国西部のレジストリからプルします。これらのリモート コンテナー ホストはそれぞれ米国西部データセンターからイメージをプルするため、エグレス料金が発生します。
* 開発チームは、米国西部と西ヨーロッパのレジストリにイメージをプッシュする必要があります。
* 開発チームは、ローカル レジストリを参照するイメージ名で各リージョン デプロイを構成し、管理する必要があります。
* レジストリ アクセスをリージョンごとに構成する必要があります。

## <a name="benefits-of-geo-replication"></a>geo レプリケーションのメリット

![geo レプリケートされたレジストリからのプル](media/container-registry-geo-replication/after-geo-replicate-pull.png)

Azure Container Registry の geo レプリケーション機能を使用すると、次のメリットが実現されます。

* すべてのリージョンにまたがる 1 つのレジストリ (`contoso.azurecr.io`) を管理すれば済む。
* すべてのリージョンで同じイメージ URL (`contoso.azurecr.io/public/products/web:1.2`) が使用されるので、イメージのデプロイの 1 つの構成を管理すれば済む。
* 1 つのレジストリにプッシュすれば済む。geo レプリケーションは、ACR が管理する。 特定のレプリカ内のイベントを通知するように、リージョン [Webhook](container-registry-webhook.md) を構成できます。

## <a name="configure-geo-replication"></a>geo レプリケーションの構成

geo レプリケーションは、マップ上でリージョンをクリックして簡単に構成できます。 Azure CLI の [az acr replication](/cli/azure/acr/replication) コマンドなどのツールを使用して、geo レプリケーションを管理することもできます。

geo レプリケーションは、[Premium レジストリ](container-registry-skus.md)限定の機能です。 レジストリがまだ Premium でない場合は、[Azure Portal](https://portal.azure.com) で Basic および Standard から Premium に変更できます。

![Azure Portal での SKU の切り替え](media/container-registry-skus/update-registry-sku.png)

Premium レジストリの geo レプリケーションを構成するには、Azure Portal (https://portal.azure.com ) にログインします。

Azure Container Registry に移動し、 **[レプリケーション]** を選択します。

![Azure Portal のコンテナー レジストリ UI の [レプリケーション]](media/container-registry-geo-replication/registry-services.png)

現在のすべての Azure リージョンを示すマップが表示されます。

 ![Azure Portal のリージョン マップ](media/container-registry-geo-replication/registry-geo-map.png)

* 青色の六角形は、現在のレプリカを表します。
* 緑色の六角形は、レプリカを構成可能なリージョンを表します。
* 灰色の六角形は、レプリケーションをまだ利用できない Azure リージョンを表します。

レプリカを構成するには、緑色の六角形を選択し、 **[作成]** をクリックします。

 ![Azure Portal の [レプリケーションの作成] UI](media/container-registry-geo-replication/create-replication.png)

追加のレプリカを構成するには、他のリージョンの緑色の六角形を選択し、 **[作成]** をクリックします。

ACR は、構成済みのレプリカ間でイメージの同期を開始します。 同期が完了すると、ポータルに *[準備完了]* と表示されます。 ポータルのレプリカの状態は自動的に更新されるわけではありません。 最新の状態を表示するには、更新ボタンを使用します。

## <a name="considerations-for-using-a-geo-replicated-registry"></a>geo レプリケーションされたレジストリの使用に関する注意点

* geo レプリケーションされたレジストリの各リージョンは、設定後は独立しています。 Azure Container Registry の SLA は、geo レプリケーションされた各リージョンに適用されます。
* geo レプリケーションされたレジストリからイメージをプッシュまたはプルすると、バックグラウンドの Azure Traffic Manager は最も近いリージョンにあるレジストリに要求を送信します。
* イメージまたはタグの更新を最も近いリージョンにプッシュした後、Azure Container Registry がマニフェストとレイヤーを、選択された残りのリージョンにレプリケートするまでに、少し時間がかかります。 大きいイメージは、小さいイメージよりもレプリケートに時間がかかります。 イメージとタグは、最終的な整合性モデルを使用して、レプリケーションのリージョン間で同期されます。
* geo レプリケーションされたレジストリへのプッシュ更新に依存するワークフローを管理するには、プッシュ イベントに応答するように [Webhook](container-registry-webhook.md) を構成することをお勧めします。 geo レプリケーションされたレジストリ内にリージョンの Webhook を設定して、geo レプリケーションされたすべてのリージョンにわたってプッシュ イベントが完了したときにそれを追跡できます。


## <a name="geo-replication-pricing"></a>geo レプリケーションの価格

geo レプリケーションは、Azure Container Registry の [Premium SKU](container-registry-skus.md) の機能です。 レジストリを目的のリージョンにレプリケートすると、リージョンごとに Premium レジストリ料金が発生します。

前の例では、Contoso は、米国東部、カナダ中部、西ヨーロッパにレプリカを追加して、2 つのレジストリを 1 つに統合しました。 Contoso には、1 か月あたり 4 倍の Premium 料金が課金されます。追加の構成や管理は不要です。 各リージョンではイメージをローカルでプルできるようになったため、米国西部からカナダおよび米国東部へのネットワーク エグレス料金が発生することなく、パフォーマンスと信頼性が向上します。

## <a name="next-steps"></a>次の手順

[Azure Container Registry の geo レプリケーション](container-registry-tutorial-prepare-registry.md)に関する 3 部構成のチュートリアル シリーズを確認します。 これらのチュートリアルでは、geo レプリケートされたレジストリを作成する方法、コンテナーをビルドする方法、1 つの `docker push` コマンドを使用してリージョンの複数の Web App for Containers インスタンスにデプロイする方法を説明します。

> [!div class="nextstepaction"]
> [Azure Container Registry の geo レプリケーション](container-registry-tutorial-prepare-registry.md)
