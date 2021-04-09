---
title: レジストリの geo レプリケーション
description: geo レプリケートされた Azure コンテナー レジストリの作成と管理の概要について説明します。これにより、レジストリからマルチマスター リージョン レプリカを持つ複数のリージョンにサービスを提供できるようになります。 geo レプリケーションは、Premium サービス レベルの機能です。
author: stevelas
ms.topic: article
ms.date: 07/21/2020
ms.author: stevelas
ms.openlocfilehash: 4e82be0e81e5e8c0182e061a0fba0f880bd45cc6
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102632392"
---
# <a name="geo-replication-in-azure-container-registry"></a>Azure Container Registry の geo レプリケーション

ローカル プレゼンスやホット バックアップを必要とする企業は、複数の Azure リージョンからサービスを実行しています。 ベスト プラクティスとして、イメージが実行されている各リージョンにコンテナー レジストリを配置してネットワーク上の近い場所での操作を可能にすることで、高速で信頼性の高いイメージ レイヤー転送を実現します。 geo レプリケーションにより、Azure コンテナー レジストリが単一のレジストリとして機能することが可能になり、マルチマスター リージョン レジストリで複数のリージョンに対応できます。 

geo レプリケートされたレジストリには次の利点があります。

* 複数のリージョンで単一のレジストリ、イメージ、タグの名前を使用できる
* ネットワーク上の近い場所のレジストリにアクセスできることで、リージョンのデプロイのパフォーマンスと信頼性が向上する
* コンテナー ホストと同じまたは隣接するリージョンにあるレプリケートされたローカルのレジストリからイメージ レイヤーをプルすることで、データ転送コストを削減する
* 複数のリージョンにまたがってレジストリを一元管理できる
* リージョンの障害が発生した場合のレジストリの復元

> [!NOTE]
> コンテナー イメージのコピーを複数の Azure コンテナー レジストリに保持する必要がある場合、Azure Container Registry も[イメージのインポート](container-registry-import-images.md)をサポートします。 たとえば DevOps ワークフローでは、Docker コマンドを使用する必要なしに、開発レジストリから運用レジストリにイメージをインポートできます。
>

## <a name="example-use-case"></a>ユース ケースの例
Contoso では、米国、カナダ、ヨーロッパにまたがる パブリック プレゼンスの Web サイトを運用しています。 Contoso は、ネットワーク上の近い場所にあるローカルのコンテンツでこれらの市場に対応するために、米国西部、米国東部、カナダ中部、西ヨーロッパで [Azure Kubernetes Service](../aks/index.yml) (AKS) クラスターを実行しています。 Docker イメージとしてデプロイされた Web サイト アプリケーションでは、すべてのリージョンで同じコードとイメージを使用します。 リージョンのローカルのコンテンツは、各リージョンに独自にプロビジョニングされたデータベースから取得されます。 各リージョン デプロイには、ローカル データベースなどのリソースの独自の構成があります。

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
* 1 つのレジストリにプッシュすれば済む。geo レプリケーションは、ACR が管理する。 ACR は一意のレイヤーのみをレプリケートし、リージョン間のデータ転送を削減する。 
* 特定のレプリカ内のイベントを通知するように、リージョン [Webhook](container-registry-webhook.md) を構成する。
* リージョンの障害に対して回復性がある高可用性レジストリを提供します。

Azure Container Registry では、耐障害性と可用性に優れた Azure コンテナー レジストリを Azure リージョンに作成するため、[可用性ゾーン](zone-redundancy.md)もサポートされています。 リージョン内の冗長性のための可用性ゾーンと、複数のリージョンをまたぐ geo レプリケーションを組み合わせることで、レジストリの信頼性とパフォーマンスが強化されます。

## <a name="configure-geo-replication"></a>geo レプリケーションの構成

