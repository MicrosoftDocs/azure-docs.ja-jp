---
title: CLI を使用して Azure スポット VM をデプロイする
description: CLI を使用して Azure スポット VM をデプロイし、コストを節約する方法について説明します。
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 165a2c6221c8d3c14f71134deef962d0859bb438
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88816713"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>Azure CLI を使用してスポット VM をデプロイする

[Azure スポット VM](spot-vms.md) を使うと、非常に低コストで未使用の容量を利用できます。 Azure で容量の回復が必要になると常に、Azure インフラストラクチャはスポット VM を削除します。 したがって、スポット VM は、バッチ処理ジョブ、開発/テスト環境、大規模なコンピューティング ワークロードなど、中断してもかまわないワークロードに最適です。

スポット VM の価格は、リージョンと SKU に基づいて変化します。 詳細については、[Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) と [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) の VM 価格を参照してください。 

VM に対して、1 時間あたりに支払うことができる最大価格を設定するオプションがあります。 スポット VM の最大価格は、小数点以下最大 5 桁を使用して米ドル (USD) で設定できます。 たとえば、`0.98765` の値は、1 時間あたり $0.98765 米ドルの最大価格になります。 最大価格を `-1` に設定した場合、VM は価格に基づいて削除されません。 VM の価格は、使用可能な容量とクォータがある限り、現在のスポットの価格または標準 VM の価格のいずれか低い方になります。 最大価格の設定の詳細については、[スポット VM の価格](../spot-vms.md#pricing)に関するページを参照してください。

Azure CLI を使用してスポットで VM を作成するプロセスは、[クイックスタートの記事](./quick-create-cli.md)で詳しく説明されている内容と同じです。 '--priority Spot' パラメーターを追加し、`--eviction-policy` を Deallocate (既定値) または `Delete` に設定して、最大価格または `-1` を指定するだけです。 


## <a name="install-azure-cli"></a>Azure CLI のインストール

スポット VM を作成するには、Azure CLI バージョン 2.0.74 以降が実行されている必要があります。 バージョンを確認するには、**az --version** を実行します。 インストールまたはアップグレードが必要な場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。 

[az login](/cli/azure/reference-index#az-login) を使用して Azure にサインインします。

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>スポット VM を作成する

この例では、価格に基づいて削除されない Linux スポット VM をデプロイする方法を示しています。 この VM の割り当てを解除するように削除ポリシーが設定されているため、後で再起動することができます。 VM が削除されるときに VM と基になるディスクを削除する場合は、`--eviction-policy` を `Delete` に設定します。

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



VM が作成されたら、クエリを実行して、リソース グループ内のすべての VM の最大請求価格を確認できます。

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

**次の手順**

[Azure PowerShell](../windows/spot-powershell.md)、[portal](../windows/spot-portal.md)、または[テンプレート](spot-template.md)を使用して、スポット VM を作成することもできます。

エラーが発生した場合は、[エラー コード](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)を参照してください。
