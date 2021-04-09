---
title: インクルード ファイル
description: インクルード ファイル
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 520a32ed44951a711dcb1d0975fb452829530c4f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "102244949"
---
[!INCLUDE [resource group intro text](resource-group.md)]

Cloud Shell で [`az group create`](/cli/azure/group) コマンドを使用して、リソース グループを作成します。 次の例では、*myResourceGroup* という名前のリソース グループを "*西ヨーロッパ*" の場所に作成します。 **Free** レベルの App Service がサポートされているすべての場所を表示するには、[`az appservice list-locations --sku FREE`](/cli/azure/appservice) コマンドを実行します。

```azurecli-interactive
az group create --name myResourceGroup --location "West Europe"
```

通常は、現在地付近の地域にリソース グループおよびリソースを作成します。 

コマンドが完了すると、リソース グループのプロパティが JSON 出力に表示されます。