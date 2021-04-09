---
author: laujan
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 06/12/2019
ms.author: lajanuar
ms.openlocfilehash: 89b035397ea2050ae7e61f2a19310b6a7fb4192c
ms.sourcegitcommit: 3ea12ce4f6c142c5a1a2f04d6e329e3456d2bda5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/15/2021
ms.locfileid: "103467244"
---
`"pageResults"` ノードの下で、キーと値形式の結果ごとに `"confidence"` 値を調べます。 また、`"readResults"` ノード内の信頼度スコアにも注目してください。こちらはテキスト読み取り操作に対応します。 読み取り結果の信頼度は、キーと値の抽出結果の信頼度には影響しません。したがって両方を確認する必要があります。
* 読み取り操作の信頼度スコアが低い場合は、入力ドキュメントの品質の改善を試みてください (「[入力の要件](../overview.md#input-requirements)」を参照)。
* キーと値の抽出操作の信頼度スコアが低い場合は、分析対象となるドキュメントの種類が、トレーニング セットで使用されているドキュメントと同じであることを確認してください。 トレーニング セットに含まれるドキュメントの体裁にばらつきがある場合は、別々のフォルダーに分けて、バリエーションごとに 1 つのモデルをトレーニングすることを検討してください。

目標とする信頼度スコアは、実際のユース ケースによって異なりますが、一般には、80% 以上のスコアを目標にするのがよいでしょう。 医療記録や請求書の読み取りなど、もっと正確さが要求されるケースでは、100% のスコアが推奨されます。