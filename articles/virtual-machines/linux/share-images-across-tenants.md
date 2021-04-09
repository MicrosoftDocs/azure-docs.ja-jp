---
title: テナント間でギャラリー イメージを共有する
description: 共有イメージ ギャラリーと Azure CLI を使用して Azure テナント間で VM イメージを共有する方法について説明します。
author: axayjo
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/04/2019
ms.author: akjosh
ms.reviewer: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: 2ef788659c871a9bcef6f519664689eacda94daa
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102552883"
---
# <a name="share-gallery-vm-images-across-azure-tenants-using-the-azure-cli"></a>Azure CLI を使用して Azure テナント間でギャラリー VM イメージを共有する

共有イメージ ギャラリーでは、Azure RBAC を使用してイメージを共有できます。 Azure RBAC を使用すると、テナント内でイメージを共有できます。また、テナントの外部にいる個人と共有することもできます。 この単純な共有オプションの詳細については、「[ギャラリーを共有する](./shared-images-portal.md#share-the-gallery)」を参照してください。

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]

> [!IMPORTANT]
> ポータルを使用して、別の Azure テナント内のイメージから VM をデプロイすることはできません。 テナント間で共有されているイメージから VM を作成するには、Azure CLI または [Powershell](../windows/share-images-across-tenants.md) を使用する必要があります。

## <a name="create-a-vm-using-azure-cli"></a>Azure CLI を使用した VM の作成

appID、アプリ キー、およびテナント 1 の ID を使用して、テナント 1 のサービス プリンシパルにサインインします。 `az account show --query "tenantId"` を使用して、必要に応じてテナント ID を取得できます。

```azurecli-interactive
az account clear
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 1 ID>'
az account get-access-token 
```
 
appID、アプリ キー、およびテナント 2 の ID を使用して、テナント 2 のサービス プリンシパルにサインインします。

```azurecli-interactive
az login --service-principal -u '<app ID>' -p '<Secret>' --tenant '<tenant 2 ID>'
az account get-access-token
```

VM を作成します。 例の情報を自身のものに置き換えます。

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>" \
  --admin-username azureuser \
  --generate-ssh-keys
```

## <a name="next-steps"></a>次のステップ

問題が生じた場合は、[共有イメージ ギャラリーに関するトラブルシューティング](../troubleshooting-shared-images.md)を行うことができます。
