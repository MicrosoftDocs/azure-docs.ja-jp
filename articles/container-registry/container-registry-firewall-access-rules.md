---
title: ファイアウォール アクセス規則
description: ファイアウォールの内側から Azure コンテナー レジストリにアクセスする規則を構成します。これには、REST API およびデータ エンドポイントのドメイン名またはサービス固有の IP アドレス範囲へのアクセスを許可します。
ms.topic: article
ms.date: 05/18/2020
ms.openlocfilehash: 548d64632c1d726111770dfb49f705d31f5ca714
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935990"
---
# <a name="configure-rules-to-access-an-azure-container-registry-behind-a-firewall"></a>ファイアウォールの内側から Azure コンテナー レジストリにアクセスする規則を構成する

この記事では、Azure コンテナー レジストリへのアクセスを許可するようにファイアウォールの規則を構成する方法について説明します。 たとえば、ファイアウォールまたはプロキシ サーバーの背後にある Azure IoT Edge デバイスは、コンテナーのイメージをプルするためにコンテナー レジストリにアクセスする必要がある場合があります。 または、オンプレミス ネットワーク内のロックダウンされたサーバーが、イメージをプッシュするためのアクセスを必要とする場合があります。

代わりに、Azure 仮想ネットワーク内でのみコンテナー レジストリへの着信ネットワーク アクセスを構成する場合は、「[Azure コンテナー レジストリ用に Azure Private Link を構成する](container-registry-private-link.md)」を参照してください。

## <a name="about-registry-endpoints"></a>レジストリ エンドポイントについて

イメージまたはその他の成果物を Azure コンテナー レジストリにプルまたはプッシュするには、Docker デーモンなどのクライアントが 2 つの異なるエンドポイントを使用して HTTPS 経由でやり取りする必要があります。 ファイアウォールの内側からレジストリにアクセスするクライアントでは、両方のエンドポイントのアクセス規則を構成する必要があります。 どちらのエンドポイントもポート 443 経由でアクセスされます。

* **レジストリ REST API エンドポイント** - 認証およびレジストリ管理操作は、レジストリのパブリック REST API エンドポイントを介して処理されます。 このエンドポイントは、レジストリのログイン サーバー名です。 例: `myregistry.azurecr.io`

* **ストレージ (データ) エンドポイント** - Azure では、コンテナー イメージやその他の成果物のデータを管理するために、各レジストリに代わって Azure Storage アカウントに [BLOB ストレージが割り当てられます](container-registry-storage.md)。 クライアントは、Azure コンテナー レジストリ内のイメージ レイヤーにアクセスするときに、レジストリによって提供されるストレージ アカウントのエンドポイントを使用して要求を行います。

レジストリが [geo レプリケート](container-registry-geo-replication.md)されている場合、クライアントは、特定のリージョンまたは複数のレプリケートされたリージョンでデータ エンドポイントを操作する必要がある場合があります。

## <a name="allow-access-to-rest-and-data-endpoints"></a>REST エンドポイントとデータ エンドポイントへのアクセスを許可する

