---
title: Azure CLI を使用した Linux VM イメージの選択
description: Azure CLI を使用して Marketplace VM イメージの発行元、プラン、SKU、バージョンを決定する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.date: 01/25/2019
ms.author: cynthn
ms.collection: linux
ms.openlocfilehash: efa0b91c9c0e43104f36017c3b1a1f3167190d63
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102562811"
---
# <a name="find-linux-vm-images-in-the-azure-marketplace-with-the-azure-cli"></a>Azure CLI を使用して Azure Marketplace の Linux VM イメージを見つける

このトピックでは、Azure CLI を使用して Azure Marketplace で VM イメージを見つける方法を説明します。 これらの情報は、CLI、Resource Manager テンプレート、またはその他のツールを使用して、VM をプログラムによって作成する際、Marketplace イメージを指定するために使用できます。

また、[Azure Marketplace](https://azuremarketplace.microsoft.com/) のストアフロント、[Azure portal](https://portal.azure.com)、または [Azure PowerShell](../windows/cli-ps-findimage.md) を使用して、使用できるイメージとオファーを参照することもできます。 

Azure アカウントにログイン (`az login`) していることを確認します。

[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="deploy-from-a-vhd-using-purchase-plan-parameters"></a>購入プラン パラメーターを使用して VHD からデプロイする

有料の Azure Marketplace イメージを使用して作成された既存の VHD がある場合は、その VHD から新しい VM を作成するときに、購入プランの情報を指定することが必要になる場合があります。 

元の VM、または同じ Marketplace イメージを使用して作成した他の VM がある場合は、[az vm get-instance-view](/cli/azure/vm#az_vm_get_instance_view) を使用して、その VM からプラン名、発行元、および製品情報を取得できます。 この例では、*myResourceGroup* リソース グループの *myVM* という名前の VM を取得し、購入プラン情報を表示します。

```azurepowershell-interactive
az vm get-instance-view -g myResourceGroup -n myVM --query plan
```

元の VM が削除される前にプラン情報を取得しなかった場合は、[サポート リクエスト](https://ms.portal.azure.com/#create/Microsoft.Support)を提出できます。 VM 名、サブスクリプション ID、および削除操作のタイム スタンプが必要になります。

プラン情報を取得したら、`--attach-os-disk` パラメーターを使用して VHD を指定することで、新しい VM を作成できます。

```azurecli-interactive
az vm create \
   --resource-group myResourceGroup \
  --name myNewVM \
  --nics myNic \
  --size Standard_DS1_v2 --os-type Linux \
  --attach-os-disk myVHD \
  --plan-name planName \
  --plan-publisher planPublisher \
  --plan-product planProduct 
```

## <a name="deploy-a-new-vm-using-purchase-plan-parameters"></a>購入プラン パラメーターを使用して新しい VM をデプロイする

イメージに関する情報が既にある場合は、`az vm create` コマンドを使用してデプロイできます。 この例では、次のように RabbitMQ Certified by Bitnami イメージを使用して VM をデプロイします。

```azurecli
az group create --name myResourceGroupVM --location westus

az vm create --resource-group myResourceGroupVM --name myVM --image bitnami:rabbitmq:rabbitmq:latest --plan-name rabbitmq --plan-product rabbitmq --plan-publisher bitnami
```

イメージの使用条件への同意に関するメッセージが表示された場合は、この記事で後述する「[使用条件への同意](#accept-the-terms)」セクションを参照してください。

## <a name="list-popular-images"></a>よく使われるイメージを一覧表示する

`--all` オプションを指定せずに [az vm image list](/cli/azure/vm/image) コマンドを実行して、Azure Marketplace でよく使われる VM イメージの一覧を確認します。 たとえば、次のコマンドを実行して、よく使用されるイメージのキャッシュされた一覧を表形式で表示します。

```azurecli
az vm image list --output table
```

この出力にはイメージ URN (*Urn* 列の値) が含まれます。 よく使われる Marketplace イメージのいずれかを使用して VM を作成する場合は、代わりに、*UrnAlias* (*UbuntuLTS* などの短縮形) を指定することもできます。

```output
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
CentOS         OpenLogic               7.5                 OpenLogic:CentOS:7.5:latest                                     CentOS               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
openSUSE-Leap  SUSE                    42.3                SUSE:openSUSE-Leap:42.3:latest                                  openSUSE-Leap        latest
RHEL           RedHat                  7-RAW               RedHat:RHEL:7-RAW:latest                                        RHEL                 latest
SLES           SUSE                    12-SP2              SUSE:SLES:12-SP2:latest                                         SLES                 latest
UbuntuServer   Canonical               16.04-LTS           Canonical:UbuntuServer:16.04-LTS:latest                         UbuntuLTS            latest
...
```

## <a name="find-specific-images"></a>特定のイメージを検索する

Marketplace で特定の VM イメージを検索するには、`az vm image list` コマンドと `--all` オプションを使用します。 このバージョンのコマンドは完了するまでに少し時間がかかります。また、大量の出力が返されることがあるため、通常は `--publisher` または他のパラメーターを使用してリストをフィルター処理します。 

たとえば、次のコマンドではすべての Debian プランが表示されます (`--all` スイッチがなければ、一般的なイメージのローカル キャッシュを検索するだけであることを忘れないでください)。

```azurecli
az vm image list --offer Debian --all --output table 

```

出力の一部を次に示します。 

```output
Offer              Publisher    Sku                  Urn                                                    Version
-----------------  -----------  -------------------  -----------------------------------------------------  --------------
Debian             credativ     7                    credativ:Debian:7:7.0.201602010                        7.0.201602010
Debian             credativ     7                    credativ:Debian:7:7.0.201603020                        7.0.201603020
Debian             credativ     7                    credativ:Debian:7:7.0.201604050                        7.0.201604050
Debian             credativ     7                    credativ:Debian:7:7.0.201604200                        7.0.201604200
Debian             credativ     7                    credativ:Debian:7:7.0.201606280                        7.0.201606280
Debian             credativ     7                    credativ:Debian:7:7.0.201609120                        7.0.201609120
Debian             credativ     7                    credativ:Debian:7:7.0.201611020                        7.0.201611020
Debian             credativ     7                    credativ:Debian:7:7.0.201701180                        7.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201602010                        8.0.201602010
Debian             credativ     8                    credativ:Debian:8:8.0.201603020                        8.0.201603020
Debian             credativ     8                    credativ:Debian:8:8.0.201604050                        8.0.201604050
Debian             credativ     8                    credativ:Debian:8:8.0.201604200                        8.0.201604200
Debian             credativ     8                    credativ:Debian:8:8.0.201606280                        8.0.201606280
Debian             credativ     8                    credativ:Debian:8:8.0.201609120                        8.0.201609120
Debian             credativ     8                    credativ:Debian:8:8.0.201611020                        8.0.201611020
Debian             credativ     8                    credativ:Debian:8:8.0.201701180                        8.0.201701180
Debian             credativ     8                    credativ:Debian:8:8.0.201703150                        8.0.201703150
Debian             credativ     8                    credativ:Debian:8:8.0.201704110                        8.0.201704110
Debian             credativ     8                    credativ:Debian:8:8.0.201704180                        8.0.201704180
Debian             credativ     8                    credativ:Debian:8:8.0.201706190                        8.0.201706190
Debian             credativ     8                    credativ:Debian:8:8.0.201706210                        8.0.201706210
Debian             credativ     8                    credativ:Debian:8:8.0.201708040                        8.0.201708040
Debian             credativ     8                    credativ:Debian:8:8.0.201710090                        8.0.201710090
Debian             credativ     8                    credativ:Debian:8:8.0.201712040                        8.0.201712040
Debian             credativ     8                    credativ:Debian:8:8.0.201801170                        8.0.201801170
Debian             credativ     8                    credativ:Debian:8:8.0.201803130                        8.0.201803130
Debian             credativ     8                    credativ:Debian:8:8.0.201803260                        8.0.201803260
Debian             credativ     8                    credativ:Debian:8:8.0.201804020                        8.0.201804020
Debian             credativ     8                    credativ:Debian:8:8.0.201804150                        8.0.201804150
Debian             credativ     8                    credativ:Debian:8:8.0.201805160                        8.0.201805160
Debian             credativ     8                    credativ:Debian:8:8.0.201807160                        8.0.201807160
Debian             credativ     8                    credativ:Debian:8:8.0.201901221                        8.0.201901221
...
```

`--location`、`--publisher`、および `--sku` オプションを指定して、同様のフィルターを適用します。 すべての Debian イメージを見つけるために `--offer Deb` を検索するなど、フィルターで部分一致を実行することができます。

`--location` オプションを使って特定の場所を指定しない場合は、既定の場所の値が返されます (`az configure --defaults location=<location>` を実行すると、別の既定の場所を設定できます)。

たとえば、次のコマンドでは、西ヨーロッパにあるすべての Debian 8 SKU を一覧表示します。

```azurecli
az vm image list --location westeurope --offer Deb --publisher credativ --sku 8 --all --output table
```

出力の一部を次に示します。

```output
Offer    Publisher    Sku                Urn                                              Version
-------  -----------  -----------------  -----------------------------------------------  -------------
Debian   credativ     8                  credativ:Debian:8:8.0.201602010                  8.0.201602010
Debian   credativ     8                  credativ:Debian:8:8.0.201603020                  8.0.201603020
Debian   credativ     8                  credativ:Debian:8:8.0.201604050                  8.0.201604050
Debian   credativ     8                  credativ:Debian:8:8.0.201604200                  8.0.201604200
Debian   credativ     8                  credativ:Debian:8:8.0.201606280                  8.0.201606280
Debian   credativ     8                  credativ:Debian:8:8.0.201609120                  8.0.201609120
Debian   credativ     8                  credativ:Debian:8:8.0.201611020                  8.0.201611020
Debian   credativ     8                  credativ:Debian:8:8.0.201701180                  8.0.201701180
Debian   credativ     8                  credativ:Debian:8:8.0.201703150                  8.0.201703150
Debian   credativ     8                  credativ:Debian:8:8.0.201704110                  8.0.201704110
Debian   credativ     8                  credativ:Debian:8:8.0.201704180                  8.0.201704180
Debian   credativ     8                  credativ:Debian:8:8.0.201706190                  8.0.201706190
Debian   credativ     8                  credativ:Debian:8:8.0.201706210                  8.0.201706210
Debian   credativ     8                  credativ:Debian:8:8.0.201708040                  8.0.201708040
Debian   credativ     8                  credativ:Debian:8:8.0.201710090                  8.0.201710090
Debian   credativ     8                  credativ:Debian:8:8.0.201712040                  8.0.201712040
Debian   credativ     8                  credativ:Debian:8:8.0.201801170                  8.0.201801170
Debian   credativ     8                  credativ:Debian:8:8.0.201803130                  8.0.201803130
Debian   credativ     8                  credativ:Debian:8:8.0.201803260                  8.0.201803260
Debian   credativ     8                  credativ:Debian:8:8.0.201804020                  8.0.201804020
Debian   credativ     8                  credativ:Debian:8:8.0.201804150                  8.0.201804150
Debian   credativ     8                  credativ:Debian:8:8.0.201805160                  8.0.201805160
Debian   credativ     8                  credativ:Debian:8:8.0.201807160                  8.0.201807160
Debian   credativ     8                  credativ:Debian:8:8.0.201901221                  8.0.201901221
...
```

## <a name="navigate-the-images"></a>イメージの移動
 
任意の場所にあるイメージを検索する別の方法として、[az vm image list-publishers](/cli/azure/vm/image)、[az vm image list-offers](/cli/azure/vm/image)、および[az vm image list-skus](/cli/azure/vm/image) コマンドを連続で実行します。 これらのコマンドを使用する際は、以下の値を決定します。

1. イメージの発行元を一覧表示する。
2. 指定された発行元について、そのプランを一覧表示する。
3. 指定されたプランについて、その SKU を一覧表示する。

その後、選択した SKU について、デプロイするバージョンを選択できます。

たとえば、次のコマンドでは、米国西部の場所にあるイメージの発行元を一覧表示します。

```azurecli
az vm image list-publishers --location westus --output table
```

出力の一部を次に示します。

```output
Location    Name
----------  ----------------------------------------------------
westus      128technology
westus      1e
westus      4psa
westus      5nine-software-inc
westus      7isolutions
westus      a10networks
westus      abiquo
westus      accellion
westus      accessdata-group
westus      accops
westus      Acronis
westus      Acronis.Backup
westus      actian-corp
westus      actian_matrix
westus      actifio
westus      activeeon
westus      advantech-webaccess
westus      aerospike
westus      affinio
westus      aiscaler-cache-control-ddos-and-url-rewriting-
westus      akamai-technologies
westus      akumina
...
```

特定の発行元からのプランを検索するには、この情報を使用します。 たとえば、米国西部の *Canonical* 発行元の場合、`azure vm image list-offers` を実行することでプランを検索します。 次の例のように、場所と発行元を渡します。

```azurecli
az vm image list-offers --location westus --publisher Canonical --output table
```

出力:

```output
Location    Name
----------  -------------------------
westus      Ubuntu15.04Snappy
westus      Ubuntu15.04SnappyDocker
westus      UbunturollingSnappy
westus      UbuntuServer
westus      Ubuntu_Core
```
米国西部のリージョンで、Canonical が Azure で *UbuntuServer* プランを発行していることが分かります。 どんな SKU でしょうか。 その値を入手するには、`azure vm image list-skus` を実行して、見つけた場所、発行元、およびプランを設定します。

```azurecli
az vm image list-skus --location westus --publisher Canonical --offer UbuntuServer --output table
```

出力:

```output
Location    Name
----------  -----------------
westus      12.04.3-LTS
westus      12.04.4-LTS
westus      12.04.5-LTS
westus      14.04.0-LTS
westus      14.04.1-LTS
westus      14.04.2-LTS
westus      14.04.3-LTS
westus      14.04.4-LTS
westus      14.04.5-DAILY-LTS
westus      14.04.5-LTS
westus      16.04-DAILY-LTS
westus      16.04-LTS
westus      16.04.0-LTS
westus      18.04-DAILY-LTS
westus      18.04-LTS
westus      18.10
westus      18.10-DAILY
westus      19.04-DAILY
```

最後に、`az vm image list` コマンドを使用して、*18.04-LTS* など、目的とする特定のバージョンの SKU を検索します。

```azurecli
az vm image list --location westus --publisher Canonical --offer UbuntuServer --sku 18.04-LTS --all --output table
```

出力の一部を次に示します。

```output
Offer         Publisher    Sku        Urn                                               Version
------------  -----------  ---------  ------------------------------------------------  ---------------
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201804262  18.04.201804262
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805170  18.04.201805170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201805220  18.04.201805220
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806130  18.04.201806130
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201806170  18.04.201806170
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201807240  18.04.201807240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808060  18.04.201808060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808080  18.04.201808080
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808140  18.04.201808140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201808310  18.04.201808310
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201809110  18.04.201809110
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810030  18.04.201810030
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810240  18.04.201810240
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201810290  18.04.201810290
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201811010  18.04.201811010
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812031  18.04.201812031
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812040  18.04.201812040
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201812060  18.04.201812060
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901140  18.04.201901140
UbuntuServer  Canonical    18.04-LTS  Canonical:UbuntuServer:18.04-LTS:18.04.201901220  18.04.201901220
...
```

これで、URN 値をメモして、使用するイメージを正確に選べるようになりました。 [az vm create](/cli/azure/vm) コマンドで VM を作成するときに、この値を `--image` パラメーターを使用して渡します。 必要に応じて URN のバージョン番号を "latest" に置き換えられることに注意してください。 このバージョンは常に、イメージの最新バージョンです。 

Resource Manager テンプレートを使って VM をデプロイする場合は、`imageReference` プロパティでイメージ パラメーターを個別に設定します。 [テンプレート リファレンス](/azure/templates/microsoft.compute/virtualmachines)をご覧ください。

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>プランのプロパティの表示

イメージの購入プラン情報を表示するには、[az vm image show](/cli/azure/image) コマンドを実行します。 出力内の `plan` プロパティが `null` ではない場合、イメージには、プログラムによるデプロイの前に同意しなければならない使用条件があります。

たとえば、Canonical Ubuntu Server 18.04 LTS イメージに追加条項がないのは、`plan` 情報が `null` であるためです。

```azurecli
az vm image show --location westus --urn Canonical:UbuntuServer:18.04-LTS:latest
```

出力:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/Canonical/ArtifactTypes/VMImage/Offers/UbuntuServer/Skus/18.04-LTS/Versions/18.04.201901220",
  "location": "westus",
  "name": "18.04.201901220",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

RabbitMQ Certified by Bitnami イメージに対して同様のコマンドを実行すると、次の `plan` プロパティが表示されます: `name`、 `product`、および `publisher` (イメージによっては、`promotion code` プロパティもあります)。このイメージをデプロイするには、次のセクションを参照して使用条件に同意し、プログラムによるデプロイを有効にします。

```azurecli
az vm image show --location westus --urn bitnami:rabbitmq:rabbitmq:latest
```
出力:

```output
{
  "dataDiskImages": [],
  "id": "/Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/bitnami/ArtifactTypes/VMImage/Offers/rabbitmq/Skus/rabbitmq/Versions/3.7.1901151016",
  "location": "westus",
  "name": "3.7.1901151016",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": {
    "name": "rabbitmq",
    "product": "rabbitmq",
    "publisher": "bitnami"
  },
  "tags": null
}
```

## <a name="accept-the-terms"></a>使用条件への同意

ライセンス条項を表示し、それらに同意するには、[az vm image accept-terms](/cli/azure/vm/image?) コマンドを使用します。 使用条件に同意すると、サブスクリプション内で、プログラムによるデプロイが有効になります。 使用条件に同意する必要があるのは、イメージのサブスクリプションごとに 1 回だけです。 次に例を示します。

```azurecli
az vm image accept-terms --urn bitnami:rabbitmq:rabbitmq:latest
``` 

出力では、ライセンス条項への `licenseTextLink` が示され、`accepted` の値が `true` であることが示されます。

```output
{
  "accepted": true,
  "additionalProperties": {},
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/Microsoft.MarketplaceOrdering/offertypes/bitnami/offers/rabbitmq/plans/rabbitmq",
  "licenseTextLink": "https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_BITNAMI%253a24RABBITMQ%253a24RABBITMQ%253a24IGRT7HHPIFOBV3IQYJHEN2O2FGUVXXZ3WUYIMEIVF3KCUNJ7GTVXNNM23I567GBMNDWRFOY4WXJPN5PUYXNKB2QLAKCHP4IE5GO3B2I.txt",
  "name": "rabbitmq",
  "plan": "rabbitmq",
  "privacyPolicyLink": "https://bitnami.com/privacy",
  "product": "rabbitmq",
  "publisher": "bitnami",
  "retrieveDatetime": "2019-01-25T20:37:49.937096Z",
  "signature": "XXXXXXLAZIK7ZL2YRV5JYQXONPV76NQJW3FKMKDZYCRGXZYVDGX6BVY45JO3BXVMNA2COBOEYG2NO76ONORU7ITTRHGZDYNJNXXXXXX",
  "type": "Microsoft.MarketplaceOrdering/offertypes"
}
```

## <a name="next-steps"></a>次のステップ
イメージ情報を使って仮想マシンをすぐに作成するには、「[Azure CLI を使用した Linux VM の作成と管理](tutorial-manage-vm.md)」をご覧ください。
