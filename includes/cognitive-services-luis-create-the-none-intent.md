---
title: インクルード ファイル
description: インクルード ファイル
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.custom: include file
ms.date: 12/21/2018
ms.author: diberry
ms.openlocfilehash: 355fe134939b26c51d6e03368f782845628a6b96
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/23/2019
ms.locfileid: "56738898"
---
クライアント アプリケーションは、発話がアプリケーションにとって無意味または不適切なものでないかどうかを把握する必要があります。 **None** 意図は、発話がクライアント アプリケーションから返答できないものであるかどうかを判断するための作成プロセスの一部として、各アプリケーションに追加されます。

LUIS が発話に対して **None** 意図を返した場合、クライアント アプリケーションは、ユーザーが会話の終了を望んでいるかどうかを確認するか、会話を続行するよう追加の指示を出すことができます。 

> [!CAUTION] 
> **None** 意図は空のままにしないでください。 

1. 左側のパネルから **[Intents]\(意図\)** を選びます。

2. **None** 意図を選択します。 ユーザーが入力する可能性があっても、この人事アプリには関係のない 3 つの発話を追加します。

    | 発話の例|
    |--|
    |吠える犬はうっとうしい|
    |ピザを注文して|
    |海の中のペンギン|
