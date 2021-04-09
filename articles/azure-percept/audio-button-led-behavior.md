---
title: Azure Percept Audio のボタンと LED の動作
description: Azure Percept Audio のボタンと LED の状態について詳しく説明します
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: conceptual
ms.date: 02/18/2021
ms.custom: template-concept
ms.openlocfilehash: 1d956e33a84b5509c16400c8f5f8e813d116411a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102095750"
---
# <a name="azure-percept-audio-button-and-led-behavior"></a>Azure Percept Audio のボタンと LED の動作

Azure Percept Audio のボタンと LED の状態については、次のガイダンスを参照してください。

## <a name="button-behavior"></a>ボタンの動作

デバイスの動作は、ボタンを使用して制御することができます。

|ボタンの状態|  動作|
|------------|----------|
|Mute|  マイクアレイをミュートまたはミュート解除します。 ボタンを押すと、そのイベントがトリガーされます。|
|PTT と PTS|   キーワード スポッティング状態をバイパスし、コマンド リッスン状態をアクティブ化するには、PTT を押します。 もう一度押すと、エージェントのアクティブな会話が停止され、キーワード スポッティング状態に戻ります。 ボタンを押すと、そのイベントがトリガーされます。 PTS は、エージェントが聞いたり、考えたりしているときではなく、エージェントが話している間にボタンが押されている場合にのみ機能します。|

## <a name="led-behavior"></a>LED の動作

LED インジケーターを使用して、デバイスの状態を把握できます。

|LED|   LED の状態|  Ear SoM 状態|
|---|------------|----------------| 
|L02|   1 つが白色、静的オン |電源投入 |
|L02|   1 つが白色、0.5 Hz 点滅|  認証が進行中 |
|L01、L02、L03|   3 つが青色、静的オン|     キーワードの待機中|
|L01、L02、L03|   LED アレイ点滅、20fps | リッスン中または話し中|
|L01、L02、L03|   LED アレイ レーシング、20fps|    処理中|
|L01、L02、L03|   3 つが赤色、静的オン | Mute|

## <a name="next-steps"></a>次のステップ

Azure Percept Audio デバイスのトラブルシューティングのヒントについては、こちらの[ガイド](./troubleshoot-audio-accessory-speech-module.md)を参照してください。