* **REST エンドポイント** - レジストリ ログイン サーバーの完全修飾名 (`<registry-name>.azurecr.io`) または関連付けられた IP アドレス範囲へのアクセスを許可します
* **ストレージ (データ) エンドポイント** - ワイルドカード (`*.blob.core.windows.net`) または関連付けられた IP アドレス範囲を使用して、すべての Azure BLOB ストレージ アカウントへのアクセスを許可します。
> [!NOTE]
> Azure Container Registry には[専用データ エンドポイント](#enable-dedicated-data-endpoints)が導入されています。これにより、レジストリ ストレージのクライアント ファイアウォール規則のスコープを厳密に設定できます。 必要に応じて、`<registry-name>.<region>.data.azurecr.io` の形式を使用して、レジストリが配置またはレプリケートされているすべてのリージョンで、データ エンドポイントを有効にします。

## <a name="allow-access-by-ip-address-range"></a>IP アドレス範囲によりアクセスを許可する

組織に特定の IP アドレスまたはアドレス範囲のみへのアクセスを許可するポリシーがある場合は、[Azure の IP 範囲とサービス タグ – パブリック クラウド](https://www.microsoft.com/download/details.aspx?id=56519)をダウンロードしてください。

アクセスを許可する必要がある ACR REST エンドポイントの IP 範囲を検索するには、JSON ファイル内で **AzureContainerRegistry** を検索します。

> [!IMPORTANT]
> Azure サービスの IP アドレス範囲は変更でき、更新は毎週公開されます。 JSON ファイルを定期的にダウンロードし、アクセス規則で必要な更新を行います。 実際のシナリオで、Azure 仮想ネットワークでネットワーク セキュリティ グループの規則を構成する必要がある場合、または Azure Firewall を使用する場合、代わりに **AzureContainerRegistry** の [サービス タグ](#allow-access-by-service-tag)を使用します。
>

### <a name="rest-ip-addresses-for-all-regions"></a>すべてのリージョンの REST IP アドレス

```json
{
  "name": "AzureContainerRegistry",
  "id": "AzureContainerRegistry",
  "properties": {
    "changeNumber": 10,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.66.140.72/29",
    [...]
```

### <a name="rest-ip-addresses-for-a-specific-region"></a>特定のリージョンの REST IP アドレス

特定のリージョン (**AzureContainerRegistry.AustraliaEast** など) を検索します。

```json
{
  "name": "AzureContainerRegistry.AustraliaEast",
  "id": "AzureContainerRegistry.AustraliaEast",
  "properties": {
    "changeNumber": 1,
    "region": "australiaeast",
    "platform": "Azure",
    "systemService": "AzureContainerRegistry",
    "addressPrefixes": [
      "13.70.72.136/29",
    [...]
```

### <a name="storage-ip-addresses-for-all-regions"></a>すべてのリージョンのストレージ IP アドレス

```json
{
  "name": "Storage",
  "id": "Storage",
  "properties": {
    "changeNumber": 19,
    "region": "",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "13.65.107.32/28",
    [...]
```

### <a name="storage-ip-addresses-for-specific-regions"></a>特定のリージョンのストレージ IP アドレス

特定のリージョン (**Storage.AustraliaCentral** など) を検索します。

```json
{
  "name": "Storage.AustraliaCentral",
  "id": "Storage.AustraliaCentral",
  "properties": {
    "changeNumber": 1,
    "region": "australiacentral",
    "platform": "Azure",
    "systemService": "AzureStorage",
    "addressPrefixes": [
      "52.239.216.0/23"
    [...]
```

## <a name="allow-access-by-service-tag"></a>サービス タグによるアクセスを許可する

Azure 仮想ネットワークでは、ネットワーク セキュリティ規則を使用して、仮想マシンなどのリソースからコンテナー レジストリへのトラフィックをフィルター処理します。 Azure ネットワーク規則の作成を簡略化するには、**AzureContainerRegistry** の [サービス タグ](../virtual-network/network-security-groups-overview.md#service-tags)を使用します。 サービス タグは、グローバルまたは Azure リージョンごとに Azure サービスにアクセスするための IP アドレス プレフィックスのグループを表します。 アドレスが変更されると、タグは自動的に更新されます。 

たとえば、Azure コンテナー レジストリへのトラフィックを許可するために、宛先が **AzureContainerRegistry** の送信ネットワーク セキュリティ グループの規則を作成します。 特定のリージョンでのみサービス タグへのアクセスを許可するには、次の形式でリージョンを指定します。**AzureContainerRegistry**.[*リージョン名*]

## <a name="enable-dedicated-data-endpoints"></a>専用データ エンドポイントを有効にする 

> [!WARNING]
> 既存の `*.blob.core.windows.net` エンドポイントへのクライアント ファイアウォール アクセスを以前に構成した場合、専用データ エンドポイントへの切り替えによってクライアント接続が影響を受け、プル エラーが発生します。 クライアントが一貫してアクセスできるようにするには、新しいデータ エンドポイント規則をクライアントのファイアウォール規則に追加します。 完了したら、Azure CLI またはその他のツールを使用して、レジストリの専用データ エンドポイントを有効にします。

専用データ エンドポイントは、**Premium** コンテナー レジストリ サービス レベルのオプション機能です。 レジストリ サービスのレベルと制限については、「[Azure Container Registry のサービス レベル](container-registry-skus.md)」を参照してください。 

Azure portal または Azure CLI を使用して、専用のデータ エンドポイントを有効にすることができます。 データ エンドポイントは、リージョンのパターン `<registry-name>.<region>.data.azurecr.io` に従います。 Geo レプリケートされたレジストリでは、データ エンドポイントを有効にすると、すべてのレプリカ リージョンでエンドポイントが有効になります。

### <a name="portal"></a>ポータル

ポータルを使用してデータ エンドポイントを有効にするには:

1. お使いのコンテナー レジストリに移動します。
1. **[ネットワーク]**  >  **[パブリック アクセス]** を選択します。
1. **[Enable dedicated data endpoint]\(専用データ エンドポイントを有効にする\)** チェックボックスを選択し ます。
1. **[保存]** を選択します。

データ エンドポイントがポータルに表示されます。

:::image type="content" source="media/container-registry-firewall-access-rules/dedicated-data-endpoints-portal.png" alt-text="ポータルの専用データ エンドポイント":::

### <a name="azure-cli"></a>Azure CLI

Azure CLI を使用してデータ エンドポイントを有効にするには、Azure CLI バージョン 2.4.0 以降を使用します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

次の [az acr update][az-acr-update] コマンドを使用すると、レジストリ *myregistry* の専用データ エンドポイントが有効になります。 

```azurecli
az acr update --name myregistry --data-endpoint-enabled
```

データ エンドポイントを表示するには、[az acr show-endpoints][az-acr-show-endpoints] コマンドを使用します。

```azurecli
az acr show-endpoints --name myregistry
```

デモンストレーション目的での出力には、2 つのリージョン エンドポイントが示されています

```
{
    "loginServer": "myregistry.azurecr.io",
    "dataEndpoints": [
        {
            "region": "eastus",
            "endpoint": "myregistry.eastus.data.azurecr.io",
        },
        {
            "region": "westus",
            "endpoint": "myregistry.westus.data.azurecr.io",
        }
    ]
}
```

レジストリの専用データ エンドポイントを設定した後、データ エンドポイントのクライアント ファイアウォール アクセス規則を有効にすることができます。 必要なすべてのレジストリ リージョンについて、データ エンドポイント アクセス規則を有効にします。

## <a name="configure-client-firewall-rules-for-mcr"></a>MCR 用のクライアント ファイアウォール規則を構成する

ファイアウォールの内側から Microsoft Container Registry (MCR) にアクセスする必要がある場合は、[MCR クライアント ファイアウォール規則](https://github.com/microsoft/containerregistry/blob/master/client-firewall-rules.md)を構成するためのガイダンスを参照してください。 MCR は、Microsoft が公開しているすべての docker イメージ (Windows Server イメージなど) のプライマリ レジストリです。

## <a name="next-steps"></a>次のステップ

* [Azure のネットワーク セキュリティのベスト プラクティス](../security/fundamentals/network-best-practices.md)について学習する

* Azure 仮想ネットワークの[セキュリティ グループ](../virtual-network/network-security-groups-overview.md)について学習する

* コンテナー レジストリの [Private Link](container-registry-private-link.md) の設定について学習する

* Azure Container Registry の[専用データ エンドポイント](https://azure.microsoft.com/blog/azure-container-registry-mitigating-data-exfiltration-with-dedicated-data-endpoints/)について学習する



<!-- IMAGES -->

<!-- LINKS - External -->

<!-- LINKS - Internal -->

[az-acr-update]: /cli/azure/acr#az-acr-update
[az-acr-show-endpoints]: /cli/azure/acr#az-acr-show-endpoints