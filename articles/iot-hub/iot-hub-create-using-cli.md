---
title: Azure CLI を使用して IoT Hub を作成する | Microsoft Docs
description: Azure CLI を使用して Azure IoT ハブを作成する方法。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: 78ea9071f220b2a78c6d9260d47145f22284d760
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250267"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Azure CLI を使用して IoT ハブを作成する

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

この記事では、Azure CLI を使用して IoT ハブを作成する方法を説明します。

## <a name="prerequisites"></a>前提条件

このハウツー記事を完了するには、Azure サブスクリプションが必要です。 Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-and-set-your-azure-account"></a>サインインして Azure アカウントを設定する

Cloud Shell を使用するのではなくローカルで Azure CLI を実行している場合、Azure アカウントにサインインする必要があります。

コマンド プロンプトで、[ログイン コマンド](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)を実行します。

   ```azurecli
   az login
   ```

指示に従って、コードを使用して認証し、Web ブラウザーで Azure アカウントにサインインします。

## <a name="create-an-iot-hub"></a>IoT Hub の作成

Azure CLI を使用してリソース グループを作成してから、IoT ハブを追加します。

1. IoT ハブを作成するときは、リソース グループの中に作成する必要があります。 既存のリソース グループを使用するか、以下の、[リソース グループを作成するコマンド](https://docs.microsoft.com/cli/azure/resource)を実行します。
    
   ```azurecli
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > 上の例では、West US という場所にリソース グループを作成します。 このコマンドを実行すると、利用できる場所を一覧表示できます。 
   >
   >``` bash
   >az account list-locations -o table
   >```
   >

2. IoT ハブに対してグローバルに一意の名前を使用して、以下の、リソース グループに [IoT ハブを作成するコマンド](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-create)を実行します。
    
   ```azurecli
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


上のコマンドは、課金の対象となる S1 価格レベルに IoT ハブを作成します。 詳細については、「[Azure IoT Hub の価格](https://azure.microsoft.com/pricing/details/iot-hub/)」を参照してください。

## <a name="remove-an-iot-hub"></a>IoT Hub の削除

Azure CLI を使用して、IoT ハブなどの[リソースを個別に削除](https://docs.microsoft.com/cli/azure/resource)するか、または、リソース グループを削除して、IoT ハブをはじめとするそのすべてのリソースを削除することができます。

[IoT ハブを削除](https://docs.microsoft.com/cli/azure/iot/hub#az-iot-hub-delete)するには、次のコマンドを実行します。

```azurecli
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

[リソース グループとそのすべてのリソースを削除](https://docs.microsoft.com/cli/azure/group#az-group-delete)するには、次のコマンドを実行します。

```azurecli
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>次の手順

IoT ハブの使用について詳しくは、以下の記事をご覧ください。

* [IoT Hub 開発者ガイド](iot-hub-devguide.md)
* [Azure portal を使用した IoT Hub の管理](iot-hub-create-through-portal.md)
