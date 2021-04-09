---
author: baanders
description: Azure Digital Twins インスタンスをクリーンアップするためのインクルード ファイル
ms.service: digital-twins
ms.topic: include
ms.date: 2/4/2021
ms.author: baanders
ms.openlocfilehash: 9a02c4f5c5699b4a6308bfaa519fa9eb776414d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "102245024"
---
* **このチュートリアルで作成したリソースがすべて不要である場合**、この記事で使用した Azure Digital Twins インスタンスとその他すべてのリソースを [az group delete](/cli/azure/group#az-group-delete) コマンドで削除できます。 そのリソース グループとそこに含まれるすべての Azure リソースが削除されます。
    
    > [!IMPORTANT]
    > リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。
    
    [Azure Cloud Shell](https://shell.azure.com) を開き、次のコマンドを実行すると、リソース グループとそこに含まれる内容がすべて削除されます。
    
    ```azurecli-interactive
    az group delete --name <your-resource-group>
    ```