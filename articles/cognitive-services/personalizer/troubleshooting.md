---
title: トラブルシューティング - Personalizer
titleSuffix: Azure Cognitive Services
description: この記事では、Personalizer のトラブルシューティングに関する質問を確認できます。
author: edjez
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: article
ms.date: 06/15/2019
ms.author: edjez
ms.openlocfilehash: be6119d96b89622f45db1099a47e858a5893c2cb
ms.sourcegitcommit: dad277fbcfe0ed532b555298c9d6bc01fcaa94e2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/10/2019
ms.locfileid: "67722249"
---
# <a name="personalizer-troubleshooting"></a>Personalizer のトラブルシューティング

この記事では、Personalizer についてよく寄せられる質問とその回答を示します。

## <a name="learning-loop"></a>学習ループ

### <a name="the-learning-loop-doesnt-seem-to-learn-how-do-i-fix-this"></a>学習ループで学習が行われていないようです。 これをどのように修正すればよいですか?

学習ループでは、ランキング呼び出しによる優先順位の設定を効果的に行う前に、数千の報酬呼び出しが必要です。 

学習ループの現在の動作に不安がある場合は、[オフライン評価](concepts-offline-evaluation.md)を実行し、修正された学習ポリシーを適用してください。 

## <a name="next-steps"></a>次の手順

[モデルの更新頻度を構成する](how-to-settings.md#model-update-frequency)