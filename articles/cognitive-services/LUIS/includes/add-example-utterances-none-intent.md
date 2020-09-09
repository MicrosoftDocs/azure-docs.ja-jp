---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.custom: include file
ms.date: 02/14/2020
ms.subservice: language-understanding
ms.author: diberry
ms.openlocfilehash: 956aa308bf1cb3736c491031239661ec6b295ddb
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/28/2020
ms.locfileid: "77279467"
---
クライアント アプリケーションは、発話がアプリケーションにとって無意味または不適切なものでないかどうかを把握する必要があります。 **None** 意図は、クライアント アプリケーションが発話に返答すべきでないかどうかを判断するために、作成プロセスの一部として各アプリケーションに追加されます。

LUIS が発話に対して **None** 意図を返した場合、クライアント アプリケーションは、ユーザーが会話の終了を望んでいるかどうかを確認するか、会話を続行するよう追加の指示を出すことができます。

**None** 意図を空のままにした場合、主題の領域の範囲外で予測する必要のある発話は、既存の主題の領域の意図のいずれかで予測されます。 その結果、チャット ボットなどのクライアント アプリケーションでは、正しくない予測に基づいて正しくない操作が実行されます。

1. 左側のパネルから **[Intents]\(意図\)** を選びます。

1. **None** 意図を選択します。 ユーザーが入力する可能性があっても、このピザの注文アプリには関係のない 3 つの発話を追加します。

    |`None` の発話例|
    |--|
    |`Barking dogs are annoying`|
    |`Penguins in the ocean`|

    これらの例では、主題の領域で予測される単語 (`pizza`、`cheese`、`crust`、`pickup``deliver` など) を使用しないでください。