---
title: 学習動作を構成する
description: 見習いモードを使用すると、Personalizer サービスとその機械学習機能が信頼できるようになり、オンライン トラフィックを危険にすることなく、学習可能な情報がサービスに送信されることのメトリックが提供されます。
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: how-to
ms.date: 05/01/2020
ms.openlocfilehash: 57a03b107678f83200b11f408784f6455cbceffd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "94579293"
---
# <a name="configure-the-personalizer-learning-behavior"></a>Personalizer の学習動作を構成する

[見習いモード](concept-apprentice-mode.md)を使用すると、Personalizer サービスとその機械学習機能が信頼できるようになり、オンライン トラフィックを危険にすることなく、学習可能な情報がサービスに送信されることの保証が提供されます。

[!INCLUDE [Important Blue Box - Apprentice mode pricing tier](./includes/important-apprentice-mode.md)]

## <a name="configure-apprentice-mode"></a>見習いモードを構成する

1. [Azure portal](https://portal.azure.com) にサインインし、Personalizer リソースに移動します。

1. **[構成]** ページの **[Learning behavior]\(学習動作\)** タブで、 **[Return baseline action, learn as an apprentice」]\(ベースライン アクションを返し、見習いとして学習する\)** を選択して、 **[保存]** を選択します。

> [!div class="mx-imgBorder"]
> ![Azure portal での見習いモード学習動作の構成のスクリーンショット](media/settings/configure-learning-behavior-azure-portal.png)

## <a name="changes-to-the-existing-application"></a>既存のアプリケーションを変更する

既存のアプリケーションでは、表示するアクションの現在の選択方法、またはアプリケーションで値 (そのアクションの **報酬**) を決定する方法を変更することはできません。 アプリケーションで変更できるのは、Personalizer の Rank API に送信されるアクションの順序だけです。 アプリケーションで現在表示されているアクションは、アクション リストの "_最初のアクション_" として送信されます。 [Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) では、この最初のアクションを使用して、Personalizer モデルがトレーニングされます。

### <a name="configure-your-application-to-call-the-rank-api"></a>Rank API を呼び出すようにアプリケーションを構成する

アプリケーションに Personalizer を追加するには、Rank API と Reward API を呼び出す必要があります。

1. 既存のアプリケーションのロジックで、アクションとその特徴のリストを決定する場所の後に、[Rank API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Rank) の呼び出しを追加します。 アクション リストの最初のアクションは、既存のロジックによって選択されたアクションである必要があります。

1. Rank API の応答の **報酬アクション ID** に関連付けられているアクションを表示するように、コードを構成しします。

### <a name="configure-your-application-to-call-reward-api"></a>Reward API を呼び出すようにアプリケーションを構成する

1. 既存のビジネス ロジックを使用して、表示されたアクションの **報酬** を計算します。 値は、0 から 1 の範囲になる必要があります。 [Reward API](https://westus2.dev.cognitive.microsoft.com/docs/services/personalizer-api/operations/Reward) を使用して、この報酬を Personalizer に送ります。 ビジネス ロジックによっては、報酬の値はすぐに必要ではなく、遅くなってもかまわない場合があります。

1. 構成された **報酬待機時間** 内に報酬を返さないと、代わりに既定の報酬が使用されます。

## <a name="evaluate-apprentice-mode"></a>見習いモードを評価する

Azure portal の Personalizer リソースに対する **[評価]** ページで、 **[Current learning behavior performance]\(現在の学習動作のパフォーマンス\)** を確認します。

> [!div class="mx-imgBorder"]
> ![Azure portal での見習いモード学習動作の評価確認のスクリーンショット](media/settings/evaluate-apprentice-mode.png)

見習いモードでは、次の **評価メトリック** が提供されます。
* **ベースライン - 平均報酬**: アプリケーションの既定値 (ベースライン) の平均報酬。
* **Personalizer - 平均報酬**: Personalizer で達成している可能性がある報酬合計の平均。
* **最新 1000 イベントでの報酬達成率**: ベースライン報酬と Personalizer 報酬の比率 – 最新の 1000 件のイベントで正規化されます。

## <a name="switch-behavior-to-online-mode"></a>動作をオンライン モードに切り替える

75-85% のローリング平均で Personalizer がトレーニングされたと判断したら、モデルはオンライン モードに切り替えることができる状態です。

Azure portal で、Personalizer リソースの **[構成]** ページの **[Learning behavior]\(学習動作\)** タブに移動し、 **[Return the best action]\(最適なアクションを返す\)** を選択して、 **[保存]** を選択します。

Rank API と Reward API の呼び出しを変更する必要はありません。

## <a name="next-steps"></a>次のステップ

* [モデルと学習設定の管理](how-to-manage-model.md)
