---
title: インクルード ファイル
description: インクルード ファイル
services: iot-hub
author: wesmc7777
ms.service: iot-hub
ms.topic: include
ms.date: 06/19/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: 49fbfe116a2fe554abb3a3ca4d1dcd6cab2b746d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "69626359"
---
次の推奨記事に進む場合は、既に作成したリソースを維持して、再利用することができます。

それ以外の場合は、課金されないようにするために、この記事で作成した Azure リソースを削除してもかまいません。 

> [!IMPORTANT]
> リソース グループを削除すると、元に戻すことができません。 リソース グループとそこに含まれるすべてのリソースは完全に削除されます。 間違ったリソース グループやリソースをうっかり削除しないようにしてください。 IoT ハブを、保持したいリソースが含まれている既存のリソース グループ内に作成した場合は、リソース グループを削除するのではなく、IoT Hub リソースだけを削除してください。
>

名前でリソース グループを削除するには、以下の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインし、 **[リソース グループ]** を選択します。

2. **[名前でフィルター処理してください]** テキスト ボックスに、IoT ハブが含まれているリソース グループの名前を入力します。 

3. 結果一覧のリソース グループの右側で、 **[...]** 、 **[リソース グループの削除]** の順に選択します。

    ![削除](./media/iot-hub-quickstarts-clean-up-resources/iot-hub-delete-resource-group.png)

4. リソース グループの削除の確認を求めるメッセージが表示されます。 確認のためにもう一度リソース グループの名前を入力し、 **[削除]** を選択します。 しばらくすると、リソース グループとそこに含まれているすべてのリソースが削除されます。