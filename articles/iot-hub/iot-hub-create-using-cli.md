---
title: Azure CLI を使用して IoT Hub を作成する | Microsoft Docs
description: Azure CLI コマンドを使用してリソース グループを作成し、そのリソース グループに IoT ハブを作成する方法を説明します。 また、ハブを削除する方法も説明します。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e9757b94330f889869080a0e961ab2728cee86f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "94659932"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Azure CLI を使用して IoT ハブを作成する

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

この記事では、Azure CLI を使用して IoT ハブを作成する方法を説明します。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>IoT Hub の作成

Azure CLI を使用してリソース グループを作成してから、IoT ハブを追加します。

1. IoT ハブを作成するときは、リソース グループの中に作成する必要があります。 既存のリソース グループを使用するか、以下の、[リソース グループを作成するコマンド](/cli/azure/resource)を実行します。
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > 上の例では、West US という場所にリソース グループを作成します。 このコマンドを実行すると、利用できる場所を一覧表示できます。 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. IoT ハブに対してグローバルに一意の名前を使用して、以下の、リソース グループに [IoT ハブを作成するコマンド](/cli/azure/iot/hub#az-iot-hub-create)を実行します。
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


上のコマンドは、課金の対象となる S1 価格レベルに IoT ハブを作成します。 詳細については、「[Azure IoT Hub の価格](https://azure.microsoft.com/pricing/details/iot-hub/)」を参照してください。

## <a name="remove-an-iot-hub"></a>IoT Hub の削除

Azure CLI を使用して、IoT ハブなどの[リソースを個別に削除](/cli/azure/resource)するか、または、リソース グループを削除して、IoT ハブをはじめとするそのすべてのリソースを削除することができます。

[IoT ハブを削除](/cli/azure/iot/hub#az-iot-hub-delete)するには、次のコマンドを実行します。

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

[リソース グループとそのすべてのリソースを削除](/cli/azure/group#az-group-delete)するには、次のコマンドを実行します。

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>次のステップ

IoT ハブの使用について詳しくは、以下の記事をご覧ください。

* [IoT Hub 開発者ガイド](iot-hub-devguide.md)
* [Azure portal を使用した IoT Hub の管理](iot-hub-create-through-portal.md)