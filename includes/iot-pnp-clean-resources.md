---
author: dominicbetts
ms.author: dominicbetts
ms.service: iot-pnp
ms.topic: include
ms.date: 11/15/2019
ms.openlocfilehash: 647226091c3e15a19bf8262c23e62d95018488b3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99831207"
---
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
