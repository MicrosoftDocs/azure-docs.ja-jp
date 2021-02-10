---
author: v-dalc
ms.service: databox
ms.author: alkohli
ms.topic: include
ms.date: 01/21/2021
ms.openlocfilehash: 3defa62c55bb5ab042ade816f611ea45b39a0117
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/25/2021
ms.locfileid: "98761558"
---
IoT Edge エージェント ランタイムの応答を使用すると、コンピューティング関連のエラーをトラブルシューティングできます。 考えられる応答の一覧を以下に示します。

* 200 - OK
* 400 - デプロイ構成が正しくない形式であるか、無効です。
* 417 - デバイスにデプロイ構成セットがありません。
* 412 - デプロイ構成にあるスキーマ バージョンが無効です。
* 406 - IoT Edge デバイスがオフラインであるか、状態レポートを送信していません。
* 500 - IoT Edge ランタイムでエラーが発生しました。

詳細については、「[IoT Edge エージェント](/azure/iot-edge/iot-edge-runtime?view=iotedge-2018-06&preserve-view=true#iot-edge-agent)」を参照してください。