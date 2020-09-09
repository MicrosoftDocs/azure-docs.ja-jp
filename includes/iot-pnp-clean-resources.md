---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 6cd983b6421fc821853aa22d4dc2c297f672d292
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87336899"
---
## <a name="clean-up-resources"></a>リソースをクリーンアップする

引き続きその他の IoT プラグ アンド プレイの記事に進む場合は、この記事で使用したリソースを保持し、再使用できます。 それ以外の場合、追加料金が発生するのを防ぐために、この記事で作成したリソースは削除してください。

次の Azure CLI コマンドを使用してリソース グループ全体を削除することで、ハブと登録済みデバイスの両方を一度に削除できます。 ただし、これらのリソースと保持したい他のリソースがリソース グループを共有している場合、このコマンドは使用しないでください。

```azurecli-interactive
az group delete --name <YourResourceGroupName>
```

IoT ハブのみを削除するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli-interactive
az iot hub delete --name <YourIoTHubName>
```

IoT ハブに登録したデバイス ID のみを削除するには、Azure CLI を使用して次のコマンドを実行します。

```azurecli-interactive
az iot hub device-identity delete --hub-name <YourIoTHubName> --device-id <YourDeviceID>
```

また、クローンしたサンプル ファイルをお使いの開発用マシンから削除することもできます。
