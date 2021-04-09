---
title: インクルード ファイル
description: インクルード ファイル
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 03/05/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 178236f774b0e1a6fe20c73afe0e7fd6965e67a5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "67235367"
---
エンドポイントとメッセージのルートを設定したら、その構成をポータルで確認できます。 [Azure portal](https://portal.azure.com) にサインインし、**[リソース グループ]** に移動します。 次に、目的のリソース グループを選択し、該当するハブを選択します (このチュートリアルのハブ名は `ContosoTestHub` で始まります)。 [IoT Hub] ウィンドウが表示されます。

![IoT Hub のプロパティ画面](./media/iot-hub-include-view-routing-in-portal/01-show-hub-properties.png)

[IoT Hub] のオプションで、**[メッセージ ルーティング]** を選択します。 設定したルートが正しく表示されます。

![設定したルート](./media/iot-hub-include-view-routing-in-portal/02-show-message-routes.png)

**[メッセージ ルーティング]** 画面で **[カスタム エンドポイント]** を選択すると、そのルートに対して定義したエンドポイントが表示されます。

![ルートに対して設定したエンドポイント](./media/iot-hub-include-view-routing-in-portal/03-show-routing-endpoints.png)