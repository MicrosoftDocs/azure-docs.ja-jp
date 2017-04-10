---
title: "Azure CLI を使用した Linux VM イメージの選択 |Microsoft Docs"
description: "リソース マネージャー デプロイ モデルで Linux 仮想マシンを作成するときに、イメージの発行元、プラン、および SKU を決定する方法について説明します。"
services: virtual-machines-linux
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 7a858e38-4f17-4e8e-a28a-c7f801101721
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 02/15/2017
ms.author: rasquill
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: fa842efd99718be7fa9eaf8aac8030c32cbceeec
ms.openlocfilehash: e9be064425ae6b9048098333cc664310e7128314
ms.lasthandoff: 03/01/2017


---
# <a name="how-to-find-linux-vm-images-with-the-azure-cli"></a>Azure CLI を使用して Linux VM イメージを探す方法
このトピックでは、デプロイする可能性のある場所ごとに、発行元、プラン、SKU、およびバージョンを検索する方法について説明します。 


## <a name="use-azure-cli-20"></a>Azure CLI 2.0 の使用

[Azure CLI 2.0 をインストール](https://docs.microsoft.com/cli/azure/install-az-cli2)すると、`az vm image list` コマンドを使用して、一般的な VM イメージのキャッシュ リストを確認できます。 たとえば、コマンド `az vm image list -o table` の例は次のようになります。

```
You are viewing an offline list of images, use --all to retrieve an up-to-date list
Offer          Publisher               Sku                 Urn                                                             UrnAlias             Version
-------------  ----------------------  ------------------  --------------------------------------------------------------  -------------------  ---------
WindowsServer  MicrosoftWindowsServer  2012-R2-Datacenter  MicrosoftWindowsServer:WindowsServer:2012-R2-Datacenter:latest  Win2012R2Datacenter  latest
WindowsServer  MicrosoftWindowsServer  2008-R2-SP1         MicrosoftWindowsServer:WindowsServer:2008-R2-SP1:latest         Win2008R2SP1         latest
WindowsServer  MicrosoftWindowsServer  2012-Datacenter     MicrosoftWindowsServer:WindowsServer:2012-Datacenter:latest     Win2012Datacenter    latest
UbuntuServer   Canonical               14.04.4-LTS         Canonical:UbuntuServer:14.04.4-LTS:latest                       UbuntuLTS            latest
CentOS         OpenLogic               7.2                 OpenLogic:CentOS:7.2:latest                                     CentOS               latest
openSUSE       SUSE                    13.2                SUSE:openSUSE:13.2:latest                                       openSUSE             latest
RHEL           RedHat                  7.2                 RedHat:RHEL:7.2:latest                                          RHEL                 latest
SLES           SUSE                    12-SP1              SUSE:SLES:12-SP1:latest                                         SLES                 latest
Debian         credativ                8                   credativ:Debian:8:latest                                        Debian               latest
CoreOS         CoreOS                  Stable              CoreOS:CoreOS:Stable:latest                                     CoreOS               latest
```

### <a name="finding-all-current-images"></a>現在のイメージをすべて検索する

現在のすべてのイメージの一覧を取得するには、`az vm image list` コマンドを `--all` オプションと組み合わせて使用します。 Azure CLI 1.0 のコマンドとは異なり、`az vm image list --all` コマンドは**westus** にあるすべてのイメージを (特定の `--location` 引数を指定しない限り) 既定で返すため、`--all` コマンドを完了するには少し時間がかかります。 対話形式で調査する場合は、`az vm image list --all > allImages.json` を使用すると、Azure で現在利用可能なすべてのイメージの一覧が取得され、ローカルで使用するためのファイルとして保存されます。 

すでに念頭に置いているものがある場合は、複数のオプションから&1; つを指定して、特定の場所、プラン、発行元、SKU に絞り込むことができます。 場所を指定しない場合は、**westus** の値が返されます。

### <a name="find-specific-images"></a>特定のイメージを検索する

特定の情報を検索するには、`az vm image list` をフィルターと共に使用します。 たとえば、次の例では **Debian** で利用可能な**プラン**が表示されます (`--all` スイッチがなければ、一般的なイメージのローカル キャッシュを検索するだけであることを忘れないでください)。

```azurecli
az vm image list --offer Debian -o table --all
```

結果は次のようになります。 
```
Offer   Publisher   Sku   Urn                              Version
------  ---------   ---   -------------------------------  -------------
Debian  credativ    8     credativ:Debian:8:8.0.201701180  8.0.201701180

<list shortened for the example>
```

**--publisher** と **--sku** オプションで同様のフィルター処理が可能です。 また、**--offer Deb** で Debian イメージを検索したり、**--publisher Micr** で Microsoft が公開しているイメージを検索したりするなど、フィルターでの部分一致検索が可能です。

デプロイ先がわかる場合は、`az vm image list-skus`、`az vm image list-offers`、`az vm image list-publishers` コマンドで一般的なイメージの検索結果を使用でき、入手したいイメージや、デプロイ可能な場所を正確に検索できます。 たとえば、前述の例で `credativ` に Debian のプランがあることがわかっている場合は、`--location` などのオプションを使って入手したいものを正確に検索できます。 次の例では、**westeurope** にある Debian 8 イメージを検索します。

```azurecli 
az vm image show -l westeurope -f debian -p credativ --skus 8 --version 8.0.201701180
```

出力は次のようになります。

```json
{
  "dataDiskImages": [],
  "id": "/Subscriptions/<guid>/Providers/Microsoft.Compute/Locations/westeurope/Publishers/credativ/ArtifactTypes/VMImage/Offers/debian/Skus/8/Versions/8.0.201701180",
  "location": "westeurope",
  "name": "8.0.201701180",
  "osDiskImage": {
    "operatingSystem": "Linux"
  },
  "plan": null,
  "tags": null
}
```

## <a name="use-azure-cli-10"></a>Azure CLI 1.0 の使用 

> [!NOTE]
> この記事では、Azure Resource Manager デプロイメント モデルをサポートする Azure CLI 1.0 または Azure PowerShell を使用して、仮想マシン イメージの入手場所に移動し、イメージを選択する方法について説明します。 前提条件として、リソース マネージャー モードに変更してください。 Azure CLI を使用している場合、次のように入力してこのモードに入ります`azure config mode arm`。 
> 

イメージを検索する最も早い方法は、`azure vm image list` コマンドを呼び出して、場所、発行元の名称 (大文字と小文字は区別されません)、プラン (わかっている場合) を渡すことです。 たとえば、次のリストは、"Canonical" が "UbuntuServer" プランの発行元であることを知っている場合の短い例にすぎません。多数のリストは非常に長くなります。

```azurecli
azure vm image list westus canonical ubuntuserver
info:    Executing command vm image list
warn:    The parameter --sku if specified will be ignored
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         Sku                OS     Version          Location  Urn
data:    ---------  ------------  -----------------  -----  ---------------  --------  --------------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS        Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607220  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607220
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607230  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607230
data:    canonical  ubuntuserver  16.10-DAILY        Linux  16.10.201607240  westus    canonical:ubuntuserver:16.10-DAILY:16.10.201607240
```

**Urn** 列は、`azure vm quick-create` に渡されるフォームです。

ただし、多くの場合、何が使用可能かをまだ知りません。 この場合、`azure vm image list-publishers` コマンドを使用してデータ センターの場所をプロンプトで指定することで、イメージの場所に移動できます。 たとえば、以下は米国西部の場所にあるすべてのイメージ発行元をリストしたものです (場所の引数は、標準の場所名を小文字化し、スペースを削除して渡します)

```azurecli
azure vm image list-publishers
info:    Executing command vm image list-publishers
Location: westus
+ Getting virtual machine and/or extension image publishers (Location: "westus")
data:    Publisher                                       Location
data:    ----------------------------------------------  --------
data:    a10networks                                     westus  
data:    aiscaler-cache-control-ddos-and-url-rewriting-  westus  
data:    alertlogic                                      westus  
data:    AlertLogic.Extension                            westus  
```

このリストはかなり長いため、前述の例のリストは抜粋です。 たとえば、「Canonical 」が、実際に米国西部の場所にあるイメージ発行元であることに気付いたとします。 これで、次の例のように、 `azure vm image list-offers` を呼び出して、プロンプトが出されたときに場所と発行元を渡すことで、プランを検索できます。

```azurecli
azure vm image list-offers
info:    Executing command vm image list-offers
Location: westus
Publisher: canonical
+ Getting virtual machine image offers (Publisher: "canonical" Location:"westus")
data:    Publisher  Offer                      Location
data:    ---------  -------------------------  --------
data:    canonical  Ubuntu15.04Snappy          westus
data:    canonical  Ubuntu15.04SnappyDocker    westus
data:    canonical  UbunturollingSnappy        westus
data:    canonical  UbuntuServer               westus
data:    canonical  Ubuntu_Snappy_Core         westus
data:    canonical  Ubuntu_Snappy_Core_Docker  westus
info:    vm image list-offers command OK
```

これで、米国西部のリージョンで、Canonical が Azure で **UbuntuServer** プランを発行していることが分かりました。 どんな SKU でしょうか。 その値を入手するには、`azure vm image list-skus` を呼び出し、見つけたい場所、発行元、プランをプロンプトに返します。

```azurecli
azure vm image list-skus
info:    Executing command vm image list-skus
Location: westus
Publisher: canonical
Offer: ubuntuserver
+ Getting virtual machine image skus (Publisher:"canonical" Offer:"ubuntuserver" Location:"westus")
data:    Publisher  Offer         sku                Location
data:    ---------  ------------  -----------------  --------
data:    canonical  ubuntuserver  12.04.2-LTS        westus
data:    canonical  ubuntuserver  12.04.3-LTS        westus
data:    canonical  ubuntuserver  12.04.4-LTS        westus
data:    canonical  ubuntuserver  12.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  12.04.5-LTS        westus
data:    canonical  ubuntuserver  12.10              westus
data:    canonical  ubuntuserver  14.04-beta         westus
data:    canonical  ubuntuserver  14.04.0-LTS        westus
data:    canonical  ubuntuserver  14.04.1-LTS        westus
data:    canonical  ubuntuserver  14.04.2-LTS        westus
data:    canonical  ubuntuserver  14.04.3-LTS        westus
data:    canonical  ubuntuserver  14.04.4-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.4-LTS        westus
data:    canonical  ubuntuserver  14.04.5-DAILY-LTS  westus
data:    canonical  ubuntuserver  14.04.5-LTS        westus
data:    canonical  ubuntuserver  14.10              westus
data:    canonical  ubuntuserver  14.10-beta         westus
data:    canonical  ubuntuserver  14.10-DAILY        westus
data:    canonical  ubuntuserver  15.04              westus
data:    canonical  ubuntuserver  15.04-beta         westus
data:    canonical  ubuntuserver  15.04-DAILY        westus
data:    canonical  ubuntuserver  15.10              westus
data:    canonical  ubuntuserver  15.10-alpha        westus
data:    canonical  ubuntuserver  15.10-beta         westus
data:    canonical  ubuntuserver  15.10-DAILY        westus
data:    canonical  ubuntuserver  16.04-alpha        westus
data:    canonical  ubuntuserver  16.04-beta         westus
data:    canonical  ubuntuserver  16.04.0-DAILY-LTS  westus
data:    canonical  ubuntuserver  16.04.0-LTS        westus
data:    canonical  ubuntuserver  16.10-DAILY        westus
info:    vm image list-skus command OK
```

この情報を使用すると、元の呼び出しを先頭で呼び出すことによって、必要なイメージを正確に検索できます。

```azurecli
azure vm image list westus canonical ubuntuserver 16.04.0-LTS
info:    Executing command vm image list
+ Getting virtual machine images (Publisher:"canonical" Offer:"ubuntuserver" Sku: "16.04.0-LTS" Location:"westus")
data:    Publisher  Offer         Sku          OS     Version          Location  Urn
data:    ---------  ------------  -----------  -----  ---------------  --------  --------------------------------------------------
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201604203  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201604203
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201605161  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201605161
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606100  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606100
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201606270  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201606270
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201607210  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201607210
data:    canonical  ubuntuserver  16.04.0-LTS  Linux  16.04.201608150  westus    canonical:ubuntuserver:16.04.0-LTS:16.04.201608150
info:    vm image list command OK
```

## <a name="next-steps"></a>次のステップ
これで、使用するイメージを正確に選択できます。 検索した URN 情報を使って仮想マシンをすぐに作成する、または URN 情報のテンプレートを使用するには、[Azure CLI を使用した Linux VM の作成](virtual-machines-linux-quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)に関するページをご覧ください。

