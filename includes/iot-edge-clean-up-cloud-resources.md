---
title: インクルード ファイル
description: インクルード ファイル
services: iot-edge
author: kgremban
ms.service: iot-edge
ms.topic: include
ms.date: 08/10/2018
ms.author: kgremban
ms.custom: include file
ms.openlocfilehash: 99dcfa46280c6fc00b27fa43fd6079c4ac32bd3a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97704684"
---
### <a name="delete-azure-resources"></a>Azure リソースを削除する 

Azure のリソースとリソース グループは、削除すると元に戻すことができません。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 保持したいリソースが含まれている既存のリソース グループ内に IoT ハブを作成した場合は、リソース グループを削除するのではなく、IoT ハブ リソースだけを削除してください。

リソースを削除するには、次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。

2. IoT Edge のテスト リソースを含んだリソース グループの名前を選択します。 

3. リソース グループに含まれているリソースの一覧を確認します。 それらすべてを削除する場合は、 **[リソース グループの削除]** を選択します。 一部だけを削除する場合は、削除する各リソースをクリックして個別に削除してください。 