geo レプリケーションは、マップ上でリージョンをクリックして簡単に構成できます。 Azure CLI の [az acr replication](/cli/azure/acr/replication) コマンドなどのツールを使用して geo レプリケーションを管理することや、[Azure Resource Manager テンプレート](https://github.com/Azure/azure-quickstart-templates/tree/master/101-container-registry-geo-replication)を使用して geo レプリケーションが有効なレジストリをデプロイすることもできます。

geo レプリケーションは、[Premium レジストリ](container-registry-skus.md)の機能です。 レジストリがまだ Premium でない場合は、[Azure Portal](https://portal.azure.com) で Basic および Standard から Premium に変更できます。

![Azure portal でのサービス レベルの切り替え](media/container-registry-skus/update-registry-sku.png)

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
* geo レプリケーションされたレジストリからイメージをプッシュまたはプルすると、バックグラウンドの Azure Traffic Manager は、ネットワーク待機時間の観点から最も近いリージョンにあるレジストリに要求を送信します。
* イメージまたはタグの更新を最も近いリージョンにプッシュした後、Azure Container Registry がマニフェストとレイヤーを、選択された残りのリージョンにレプリケートするまでに、少し時間がかかります。 大きいイメージは、小さいイメージよりもレプリケートに時間がかかります。 イメージとタグは、最終的な整合性モデルを使用して、レプリケーションのリージョン間で同期されます。
* geo レプリケーションされたレジストリへのプッシュ更新に依存するワークフローを管理するには、プッシュ イベントに応答するように [Webhook](container-registry-webhook.md) を構成することをお勧めします。 geo レプリケーションされたレジストリ内にリージョンの Webhook を設定して、geo レプリケーションされたすべてのリージョンにわたってプッシュ イベントが完了したときにそれを追跡できます。
* コンテンツ レイヤーを表す BLOB にサービスを提供するために、Azure Container Registry ではデータ エンドポイントを使用します。 各レジストリの geo レプリケートされたリージョンで、レジストリの[専用データ エンドポイント](container-registry-firewall-access-rules.md#enable-dedicated-data-endpoints)を有効にすることができます。 これらのエンドポイントを使用すると、スコープが厳密に設定されたファイアウォール アクセス規則を構成できます。 トラブルシューティング時には、レプリケートされたデータを維持したまま、必要に応じて[レプリケーションへのルーティングを無効にする](#temporarily-disable-routing-to-replication)ことができます。
* 仮想ネットワークのプライベート エンドポイントを使用して、レジストリの [Private Link](container-registry-private-link.md) を構成した場合、geo レプリケートされた各リージョンの専用データ エンドポイントが既定で有効になります。 

## <a name="delete-a-replica"></a>レプリカの削除

レジストリのレプリカを構成した後に、それが不要になった場合はいつでも削除できます。 Azure portal またはその他のツール (Azure CLI の[az acr replication delete](/cli/azure/acr/replication#az-acr-replication-delete) コマンドなど) を使用してレプリカを削除します。

Azure portal でレプリカを削除するには、次の手順に従います。

1. Azure Container Registry に移動し、 **[レプリケーション]** を選択します。
1. レプリカの名前を選択し、 **[削除]** を選択します。 レプリカを削除することを確認します。

Azure CLI を使用して、米国東部リージョンで *myregistry* のレプリカを削除するには:

```azurecli
az acr replication delete --name eastus --registry myregistry
```

## <a name="geo-replication-pricing"></a>geo レプリケーションの価格

geo レプリケーションは、Azure Container Registry の [Premium サービス レベル](container-registry-skus.md)の機能です。 レジストリを目的のリージョンにレプリケートすると、リージョンごとに Premium レジストリ料金が発生します。

前の例では、Contoso は、米国東部、カナダ中部、西ヨーロッパにレプリカを追加して、2 つのレジストリを 1 つに統合しました。 Contoso には、1 か月あたり 4 倍の Premium 料金が課金されます。追加の構成や管理は不要です。 各リージョンではイメージをローカルでプルできるようになったため、米国西部からカナダおよび米国東部へのネットワーク エグレス料金が発生することなく、パフォーマンスと信頼性が向上します。

## <a name="troubleshoot-push-operations-with-geo-replicated-registries"></a>geo レプリカ レジストリでプッシュ操作の問題を解決する
 
geo レプリカ レジストリにイメージをプッシュする Docker クライアントでは、イメージ層とそのマニフェストの一部が 1 つのレプリカ リージョンにプッシュされないことがあります。 これは、Azure Traffic Manager ではネットワークで一番近いレプリカ レジストリに要求がルーティングされることが原因で発生することがあります。 レジストリに *近くの* レプリケーション リージョンが 2 つある場合、イメージ層とマニフェストはその 2 つのサイトに分配されることがあり、マニフェストの有効性が検証されると、プッシュ操作に失敗します。 この問題は、レジストリの DNS 名が一部の Linux ホストで解決される方法に起因して発生します。 クライアント側 DNS キャッシュが提供される Windows 上ではこの問題は起こりません。
 
この問題が発生する場合、Linux ホスト上で、`dnsmasq` など、クライアント側 DNS キャッシュを適用することが 1 つの解決策です。 これでレジストリ名の解決に一貫性が与えられます。 Azure で Linux VM を使用してレジストリにプッシュしている場合、「[Azure での Linux 仮想マシンの DNS 名前解決のオプション](../virtual-machines/linux/azure-dns.md)」を参照してください。

イメージをプッシュするとき、DNS 解決を最も近くのレプリカに求めることで最適化するには、プッシュ操作のソースと同じ Azure リージョンか、Azure 外での作業となる場合、最も近くのリージョンで geo レプリカ レジストリを構成します。

### <a name="temporarily-disable-routing-to-replication"></a>レプリケーションへのルーティングを一時的に無効にする

Geo レプリケートされたレジストリでの操作をトラブルシューティングする場合、1 つまたは複数のレプリケーションへの Traffic Manager のルーティングを一時的に無効にすることができます。 Azure CLI バージョン 2.8 以降では、レプリケートされたリージョンを作成または更新する際に、`--region-endpoint-enabled` オプション (プレビュー) を構成できます。 レプリケーションの `--region-endpoint-enabled` オプションを `false` に設定すると、Traffic Manager はそのリージョンに docker push 要求または docker pull 要求をルーティングしなくなります。 既定では、すべてのレプリケーションへのルーティングが有効になっており、ルーティングの有効/無効にかかわらず、すべてのレプリケーション間でデータ同期が実行されます。

既存のレプリケーションへのルーティングを無効にするには、最初に [az acr replication list][az-acr-replication-list] を実行して、レジストリ内のレプリケーションを一覧表示します。 次に、[az acr replication update][az-acr-replication-update] を実行し、特定のレプリケーションに `--region-endpoint-enabled false` を設定します。 たとえば、*myregistry* の *westus* レプリケーションの設定を構成するには、以下のように指定します。

```azurecli
# Show names of existing replications
az acr replication list --registry --output table

# Disable routing to replication
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled false
```

レプリケーションへのルーティングを復元するには、以下のように指定します。

```azurecli
az acr replication update --name westus \
  --registry myregistry --resource-group MyResourceGroup \
  --region-endpoint-enabled true
```

## <a name="next-steps"></a>次のステップ

[Azure Container Registry の geo レプリケーション](container-registry-tutorial-prepare-registry.md)に関する 3 部構成のチュートリアル シリーズを確認します。 これらのチュートリアルでは、geo レプリケートされたレジストリを作成する方法、コンテナーをビルドする方法、1 つの `docker push` コマンドを使用してリージョンの複数の Web App for Containers インスタンスにデプロイする方法を説明します。

> [!div class="nextstepaction"]
> [Azure Container Registry の geo レプリケーション](container-registry-tutorial-prepare-registry.md)

[az-acr-replication-list]: /cli/azure/acr/replication#az-acr-replication-list
[az-acr-replication-update]: /cli/azure/acr/replication#az-acr-replication-update
