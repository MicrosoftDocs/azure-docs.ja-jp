---
title: Conversation Learner 制御フロー - Microsoft Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Conversation Learner 制御フローについて説明します。
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: e0a0a88e249c0a032e5afaeea14b9b3cfcbdc319
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58080662"
---
## <a name="control-flow"></a>制御フロー

このドキュメントでは、次の図に示すように Conversation Learner (CL) の制御フローについて説明します。

![](media/controlflow.PNG)

1. ユーザーはボットに、「シアトルはどんな天気ですか?」のような用語や句を入力します
1. CL は、エンティティを抽出する機械学習モデルにユーザー入力を渡します
   - このモデルは、Conversation Learner によってビルドされ、 www.luis.ai によってホストされています
1. 抽出されたすべてのエンティティとユーザーの入力テキストは、ボットのコードで Entity Detection Callback メソッドに渡されます。
    - このコードは、エンティティ値を設定/クリア/操作できます
1. CL ニューラル ネットワークが続いて、エンティティ抽出の出力とユーザー入力を受け取り、ボットに定義されているすべてのアクションを点数化します
   - この例では、最も可能性の高いアクションは、天気予報を提供することになります。

     ![](media/controlflow_forecast.PNG)

1. この場合、選択したアクションは、API 呼び出しに天気予報を取得するように要求します。 
1. CL.AddCallback メソッドを使用して登録されたこの API が続いて呼び出されます。  次にこの API の結果が、「晴れ、華氏 67 度」のようなメッセージとしてユーザーに返されます。
1. この呼び出しは続いて、ニューラル ネットワークにされ、前の手順に基づいて次のアクションを決定します。
1. ニューラル ネットワークは続いて、次の一連の可能なアクションを予測し、この場合は「他に何かありますか?」という選択したアクションがユーザーに提示されます。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Conversation Learner での学習方法](./how-to-teach-cl.md)
