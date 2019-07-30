---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: 198c3a585dd2d6f6809a1a25b9a732c0ad4a704f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2019
ms.locfileid: "68423581"
---
**[パフォーマンス]** タブの左ウィンドウにある **確率しきい値** スライダーに注目してください。これは、正しいと見なされるために予測で必要となる信頼度です (精度と再現率を計算するため)。 

高い確率しきい値を指定した予測呼び出しを解釈する場合、再現率を犠牲にして高い精度で結果が返される傾向があります&mdash;検出される分類は正しいですが、多くは検出されないままです。 低い確率しきい値はその逆を行います&mdash;実際の分類のほとんどが検出されますが、そのセット内には誤検知が多くあります。 これを念頭に置いて、プロジェクトの具体的な必要に応じて確率しきい値を設定する必要があります。 その後、クライアント側で予測結果を受け取る場合は、ここで使用したのと同じ確率しきい値の値を使用する必要があります。