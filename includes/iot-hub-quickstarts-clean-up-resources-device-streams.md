---
title: インクルード ファイル (デバイス ストリーム)
description: インクルード ファイル
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 01/15/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 46ab75f161692dd048c19698af7027d0e0622b37
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "82506424"
---
次の推奨記事に進む予定の場合は、既に作成したリソースを維持して、再利用することができます。

それ以外の場合は、課金されないようにするために、この記事で作成した Azure リソースを削除してもかまいません。

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 IoT ハブを、保持したいリソースが含まれている既存のリソース グループ内に作成した場合は、リソース グループを削除するのではなく、IoT ハブ リソースだけを削除してください。
>

名前でリソース グループを削除するには、以下の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。

1. **[名前でフィルター]** ボックスに、IoT ハブが含まれているリソース グループの名前を入力します。

1. 結果一覧で、目的のリソース グループの右側にある省略記号 (**[...]**) を選択し、**[リソース グループの削除]** を選択します。

    ![[リソース グループの削除] ボタン](./media/iot-hub-quickstarts-clean-up-resources-device-streams/iot-hub-delete-resource-group.png)

1. リソース グループの削除を確認するために、リソース グループ名を再入力してから、**[削除]** を選択します。 しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。
