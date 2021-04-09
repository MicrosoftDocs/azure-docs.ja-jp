---
title: アクティブなイベントと非アクティブなイベント- Personalizer
description: この記事では、Personalizer サービス内でのアクティブおよび非アクティブなイベントの使用について説明します。
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 7d1044d02ceba1f3d0996b1fe1c8a9a44b31049b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "91253651"
---
# <a name="active-and-inactive-events"></a>アクティブなイベントと非アクティブなイベント

**アクティブな** イベントは、顧客に結果を示し、報酬スコアを決定することがわかっている場合の Rank のすべての呼び出しです。 これが既定の動作です。

**非アクティブな** イベントは、ビジネス ロジックが原因でユーザーが推奨されるアクションを確認するかどうか不明な場合の Rank の呼び出しです。 これにより、Personalizer が既定の報酬でトレーニングされないように、そのイベントを破棄することができます。 非アクティブなイベントは、Reward API を呼び出すことはできません。

学習ループでイベントの実際の種類が認識されていることが重要です。 非アクティブなイベントには、Reward の呼び出しはありません。 アクティブなイベントには Reward 呼び出しが必要ですが、その API 呼び出しが行われない場合は、既定の報酬スコアが適用されます。 イベントの状態がユーザー エクスペリエンスに影響することがわかった場合は、すぐにそれを非アクティブからアクティブに変更します。

## <a name="typical-active-events-scenario"></a>アクティブなイベントの一般的なシナリオ

アプリケーションから Rank API を呼び出すと、アプリケーションで **rewardActionId** フィールドに表示する必要のあるアクションを受け取ります。  その時点から、Personalizer では、eventId が同じである報酬スコアを含む Reward 呼び出しが予期されます。 報酬スコアは、以降の Rank 呼び出しに対するモデルのトレーニングに使用されます。 eventId に対して Reward の呼び出しを受け取っていない場合は、既定の報酬が適用されます。 [既定の報酬](how-to-settings.md#configure-rewards-for-the-feedback-loop)は、Azure portal の Personalizer リソースに設定されます。

## <a name="other-event-type-scenarios"></a>その他のイベントの種類のシナリオ

一部のシナリオでは、結果が使用されるか、ユーザーに表示されるかを知るよりも前に、アプリケーションで Rank を呼び出す必要があります。 これは、たとえば、昇格したコンテンツのページ レンダリングがマーケティング キャンペーンで上書きされる場合に発生する可能性があります。 Rank の呼び出しの結果が使用されておらず、ユーザーがそれを見ていない場合は、対応する Reward の呼び出しを送信しないでください。

通常、これらのシナリオは次の場合に発生します。

* ユーザーに表示されるかどうかにかかわらず、UI の事前レンダリングを行っている。
* アプリケーションで、リアルタイム性の低いコンテキストを使用して Rank 呼び出しが行われる予測パーソナル化が行われていて、その出力がアプリケーションで使用される場合もあれば、使用されない場合もある。

このような場合は、Personalizer を使用して Rank を呼び出し、イベントを "_非アクティブ_" にするよう要求します。 Personalizer では、このイベントに対する報酬は必要とされず、既定の報酬も適用されません。

後のビジネス ロジックにおいて、アプリケーションで Rank 呼び出しの情報を使用する場合は、イベントを "_アクティブ化_" するだけです。 イベントがアクティブになると直ちに、Personalizer でイベントの報酬が必要になります。 Reward API の明示的な呼び出しが行われない場合、Personalizer では既定の報酬が適用されます。

## <a name="inactive-events"></a>非アクティブなイベント

イベントのトレーニングを無効にするには、`learningEnabled = False` を使用して Rank を呼び出します。

非アクティブなイベントの学習は、eventId に対して報酬が送信されたり、その eventId に対して `activate` API が呼び出されたりすると、暗黙的にアクティブになります。

## <a name="next-steps"></a>次のステップ

* [報酬スコアを決定する方法と考慮すべきデータ](concept-rewards.md)を確認します。
